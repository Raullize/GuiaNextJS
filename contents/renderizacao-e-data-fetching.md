<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# Renderização e Data Fetching no Next.js

O Next.js se destaca pela sua abordagem flexível à renderização, oferecendo múltiplas estratégias para otimizar o desempenho, SEO e experiência do usuário. Neste guia, vamos explorar as diferentes formas de renderização e como buscar dados em cada uma delas.

## Limitações das SPAs Tradicionais

Antes de explorarmos as soluções do Next.js, é importante entender os desafios das **Single Page Applications (SPAs)** tradicionais:

### Problemas Comuns das SPAs:

1. **SEO Prejudicado**:
   - Conteúdo renderizado apenas no cliente
   - Mecanismos de busca podem não indexar adequadamente
   - Meta tags dinâmicas são difíceis de implementar

2. **Tempo de Carregamento Inicial Lento**:
   - Todo o bundle JavaScript deve ser baixado antes da primeira renderização
   - Usuários veem uma tela em branco por mais tempo
   - Experiência ruim em conexões lentas

3. **Performance Inconsistente**:
   - Dispositivos menos potentes sofrem com renderização pesada no cliente
   - Bateria dos dispositivos móveis é drenada mais rapidamente
   - Experiência degradada em hardware limitado

4. **Problemas de Acessibilidade**:
   - Leitores de tela podem ter dificuldade com conteúdo dinâmico
   - Navegação por teclado pode ser comprometida
   - Falta de progressive enhancement

## ✅ Como o Next.js Resolve Esses Problemas

O Next.js oferece **múltiplas estratégias de renderização** que resolvem essas limitações:

## Estratégias de Renderização

O Next.js oferece quatro principais estratégias de renderização:

1. **Server-Side Rendering (SSR)**: Renderização no servidor a cada requisição
   - ✅ **Resolve**: SEO, tempo de carregamento inicial, acessibilidade
  
2. **Static Site Generation (SSG)**: Renderização durante o build
   - ✅ **Resolve**: Performance máxima, SEO perfeito, experiência consistente
  
3. **Incremental Static Regeneration (ISR)**: Atualização de páginas estáticas em intervalos
   - ✅ **Resolve**: Equilibra performance estática com conteúdo dinâmico
  
4. **Client-Side Rendering (CSR)**: Renderização no navegador do usuário
   - ✅ **Usado estrategicamente**: Para conteúdo altamente interativo e personalizado

Cada estratégia tem casos de uso específicos, e o Next.js permite **misturá-las na mesma aplicação** para obter o melhor resultado possível.

## Server-Side Rendering (SSR)

Na renderização do lado do servidor, o HTML é gerado no servidor a cada requisição.

### Pages Router (getServerSideProps)

```jsx
// pages/produtos/[id].js
export async function getServerSideProps(context) {
  const { id } = context.params;
  const res = await fetch(`https://api.exemplo.com/produtos/${id}`);
  const produto = await res.json();
  
  return {
    props: { produto }, // Será passado para o componente
  };
}

export default function ProdutoPage({ produto }) {
  return (
    <div>
      <h1>{produto.nome}</h1>
      <p>{produto.descricao}</p>
      <p>R$ {produto.preco}</p>
    </div>
  );
}
```

### App Router (Server Components)

No App Router, todos os componentes são Server Components por padrão:

```jsx
// app/produtos/[id]/page.js
async function getProduto(id) {
  const res = await fetch(`https://api.exemplo.com/produtos/${id}`);
  return res.json();
}

export default async function ProdutoPage({ params }) {
  const produto = await getProduto(params.id);
  
  return (
    <div>
      <h1>{produto.nome}</h1>
      <p>{produto.descricao}</p>
      <p>R$ {produto.preco}</p>
    </div>
  );
}
```

### Quando usar SSR

- Páginas que precisam de dados em tempo real
- Conteúdo personalizado para cada usuário
- Quando os dados mudam frequentemente
- Quando é necessário acesso a cookies ou cabeçalhos de requisição

## Static Site Generation (SSG)

Com o SSG, as páginas são geradas durante o build e servidas como arquivos estáticos.

### Pages Router (getStaticProps)

```jsx
// pages/blog/[slug].js
export async function getStaticProps({ params }) {
  const { slug } = params;
  const post = await getBlogPost(slug);
  
  return {
    props: { post },
  };
}

export async function getStaticPaths() {
  const posts = await getAllPosts();
  
  return {
    paths: posts.map((post) => ({
      params: { slug: post.slug },
    })),
    fallback: false, // ou 'blocking' ou true
  };
}

