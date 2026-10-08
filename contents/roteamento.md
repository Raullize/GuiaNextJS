<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# Roteamento no Next.js

O sistema de roteamento do Next.js é uma das suas características mais poderosas, permitindo navegar entre diferentes páginas da sua aplicação de forma simples e eficiente. Neste guia, vamos explorar as diferentes abordagens de roteamento disponíveis no Next.js.

## Problemas de Roteamento em SPAs Tradicionais

As **Single Page Applications (SPAs)** convencionais enfrentam diversos desafios relacionados ao roteamento:

### Limitações Comuns:

1. **URLs Não Refletem o Estado Real**:
   - Hash routing (#) não é ideal para SEO
   - Estados complexos da aplicação podem não ter URLs correspondentes
   - Dificuldade em compartilhar links específicos
   - Problemas com botões voltar/avançar do navegador

2. **Todas as Rotas em Um Bundle**:
   - Todo o código de roteamento é baixado na primeira visita
   - Rotas não utilizadas consomem recursos desnecessariamente
   - Tempo de carregamento inicial muito alto

3. **SEO Comprometido**:
   - Mecanismos de busca podem não indexar rotas adequadamente
   - Falta de meta tags específicas por rota
   - Conteúdo dinâmico pode não ser crawleado

4. **Configuração Complexa**:
   - Necessidade de configurar bibliotecas de roteamento manualmente
   - Gerenciamento manual de lazy loading
   - Configuração de prefetching e code splitting

## ✅ Vantagens do Sistema de Roteamento do Next.js

O Next.js resolve esses problemas com um **sistema de roteamento baseado em arquivos** que oferece:

### Benefícios Principais:

1. **File-based Routing**:
   - **Zero configuração**: Estrutura de pastas = estrutura de rotas
   - **URLs semânticas**: Cada arquivo representa uma rota real
   - **Fácil organização**: Estrutura intuitiva e escalável
   - **SEO otimizado**: Cada rota é uma página real indexável

2. **Code Splitting Automático**:
   - **Bundles separados**: Cada página é um bundle independente
   - **Carregamento sob demanda**: Apenas o código necessário é baixado
   - **Performance otimizada**: Tempo de carregamento inicial reduzido

3. **Prefetching Inteligente**:
   - **Links visíveis são pré-carregados**: Navegação instantânea
   - **Estratégias configuráveis**: Controle fino sobre o prefetching
   - **Otimização automática**: Baseado na viewport e conexão do usuário

4. **Suporte Completo a SSR/SSG**:
   - **Cada rota pode ter sua estratégia**: SSR, SSG, ISR ou CSR
   - **Meta tags dinâmicas**: SEO otimizado por página
   - **Conteúdo indexável**: Mecanismos de busca veem o conteúdo real

5. **Navegação Híbrida**:
   - **Client-side navigation**: Navegação rápida após carregamento inicial
   - **Fallback para navegação tradicional**: Funciona mesmo com JS desabilitado
   - **Histórico do navegador**: Botões voltar/avançar funcionam perfeitamente

### Resultado:
**O Next.js transforma o roteamento de um problema complexo em uma solução elegante e automática**, oferecendo performance superior e SEO otimizado sem configuração manual.

## Sistemas de Roteamento

O Next.js oferece dois sistemas de roteamento principais:

1. **Pages Router**: O sistema tradicional, baseado no diretório `/pages`
2. **App Router**: O sistema mais recente (desde Next.js 13), baseado no diretório `/app`

Ambos podem coexistir no mesmo projeto durante a migração, mas é recomendado escolher um para novos projetos.

## Pages Router (Sistema Tradicional)

### Roteamento Baseado em Arquivos

No Pages Router, cada arquivo JavaScript/TypeScript dentro da pasta `pages` torna-se automaticamente uma rota:

```
pages/
  ├── index.js         # → /
  ├── about.js         # → /about
  ├── contact.js       # → /contact
  └── blog/
      ├── index.js     # → /blog
      └── post.js      # → /blog/post
```

### Navegação entre Páginas

Para navegar entre páginas, use o componente `Link` do Next.js:

```jsx
import Link from 'next/link';

function Navbar() {
  return (
    <nav>
      <Link href="/">Início</Link>
      <Link href="/about">Sobre</Link>
      <Link href="/contact">Contato</Link>
      <Link href="/blog">Blog</Link>
    </nav>
  );
}
```

Para navegação programática, use o hook `useRouter`:

```jsx
import { useRouter } from 'next/router';

function LoginButton() {
  const router = useRouter();
  
  const handleLogin = async (formData) => {
    // Lógica de login...
    if (loginSuccessful) {
      router.push('/dashboard'); // Navega para o dashboard
    }
  };
  
  return <button onClick={handleLogin}>Entrar</button>;
}
```

### Rotas Dinâmicas

Para criar rotas dinâmicas, use colchetes no nome do arquivo:

```
pages/
  └── blog/
      └── [slug].js    # → /blog/qualquer-post
```

Acessando o parâmetro da rota:

```jsx
import { useRouter } from 'next/router';

export default function BlogPost() {
  const router = useRouter();
  const { slug } = router.query;
  
  return <h1>Post: {slug}</h1>;
}
```

### Rotas Aninhadas Dinâmicas

Para rotas aninhadas dinâmicas:

```
pages/
  └── [category]/
      └── [product].js  # → /eletronicos/smartphone
```

```jsx
import { useRouter } from 'next/router';

export default function Product() {
  const router = useRouter();
  const { category, product } = router.query;
  
  return (
    <div>
      <h1>Categoria: {category}</h1>
      <h2>Produto: {product}</h2>
    </div>
  );
}
```

### Rotas Catch-all

Para capturar múltiplos segmentos de rota, use reticências `...` com colchetes:

```
pages/
  └── docs/
      └── [...slug].js  # → /docs/conceitos/rotas/parametros
```

```jsx
import { useRouter } from 'next/router';

export default function Docs() {
  const router = useRouter();
  const { slug } = router.query; // slug é um array: ['conceitos', 'rotas', 'parametros']
  
  return <h1>Docs: {slug?.join('/')}</h1>;
}
```

### Rotas Opcionais Catch-all

Para tornar os parâmetros opcionais, adicione colchetes duplos:

```
pages/
  └── [[...slug]].js  # → /, /a, /a/b, /a/b/c, etc.
```

## App Router (Next.js 13+)

### Estrutura de Pastas

No App Router, a estrutura de pastas define as rotas e cada pasta precisa de um arquivo `page.js` para ser acessível:

```
app/
  ├── page.js          # → /
  ├── about/
  │   └── page.js      # → /about
  ├── blog/
  │   ├── page.js      # → /blog
  │   └── [slug]/
  │       └── page.js  # → /blog/qualquer-post
```

### Navegação no App Router

A navegação com Links funciona de forma similar:

```jsx
import Link from 'next/link';

function Navbar() {
  return (
    <nav>
      <Link href="/">Início</Link>
      <Link href="/about">Sobre</Link>
    </nav>
  );
}
```

Para navegação programática, use o hook `useRouter` do pacote `next/navigation`:

```jsx
'use client'; // Necessário para componentes client-side

import { useRouter } from 'next/navigation';

export default function LoginForm() {
  const router = useRouter();
  
  const handleSubmit = async (event) => {
    event.preventDefault();
    // Lógica de login...
    router.push('/dashboard');
  };
  
  return <form onSubmit={handleSubmit}>...</form>;
}
```

### Rotas Dinâmicas no App Router

Crie uma pasta com nome entre colchetes e adicione um arquivo `page.js` dentro:

```
app/
  └── blog/
      └── [slug]/
          └── page.js  # → /blog/qualquer-post
```

Acessando os parâmetros:

```jsx
// app/blog/[slug]/page.js
export default function BlogPost({ params }) {
  return <h1>Post: {params.slug}</h1>;
}
```

### Segmentos de Rota Catch-all

Similar ao Pages Router, mas usando pastas:

```
app/
  └── docs/
      └── [...slug]/
          └── page.js  # → /docs/conceitos/rotas/parametros
```

```jsx
export default function Docs({ params }) {
  const slugArray = params.slug; // ['conceitos', 'rotas', 'parametros']
  
  return <h1>Docs: {slugArray.join('/')}</h1>;
}
```

### Segmentos Opcionais

Para tornar segmentos opcionais, use colchetes duplos:

```
app/
  └── [[...slug]]/
      └── page.js  # → /, /a, /a/b, etc.
```

## Recursos Avançados de Roteamento

### Middleware

O middleware do Next.js permite executar código antes de uma requisição ser concluída:

```js
// middleware.js (na raiz do projeto)
import { NextResponse } from 'next/server';

export function middleware(request) {
  // Verificar autenticação
  const isAuthenticated = checkAuth(request);
  
  if (!isAuthenticated && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/perfil/:path*'],
};
```

### Grupos de Rotas

No App Router, você pode criar grupos de rotas usando parênteses, que não afetam a URL:

```
app/
  ├── (marketing)/     # Grupo que não afeta a URL
  │   ├── about/
  │   │   └── page.js  # → /about
  │   └── blog/
  │       └── page.js  # → /blog
  └── (dashboard)/
      ├── profile/
  │       └── page.js  # → /profile
  └── settings/
      └── page.js  # → /settings
```

### Layouts Compartilhados

No App Router, use arquivos `layout.js` para compartilhar layouts entre rotas:

```jsx
// app/layout.js
export default function RootLayout({ children }) {
  return (
    <html lang="pt-BR">
      <body>
        <header>Meu Site</header>
        <main>{children}</main>
        <footer>© 2025</footer>
      </body>
    </html>
  );
}
```

Layouts aninhados:

```jsx
// app/dashboard/layout.js
export default function DashboardLayout({ children }) {
  return (
    <div>
      <nav>Menu do Dashboard</nav>
      <section>{children}</section>
    </div>
  );
}
```

### Páginas de Carregamento

No App Router, crie arquivos `loading.js` para mostrar estados de carregamento:

```jsx
// app/dashboard/loading.js
export default function Loading() {
  return <div>Carregando...</div>;
}
```

### Tratamento de Erros

Arquivos `error.js` capturam erros dentro do seu escopo:

```jsx
'use client'; // Error components must be Client Components

import { useEffect } from 'react';

export default function Error({ error, reset }) {
  useEffect(() => {
    console.error(error);
  }, [error]);

  return (
    <div>
      <h2>Algo deu errado!</h2>
      <button onClick={() => reset()}>Tentar novamente</button>
    </div>
  );
}
```

## Melhores Práticas de Roteamento

1. **Prefetch**: Links pré-carregam páginas por padrão quando visíveis (desative com `prefetch={false}` se necessário)

2. **URLs amigáveis**: Use slugs descritivos para melhor SEO
   ```jsx
   <Link href="/blog/como-usar-nextjs-routing">Como usar Next.js Routing</Link>
   ```

3. **Scroll automático**: O Next.js restaura a posição de rolagem ao navegar para trás/frente

4. **Shallow Routing**: Atualize a URL sem executar novamente os métodos de data fetching
   ```js
   router.push('/dashboard?tab=settings', undefined, { shallow: true });
   ```

5. **Interceptação de rotas**: No App Router, use convenções como `(.)` para interceptar rotas sem mudar a URL

6. **Parallel Routes**: Use slots com `@` para renderizar várias páginas simultaneamente
   ```
   app/
     └── @dashboard/
         └── page.js
   ```

## Recursos e Links Úteis

- [Documentação oficial de roteamento no Next.js](https://nextjs.org/docs/routing)
- [Guia de migração do Pages para App Router](https://nextjs.org/docs/app/building-your-application/upgrading/app-router-migration)

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/>