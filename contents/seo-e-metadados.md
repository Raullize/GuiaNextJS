<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# 🔍 SEO e Metadados no Next.js 📊

O Search Engine Optimization (SEO) é fundamental para garantir que sua aplicação seja encontrada pelos mecanismos de busca. O Next.js oferece ferramentas poderosas para otimizar sua aplicação para SEO, permitindo que você configure metadados, gere sitemap, robots.txt e muito mais. Neste guia, vamos explorar como aproveitar essas funcionalidades para melhorar o posicionamento da sua aplicação nos resultados de busca.

## 🚫 Desafios de SEO em SPAs Tradicionais

As **Single Page Applications (SPAs)** enfrentam sérios desafios quando se trata de SEO:

### Problemas Comuns:

1. **🕷️ Crawling Limitado**:
   - Bots de mecanismos de busca podem não executar JavaScript adequadamente
   - Conteúdo dinâmico pode não ser indexado
   - Tempo limite de crawling pode ser excedido

2. **📄 Conteúdo Vazio Inicial**:
   - HTML inicial contém apenas um div vazio
   - Meta tags são genéricas para todas as páginas
   - Falta de conteúdo estruturado para indexação

3. **🔗 URLs Problemáticas**:
   - Hash routing (#) não é ideal para SEO
   - Estados de aplicação podem não corresponder a URLs únicos
   - Dificuldade em compartilhar páginas específicas

4. **⏱️ Core Web Vitals Ruins**:
   - First Contentful Paint (FCP) lento
   - Largest Contentful Paint (LCP) prejudicado
   - Cumulative Layout Shift (CLS) por carregamento dinâmico

## ✅ Como o Next.js Resolve os Problemas de SEO

O Next.js foi projetado desde o início para resolver esses desafios de SEO:

### Soluções Integradas:

1. **🏗️ Server-Side Rendering (SSR)**:
   - HTML completo é gerado no servidor
   - Bots veem o conteúdo imediatamente
   - Meta tags dinâmicas por página
   - Conteúdo estruturado disponível na primeira requisição

2. **📄 Static Site Generation (SSG)**:
   - Páginas pré-renderizadas durante o build
   - Performance máxima para crawlers
   - Conteúdo sempre disponível
   - Ideal para blogs, documentação e landing pages

3. **🔄 Incremental Static Regeneration (ISR)**:
   - Combina benefícios de SSG com atualizações dinâmicas
   - Conteúdo sempre fresco sem comprometer SEO
   - Regeneração automática baseada em tempo ou eventos

4. **🎯 API de Metadados Avançada**:
   - Metadados específicos por página
   - Open Graph e Twitter Cards automáticos
   - Schema.org JSON-LD integrado
   - Sitemap e robots.txt dinâmicos

5. **📊 Core Web Vitals Otimizados**:
   - Code splitting automático
   - Otimização de imagens integrada
   - Prefetching inteligente
   - Bundle optimization automático

### Resultado:
**O Next.js transforma sua aplicação React em uma máquina de SEO**, oferecendo todos os benefícios de uma SPA moderna com a indexabilidade de sites tradicionais.

## 📋 Metadados no Next.js

O Next.js fornece uma forma simples e eficiente de definir metadados para suas páginas. Existem duas abordagens principais, dependendo do router que você está utilizando:

### Metadados no App Router (Next.js 13+)

No App Router, você pode usar a API de Metadados para definir informações importantes para SEO:

```jsx
// app/layout.jsx (Metadados para toda a aplicação)
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: {
    default: 'Minha Aplicação Next.js',
    template: '%s | Minha Aplicação Next.js',
  },
  description: 'Uma aplicação Next.js com excelente SEO',
  keywords: ['Next.js', 'React', 'JavaScript', 'SEO'],
  authors: [{ name: 'Seu Nome', url: 'https://seusite.com' }],
  creator: 'Seu Nome',
  publisher: 'Sua Empresa',
  formatDetection: {
    email: false,
    telephone: false,
    address: false,
  },
};

export default function RootLayout({ children }) {
  return (
    <html lang="pt-BR">
      <body>{children}</body>
    </html>
  );
}
```

### Metadados para Páginas Específicas

Você pode definir metadados específicos para cada página, que substituirão ou complementarão os metadados globais:

```jsx
// app/blog/[slug]/page.jsx
import { getPostBySlug } from '@/lib/posts';

export async function generateMetadata({ params }) {
  const post = await getPostBySlug(params.slug);
  
  return {
    title: post.title,
    description: post.resumo,
    openGraph: {
      title: post.title,
      description: post.resumo,
      images: [
        {
          url: post.imagemCapa,
          width: 1200,
          height: 630,
          alt: post.title,
        },
      ],
      type: 'article',
      publishedTime: post.dataPublicacao,
      authors: [post.autor],
      tags: post.categorias,
    },
    twitter: {
      card: 'summary_large_image',
      title: post.title,
      description: post.resumo,
      images: [post.imagemCapa],
      creator: '@seuhandle',
    },
  };
}

export default async function PostPage({ params }) {
  const post = await getPostBySlug(params.slug);
  
  return (
    <article>
      <h1>{post.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: post.conteudo }} />
    </article>
  );
}
```

### Metadados no Pages Router (Legacy)

Para projetos que ainda usam o Pages Router, utilize o componente `Head` do `next/head`:

```jsx
// pages/index.js
import Head from 'next/head';

export default function Home() {
  return (
    <>
      <Head>
        <title>Minha Aplicação Next.js</title>
        <meta name="description" content="Uma aplicação Next.js com excelente SEO" />
        <meta name="keywords" content="Next.js, React, JavaScript, SEO" />
        <meta name="author" content="Seu Nome" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      
      <main>
        <h1>Bem-vindo à minha aplicação!</h1>
      </main>
    </>
  );
}
```

## 🌐 Open Graph e Social Media

Os metadados Open Graph permitem controlar como sua página aparece quando compartilhada em redes sociais como Facebook, Twitter e LinkedIn.

### Configurando Open Graph no Next.js

```jsx
// app/layout.jsx (App Router)
export const metadata = {
  openGraph: {
    type: 'website',
    locale: 'pt_BR',
    url: 'https://seusite.com',
    title: 'Minha Aplicação Next.js',
    description: 'Uma aplicação Next.js com excelente SEO',
    siteName: 'Nome do seu site',
    images: [
      {
        url: 'https://seusite.com/og-image.jpg',
        width: 1200,
        height: 630,
        alt: 'Minha Aplicação Next.js',
      },
    ],
  },
  twitter: {
    card: 'summary_large_image',
    title: 'Minha Aplicação Next.js',
    description: 'Uma aplicação Next.js com excelente SEO',
    creator: '@seuhandle',
    images: ['https://seusite.com/twitter-image.jpg'],
  },
};
```

### Imagens Dinâmicas para Redes Sociais

Para gerar imagens dinâmicas para compartilhamento, você pode usar APIs como Cloudinary, Vercel OG ou criar suas próprias:

```jsx
// app/api/og/route.jsx
import { ImageResponse } from 'next/server';

export async function GET(request) {
  const { searchParams } = new URL(request.url);
  const title = searchParams.get('title') || 'Minha Aplicação Next.js';
  
  return new ImageResponse(
    (
      <div
        style={{
          display: 'flex',
          fontSize: 60,
          color: 'white',
          background: 'linear-gradient(to right, #3b82f6, #2563eb)',
          width: '100%',
          height: '100%',
          padding: '50px 200px',
          textAlign: 'center',
          justifyContent: 'center',
          alignItems: 'center',
        }}
      >
        <h1>{title}</h1>
      </div>
    ),
    {
      width: 1200,
      height: 630,
    }
  );
}
```

Use esta API em seus metadados:

```jsx
export async function generateMetadata({ params }) {
  const post = await getPostBySlug(params.slug);
  
  return {
    openGraph: {
      images: [{
        url: `https://seusite.com/api/og?title=${encodeURIComponent(post.title)}`,
        width: 1200,
        height: 630,
      }],
    },
  };
}
```

## 🤖 Robots.txt e Controle de Indexação

### Criando um arquivo Robots.txt

No App Router, crie um arquivo `app/robots.js` ou `app/robots.ts`:

```jsx
// app/robots.js
export default function robots() {
  return {
    rules: [
      {
        userAgent: '*',
        allow: '/',
        disallow: ['/admin/', '/api/'],
      },
    ],
    sitemap: 'https://seusite.com/sitemap.xml',
    host: 'https://seusite.com',
  };
}
```

No Pages Router, crie um arquivo estático em `public/robots.txt`:

```
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/

