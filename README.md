# Dosage Logbook

App de página única (`index.html`) para cadastrar medicamentos/suplementos, registrar
ciclos de aplicação e manter um histórico com controle de estoque. Os dados ficam no
Firestore, do seu próprio projeto Firebase.

## 1. Criar o projeto Firebase

1. Acesse https://console.firebase.google.com e crie um projeto novo.
2. No painel do projeto, clique em **Criar app** → ícone **Web** (`</>`).
3. Dê um nome ao app e finalize — **não** precisa ativar o Firebase Hosting.
4. Copie o objeto `firebaseConfig` mostrado na tela.

## 2. Ativar o Authentication (login)

1. No menu lateral, vá em **Build → Authentication → Get started**.
2. Na aba **Sign-in method**, ative o provedor **E-mail/senha**.
3. Não é preciso criar nenhum usuário por aqui — a própria tela de login do app
   tem a opção "Ainda não tenho conta — criar acesso" para o primeiro cadastro.
   Depois disso, se quiser, você pode voltar a esse painel e desativar a opção
   de auto-cadastro removendo esse botão do `index.html` (ou simplesmente não
   compartilhando o link com mais ninguém).

## 3. Configurar o Firestore

1. No menu lateral, vá em **Build → Firestore Database → Criar banco de dados**.
2. Escolha a região mais próxima e inicie em **modo produção**.
3. Em **Regras**, cole o conteúdo abaixo — ele exige login para ler ou escrever
   qualquer dado, então mesmo com a URL do projeto em mãos ninguém sem conta
   consegue acessar:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
         allow read, write: if request.auth != null;
       }
     }
   }
   ```

## 4. Editar o `index.html`

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

## 5. Publicar no GitHub Pages

1. Crie um repositório novo no GitHub (pode ser privado ou público).
2. Suba o `index.html` para a raiz do repositório.
3. Vá em **Settings → Pages**.
4. Em **Source**, escolha a branch `main` e a pasta `/ (root)`.
5. Salve — em alguns minutos o app estará em
   `https://SEU_USUARIO.github.io/NOME_DO_REPOSITORIO/`.

## Como o app funciona

- **Login**: a primeira tela pede e-mail e senha. Use "criar acesso" na primeira vez;
  depois disso, é só entrar normalmente. O botão "sair" fica no topo do app.
- **Início (Dashboard)**: tela inicial com a última aplicação registrada, a previsão
  da próxima aplicação (calculada a partir dos ciclos), um resumo dos últimos 7 dias
  (nº de aplicações e mg totais), um alerta quando algum produto está com estoque
  baixo ou zerado, e a visão geral do estoque de todas as substâncias.
- **Substâncias**: cadastra nome, apelido (opcional) concentração (mg/ml) e volume
  disponível (ml). Cada uma aparece com um indicador visual do estoque restante,
  e o apelido é mostrado entre aspas ao lado do nome nas listagens e seletores.
- **Ciclos**: define um nome e o horário, e adiciona uma ou mais substâncias ao
  ciclo — cada substância tem sua própria dose (ml) e seus próprios dias da semana
  (use "+ Adicionar substância" para incluir mais de uma).
- **Registrar**: escolhe o ciclo e o app já mostra, marcadas, todas as substâncias
  daquele ciclo agendadas para o dia de hoje, com a dose de cada uma pré-preenchida
  (editável). Dá pra desmarcar as que não vai aplicar e registrar várias substâncias
  de uma vez só. A data e hora vêm preenchidas com o momento atual, também editável.
  O app calcula o total em mg antes de salvar e desconta cada quantidade do estoque
  da respectiva substância.
- **Histórico**: lista todas as aplicações registradas, com data/hora, ml e mg.

### Navegação

- **No celular**: uma barra de navegação fixa embaixo (Início · Estoque · Ciclos ·
  Histórico) e um botão flutuante (+) no canto inferior direito. Tocando nele, abre
  um menu com "Registrar aplicação", "Nova substância" e "Novo ciclo" — ao escolher
  uma opção, o app já leva direto pra tela correspondente.
- **No desktop** (telas a partir de 640px): as mesmas seções ficam em abas no topo,
  incluindo "Registrar", sem o botão flutuante.

Todos os dados sincronizam em tempo real com o Firestore — se você abrir o app em
dois dispositivos logados no mesmo projeto, ambos atualizam automaticamente.
