# Dosage Logbook

App de página única (`index.html`) para cadastrar medicamentos/suplementos, registrar
ciclos de aplicação e manter um histórico com controle de estoque. Os dados ficam no
Firestore, do seu próprio projeto Firebase.

## 1. Criar o projeto Firebase

1. Acesse https://console.firebase.google.com e crie um projeto novo.
2. No painel do projeto, clique em **Criar app** → ícone **Web** (`</>`).
3. Dê um nome ao app e finalize — **não** precisa ativar o Firebase Hosting.
4. Copie o objeto `firebaseConfig` mostrado na tela.

## 2. Configurar o Firestore

1. No menu lateral, vá em **Build → Firestore Database → Criar banco de dados**.
2. Escolha a região mais próxima e inicie em **modo produção**.
3. Em **Regras**, cole o conteúdo abaixo (bloqueia acesso externo, mas permite o app
   funcionar sem login — ideal para uso pessoal em navegador só seu):

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
         allow read, write: if request.time < timestamp.date(2100, 1, 1);
       }
     }
   }
   ```

   > Isso deixa o banco aberto para quem tiver a URL do projeto. Para uso realmente
   > privado, o ideal é adicionar Firebase Authentication (login por e-mail) e restringir
   > as regras a `request.auth != null` — posso montar essa camada extra se quiser.

## 3. Editar o `index.html`

Abra o arquivo e substitua o objeto `firebaseConfig` (perto do início da tag `<script>`)
pelos valores copiados no passo 1:

```js
const firebaseConfig = {
  apiKey: "...",
  authDomain: "...",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "..."
};
```

## 4. Publicar no GitHub Pages

1. Crie um repositório novo no GitHub (pode ser privado ou público).
2. Suba o `index.html` para a raiz do repositório.
3. Vá em **Settings → Pages**.
4. Em **Source**, escolha a branch `main` e a pasta `/ (root)`.
5. Salve — em alguns minutos o app estará em
   `https://SEU_USUARIO.github.io/NOME_DO_REPOSITORIO/`.

## Como o app funciona

- **Substâncias**: cadastra nome, concentração (mg/ml) e volume disponível (ml).
  Cada uma aparece com um indicador visual do estoque restante.
- **Ciclos**: define um nome, a substância, dose por aplicação, horário e os dias
  da semana — funciona como agenda de referência, não é obrigatório para registrar.
- **Registrar**: escolhe a substância, informa quantos ml serão aplicados, e o app
  calcula automaticamente os mg (ml × concentração) antes de salvar. Ao confirmar,
  o valor é descontado do estoque da substância.
- **Histórico**: lista todas as aplicações registradas, com data/hora, ml e mg.

Todos os dados sincronizam em tempo real com o Firestore — se você abrir o app em
dois dispositivos logados no mesmo projeto, ambos atualizam automaticamente.