Sitemap: https://seusite.com/sitemap.xml
```

### Controle de Indexação por Página

Para controlar a indexação em páginas específicas:

```jsx
// app/area-privada/page.jsx
export const metadata = {
  robots: {
    index: false,
    follow: false,
    nocache: true,
    googleBot: {
      index: false,
      follow: false,
      noimageindex: true,
    },
  },
};
```

## 🗺️ Sitemap

Um sitemap ajuda os mecanismos de busca a entender a estrutura do seu site.

### Sitemap Dinâmico no App Router

```jsx
// app/sitemap.js
export default async function sitemap() {
  // Buscar páginas dinâmicas
  const posts = await getAllPosts();
  const blogUrls = posts.map((post) => ({
    url: `https://seusite.com/blog/${post.slug}`,
    lastModified: post.updatedAt || post.publishedAt,
  }));
  
  // Páginas estáticas
  const routes = [
    '',
    '/sobre',
    '/contato',
    '/produtos',
  ].map((route) => ({
    url: `https://seusite.com${route}`,
    lastModified: new Date().toISOString(),
  }));
  
  return [...routes, ...blogUrls];
}
```

### Sitemap no Pages Router

Para o Pages Router, você pode criar um endpoint personalizado:

```jsx
// pages/sitemap.xml.js
import { getAllPosts } from '@/lib/api';

