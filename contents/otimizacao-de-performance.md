<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# ⚡ Otimização de Performance no Next.js 🚀

A performance é um aspecto crítico para o sucesso de aplicações web modernas. O Next.js oferece diversas funcionalidades e otimizações integradas que ajudam a criar sites rápidos e eficientes. Neste guia, vamos explorar as técnicas e ferramentas disponíveis para maximizar a performance da sua aplicação Next.js.

## 📋 Por que Performance Importa?

Uma aplicação rápida proporciona:
- 💰 Maiores taxas de conversão
- 🔄 Menores taxas de rejeição
- 📱 Melhor experiência em dispositivos móveis
- 🔍 Melhor posicionamento em mecanismos de busca (SEO)
- 👥 Maior engajamento dos usuários

O Next.js foi projetado com performance em mente, oferecendo otimizações automáticas e ferramentas para alcançar excelentes métricas de desempenho.

## 🚫 Problemas de Performance em SPAs Tradicionais

As **Single Page Applications (SPAs)** convencionais enfrentam diversos desafios de performance:

### Principais Limitações:

1. **📦 Bundle Size Gigante**:
   - Todo o JavaScript da aplicação é baixado na primeira visita
   - Bibliotecas e dependências são carregadas mesmo se não utilizadas
   - Tempo de carregamento inicial extremamente lento
   - Experiência ruim em conexões lentas

2. **⏱️ Time to Interactive (TTI) Alto**:
   - Usuários precisam esperar todo o JavaScript ser baixado e executado
   - Tela em branco por longos períodos
   - Frustração do usuário e alta taxa de abandono

3. **🔄 Renderização Bloqueante**:
   - JavaScript bloqueia a renderização inicial
   - Conteúdo só aparece após execução completa do JS
   - Dispositivos lentos sofrem ainda mais

4. **📱 Consumo Excessivo de Recursos**:
   - Processamento intensivo no dispositivo do usuário
   - Drenagem rápida da bateria em dispositivos móveis
   - Experiência degradada em hardware limitado

5. **🌐 Problemas de Cache**:
   - Dificuldade em implementar estratégias de cache eficientes
   - Invalidação de cache complexa
   - Recursos não são reutilizados adequadamente

## ✅ Como o Next.js Resolve os Problemas de Performance

O Next.js implementa **otimizações automáticas** que resolvem sistematicamente os problemas de SPAs:

### Soluções Integradas:

1. **📦 Code Splitting Automático**:
   - Cada página se torna um bundle separado
   - Apenas o código necessário é carregado
   - Redução drástica do tempo de carregamento inicial
   - Carregamento sob demanda de funcionalidades

2. **⚡ Server-Side Rendering (SSR)**:
   - HTML é gerado no servidor
   - Conteúdo visível imediatamente
   - JavaScript é hidratado progressivamente
   - Time to First Byte (TTFB) otimizado

3. **🏗️ Static Site Generation (SSG)**:
   - Páginas pré-renderizadas durante o build
   - Performance máxima possível
   - Servidas via CDN globalmente
   - Zero tempo de processamento no servidor

4. **🔄 Incremental Static Regeneration (ISR)**:
   - Combina velocidade estática com conteúdo dinâmico
   - Regeneração automática em background
   - Cache inteligente e invalidação eficiente

5. **🖼️ Otimização de Imagens Automática**:
   - Lazy loading nativo
   - Conversão automática para WebP/AVIF
   - Responsive images automáticas
   - Prevenção de Cumulative Layout Shift (CLS)

6. **🔗 Prefetching Inteligente**:
   - Links visíveis são pré-carregados automaticamente
   - Navegação instantânea entre páginas
   - Estratégias de prefetch configuráveis

### Resultado:
**O Next.js transforma uma SPA lenta em uma aplicação ultra-rápida**, mantendo todos os benefícios de interatividade enquanto resolve os problemas fundamentais de performance.

## 🔄 Core Web Vitals e Métricas de Performance

