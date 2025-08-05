<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# 🏆 Boas Práticas em Next.js 📋

O Next.js oferece diversas ferramentas e recursos para construir aplicações web modernas. Para aproveitar ao máximo esse framework, é importante seguir boas práticas de desenvolvimento. Este guia reúne recomendações e padrões que ajudarão você a construir aplicações Next.js mais eficientes, escaláveis e de fácil manutenção.

## 🚫 Problemas de Boas Práticas em SPAs Tradicionais

As **Single Page Applications (SPAs)** convencionais frequentemente enfrentam desafios relacionados à falta de padrões estabelecidos:

### Limitações Comuns:

1. **🏗️ Arquitetura Inconsistente**:
   - **Estrutura de pastas variável**: Cada projeto organizado diferentemente
   - **Padrões não estabelecidos**: Falta de convenções para componentes e estado
   - **Escalabilidade problemática**: Dificuldade para crescer o projeto
   - **Onboarding lento**: Novos desenvolvedores demoram para entender a estrutura

2. **⚡ Performance Não Otimizada**:
   - **Bundle splitting manual**: Code splitting configurado manualmente
   - **Lazy loading complexo**: Implementação manual de carregamento sob demanda
   - **Otimizações esquecidas**: Imagens, fonts e scripts não otimizados
   - **Core Web Vitals ignorados**: Métricas de performance não monitoradas

3. **🔍 SEO Negligenciado**:
   - **Meta tags estáticas**: Mesmo título/descrição para todas as páginas
   - **Structured data ausente**: Falta de dados estruturados para crawlers
   - **Sitemap manual**: XML sitemaps criados e mantidos manualmente
   - **Open Graph básico**: Compartilhamento social não otimizado

4. **🛡️ Segurança Reativa**:
   - **Headers de segurança manuais**: CSP, HSTS configurados separadamente
   - **Sanitização inconsistente**: XSS e outras vulnerabilidades
   - **Secrets expostos**: Chaves de API no frontend
   - **CORS problemático**: Configuração complexa entre frontend/backend

5. **🧪 Testes Fragmentados**:
   - **Estratégias inconsistentes**: Unit, integration, e2e sem padrão
   - **Mocking complexo**: APIs e dependências mockadas manualmente
   - **Coverage baixo**: Cobertura de testes não monitorada
   - **CI/CD manual**: Pipeline de testes configurado do zero

## ✅ Vantagens das Boas Práticas Next.js

O Next.js estabelece **padrões e convenções que resolvem problemas comuns** de desenvolvimento:

### Benefícios Principais:

1. **🏗️ Arquitetura Padronizada**:
   - **File-based routing**: Estrutura de pastas = estrutura de rotas
   - **Convenções estabelecidas**: Padrões claros para componentes, páginas, APIs
   - **Colocation**: Componentes, estilos e testes próximos
   - **Escalabilidade built-in**: Estrutura que cresce naturalmente

2. **⚡ Performance por Padrão**:
   - **Otimizações automáticas**: Code splitting, tree shaking, minificação
   - **Image optimization**: Componente `<Image>` com lazy loading e formatos modernos
   - **Font optimization**: Google Fonts otimizadas automaticamente
   - **Script optimization**: Third-party scripts carregados eficientemente

3. **🔍 SEO Integrado**:
   - **Metadata API**: Meta tags dinâmicas por página
   - **Structured data**: JSON-LD e microdata automáticos
   - **Sitemap generation**: XML sitemaps gerados automaticamente
   - **Open Graph otimizado**: Compartilhamento social rico

4. **🛡️ Segurança por Design**:
   - **Headers automáticos**: CSP, HSTS, X-Frame-Options configurados
   - **API Routes seguras**: Endpoints privados por padrão
   - **Environment variables**: Secrets seguros no servidor
   - **CSRF protection**: Proteção automática contra ataques