const Sitemap = () => {
  // Este componente retorna um XML, então não precisa de JSX
  return null;
};

export async function getServerSideProps({ res }) {
  const posts = await getAllPosts();
  
  const sitemap = `<?xml version="1.0" encoding="UTF-8"?>
    <urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
      <url>
        <loc>https://seusite.com</loc>
        <lastmod>${new Date().toISOString()}</lastmod>
        <changefreq>monthly</changefreq>
        <priority>1.0</priority>
      </url>
      ${posts.map((post) => `
        <url>
          <loc>https://seusite.com/blog/${post.slug}</loc>
          <lastmod>${post.updatedAt || post.publishedAt}</lastmod>
          <changefreq>weekly</changefreq>
          <priority>0.8</priority>
        </url>
      `).join('')}
    </urlset>
  `;
  
  res.setHeader('Content-Type', 'text/xml');
  res.write(sitemap);
  res.end();
  
  return {
    props: {},
  };
}

export default Sitemap;
```

## 📱 Responsividade e Mobile Friendliness

A otimização para dispositivos móveis é um fator de ranqueamento importante:

```jsx
// app/layout.jsx
export const metadata = {
  viewport: {
    width: 'device-width',
    initialScale: 1,
    maximumScale: 5,
    userScalable: true,
  },
  themeColor: [
    { media: '(prefers-color-scheme: light)', color: '#ffffff' },
    { media: '(prefers-color-scheme: dark)', color: '#111111' },
  ],
};
```

## 🌍 Internacionalização (i18n)

A internacionalização permite que seu site atenda audiências em diferentes idiomas:

### i18n no App Router

```jsx
// middleware.js (detecção de idioma e redirecionamento)
import { NextResponse } from 'next/server';

const defaultLocale = 'pt';
const locales = ['pt', 'en', 'es'];

export function middleware(request) {
  const { pathname } = request.nextUrl;
  
  // Verificar se o caminho já tem um locale
  const pathnameHasLocale = locales.some(
    (locale) => pathname.startsWith(`/${locale}/`) || pathname === `/${locale}`
  );
  
  if (pathnameHasLocale) return;
  
  // Obter locale preferido do usuário
  const locale = request.headers.get('accept-language')?.split(',')[0].split('-')[0] || defaultLocale;
  const matchedLocale = locales.includes(locale) ? locale : defaultLocale;
  
  // Redirecionar para o mesmo caminho, mas com o locale
  request.nextUrl.pathname = `/${matchedLocale}${pathname}`;
  return NextResponse.redirect(request.nextUrl);
}

export const config = {
  matcher: [
    '/((?!api|_next/static|_next/image|favicon.ico|images|.*\\.svg).*)',
  ],
};
```

## 📊 Estrutura de Dados Schema.org

Adicionar Schema.org JSON-LD ajuda os mecanismos de busca a entender o conteúdo da sua página:

### Exemplo para Página de Produto

```jsx
// app/produtos/[id]/page.jsx
import { getProduto } from '@/lib/produtos';