export default function BlogPost({ post }) {
  return (
    <article>
      <h1>{post.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  );
}
```

### App Router (generateStaticParams)

```jsx
// app/blog/[slug]/page.js
export async function generateStaticParams() {
  const posts = await getAllPosts();
  
  return posts.map((post) => ({
    slug: post.slug,
  }));
}

async function getPost(slug) {
  return getBlogPost(slug);
}

export default async function BlogPost({ params }) {
  const post = await getPost(params.slug);
  
  return (
    <article>
      <h1>{post.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  );
}
```

### Quando usar SSG

- Blogs, documentação, landing pages
- Páginas de marketing
- Listagens de produtos que não mudam frequentemente
- Quando o SEO é prioritário

## Incremental Static Regeneration (ISR)

O ISR permite regenerar páginas estáticas após a build, em intervalos definidos.

### Pages Router (revalidate)

```jsx
// pages/produtos/[id].js
export async function getStaticProps({ params }) {
  const { id } = params;
  const produto = await getProduto(id);
  
  return {
    props: { produto },
    revalidate: 60, // Regenera a cada 60 segundos
  };
}

export async function getStaticPaths() {
  const produtosEmDestaque = await getProdutosEmDestaque();
  
  return {
    paths: produtosEmDestaque.map((produto) => ({
      params: { id: produto.id.toString() },
    })),
    fallback: 'blocking', // Gera páginas sob demanda
  };
}

export default function ProdutoPage({ produto }) {
  return (
    <div>
      <h1>{produto.nome}</h1>
      <p>R$ {produto.preco}</p>
    </div>
  );
}
```

### App Router (revalidate)

```jsx
// app/produtos/[id]/page.js
export const revalidate = 60; // Revalidação a cada 60 segundos

// Ou, para uma única fetch
async function getProduto(id) {
  const res = await fetch(`https://api.exemplo.com/produtos/${id}`, {
    next: { revalidate: 60 }
  });
  return res.json();
}
```

### On-Demand Revalidation

Para revalidar páginas manualmente:

```js
// pages/api/revalidate.js (Pages Router)
export default async function handler(req, res) {
  if (req.headers.authorization !== `Bearer ${process.env.REVALIDATE_TOKEN}`) {
    return res.status(401).json({ message: 'Não autorizado' });
  }

  try {
    const path = req.query.path;
    await res.revalidate(path);
    return res.json({ revalidated: true });
  } catch (err) {
    return res.status(500).send('Erro na revalidação');
  }
}
```

```js
// app/api/revalidate/route.js (App Router)
import { revalidatePath } from 'next/cache';
import { NextResponse } from 'next/server';

export async function POST(request) {
  const { path, token } = await request.json();
  
  if (token !== process.env.REVALIDATE_TOKEN) {
    return NextResponse.json({ message: 'Não autorizado' }, { status: 401 });
  }
  
  revalidatePath(path);
  return NextResponse.json({ revalidated: true, now: Date.now() });
}
```

### Quando usar ISR

- Lojas com muitos produtos
- Conteúdo que muda ocasionalmente
- Dashboards com atualizações periódicas
- Quando é necessário equilibrar performance e atualidade

## Client-Side Rendering (CSR)

Para dados que mudam frequentemente ou são específicos do usuário.

### SWR (Pages e App Router)

```jsx
'use client'; // No App Router

import useSWR from 'swr';

const fetcher = (...args) => fetch(...args).then(res => res.json());

export default function Dashboard() {
  const { data, error, isLoading } = useSWR('/api/dashboard', fetcher, {
    refreshInterval: 5000 // Atualiza a cada 5 segundos
  });
  
  if (isLoading) return <div>Carregando...</div>;
  if (error) return <div>Erro ao carregar dados</div>;
  
  return (
    <div>
      <h1>Dashboard</h1>
      <p>Usuários ativos: {data.activeUsers}</p>
      <p>Conversões hoje: {data.conversions}</p>
    </div>
  );
}
```

### React Query

```jsx
'use client'; // No App Router

import { useQuery } from '@tanstack/react-query';

export default function NotificacoesUsuario() {
  const { data, isLoading } = useQuery({
    queryKey: ['notificacoes'],
    queryFn: () => fetch('/api/notificacoes').then(res => res.json()),
    refetchInterval: 10000 // Atualiza a cada 10 segundos
  });
  
  if (isLoading) return <div>Carregando...</div>;
  
  return (
    <div>
      <h2>Notificações ({data.length})</h2>
      <ul>
        {data.map(notificacao => (
          <li key={notificacao.id}>{notificacao.mensagem}</li>
        ))}
      </ul>
    </div>
  );
}
```

### Quando usar CSR

- Dashboards privados
- Páginas que não precisam de SEO
- Dados personalizados por usuário
- Interfaces altamente interativas

## Renderização Híbrida (App Router)

No App Router, você pode combinar Server e Client Components:

```jsx
// app/dashboard/page.js (Server Component)
import { Suspense } from 'react';
import DadosUsuarioClientSide from './DadosUsuarioClientSide';
import EstatisticasServidor from './EstatisticasServidor';

// Dados buscados no servidor
async function getEstatisticasGerais() {
  return fetch('https://api.exemplo.com/estatisticas').then(res => res.json());
}

export default async function Dashboard() {
  const estatisticas = await getEstatisticasGerais();
  
  return (
    <div>
      <h1>Dashboard</h1>
      <EstatisticasServidor dados={estatisticas} />
      
      <Suspense fallback={<p>Carregando dados do usuário...</p>}>
        <DadosUsuarioClientSide />
      </Suspense>
    </div>
  );
}
```

```jsx
// app/dashboard/DadosUsuarioClientSide.js
'use client';

import { useEffect, useState } from 'react';

export default function DadosUsuarioClientSide() {
  const [dados, setDados] = useState(null);
  
  useEffect(() => {
    async function carregarDados() {
      const res = await fetch('/api/usuario/dados');
      setDados(await res.json());
    }
    
    carregarDados();
  }, []);
  
  if (!dados) return <p>Carregando...</p>;
  
  return (
    <div>
      <h2>Seus dados</h2>
      <p>Último acesso: {new Date(dados.ultimoAcesso).toLocaleString()}</p>
    </div>
  );
}
```

## Estratégias de Data Fetching

### Fetch API (App Router)

O Next.js estende a API nativa fetch com opções adicionais:

```jsx
// Busca com cache (padrão)
const dados = await fetch('https://api.exemplo.com/dados');

// Desativar cache (como SSR)
const dadosAtuais = await fetch('https://api.exemplo.com/tempo', { cache: 'no-store' });

// Revalidar a cada 60 segundos (como ISR)
const produtos = await fetch('https://api.exemplo.com/produtos', { 
  next: { revalidate: 60 } 
});

// Definir timeout
const resultado = await fetch('https://api.lenta.com/dados', { 
  next: { revalidate: 3600 },
  signal: AbortSignal.timeout(5000) // 5 segundos
});
```

### Paralelizando Requisições

```jsx
// Busca em paralelo para melhor performance
async function PaginaProduto({ params }) {
  // Inicia as requisições em paralelo
  const produtoPromise = getProduto(params.id);
  const recomendacoesPromise = getRecomendacoes(params.id);
  const avaliacoesPromise = getAvaliacoes(params.id);
  
  // Aguarda todas as promessas
  const [produto, recomendacoes, avaliacoes] = await Promise.all([
    produtoPromise,
    recomendacoesPromise, 
    avaliacoesPromise
  ]);
  
  return (
    <div>
      <h1>{produto.nome}</h1>
      <RecomendacoesComponent produtos={recomendacoes} />
      <AvaliacoesComponent avaliacoes={avaliacoes} />
    </div>
  );
}
```

### Suspense para Streaming

```jsx
// app/produto/[id]/page.js
import { Suspense } from 'react';
import DetalheProduto from './DetalheProduto';
import Avaliacoes from './Avaliacoes';
import Recomendacoes from './Recomendacoes';

export default function PaginaProduto({ params }) {
  return (
    <div>
      <DetalheProduto id={params.id} />
      
      <Suspense fallback={<div>Carregando avaliações...</div>}>
        <Avaliacoes produtoId={params.id} />
      </Suspense>
      
      <Suspense fallback={<div>Carregando recomendações...</div>}>
        <Recomendacoes produtoId={params.id} />
      </Suspense>
    </div>
  );
}
```

## Manipulando Cache

### Revalidar Todo o Cache

```jsx
import { revalidatePath } from 'next/cache';

async function atualizarProduto(formData) {
  'use server';
  
  // Atualização no banco de dados...
  
  // Revalidar todas as páginas que contêm produtos
  revalidatePath('/produtos');
}
```

### Limpeza de Cache Seletiva

```jsx
import { revalidateTag } from 'next/cache';

async function atualizarPreco(id, novoPreco) {
  'use server';
  
  // Atualizar preço no banco de dados...
  
  // Revalidar apenas as requisições específicas com esta tag
  revalidateTag(`produto-${id}`);
}

// Na página:
async function getProduto(id) {
  const produto = await fetch(`https://api.exemplo.com/produtos/${id}`, {
    next: { tags: [`produto-${id}`] }
  }).then(res => res.json());
  
  return produto;
}
```

## Melhores Práticas

1. **Escolha a estratégia certa**: Avalie cada página individualmente
2. **Combine abordagens**: Use SSG para conteúdo estático e CSR para dados dinâmicos
3. **Use o ISR** para páginas que mudam ocasionalmente
4. **Melhore a UX com Suspense**: Ofereça feedback enquanto o conteúdo carrega
5. **Otimize o waterfall de dados**: Fetching em paralelo melhora o desempenho
6. **Prefetching de dados**: Use técnicas como preload para conteúdo importante
7. **Cache eficiente**: Estratégias de caching reduzem chamadas desnecessárias à API

## Recursos Adicionais

- [Documentação oficial de Data Fetching](https://nextjs.org/docs/app/building-your-application/data-fetching)
- [SWR - Biblioteca de fetching para React](https://swr.vercel.app/)
- [React Query](https://tanstack.com/query)

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/>