O Google utiliza as Core Web Vitals como métricas importantes para avaliar a experiência do usuário:

1. **LCP (Largest Contentful Paint)**: Tempo até o maior elemento visível ser renderizado
2. **FID (First Input Delay)**: Tempo até a interatividade
3. **CLS (Cumulative Layout Shift)**: Estabilidade visual

O Next.js ajuda a otimizar estas métricas através de diversas técnicas integradas.

## 🚀 Otimizações Automáticas do Next.js

### Code Splitting

O Next.js divide automaticamente seu código em pacotes menores:

- Cada página se torna um bundle separado
- Código compartilhado entre páginas é extraído para bundles comuns
- Componentes dinâmicos são carregados sob demanda

```jsx
// Componente carregado dinamicamente
import dynamic from 'next/dynamic';

// Importação dinâmica com loading fallback
const DynamicHeader = dynamic(() => import('@/components/Header'), {
  loading: () => <p>Carregando...</p>,
});

export default function MinhaPage() {
  return (
    <div>
      <DynamicHeader />
      <main>Conteúdo da página</main>
    </div>
  );
}
```

### Otimização de Imagens Automática

O componente `Image` do Next.js otimiza imagens automaticamente:

```jsx
import Image from 'next/image';

export default function Produto() {
  return (
    <div>
      <h1>Produto em Destaque</h1>
      <Image
        src="/produto.jpg"
        alt="Produto em Destaque"
        width={600}
        height={400}
        priority // Para imagens above the fold
      />
    </div>
  );
}
```

### Script Optimization

Carregamento otimizado de scripts com o componente `Script`:

```jsx
import Script from 'next/script';

export default function Layout({ children }) {
  return (
    <>
      <Script
        src="https://analytics.example.com/script.js"
        strategy="lazyOnload" // Carrega após tudo estar pronto
      />
      <main>{children}</main>
    </>
  );
}
```

Estratégias disponíveis:
- `beforeInteractive`: Carrega antes que a página se torne interativa
- `afterInteractive` (padrão): Carrega logo após a página se tornar interativa
- `lazyOnload`: Carrega durante o tempo ocioso do navegador
- `worker`: (Experimental) Carrega em um web worker

## 🔄 Estratégias de Renderização

### Server-Side Rendering (SSR)

O SSR melhora o LCP e o SEO gerando HTML no servidor:

```jsx
// app/produtos/[id]/page.jsx (App Router)
export default async function ProdutoPage({ params }) {
  // Busca dados no servidor
  const produto = await getProduto(params.id);
  
  return (
    <div>
      <h1>{produto.nome}</h1>
      <p>{produto.descricao}</p>
    </div>
  );
}
```

### Static Site Generation (SSG)

O SSG pré-renderiza páginas durante o build para máxima performance:

```jsx
// pages/blog/[slug].js (Pages Router)
export async function getStaticProps({ params }) {
  const post = await getPostBySlug(params.slug);
  return { props: { post } };
}

export async function getStaticPaths() {
  const posts = await getAllPosts();
  
  return {
    paths: posts.map(post => ({ params: { slug: post.slug } })),
    fallback: false,
  };
}
```

### Incremental Static Regeneration (ISR)

O ISR combina SSG com atualizações controladas:

```jsx
// pages/produtos/[id].js (Pages Router)
export async function getStaticProps({ params }) {
  const produto = await getProduto(params.id);
  
  return {
    props: { produto },
    revalidate: 60, // Revalidar a cada 60 segundos
  };
}
```

No App Router (Next.js 13+):

```jsx
// app/produtos/[id]/page.jsx
export const revalidate = 60; // Revalidar a cada 60 segundos

export default async function Produto({ params }) {
  const produto = await fetch(`https://api.exemple.com/produtos/${params.id}`, {
    next: { revalidate: 60 } // Também pode ser definido por rota
  }).then(res => res.json());
  
  return <ProdutoDisplay produto={produto} />;
}
```

## 🚀 Otimização de JavaScript

### Reduzindo o Tamanho do Bundle

1. **Importações Dinâmicas**

```jsx
import dynamic from 'next/dynamic';