5. **🧪 Testing Framework**:
   - **Jest integrado**: Configuração zero para unit tests
   - **Testing Library**: Testes de componentes padronizados
   - **Playwright/Cypress**: E2E testing com exemplos
   - **Coverage reports**: Relatórios de cobertura automáticos

6. **🔧 Developer Experience**:
   - **TypeScript first-class**: Suporte nativo e configuração automática
   - **ESLint rules**: Regras específicas para Next.js
   - **Hot reload**: Mudanças refletem instantaneamente
   - **Error overlay**: Debugging visual de erros

### Resultado:
**As boas práticas do Next.js transformam desenvolvimento web de um processo ad-hoc em uma experiência estruturada e otimizada**, garantindo qualidade, performance e manutenibilidade desde o primeiro dia.

## 📋 Organização do Código

### Estrutura de Arquivos

Uma estrutura organizada é fundamental para projetos escaláveis:

```
meu-projeto/
  ├── app/                  # App Router (Next.js 13+)
  │   ├── components/       # Componentes específicos de rotas
  │   ├── lib/              # Funções específicas de rotas
  │   └── ...
  ├── components/           # Componentes compartilhados
  │   ├── ui/               # Componentes de UI reutilizáveis
  │   └── features/         # Componentes específicos de features
  ├── lib/                  # Funções utilitárias compartilhadas
  ├── hooks/                # Hooks personalizados
  ├── styles/               # Estilos globais
  ├── public/               # Arquivos estáticos
  └── middleware.js         # Middlewares do Next.js
```

### Convenções de Nomenclatura

Seguir convenções consistentes facilita o entendimento do código:

- **Componentes**: Use PascalCase (ex: `ProductCard.jsx`)
- **Funções utilitárias**: Use camelCase (ex: `formatDate.js`)
- **Constantes**: Use UPPER_SNAKE_CASE (ex: `API_ENDPOINTS.js`)
- **Hooks**: Use o prefixo "use" (ex: `useLocalStorage.js`)

## 🔄 Gerenciamento de Estado

### Estado Local vs. Global

- Use **estado local** (`useState`, `useReducer`) para componentes isolados
- Use **estado global** apenas quando necessário, com ferramentas como:
  - Context API para estados simples
  - Zustand, Jotai ou Redux para estados complexos
  - React Query/SWR para gerenciamento de cache e chamadas de API

```jsx
// Bom exemplo de uso de Context API
// context/ThemeContext.jsx
'use client';

import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext(undefined);

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme deve ser usado dentro de um ThemeProvider');
  }
  return context;
}
```

## 🚀 Otimização de Performance

### Lazy Loading e Code Splitting

Utilize carregamento sob demanda para reduzir o tempo inicial de carregamento:

```jsx
// Lazy loading de componentes pesados
import dynamic from 'next/dynamic';

const HeavyChart = dynamic(() => import('@/components/HeavyChart'), {
  loading: () => <p>Carregando gráfico...</p>,
  ssr: false, // Desativa SSR para componentes que só funcionam no cliente
});
```

### Imagens Otimizadas

Sempre use o componente `Image` do Next.js:

```jsx
import Image from 'next/image';

export function ProductImage({ product }) {
  return (
    <Image
      src={product.imageUrl}
      alt={product.name}
      width={500}
      height={300}
      placeholder="blur"
      blurDataURL={product.thumbnailUrl}
      priority={product.featured} // True para imagens above the fold
    />
  );
}
```

### Prefetching e Preloading

Aproveite o prefetching automático do Next.js e implemente preloading quando necessário:

```jsx
// Link com prefetch (comportamento padrão)
import Link from 'next/link';

<Link href="/produtos">Produtos</Link>

// Desativando prefetch para links raramente clicados
<Link href="/termos-de-uso" prefetch={false}>Termos de Uso</Link>
```

## 🧩 Componentes

### Componentização Eficiente

Divida a UI em componentes reutilizáveis e mantenha-os pequenos:

