# 🛡️ Redline Manager — Painel Web de Gestão

Painel web SPA (*Single Page Application*) moderno, responsivo e em tempo real, desenvolvido para o gerenciamento operacional de equipes e facções no GTA RP / Discord. Permite acompanhar metas de vendas de kits, plantões e horas trabalhadas, rankings de produtividade e controle hierárquico.

---

## 🚀 Funcionalidades

- 📊 **Dashboard Geral**: Visão panorâmica de metas, total arrecadado, progresso semanal e kits no baú.
- 💰 **Gestão de Vendas**: Tabela detalhada de vendas de kits por membro com busca em tempo real, filtros e cálculos automáticos de taxas.
- ⏱️ **Controle de Horas**: Acompanhamento de horas trabalhadas/plantões com indicador de cumprimento de metas mínimas por cargo.
- 🏆 **Rankings & Destaques**: Reconhecimento automático de Top Horas, Top Recrutador e Impulsionadores (Boosters).
- 👑 **Hierarquia Visual**: Exibição dos membros organizados por cargos e patentes.
- 🔒 **Segurança por PIN**:
  - **PIN de Acesso**: Protege a visualização dos dados.
  - **PIN de Administrador**: Restringe ações críticas e configurações.
  - **PIN de Recrutador**: Acesso a abas específicas de recrutamento.
- 📑 **Exportação Excel**: Geração de planilhas formatadas (`.xlsx`) com histórico de vendas e fechamentos.
- 🌓 **Interface Moderna**: Tema escuro (*Dark Mode*), design limpo com Tailwind CSS e ícones Lucide.

---

## 🛠️ Tecnologias Utilizadas

- **Frontend**: [Vue.js 3](https://vuejs.org/) (Composition API via CDN)
- **Estilização**: [Tailwind CSS](https://tailwindcss.com/)
- **Ícones**: [Lucide Icons](https://lucide.dev/)
- **Planilhas**: [SheetJS (xlsx)](https://sheetjs.com/)
- **Backend / Banco de Dados**: [Google Firebase](https://firebase.google.com/)
  - **Cloud Firestore**: Banco de dados NoSQL em tempo real.
  - **Firebase Authentication**: Autenticação anônima para controle de sessão.
  - **Firebase Hosting**: Hospedagem otimizada para aplicações web estáticas.

---

## ⚙️ Pré-requisitos e Configuração do Firebase

O painel utiliza o **Google Firebase Firestore** para sincronização em tempo real (inclusive integrado com o bot do Discord). Para configurá-lo:

### 1. Criar Projeto no Firebase
1. Acesse o [Firebase Console](https://console.firebase.google.com/).
2. Clique em **Adicionar projeto** e siga as instruções na tela.

### 2. Ativar o Cloud Firestore
1. No menu lateral, acesse **Build > Firestore Database**.
2. Clique em **Criar banco de dados**.
3. Selecione o local do servidor mais próximo (ex: `southamerica-east1` em São Paulo).
4. Em **Regras de segurança**, configure regras adequadas para leitura e escrita autenticada:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      // Permite leitura e escrita para usuários autenticados (inclusive autenticação anônima)
      allow read, write: if request.auth != null;
    }
  }
}
```

### 3. Ativar Autenticação Anônima
1. No menu lateral, acesse **Build > Authentication**.
2. Clique em **Começar** (se for a primeira vez).
3. Na aba **Sign-in method**, clique em **Anônimo** e ative a opção.

### 4. Obter as Credenciais Web
1. Nas configurações do projeto (ícone de engrenagem no topo > **Configurações do projeto**).
2. Na seção **Seus aplicativos**, clique no ícone Web (`</>`).
3. Dê um apelido ao app (ex: `Painel Web Redline`) e copie o objeto `firebaseConfig`.

### 5. Configurar no Código
Abra o arquivo `public/index.html` e localize o bloco de configuração por volta da linha 2720:

```javascript
// --- CONFIGURAÇÃO FIREBASE ---
firebaseConfig = {
    apiKey: "SUA_API_KEY_AQUI",
    authDomain: "SEU_PROJETO.firebaseapp.com",
    projectId: "SEU_PROJETO_ID",
    storageBucket: "SEU_PROJETO.firebasestorage.app",
    messagingSenderId: "SEU_MESSAGING_SENDER_ID",
    appId: "SEU_APP_ID"
};
```

Substitua os valores de exemplo pelas credenciais geradas no seu Firebase Console.

### 6. Configurar Senhas e PINs
Ainda no `public/index.html`, localize o objeto `config` (aproximadamente na linha 2750) e altere os PINs conforme desejado:

```javascript
const config = ref({
    kitPrice: 8000,
    taxValue: 4000,
    recruitValue: 300000,
    globalTopHours: 30,
    adminPin: 'SUA_SENHA_ADMIN',     // PIN para ações de administração
    accessPin: 'SUA_SENHA_ACESSO',   // PIN de acesso ao painel
    recruiterPin: 'SUA_SENHA_REC',  // PIN de recrutador
    ...
});
```

---

## 💻 Como Executar Localmente

Como a aplicação é composta por arquivos estáticos que utilizam módulos ES, ela precisa ser servida via protocolo HTTP/HTTPS (e não aberta diretamente com duplo clique em `file://`):

### Opção 1: Extensão Live Server (VS Code)
1. Instale a extensão **Live Server** no VS Code.
2. Clique com botão direito sobre `public/index.html` e escolha **Open with Live Server**.

### Opção 2: Node.js (npx serve)
No terminal, dentro da pasta do projeto:
```bash
npx serve public
```

### Opção 3: Firebase CLI
```bash
npm install -g firebase-tools
firebase login
firebase serve --only hosting
```

---

## 🌐 Como Fazer Deploy

### Deploy no Firebase Hosting (Recomendado)
1. Instale o Firebase CLI:
   ```bash
   npm install -g firebase-tools
   ```
2. Faça login:
   ```bash
   firebase login
   ```
3. Vincule ao seu projeto criado:
   ```bash
   firebase use --add
   ```
   *(Selecione o seu projeto e use o alias `default`)*
4. Envie a aplicação para o ar:
   ```bash
   firebase deploy --only hosting
   ```
5. A URL de acesso será exibida no final do comando!

### Deploy em outras plataformas (Vercel, Netlify, GitHub Pages)
Como se trata de uma pasta com arquivos estáticos, basta apontar o diretório raiz de publicação para a pasta `public`.

---

## 📂 Estrutura de Arquivos

```text
├── public/
│   └── index.html       # Aplicação SPA completa (Vue 3, Tailwind, lógica e componentes)
├── .firebaserc          # Mapeamento do projeto Firebase Hosting
├── firebase.json        # Configuração de hospedagem e reescritas de URL
├── .gitignore           # Arquivos e pastas ignorados pelo Git
└── README.md            # Documentação do projeto
```

---

## 📄 Licença

Este projeto é de uso livre para personalização e adaptação. Sinta-se à vontade para modificar de acordo com as necessidades da sua organização.