export default async function ProdutoPage({ params }) {
  const produto = await getProduto(params.id);
  
  const schemaData = {
    '@context': 'https://schema.org',
    '@type': 'Product',
    name: produto.nome,
    description: produto.descricao,
    image: produto.imagem,
    sku: produto.sku,
    brand: {
      '@type': 'Brand',
      name: produto.marca,
    },
    offers: {
      '@type': 'Offer',
      price: produto.preco.toFixed(2),
      priceCurrency: 'BRL',
      availability: produto.emEstoque
        ? 'https://schema.org/InStock'
        : 'https://schema.org/OutOfStock',
    },
    aggregateRating: {
      '@type': 'AggregateRating',
      ratingValue: produto.avaliacao.media,
      reviewCount: produto.avaliacao.total,
    },
  };
  
  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(schemaData) }}
      />
      
      <div className="produto-page">
        <h1>{produto.nome}</h1>
        <img src={produto.imagem} alt={produto.nome} />
        <p>{produto.descricao}</p>
        <div className="preco">R$ {produto.preco.toFixed(2)}</div>
        <button disabled={!produto.emEstoque}>
          {produto.emEstoque ? 'Adicionar ao Carrinho' : 'Produto Indisponível'}
        </button>
      </div>
    </>
  );
}
```

### Exemplo para Página de Blog/Artigo

```jsx
// app/blog/[slug]/page.jsx
export default async function BlogPost({ params }) {
  const post = await getPostBySlug(params.slug);
  
  const schemaData = {
    '@context': 'https://schema.org',
    '@type': 'BlogPosting',
    headline: post.title,
    description: post.resumo,
    image: post.imagemCapa,
    datePublished: post.dataPublicacao,
    dateModified: post.dataAtualizacao || post.dataPublicacao,
    author: {
      '@type': 'Person',
      name: post.autor.nome,
      url: post.autor.url,
    },
    publisher: {
      '@type': 'Organization',
      name: 'Nome da Empresa',
      logo: {
        '@type': 'ImageObject',
        url: 'https://seusite.com/logo.png',
      },
    },
    mainEntityOfPage: {
      '@type': 'WebPage',
      '@id': `https://seusite.com/blog/${post.slug}`,
    },
  };
  
  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(schemaData) }}
      />
      
      <article>
        <h1>{post.title}</h1>
        <div dangerouslySetInnerHTML={{ __html: post.conteudo }} />
      </article>
    </>
  );
}
```

## 🔄 Canonical URLs

As URLs canônicas ajudam a evitar conteúdo duplicado:

```jsx
// app/produtos/[id]/page.jsx
export const metadata = {
  alternates: {
    canonical: 'https://seusite.com/produtos/123',
    languages: {
      'pt-BR': 'https://seusite.com/pt/produtos/123',
      'en-US': 'https://seusite.com/en/products/123',
    },
  },
};
```

## 🧠 Ferramentas e Análise de SEO

### Integração com Google Analytics

```jsx
// app/layout.jsx
import Script from 'next/script';
import { GA_TRACKING_ID } from '@/lib/gtag';

export default function RootLayout({ children }) {
  return (
    <html lang="pt-BR">
      <head>
        <Script
          src={`https://www.googletagmanager.com/gtag/js?id=${GA_TRACKING_ID}`}
          strategy="afterInteractive"
        />
        <Script id="google-analytics" strategy="afterInteractive">
          {`
            window.dataLayer = window.dataLayer || [];
            function gtag(){dataLayer.push(arguments);}
            gtag('js', new Date());
            gtag('config', '${GA_TRACKING_ID}');
          `}
        </Script>
      </head>
      <body>{children}</body>
    </html>
  );
}
```

### Integração com Google Search Console

```jsx
// app/layout.jsx
export const metadata = {
  verification: {
    google: 'ID_VERIFICACAO_GOOGLE',
    yandex: 'ID_VERIFICACAO_YANDEX',
    bing: 'ID_VERIFICACAO_BING',
  },
};
```

## 🚀 Melhores Práticas de SEO com Next.js

1. **Use Renderização do Lado do Servidor (SSR)**: Garante que os mecanismos de busca vejam o conteúdo completo
2. **Implemente URLs semânticas**: Crie URLs amigáveis e descritivas
3. **Otimize para Core Web Vitals**: Melhore LCP, FID e CLS para melhor ranqueamento
4. **Use cabeçalhos hierárquicos**: Mantenha uma estrutura adequada de H1-H6
5. **Crie metadados específicos por página**: Evite títulos e descrições duplicados
6. **Implemente Schema.org**: Forneça dados estruturados para rich snippets
7. **Optimize imagens**: Use o componente `Image` do Next.js
8. **Localize seu conteúdo**: Forneça conteúdo em múltiplos idiomas com URLs apropriadas
9. **Mantenha um sitemap atualizado**: Gere sitemaps dinâmicos
10. **Configure um arquivo robots.txt**: Controle o que deve ser indexado

## 🔄 Auditoria e Monitoramento de SEO

### Integração com Lighthouse

```jsx
// .github/workflows/lighthouse.yml
name: Lighthouse CI
on: [push]
jobs:
  lighthouse:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Lighthouse
        uses: treosh/lighthouse-ci-action@v10
        with:
          urls: |
            https://seuproduto.com/
            https://seuproduto.com/produtos
          uploadArtifacts: true
          temporaryPublicStorage: true
```

## 🔗 Recursos Adicionais

- [Documentação oficial de SEO do Next.js](https://nextjs.org/docs/app/building-your-application/optimizing/metadata)
- [API de Metadados do Next.js](https://nextjs.org/docs/app/api-reference/file-conventions/metadata)
- [Google Search Console](https://search.google.com/search-console/about)
- [Schema.org](https://schema.org/)
- [Open Graph Protocol](https://ogp.me/)
- [Twitter Card Validator](https://cards-dev.twitter.com/validator)
- [Google Rich Results Test](https://search.google.com/test/rich-results)

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/>