- **Atômicos**: Botões, inputs, ícones
- **Moleculares**: Cards, formulários, tabelas
- **Organismos**: Seções de página, layouts

### Server vs. Client Components

No App Router, use corretamente os componentes server e client:

```jsx
// Server Component (padrão)
// Ótimo para: Busca de dados, componentes que não precisam de interatividade
export default async function ProductList() {
  const products = await fetchProducts();
  return (
    <div>
      {products.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
}
```

```jsx
// Client Component
// Use apenas quando necessário: Interações com o DOM, event listeners
'use client';

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <button onClick={() => setCount(count + 1)}>
      Cliques: {count}
    </button>
  );
}
```

## 📱 Responsividade e Acessibilidade

### Design Responsivo

Desenvolva para mobile-first e utilize CSS moderno:

```css
/* Exemplo com Tailwind CSS */
<div className="w-full md:w-1/2 lg:w-1/3 p-4">
  {/* Conteúdo */}
</div>

/* Exemplo com CSS Modules ou CSS puro */
.container {
  width: 100%;
}

@media (min-width: 768px) {
  .container {
    width: 50%;
  }
}
```

### Acessibilidade (A11y)

Torne sua aplicação acessível a todos os usuários:

- Use semântica HTML adequada (`<button>`, `<nav>`, `<main>`, `<article>`)
- Inclua atributos `alt` em imagens
- Garanta contraste adequado de cores
- Implemente navegação via teclado
- Teste com leitores de tela

```jsx
// Exemplo de componente acessível
function SearchForm() {
  return (
    <form role="search">
      <label htmlFor="search" className="sr-only">Buscar produtos</label>
      <input
        id="search"
        type="search"
        aria-label="Buscar produtos"
        placeholder="Buscar..."
      />
      <button type="submit" aria-label="Realizar busca">
        <span className="sr-only">Buscar</span>
        <SearchIcon />
      </button>
    </form>
  );
}
```

## 🔒 Segurança

### Validação de Entrada

Valide entrada de usuários tanto no cliente quanto no servidor:

```jsx
// Usando Zod para validação
import { z } from 'zod';

const userSchema = z.object({
  email: z.string().email('Email inválido'),
  password: z.string().min(8, 'Senha deve ter pelo menos 8 caracteres'),
});

// No servidor - API Route ou Server Action
async function createUser(formData) {
  const validatedFields = userSchema.safeParse({
    email: formData.get('email'),
    password: formData.get('password'),
  });

  if (!validatedFields.success) {
    return { error: validatedFields.error.flatten() };
  }
  
  // Prossegue com a criação do usuário
}
```

### Proteção contra Ataques Comuns

Implemente proteções contra:

- **XSS** - Use `next/script` para scripts externos, evite dangerouslySetInnerHTML
- **CSRF** - Utilize tokens anti-CSRF em formulários
- **Injection** - Use prepared statements ou ORMs para consultas ao banco de dados

## 🌐 Internacionalização e Localização

### Estrutura para Múltiplos Idiomas

Organize rotas e conteúdo para suportar diferentes idiomas:

```
app/
  ├── [lang]/           # Rota dinâmica para idioma
  │   ├── page.jsx      # Página inicial
  │   ├── about/
  │   │   └── page.jsx  # Página Sobre
  │   └── ...
  └── middleware.js    # Detecta idioma e redireciona
```

### Traduções e Formatação

Use bibliotecas como `next-intl` ou `react-i18next`:

```jsx
// Exemplo com next-intl
import { useTranslations } from 'next-intl';

function ProductPage() {
  const t = useTranslations('ProductPage');
  
  return (
    <div>
      <h1>{t('title')}</h1>
      <p>{t('description')}</p>
      <span>{t('price', { price: 29.99 })}</span>
    </div>
  );
}
```

## 🧪 Testes

### Estratégia de Testes

Implemente vários níveis de testes:

