<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# 📂 Instalação e Configuração do Next.js 📝

Neste guia, vamos aprender como configurar um novo projeto Next.js do zero. O Next.js oferece uma configuração simples e direta para começar a desenvolver aplicações web modernas.

## 📋 Pré-requisitos

Antes de começar, você precisa ter instalado em sua máquina:

- [Node.js](https://nodejs.org/) (versão 16.14 ou superior)
- [npm](https://www.npmjs.com/) (vem com Node.js) ou [Yarn](https://yarnpkg.com/) ou [pnpm](https://pnpm.io/)

## 🚀 Criando um Novo Projeto

### Usando `create-next-app` (Recomendado)

A maneira mais fácil de iniciar um novo projeto Next.js é usando o comando `create-next-app`, que configura tudo automaticamente para você:

```bash
# Usando npm
npx create-next-app@latest meu-projeto-nextjs

# Usando Yarn
yarn create next-app meu-projeto-nextjs

# Usando pnpm
pnpm create next-app meu-projeto-nextjs
```

Durante a instalação, você será guiado por algumas perguntas para configurar o projeto:

```
✔ Would you like to use TypeScript? … Yes / No
✔ Would you like to use ESLint? … Yes / No
✔ Would you like to use Tailwind CSS? … Yes / No
✔ Would you like to use `src/` directory? … Yes / No
✔ Would you like to use App Router? (recommended) … Yes / No
✔ Would you like to customize the default import alias (@/*)? … Yes / No
```

### Configuração Manual (Alternativa)

Se preferir configurar manualmente, siga estes passos:

1. Crie uma pasta para o projeto:
   ```bash
   mkdir meu-projeto-nextjs
   cd meu-projeto-nextjs
   ```

2. Inicialize um projeto npm:
   ```bash
   npm init -y
   ```

3. Instale as dependências do Next.js:
   ```bash
   npm install next react react-dom
   ```

4. Abra o arquivo `package.json` e adicione os scripts do Next.js:
   ```json
   "scripts": {
     "dev": "next dev",
     "build": "next build",
     "start": "next start",
     "lint": "next lint"
   }
   ```

5. Crie uma estrutura básica de pastas:
   ```bash
   mkdir pages
   mkdir public
   # Se você optar pelo App Router
   mkdir app
   ```

## 🏃‍♂️ Iniciando o Servidor de Desenvolvimento

Após a instalação, navegue até a pasta do projeto e inicie o servidor de desenvolvimento:

```bash
# Navegue até a pasta do projeto
cd meu-projeto-nextjs

# Inicie o servidor de desenvolvimento
npm run dev
# ou
yarn dev
# ou
pnpm dev
```

Acesse `http://localhost:3000` no seu navegador para ver seu novo projeto Next.js funcionando!

## 📁 Estrutura Básica de um Projeto Next.js

Após a criação do projeto, você terá uma estrutura semelhante a esta:

```
meu-projeto-nextjs/
  ├── node_modules/
  ├── public/          # Arquivos estáticos (imagens, fonts, etc)
  ├── pages/           # Páginas da aplicação (se usar Pages Router)
  │   ├── _app.js      # Componente principal para configuração
  │   ├── index.js     # Página inicial (rota /)
  │   └── api/         # Rotas de API
  ├── app/             # Estrutura do App Router (se escolher esta opção)
  │   ├── layout.js    # Layout principal
  │   ├── page.js      # Página inicial
  │   └── globals.css  # Estilos globais
  ├── styles/          # Arquivos de estilo
  ├── .next/           # Build gerada pelo Next.js (não editar)
  ├── next.config.js   # Configuração personalizada do Next.js
  ├── package.json     # Dependências e scripts
  └── README.md        # Documentação
```

## ⚙️ Configuração Personalizada

O Next.js pode ser configurado através do arquivo `next.config.js` na raiz do seu projeto:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,  // Modo estrito do React
  images: {
    domains: ['exemplo.com'], // Domínios permitidos para o componente Image
  },
  // Outras configurações personalizadas aqui
}

module.exports = nextConfig
```

## 🔌 Plugins e Módulos Úteis

Você pode estender seu projeto Next.js com plugins populares:

- **Estilização**: Tailwind CSS, Styled Components, Emotion, CSS Modules
- **Formulários**: React Hook Form, Formik
- **Estado**: Redux, Zustand, Jotai, React Query
- **UI Components**: Chakra UI, Material UI, Shadcn/UI
- **Autenticação**: NextAuth.js, Auth.js
- **CMS**: Contentful, Sanity, Strapi

## 🧠 Dicas para Desenvolvimento

- Use o modo de desenvolvimento (`npm run dev`) para hot reloading
- Atualize regularmente suas dependências do Next.js para obter as últimas melhorias
- Explore os exemplos oficiais do Next.js para casos de uso específicos
- Use TypeScript para obter mais segurança e autocompletar no código

## 🚀 Próximos Passos

Agora que você configurou seu projeto Next.js, é hora de começar a desenvolver!

- [Explore a estrutura de pastas e arquivos →](estrutura-de-pastas-e-arquivos.md)
- [Aprenda sobre o sistema de roteamento →](roteamento.md)
- [Conheça os diferentes métodos de renderização →](renderizacao-e-data-fetching.md)

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/> 