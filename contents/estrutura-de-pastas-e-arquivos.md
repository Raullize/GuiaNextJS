<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# 📁 Estrutura de Pastas e Arquivos no Next.js 📂

Compreender a estrutura de pastas e arquivos do Next.js é fundamental para organizar seu projeto de forma eficiente. O Next.js utiliza convenções de arquivos e pastas para simplificar o desenvolvimento e fornecer funcionalidades poderosas.

## 📑 Visão Geral da Estrutura

O Next.js oferece dois sistemas de roteamento principais: o **Pages Router** (sistema tradicional) e o **App Router** (mais recente, introduzido na versão 13). Vamos explorar ambos:

```
meu-projeto-nextjs/
  ├── node_modules/    # Dependências instaladas
  ├── public/          # Arquivos estáticos públicos
  ├── pages/           # Páginas (Pages Router)
  ├── app/             # App Router (Next.js 13+)
  ├── components/      # Componentes reutilizáveis
  ├── styles/          # Arquivos de estilo
  ├── lib/             # Funções/utilitários
  ├── .next/           # Build do Next.js (não editar)
  ├── next.config.js   # Configuração do Next.js
  ├── package.json     # Dependências e scripts
  └── README.md        # Documentação do projeto
```

## 🗄️ Diretórios Principais

### 📁 `public/`

O diretório `public/` é usado para armazenar arquivos estáticos que serão servidos diretamente:

- Imagens, ícones, fontes
- Arquivos de manifesto (favicon, robots.txt, sitemap.xml)
- Outros assets como vídeos, PDFs, etc.

Exemplo de uso:
```jsx
// Arquivo em public/images/logo.png
<img src="/images/logo.png" alt="Logo" />
```

### 📁 `pages/` (Pages Router)

No sistema Pages Router, cada arquivo JavaScript/TypeScript neste diretório se torna uma rota automaticamente:

```
pages/
  ├── index.js         # Rota: /
  ├── about.js         # Rota: /about
  ├── contact.js       # Rota: /contact
  ├── blog/
  │   ├── index.js     # Rota: /blog
  │   └── [slug].js    # Rota dinâmica: /blog/qualquer-post
  └── api/
      └── hello.js     # Rota de API: /api/hello
```

**Arquivos especiais no Pages Router:**

- `_app.js`: Componente que envolve todas as páginas (global)
- `_document.js`: Personaliza o documento HTML
- `_error.js`: Página de erro personalizada
- `404.js`: Página para "não encontrado"
- `500.js`: Página para erro de servidor

### 📁 `app/` (App Router)

Introduzido no Next.js 13, o App Router usa uma abordagem de "pastas = rotas" com arquivos especiais:

```
app/
  ├── page.js          # Rota: /
  ├── layout.js        # Layout para todas as páginas
  ├── about/
  │   └── page.js      # Rota: /about
  ├── blog/
  │   ├── page.js      # Rota: /blog
  │   └── [slug]/
  │       └── page.js  # Rota dinâmica: /blog/qualquer-post
  └── api/
      └── route.js     # API Route handlers
```

**Arquivos especiais no App Router:**

- `layout.js`: Layout compartilhado entre múltiplas páginas
- `loading.js`: Interface de carregamento automática
- `error.js`: Tratamento de erros
- `not-found.js`: Página para "não encontrado"
- `route.js`: Manipuladores de API
- `template.js`: Versão sem persistência de layouts

### 📁 `components/`

Embora não seja uma pasta especial do Next.js, é uma convenção comum organizar componentes reutilizáveis:

```
components/
  ├── ui/              # Componentes de UI básicos
  │   ├── Button.js
  │   └── Card.js
  ├── layout/          # Componentes de layout
  │   ├── Header.js
  │   └── Footer.js
  └── features/        # Componentes específicos de features
      ├── auth/
      └── products/
```

### 📁 `styles/`

Para armazenar arquivos de estilo:

```
styles/
  ├── globals.css      # Estilos globais
  ├── Home.module.css  # CSS Modules
  └── theme.js         # Configurações de tema
```

### 📁 `lib/` ou `utils/`

Para funções utilitárias, helpers e lógica de negócio:

```
lib/
  ├── api.js           # Funções para chamadas API
  ├── auth.js          # Lógica de autenticação
  ├── db.js            # Conexão com banco de dados
  └── helpers.js       # Funções auxiliares
```

## 🧩 Convenções de Nomenclatura

O Next.js segue certas convenções para nomes de arquivos:

- **Páginas**: Começam com letra minúscula (index.js, about.js)
- **Componentes**: Começam com letra maiúscula (Button.js, Header.js)
- **Páginas Dinâmicas**:
  - Pages Router: `[param].js` para rotas dinâmicas
  - App Router: Pastas com nome `[param]` e um arquivo page.js dentro

## 🔀 Rotas Dinâmicas

### Pages Router:
```
pages/
  └── posts/
      └── [id].js      # Acessível via /posts/1, /posts/2, etc.
```

### App Router:
```
app/
  └── posts/
      └── [id]/
          └── page.js  # Acessível via /posts/1, /posts/2, etc.
```

## 📊 Organização para Projetos Grandes

Para projetos maiores, considere estas estruturas:

### Organização por Funcionalidade
```
src/
  ├── features/
  │   ├── auth/
  │   │   ├── components/
  │   │   └── utils/
  │   └── products/
  │       ├── components/
  │       └── utils/
  ├── pages/
  └── app/
```

### Organização por Tipo
```
src/
  ├── components/
  ├── hooks/
  ├── context/
  ├── services/
  ├── utils/
  ├── pages/
  └── app/
```

## 🛠️ Configurações Importantes

### 📄 `next.config.js`

Arquivo de configuração principal:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  images: {
    domains: ['exemplo.com'],
  },
  i18n: {
    locales: ['pt-BR', 'en-US'],
    defaultLocale: 'pt-BR',
  },
}

module.exports = nextConfig
```

### 📄 `jsconfig.json` ou `tsconfig.json`

Configuração para caminhos absolutos:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/components/*": ["components/*"],
      "@/lib/*": ["lib/*"],
      "@/styles/*": ["styles/*"]
    }
  }
}
```

## 💡 Dicas e Boas Práticas

1. **Mantenha a lógica de negócio separada**: Use a pasta `lib/` ou `services/` para isolá-la
2. **Organize componentes por domínio**: Agrupe componentes relacionados em subpastas
3. **Use imports absolutos**: Configure paths personalizados para evitar imports relativos longos
4. **Mantenha a estrutura consistente**: Estabeleça um padrão para a equipe
5. **Documente decisões**: Adicione um README.md em pastas importantes explicando a estrutura
6. **Use co-localização**: Mantenha arquivos relacionados (teste, estilo, componente) próximos

## 🔍 Recursos Adicionais

- [Documentação oficial de estrutura de diretórios](https://nextjs.org/docs/getting-started/project-structure)
- [Exemplos de estrutura de projetos Next.js](https://github.com/vercel/next.js/tree/canary/examples)

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/> 