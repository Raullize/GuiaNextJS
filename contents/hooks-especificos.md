# Hooks Específicos do Next.js

O Next.js oferece uma série de hooks específicos que simplificam o desenvolvimento e permitem acessar facilmente funcionalidades importantes do framework. Estes hooks são projetados para resolver problemas comuns de forma elegante e eficiente.

## useRouter

O hook `useRouter` é um dos mais utilizados no Next.js, permitindo acessar o objeto router com informações sobre a rota atual, navegação programática e manipulação de parâmetros.

### Importação e uso básico

```jsx
import { useRouter } from 'next/router'; // Para Pages Router
// OU
import { useRouter } from 'next/navigation'; // Para App Router

function MinhaComponente() {
  const router = useRouter();
  
  // Acessar informações da rota
  console.log(router.pathname); // Caminho atual (apenas Pages Router)
  console.log(router.query); // Parâmetros da URL (apenas Pages Router)
  
  // Navegação programática
  const navegarParaHome = () => {
    router.push('/');
  };
  
  return (
    <div>
      <h1>Página Atual</h1>
      <button onClick={navegarParaHome}>Ir para Home</button>
    </div>
  );
}
```

### Principais propriedades e métodos

#### No Pages Router:

- `router.pathname`: Caminho atual da rota
- `router.query`: Objeto com parâmetros da URL
- `router.asPath`: URL completa mostrada no navegador
- `router.isFallback`: Indica se a página está em modo fallback
- `router.push(url, as, options)`: Navega para a URL especificada
- `router.replace(url, as, options)`: Navega sem adicionar ao histórico
- `router.back()`: Volta para a página anterior
- `router.prefetch(url)`: Pré-carrega a página para navegação mais rápida
- `router.events`: Para ouvir eventos do router

#### No App Router:

- `router.push(url, options)`: Navega para a URL especificada
- `router.replace(url, options)`: Navega sem adicionar ao histórico
- `router.back()`: Volta para a página anterior
- `router.forward()`: Avança para a próxima página
- `router.refresh()`: Atualiza a rota atual sem perder o estado do cliente
- `router.prefetch(url)`: Pré-carrega a página para navegação mais rápida

### Exemplo com parâmetros dinâmicos

```jsx
// pages/produto/[id].js (Pages Router)
import { useRouter } from 'next/router';

export default function ProdutoDetalhe() {
  const router = useRouter();
  const { id } = router.query;
  
  // Verifica se a página ainda está carregando
  if (router.isFallback) {
    return <div>Carregando...</div>;
  }
  
  return (
    <div>
      <h1>Produto #{id}</h1>
      <button onClick={() => router.back()}>Voltar</button>
    </div>
  );
}
```

## usePathname, useSearchParams e useParams

No App Router, o Next.js divide as funcionalidades do router em hooks mais específicos:

### usePathname
```jsx
// app/perfil/page.js (App Router)
'use client';
import { usePathname } from 'next/navigation';

export default function Perfil() {
  const pathname = usePathname(); // Retorna '/perfil'
  
  return (
    <div>
      <h1>Página atual: {pathname}</h1>
    </div>
  );
}
```

### useSearchParams
```jsx
// app/pesquisa/page.js (App Router)
'use client';
import { useSearchParams } from 'next/navigation';

export default function Pesquisa() {
  const searchParams = useSearchParams();
  const termo = searchParams.get('q');
  
  return (
    <div>
      <h1>Resultados para: {termo || 'Todos os produtos'}</h1>
    </div>
  );
}
```

### useParams
```jsx
// app/produto/[id]/page.js (App Router)
'use client';
import { useParams } from 'next/navigation';

export default function ProdutoDetalhe() {
  const params = useParams();
  const id = params.id;
  
  return (
    <div>
      <h1>Produto #{id}</h1>
    </div>
  );
}
```

## useSWR

Embora não seja exclusivo do Next.js, o hook `useSWR` é altamente recomendado para data fetching no lado do cliente:

```jsx
import useSWR from 'swr';

// Função para buscar dados
const fetcher = (...args) => fetch(...args).then(res => res.json());

function Perfil() {
  const { data, error, isLoading } = useSWR('/api/usuario', fetcher);

  if (error) return <div>Falha ao carregar</div>;
  if (isLoading) return <div>Carregando...</div>;
  
  return (
    <div>
      <h1>Olá, {data.nome}</h1>
      <p>Email: {data.email}</p>
    </div>
  );
}
```

### Vantagens do SWR:
- Atualização automática dos dados
- Cache e deduplicação automáticos
- Foco na experiência do usuário (revalidação ao focar a janela)
- Recuperação inteligente após desconexão
- Suporte a paginação e rolagem infinita

## useTransition

Com a introdução do React 18 e Server Components, o hook `useTransition` se torna útil para melhorar a responsividade da UI:

```jsx
'use client';
import { useTransition } from 'react';
import { updateUserPreferences } from './actions';

export default function Configuracoes() {
  const [isPending, startTransition] = useTransition();
  
  const handleSave = (data) => {
    // Marca as atualizações como não-urgentes
    startTransition(async () => {
      await updateUserPreferences(data);
    });
  };
  
  return (
    <div>
      <h1>Configurações</h1>
      <form onSubmit={handleSave}>
        {/* Campos do formulário */}
        <button type="submit" disabled={isPending}>
          {isPending ? 'Salvando...' : 'Salvar'}
        </button>
      </form>
    </div>
  );
}
```

## useSelectedLayoutSegment e useSelectedLayoutSegments

Estes hooks permitem acessar o segmento de rota ativo dentro de um layout:

```jsx
'use client';
import { useSelectedLayoutSegment, useSelectedLayoutSegments } from 'next/navigation';

export default function NavBar() {
  // Para app/(marketing)/blog/[...], retorna 'blog'
  const segmentoAtivo = useSelectedLayoutSegment();
  
  // Para app/(marketing)/blog/2023/01, retorna ['blog', '2023', '01']
  const todosSegmentos = useSelectedLayoutSegments();
  
  return (
    <nav>
      <a className={segmentoAtivo === null ? 'ativo' : ''} href="/">
        Home
      </a>
      <a className={segmentoAtivo === 'blog' ? 'ativo' : ''} href="/blog">
        Blog
      </a>
      <a className={segmentoAtivo === 'produtos' ? 'ativo' : ''} href="/produtos">
        Produtos
      </a>
    </nav>
  );
}
```

## useSession (NextAuth.js)

Um hook para gerenciar autenticação com NextAuth.js:

```jsx
import { useSession, signIn, signOut } from "next-auth/react";

export default function Header() {
  const { data: session, status } = useSession();
  const loading = status === "loading";
  
  if (loading) return <div>Carregando...</div>;
  
  if (session) {
    return (
      <div>
        <p>Olá, {session.user.name}</p>
        <button onClick={() => signOut()}>Sair</button>
      </div>
    );
  }
  
  return <button onClick={() => signIn()}>Entrar</button>;
}
```

## useFormStatus e useFormState

Hooks para trabalhar com formulários e Server Actions:

```jsx
'use client';
import { useFormStatus, useFormState } from 'react-dom';
import { enviarFormulario } from './actions';

// Componente para o botão que mostra estado de envio
function SubmitButton() {
  const { pending } = useFormStatus();
  
  return (
    <button type="submit" disabled={pending}>
      {pending ? 'Enviando...' : 'Enviar'}
    </button>
  );
}

// Estado inicial
const estadoInicial = { mensagem: null, sucesso: false };

// Função para validar no cliente e lidar com o estado
function ClientForm() {
  const [state, formAction] = useFormState(enviarFormulario, estadoInicial);
  
  return (
    <form action={formAction}>
      <input type="text" name="nome" required />
      
      {state.mensagem && (
        <p className={state.sucesso ? 'sucesso' : 'erro'}>
          {state.mensagem}
        </p>
      )}
      
      <SubmitButton />
    </form>
  );
}
```

## useMediaQuery (Personalizado)

Um hook personalizado útil para layouts responsivos:

```jsx
import { useState, useEffect } from 'react';

function useMediaQuery(query) {
  const [matches, setMatches] = useState(false);
  
  useEffect(() => {
    const media = window.matchMedia(query);
    
    // Atualiza o estado inicialmente
    setMatches(media.matches);
    
    // Define um listener para mudanças
    const listener = () => setMatches(media.matches);
    media.addEventListener('change', listener);
    
    // Cleanup
    return () => media.removeEventListener('change', listener);
  }, [query]);
  
  return matches;
}

// Uso
function ComponenteResponsivo() {
  const isMobile = useMediaQuery('(max-width: 768px)');
  
  return (
    <div>
      {isMobile ? (
        <MobileLayout />
      ) : (
        <DesktopLayout />
      )}
    </div>
  );
}
```

## usePagination (Personalizado)

Hook para gerenciar paginação:

```jsx
function usePagination(totalItems, itemsPerPage = 10, initialPage = 1) {
  const [currentPage, setCurrentPage] = useState(initialPage);
  
  const totalPages = Math.ceil(totalItems / itemsPerPage);
  
  // Impede página inválida
  useEffect(() => {
    if (currentPage > totalPages) {
      setCurrentPage(totalPages || 1);
    }
  }, [currentPage, totalPages]);
  
  const nextPage = () => {
    if (currentPage < totalPages) {
      setCurrentPage(prevPage => prevPage + 1);
    }
  };
  
  const prevPage = () => {
    if (currentPage > 1) {
      setCurrentPage(prevPage => prevPage - 1);
    }
  };
  
  const goToPage = (page) => {
    const pageNumber = Math.min(Math.max(1, page), totalPages);
    setCurrentPage(pageNumber);
  };
  
  return {
    currentPage,
    totalPages,
    nextPage,
    prevPage, 
    goToPage,
    // Índices para slice
    startIndex: (currentPage - 1) * itemsPerPage,
    endIndex: Math.min(currentPage * itemsPerPage - 1, totalItems - 1)
  };
}
```

## Boas Práticas para Uso de Hooks

1. ⚠️ **Regras dos Hooks**: Siga sempre as regras básicas dos hooks React:
   - Chame hooks apenas no nível superior do componente
   - Chame hooks apenas de componentes React ou hooks personalizados

2. **Memorização**: Use `useMemo` e `useCallback` para otimizar performance quando necessário

3. **Componentização**: Extraia lógica complexa para hooks personalizados reutilizáveis

4. **Renderização Condicional**: Em App Router, lembre-se de adicionar a diretiva 'use client' para componentes que usam hooks

5. **Hidratação**: Tenha cuidado com hooks que dependem do DOM no processo de hidratação (use `useEffect` para inicializações do lado do cliente)

## Recursos Adicionais

- [Documentação oficial do Next.js - useRouter (App)](https://nextjs.org/docs/app/api-reference/functions/use-router)
- [Documentação oficial do Next.js - useRouter (Pages)](https://nextjs.org/docs/pages/api-reference/functions/use-router)
- [Documentação do SWR](https://swr.vercel.app/)
- [React Server Components e Hooks](https://react.dev/reference/react/hooks)
- [NextAuth.js](https://next-auth.js.org/)