- **Testes unitários** para funções e componentes isolados
- **Testes de integração** para workflows importantes
- **Testes E2E** para fluxos críticos de usuário

```jsx
// Teste unitário de componente
import { render, screen } from '@testing-library/react';
import Button from '@/components/Button';

describe('Button Component', () => {
  it('renderiza corretamente', () => {
    render(<Button>Clique aqui</Button>);
    expect(screen.getByRole('button')).toHaveTextContent('Clique aqui');
  });
});
```

## 🚢 CI/CD e Deployment

### Automação de Pipeline

Configure pipelines automatizados para garantir qualidade:

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build
```

### Estratégia de Deploy

Implemente ambientes de desenvolvimento, staging e produção:

- **Preview deployments** para pull requests
- **Staging** para testes finais
- **Produção** com estratégia de rollout controlado

## 🔍 SEO e Analytics

### Metadados Dinâmicos

Otimize metadados para cada página:

```jsx
// app/blog/[slug]/page.jsx
import { type Metadata } from 'next';

export async function generateMetadata({ params }): Promise<Metadata> {
  const post = await getPostBySlug(params.slug);
  
  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [{ url: post.coverImage }],
    },
  };
}
```

### Monitoramento e Analytics

Implemente ferramentas para acompanhar performance e comportamento:

```jsx
// Exemplo com Google Analytics
import Script from 'next/script';

export function Analytics() {
  return (
    <>
      <Script
        src={`https://www.googletagmanager.com/gtag/js?id=${process.env.NEXT_PUBLIC_GA_ID}`}
        strategy="afterInteractive"
      />
      <Script id="google-analytics" strategy="afterInteractive">
        {`
          window.dataLayer = window.dataLayer || [];
          function gtag(){dataLayer.push(arguments);}
          gtag('js', new Date());
          gtag('config', '${process.env.NEXT_PUBLIC_GA_ID}');
        `}
      </Script>
    </>
  );
}
```

## 📱 Experiência de Desenvolvimento

### Ferramentas Recomendadas

Melhore seu fluxo de trabalho com:

- **ESLint** e **Prettier** para formatação de código
- **Husky** para git hooks (pre-commit, pre-push)
- **TypeScript** para type safety
- **storybook** para documentação de componentes

### Documentação

Mantenha documentação atualizada:

- README com instruções de configuração
- Comentários JSDoc para funções complexas
- Storybook para componentes visuais
- Arquivos CHANGELOG para registrar mudanças

## 🧠 Aprendizado Contínuo

### Acompanhe as Novidades

- Siga o [blog oficial do Next.js](https://nextjs.org/blog)
- Participe da comunidade no [Discord do Next.js](https://discord.com/invite/bUG2bvbtHy)
- Explore repositórios de exemplos oficiais

### Experimente Novos Recursos

Crie projetos paralelos para experimentar novas funcionalidades:

- Server Actions
- Server Components
- Parallel Routes
- Intercepting Routes

## 🔥 Solução de Problemas Comuns

### Debugging Eficiente

- Use `console.log` estrategicamente (e remova antes de commitar)
- Utilize as ferramentas de desenvolvimento do Next.js
  ```bash
  # Ativando debug
  NODE_OPTIONS='--inspect' next dev
  ```
- Aproveite as mensagens de erro detalhadas do framework

### Otimização de Build

- Monitore o tamanho do bundle com ferramentas como `@next/bundle-analyzer`
- Identifique e corrija problemas de performance com Lighthouse

## 🔍 Recursos Adicionais

- [Documentação oficial do Next.js](https://nextjs.org/docs)
- [Exemplos oficiais](https://github.com/vercel/next.js/tree/canary/examples)
- [Blog da Vercel](https://vercel.com/blog)
- [Next.js Weekly](https://nextjsweekly.com/) (Newsletter)
- [Aprenda Next.js](https://nextjs.org/learn)

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/>