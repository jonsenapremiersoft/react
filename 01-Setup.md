# Configuração do Ambiente de Desenvolvimento React

## 1. Instalação do Node.js e npm

### 1.1 Download e Instalação do Node.js
1. Acesse o site oficial: https://nodejs.org
2. Baixe a versão LTS (Long Term Support) recomendada
3. Execute o instalador e siga as instruções:
   - Aceite os termos de uso
   - Mantenha as opções padrão de instalação
   - Clique em "Next" até finalizar

### 1.2 Verificação da Instalação
Abra o terminal (Command Prompt ou PowerShell) e digite:
```bash
node --version
npm --version
```

## 2. Configuração do Visual Studio Code

### 2.1 Download e Instalação
1. Acesse: https://code.visualstudio.com
2. Baixe e instale a versão para seu sistema operacional

### 2.2 Extensões Essenciais
Instale as seguintes extensões no VSCode:

- **ES7+ React/Redux/React-Native snippets**
  - Atalhos para código React
  - ID: dsznajder.es7-react-js-snippets

- **Prettier - Code formatter**
  - Formatação automática de código
  - ID: esbenp.prettier-vscode

- **Auto Rename Tag**
  - Renomeia tags HTML/JSX automaticamente
  - ID: formulahendry.auto-rename-tag

- **ESLint**
  - Análise de código JavaScript/React
  - ID: dbaeumer.vscode-eslint

- **Color Highlight**
  - Destaca cores no código
  - ID: naumovs.color-highlight

### 2.3 Configurações Recomendadas
Abra as configurações do VSCode (Ctrl+,) e adicione:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.tabSize": 4,
  "editor.wordWrap": "on"
}
```

## 3. Criando Primeiro Projeto React com Next.js

### 3.1 Criar Novo Projeto
Abra o terminal e navegue até a pasta onde deseja criar o projeto:

```bash
npx create-next-app@latest meu-primeiro-projeto --use-npm --typescript false
```

Quando perguntado sobre as opções, selecione:
- Would you like to use TypeScript? → No
- Would you like to use ESLint? → Yes
- Would you like to use Tailwind CSS? → Yes
- Would you like to use `src/` directory? → Yes
- Would you like to use App Router? → Yes
- Would you like to customize the default import alias? → No

### 3.2 Executando o Projeto
```bash
cd meu-primeiro-projeto
npm run dev
```

Acesse http://localhost:3000 no navegador para ver seu projeto rodando.

### 3.3 Estrutura Básica do Projeto
```
meu-primeiro-projeto/
├── node_modules/
├── public/
├── src/
│   ├── app/
│   │   ├── page.js
│   │   └── layout.js
├── .eslintrc.json
├── next.config.js
├── package.json
└── README.md
```

## Exercícios Práticos

1. Modifique o conteúdo do arquivo `src/app/page.js`
2. Adicione um novo texto ou imagem
3. Observe as mudanças em tempo real no navegador

## Material Complementar
- Documentação oficial do React: https://react.dev
- Documentação do Next.js: https://nextjs.org/docs
- Guia do Node.js: https://nodejs.org/en/docs/