// Carregar componente pesado apenas quando necessário
const PesadoComponente = dynamic(() => import('@/components/PesadoComponente'), {
  ssr: false, // Opcional, para carregar apenas no cliente
});
```

2. **Tree Shaking**

```jsx
// Bom: importações específicas permitem tree shaking
import { Button, Modal } from 'ui-library';

// Evite: importações completas impedem tree shaking
// import * as UI from 'ui-library';
```

### Deferred Loading

```jsx
'use client';

import { useState, useEffect } from 'react';

export default function MapaInterativo() {
  const [mapaCarregado, setMapaCarregado] = useState(false);
  
  useEffect(() => {
    // Espera a página terminar de carregar
    if (typeof window !== 'undefined') {
      // Carrega componente pesado após interação ou tempo
      const timer = setTimeout(() => {
        setMapaCarregado(true);
      }, 3000);
      
      return () => clearTimeout(timer);
    }
  }, []);
  
  return (
    <div>
      {mapaCarregado ? (
        <MapComponent /> // Componente pesado
      ) : (
        <div>Mapa será carregado em breve...</div>
      )}
    </div>
  );
}
```

## 🔍 Otimização de SEO e Metadata

```jsx
// app/layout.jsx (App Router)
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: {
    template: '%s | Minha Loja',
    default: 'Minha Loja - Produtos Incríveis',
  },
  description: 'A melhor loja online para seus produtos favoritos',
  openGraph: {
    title: 'Minha Loja Online',
    description: 'Encontre os melhores produtos',
    images: [
      {
        url: 'https://exemplo.com/og-image.jpg',
        width: 1200,
        height: 630,
      },
    ],
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

## 📱 Responsividade e Adaptabilidade

### Mobile-First Design

```jsx
import { useMediaQuery } from '@/hooks/useMediaQuery';

export default function ResponsiveComponent() {
  const isMobile = useMediaQuery('(max-width: 768px)');
  
  return (
    <div className={isMobile ? 'mobile-layout' : 'desktop-layout'}>
      {isMobile ? (
        <MobileNavigation />
      ) : (
        <DesktopNavigation />
      )}
      <Content />
    </div>
  );
}
```

### Otimização para Diferentes Dispositivos

```jsx
'use client';

import { Suspense } from 'react';
import { useEffect, useState } from 'react';

function detectDevice() {
  const userAgent = navigator.userAgent;
  if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(userAgent)) {
    return 'mobile';
  }
  return 'desktop';
}

export default function AdaptiveComponent() {
  const [deviceType, setDeviceType] = useState('');
  
  useEffect(() => {
    setDeviceType(detectDevice());
  }, []);
  
  return (
    <div>
      {deviceType === 'mobile' ? (
        <MobileOptimizedContent />
      ) : (
        <DesktopOptimizedContent />
      )}
    </div>
  );
}
```

## 🔍 Análise e Monitoramento de Performance

### Ferramentas de Análise Integradas

1. **Next.js Analytics**

Em `next.config.js`:

```js
module.exports = {
  experimental: {
    measureFid: true,
  },
}
```

2. **Web Vitals Report**

```jsx
// pages/_app.js (Pages Router)
import { useEffect } from 'react';
import { useRouter } from 'next/router';
import * as gtag from '@/lib/gtag';

export function reportWebVitals({ id, name, label, value }) {
  // Enviar métricas para Google Analytics
  gtag.event({
    action: name,
    category: label === 'web-vital' ? 'Web Vitals' : 'Next.js metrics',
    label: id,
    value: Math.round(name === 'CLS' ? value * 1000 : value),
    nonInteraction: true,
  });
}

export default function App({ Component, pageProps }) {
  const router = useRouter();
  
  useEffect(() => {
    const handleRouteChange = (url) => {
      gtag.pageview(url);
    };
    router.events.on('routeChangeComplete', handleRouteChange);
    return () => {
      router.events.off('routeChangeComplete', handleRouteChange);
    };
  }, [router.events]);
  
  return <Component {...pageProps} />;
}
```

### Lighthouse CI

Integre o Lighthouse em seu CI/CD para monitorar performance a cada build:

```yaml
# .github/workflows/lighthouse.yml
name: Lighthouse CI
on: [push]
jobs:
  lighthouse:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Use Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18.x'
      - name: Install dependencies
        run: npm ci
      - name: Build project
        run: npm run build
      - name: Start server
        run: npm run start & npx wait-on http://localhost:3000
      - name: Run Lighthouse CI
        run: |
          npm install -g @lhci/cli@0.11.x
          lhci autorun
```

Configure o Lighthouse CI em `.lighthouserc.js`:

```js
module.exports = {
  ci: {
    collect: {
      url: ['http://localhost:3000/'],
      startServerCommand: 'npm run start',
    },
    upload: {
      target: 'temporary-public-storage',
    },
    assert: {
      preset: 'lighthouse:recommended',
      assertions: {
        'first-contentful-paint': ['warn', {maxNumericValue: 2000}],
        'interactive': ['error', {maxNumericValue: 5000}],
        'cumulative-layout-shift': ['error', {maxNumericValue: 0.1}],
        'largest-contentful-paint': ['error', {maxNumericValue: 2500}],
      },
    },
  },
};
```

## 💾 Caching e Persistência

### Caching de Dados

```jsx
// Usando SWR para caching no cliente
'use client';

import useSWR from 'swr';

const fetcher = (...args) => fetch(...args).then(res => res.json());

export default function ProdutosList() {
  const { data, error, isLoading } = useSWR('/api/produtos', fetcher, {
    revalidateOnFocus: false,
    revalidateOnReconnect: false,
    refreshInterval: 60000, // Revalidar a cada minuto
  });
  
  if (isLoading) return <div>Carregando...</div>;
  if (error) return <div>Erro ao carregar produtos</div>;
  
  return (
    <ul>
      {data.map(produto => (
        <li key={produto.id}>{produto.nome}</li>
      ))}
    </ul>
  );
}
```

### LocalStorage para Persistência

```jsx
'use client';

import { useState, useEffect } from 'react';

export default function CarrinhoCompras() {
  const [itens, setItens] = useState([]);
  
  // Carregar do localStorage ao montar
  useEffect(() => {
    const itensArmazenados = localStorage.getItem('carrinho');
    if (itensArmazenados) {
      setItens(JSON.parse(itensArmazenados));
    }
  }, []);
  
  // Salvar no localStorage ao atualizar
  useEffect(() => {
    localStorage.setItem('carrinho', JSON.stringify(itens));
  }, [itens]);
  
  // Renderização e lógica do carrinho...
}
```

## 🧰 Ferramentas e Técnicas Avançadas

### Prefetching de Dados

```jsx
// app/produtos/page.jsx
import { ProdutoCard } from '@/components/ProdutoCard';
import { preload } from '@/lib/preload';

// Pré-carregar detalhes do produto ao passar o mouse
export default function ProdutosPage({ produtos }) {
  return (
    <div className="grid">
      {produtos.map(produto => (
        <div 
          key={produto.id}
          onMouseEnter={() => preload(`/api/produtos/${produto.id}`)}
        >
          <ProdutoCard produto={produto} />
        </div>
      ))}
    </div>
  );
}

// lib/preload.js
export function preload(url) {
  // Realize a requisição e guarde em cache
  void fetch(url);
}
```

### HTTP/2 e Server Push

Configure seu servidor para usar HTTP/2 e Server Push:

```js
// next.config.js
module.exports = {
  poweredByHeader: false,
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          {
            key: 'X-DNS-Prefetch-Control',
            value: 'on',
          },
          {
            key: 'Strict-Transport-Security',
            value: 'max-age=63072000; includeSubDomains; preload',
          },
        ],
      },
    ];
  },
};
```

### Critical CSS

Extraia o CSS crítico para renderização inicial mais rápida:

```jsx
// Usando Tailwind CSS
// tailwind.config.js
module.exports = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx}',
    './components/**/*.{js,ts,jsx,tsx}',
    './app/**/*.{js,ts,jsx,tsx}',
  ],
  
  // Usar classe no HTML para desabilitar animações até a interação
  safelist: ['reduce-motion'],
  
  theme: {
    extend: {},
  },
  plugins: [],
};

// app/layout.jsx
import '@/styles/globals.css';

export default function RootLayout({ children }) {
  return (
    <html lang="pt-BR" className="reduce-motion">
      <body>
        {children}
        <script dangerouslySetInnerHTML={{
          __html: `
            // Remover classe depois que a página carrega
            document.documentElement.classList.remove('reduce-motion');
          `
        }} />
      </body>
    </html>
  );
}
```

## 📱 Otimização para Dispositivos Móveis

### Adaptação para Conexões Lentas

```jsx
'use client';

import { useNetworkStatus } from '@/hooks/useNetworkStatus';

export default function AdaptiveContent() {
  const { effectiveConnectionType } = useNetworkStatus();
  
  // Renderizar conteúdo adaptado à velocidade de conexão
  return (
    <div>
      {effectiveConnectionType === '4g' ? (
        <VideoPlayer src="/video/hd.mp4" />
      ) : (
        <VideoPlayer src="/video/sd.mp4" />
      )}
    </div>
  );
}

// hooks/useNetworkStatus.js
import { useState, useEffect } from 'react';

export function useNetworkStatus() {
  const [status, setStatus] = useState({
    effectiveConnectionType: '4g',
    downlink: 10,
    rtt: 50,
  });
  
  useEffect(() => {
    if (typeof navigator !== 'undefined' && 'connection' in navigator) {
      const connection = navigator.connection;
      
      setStatus({
        effectiveConnectionType: connection.effectiveType,
        downlink: connection.downlink,
        rtt: connection.rtt,
      });
      
      const updateStatus = () => {
        setStatus({
          effectiveConnectionType: connection.effectiveType,
          downlink: connection.downlink,
          rtt: connection.rtt,
        });
      };
      
      connection.addEventListener('change', updateStatus);
      return () => connection.removeEventListener('change', updateStatus);
    }
  }, []);
  
  return status;
}
```

### Touch Optimization

```css
/* Otimizações para dispositivos touch */
@media (pointer: coarse) {
  /* Aumentar área de toque */
  .botao {
    min-height: 44px;
    min-width: 44px;
    padding: 12px 16px;
  }
  
  /* Espaçamento adequado entre elementos interativos */
  .nav-links a {
    margin: 0 10px;
    padding: 12px;
  }
}
```

## 🧠 Melhores Práticas

1. **Minimize JavaScript**: Use componentes Server e importe dinamicamente quando possível
2. **Otimize imagens**: Sempre use o componente `Image` com dimensões adequadas
3. **Priorize conteúdo crítico**: Use `priority` para imagens above-the-fold
4. **Utilize Lazy Loading**: Carregue apenas o necessário para a visualização inicial
5. **Implemente SSG onde possível**: Pré-renderize páginas estáticas para performance máxima
6. **Evite Layout Shifts**: Defina dimensões para elementos carregados dinamicamente
7. **Minimize trabalho no thread principal**: Mova operações pesadas para web workers
8. **Utilize Service Workers**: Para melhorar a experiência offline e cache
9. **Implemente estratégias de cache**: Combine ISR, SWR e localStorage
10. **Monitore constantemente**: Use ferramentas de análise para identificar gargalos

## 🔍 Recursos Adicionais

- [Documentação de performance do Next.js](https://nextjs.org/docs/advanced-features/measuring-performance)
- [Web Vitals](https://web.dev/vitals/)
- [Next.js Performance Dashboard](https://vercel.com/docs/concepts/analytics)
- [Lighthouse](https://developers.google.com/web/tools/lighthouse)
- [WebPageTest](https://www.webpagetest.org/)

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/>