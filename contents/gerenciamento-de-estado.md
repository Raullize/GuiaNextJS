# Gerenciamento de Estado no Next.js

O gerenciamento de estado é uma parte crucial no desenvolvimento de aplicações modernas em React e Next.js. Neste guia, exploraremos as melhores abordagens para gerenciar o estado em diferentes níveis de complexidade em suas aplicações Next.js.

## Tipos de Estado em Aplicações Next.js

1. **Estado Local** - Gerenciado pelo hook `useState` para componentes individuais
2. **Estado Global** - Compartilhado entre múltiplos componentes usando Context API ou bibliotecas especializadas
3. **Estado do Servidor** - Dados provenientes de servidores externos ou APIs
4. **Estado da URL** - Informações armazenadas na própria URL
5. **Estado Persistente** - Dados armazenados entre sessões (localStorage, cookies)

## Estado Local com Hooks React

Para estados simples dentro de um componente, o hook `useState` é a solução mais elegante:

```jsx
'use client';
import { useState } from 'react';

export default function Contador() {
  const [contador, setContador] = useState(0);
  
  return (
    <div>
      <p>Você clicou {contador} vezes</p>
      <button onClick={() => setContador(contador + 1)}>
        Incrementar
      </button>
    </div>
  );
}
```

Para estados mais complexos, o hook `useReducer` é recomendado:

```jsx
'use client';
import { useReducer } from 'react';

// Definindo o reducer
function carrinhoReducer(state, action) {
  switch (action.type) {
    case 'ADICIONAR_ITEM':
      return {
        ...state,
        itens: [...state.itens, action.payload],
        total: state.total + action.payload.preco
      };
    case 'REMOVER_ITEM':
      const novoItens = state.itens.filter(item => item.id !== action.payload.id);
      return {
        ...state,
        itens: novoItens,
        total: novoItens.reduce((total, item) => total + item.preco, 0)
      };
    default:
      return state;
  }
}

export default function Carrinho() {
  const [carrinho, dispatch] = useReducer(carrinhoReducer, {
    itens: [],
    total: 0
  });
  
  const adicionarProduto = (produto) => {
    dispatch({ type: 'ADICIONAR_ITEM', payload: produto });
  };
  
  return (
    <div>
      <h1>Carrinho ({carrinho.itens.length} itens)</h1>
      <p>Total: R$ {carrinho.total.toFixed(2)}</p>
      
      <button onClick={() => adicionarProduto({ id: Date.now(), nome: 'Produto', preco: 29.90 })}>
        Adicionar Produto Teste
      </button>
      
      <ul>
        {carrinho.itens.map(item => (
          <li key={item.id}>
            {item.nome} - R$ {item.preco.toFixed(2)}
            <button onClick={() => dispatch({ type: 'REMOVER_ITEM', payload: { id: item.id } })}>
              Remover
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## Estado Global com Context API

A Context API do React é uma solução nativa para compartilhar estado entre componentes:

```jsx
// app/contexts/ThemeContext.js
'use client';
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  const toggleTheme = () => {
    setTheme(theme === 'light' ? 'dark' : 'light');
  };
  
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  return useContext(ThemeContext);
}
```

Implementação no layout principal:

```jsx
// app/layout.js
import { ThemeProvider } from './contexts/ThemeContext';

export default function RootLayout({ children }) {
  return (
    <html lang="pt-BR">
      <body>
        <ThemeProvider>
          {children}
        </ThemeProvider>
      </body>
    </html>
  );
}
```

Uso em componentes:

```jsx
// app/components/ThemeSwitcher.js
'use client';
import { useTheme } from '../contexts/ThemeContext';

export default function ThemeSwitcher() {
  const { theme, toggleTheme } = useTheme();
  
  return (
    <button
      onClick={toggleTheme}
      style={{
        background: theme === 'light' ? '#333' : '#fff',
        color: theme === 'light' ? '#fff' : '#333',
        padding: '8px 16px',
        borderRadius: '4px'
      }}
    >
      Mudar para tema {theme === 'light' ? 'escuro' : 'claro'}
    </button>
  );
}
```

## Gerenciamento de Estado com Zustand

Zustand é uma biblioteca leve e simples para gerenciamento de estado:

```jsx
// app/store/useStore.js
'use client';
import { create } from 'zustand';

export const useStore = create((set) => ({
  // Estado
  contador: 0,
  usuarioLogado: null,
  
  // Ações
  incrementar: () => set((state) => ({ contador: state.contador + 1 })),
  decrementar: () => set((state) => ({ contador: state.contador - 1 })),
  resetar: () => set({ contador: 0 }),
  
  // Ações assíncronas
  logarUsuario: async (credenciais) => {
    try {
      // Simulação de requisição API
      const resposta = await fetch('/api/login', {
        method: 'POST',
        body: JSON.stringify(credenciais)
      });
      
      const usuario = await resposta.json();
      set({ usuarioLogado: usuario });
      return usuario;
    } catch (error) {
      console.error("Erro ao fazer login:", error);
      return null;
    }
  },
  deslogarUsuario: () => set({ usuarioLogado: null })
}));
```

Usando o Zustand em componentes:

```jsx
// app/components/ContadorZustand.jsx
'use client';
import { useStore } from '../store/useStore';

export default function ContadorZustand() {
  const { contador, incrementar, decrementar, resetar } = useStore();
  
  return (
    <div>
      <h2>Contador com Zustand: {contador}</h2>
      <div>
        <button onClick={decrementar}>-</button>
        <button onClick={incrementar}>+</button>
        <button onClick={resetar}>Resetar</button>
      </div>
    </div>
  );
}
```

## Gerenciamento de Estado com Redux Toolkit

Redux Toolkit é uma solução robusta para aplicações com estado complexo:

### Configuração do Store:

```jsx
// app/redux/store.js
'use client';
import { configureStore } from '@reduxjs/toolkit';
import todoReducer from './todoSlice';
import userReducer from './userSlice';

export const store = configureStore({
  reducer: {
    todos: todoReducer,
    user: userReducer
  }
});
```

### Criando um Slice:

```jsx
// app/redux/todoSlice.js
'use client';
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

// Thunk para buscar dados assíncronos
export const fetchTodos = createAsyncThunk(
  'todos/fetchTodos',
  async () => {
    const response = await fetch('/api/todos');
    return await response.json();
  }
);

const todoSlice = createSlice({
  name: 'todos',
  initialState: {
    items: [],
    status: 'idle', // 'idle' | 'loading' | 'succeeded' | 'failed'
    error: null
  },
  reducers: {
    addTodo: (state, action) => {
      state.items.push({
        id: Date.now(),
        text: action.payload,
        completed: false
      });
    },
    toggleTodo: (state, action) => {
      const todo = state.items.find(todo => todo.id === action.payload);
      if (todo) {
        todo.completed = !todo.completed;
      }
    },
    removeTodo: (state, action) => {
      state.items = state.items.filter(todo => todo.id !== action.payload);
    }
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchTodos.pending, (state) => {
        state.status = 'loading';
      })
      .addCase(fetchTodos.fulfilled, (state, action) => {
        state.status = 'succeeded';
        state.items = action.payload;
      })
      .addCase(fetchTodos.rejected, (state, action) => {
        state.status = 'failed';
        state.error = action.error.message;
      });
  }
});

export const { addTodo, toggleTodo, removeTodo } = todoSlice.actions;
export default todoSlice.reducer;
```

### Provider no Layout:

```jsx
// app/redux/ReduxProvider.jsx
'use client';
import { Provider } from 'react-redux';
import { store } from './store';

export default function ReduxProvider({ children }) {
  return (
    <Provider store={store}>
      {children}
    </Provider>
  );
}

// app/layout.js
import ReduxProvider from './redux/ReduxProvider';

export default function RootLayout({ children }) {
  return (
    <html lang="pt-BR">
      <body>
        <ReduxProvider>
          {children}
        </ReduxProvider>
      </body>
    </html>
  );
}
```

### Uso nos Componentes:

```jsx
// app/components/TodoList.jsx
'use client';
import { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { fetchTodos, addTodo, toggleTodo, removeTodo } from '../redux/todoSlice';

export default function TodoList() {
  const dispatch = useDispatch();
  const { items, status, error } = useSelector(state => state.todos);
  
  useEffect(() => {
    if (status === 'idle') {
      dispatch(fetchTodos());
    }
  }, [dispatch, status]);
  
  const handleAddTodo = (e) => {
    e.preventDefault();
    const text = e.target.todoText.value.trim();
    if (text) {
      dispatch(addTodo(text));
      e.target.todoText.value = '';
    }
  };
  
  if (status === 'loading') {
    return <div>Carregando tarefas...</div>;
  }
  
  if (status === 'failed') {
    return <div>Erro ao carregar tarefas: {error}</div>;
  }
  
  return (
    <div>
      <h2>Lista de Tarefas</h2>
      
      <form onSubmit={handleAddTodo}>
        <input 
          type="text"
          name="todoText"
          placeholder="Nova tarefa..."
        />
        <button type="submit">Adicionar</button>
      </form>
      
      <ul>
        {items.map(todo => (
          <li key={todo.id} style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
            <span onClick={() => dispatch(toggleTodo(todo.id))}>
              {todo.text}
            </span>
            <button onClick={() => dispatch(removeTodo(todo.id))}>
              X
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## Estado do Servidor com React Query / TanStack Query

Para gerenciar o estado de dados do servidor, o TanStack Query (anteriormente React Query) é uma excelente opção:

### Configuração:

```jsx
// app/tanstack/TanstackProvider.jsx
'use client';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
import { useState } from 'react';

export default function TanstackProvider({ children }) {
  const [queryClient] = useState(() => new QueryClient());
  
  return (
    <QueryClientProvider client={queryClient}>
      {children}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  );
}

// app/layout.js
import TanstackProvider from './tanstack/TanstackProvider';

export default function RootLayout({ children }) {
  return (
    <html lang="pt-BR">
      <body>
        <TanstackProvider>
          {children}
        </TanstackProvider>
      </body>
    </html>
  );
}
```

### Hooks para API:

```jsx
// app/hooks/usePosts.js
'use client';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// Funções para comunicação com a API
const fetchPosts = async () => {
  const res = await fetch('/api/posts');
  if (!res.ok) throw new Error('Erro ao buscar posts');
  return res.json();
};

const fetchPostById = async (id) => {
  const res = await fetch(`/api/posts/${id}`);
  if (!res.ok) throw new Error('Erro ao buscar post');
  return res.json();
};

const createPost = async (newPost) => {
  const res = await fetch('/api/posts', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(newPost)
  });
  if (!res.ok) throw new Error('Erro ao criar post');
  return res.json();
};

// Hooks para uso nos componentes
export function usePosts() {
  return useQuery({ 
    queryKey: ['posts'], 
    queryFn: fetchPosts
  });
}

export function usePost(postId) {
  return useQuery({ 
    queryKey: ['posts', postId], 
    queryFn: () => fetchPostById(postId),
    enabled: !!postId // Só executa se postId existir
  });
}

export function useCreatePost() {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: createPost,
    onSuccess: () => {
      // Invalida a query para forçar refresh dos dados
      queryClient.invalidateQueries({ queryKey: ['posts'] });
    }
  });
}
```

### Uso em Componentes:

```jsx
// app/components/PostsList.jsx
'use client';
import { useState } from 'react';
import { usePosts, useCreatePost } from '../hooks/usePosts';

export default function PostsList() {
  const [title, setTitle] = useState('');
  const [content, setContent] = useState('');
  
  const { data: posts, isLoading, error } = usePosts();
  const createPostMutation = useCreatePost();
  
  const handleSubmit = (e) => {
    e.preventDefault();
    createPostMutation.mutate({ title, content });
    setTitle('');
    setContent('');
  };
  
  if (isLoading) return <div>Carregando posts...</div>;
  if (error) return <div>Erro: {error.message}</div>;
  
  return (
    <div>
      <h2>Lista de Posts</h2>
      
      <form onSubmit={handleSubmit}>
        <div>
          <input
            type="text"
            placeholder="Título"
            value={title}
            onChange={(e) => setTitle(e.target.value)}
            required
          />
        </div>
        <div>
          <textarea
            placeholder="Conteúdo"
            value={content}
            onChange={(e) => setContent(e.target.value)}
            required
          />
        </div>
        <button 
          type="submit" 
          disabled={createPostMutation.isPending}
        >
          {createPostMutation.isPending ? 'Enviando...' : 'Criar Post'}
        </button>
      </form>
      
      {createPostMutation.isError && (
        <p>Erro ao criar post: {createPostMutation.error.message}</p>
      )}
      
      <div>
        {posts?.map(post => (
          <div key={post.id} style={{ margin: '20px 0', padding: '10px', border: '1px solid #ccc' }}>
            <h3>{post.title}</h3>
            <p>{post.content}</p>
          </div>
        ))}
      </div>
    </div>
  );
}
```

## Estado na URL com useSearchParams

Para estado persistente na URL:

```jsx
'use client';
import { useRouter, useSearchParams } from 'next/navigation';
import { useMemo } from 'react';

export default function FiltroProdutos() {
  const router = useRouter();
  const searchParams = useSearchParams();
  
  // Recupera valores dos parâmetros
  const categoria = searchParams.get('categoria') || '';
  const ordenacao = searchParams.get('ordenacao') || 'recentes';
  const pagina = Number(searchParams.get('pagina') || '1');
  
  // Função para atualizar os parâmetros da URL
  const atualizarFiltros = (novosFiltros) => {
    // Criar nova instância de URLSearchParams
    const params = new URLSearchParams(searchParams.toString());
    
    // Atualizar com novos valores
    Object.entries(novosFiltros).forEach(([chave, valor]) => {
      if (valor) {
        params.set(chave, valor);
      } else {
        params.delete(chave);
      }
    });
    
    // Atualizar URL sem recarregar a página
    router.push(`?${params.toString()}`);
  };
  
  return (
    <div>
      <h2>Filtros de Produtos</h2>
      
      <div>
        <label>
          Categoria:
          <select 
            value={categoria} 
            onChange={(e) => atualizarFiltros({ categoria: e.target.value })}
          >
            <option value="">Todas</option>
            <option value="eletronicos">Eletrônicos</option>
            <option value="roupas">Roupas</option>
            <option value="livros">Livros</option>
          </select>
        </label>
      </div>
      
      <div>
        <label>
          Ordenar por:
          <select 
            value={ordenacao} 
            onChange={(e) => atualizarFiltros({ ordenacao: e.target.value })}
          >
            <option value="recentes">Mais recentes</option>
            <option value="preco_asc">Menor preço</option>
            <option value="preco_desc">Maior preço</option>
          </select>
        </label>
      </div>
      
      {/* Paginação */}
      <div>
        <button 
          disabled={pagina <= 1}
          onClick={() => atualizarFiltros({ pagina: Math.max(1, pagina - 1) })}
        >
          Anterior
        </button>
        <span>Página {pagina}</span>
        <button onClick={() => atualizarFiltros({ pagina: pagina + 1 })}>
          Próxima
        </button>
      </div>
      
      {/* Reseta todos os filtros */}
      <button onClick={() => router.push('?')}>
        Limpar Filtros
      </button>
    </div>
  );
}
```

## Estado Persistente com Cookies e Local Storage

Para armazenar estado entre sessões:

```jsx
// app/hooks/useLocalStorage.js
'use client';
import { useState, useEffect } from 'react';

export function useLocalStorage(chave, valorInicial) {
  // Estado para armazenar o valor
  const [valor, setValor] = useState(() => {
    // Inicializar apenas no lado do cliente
    if (typeof window === 'undefined') {
      return valorInicial;
    }
    
    try {
      // Recuperar do localStorage
      const item = window.localStorage.getItem(chave);
      return item ? JSON.parse(item) : valorInicial;
    } catch (error) {
      console.error(`Erro ao ler localStorage (${chave}):`, error);
      return valorInicial;
    }
  });
  
  // Atualizar localStorage quando o valor mudar
  useEffect(() => {
    if (typeof window === 'undefined') {
      return;
    }
    
    try {
      window.localStorage.setItem(chave, JSON.stringify(valor));
    } catch (error) {
      console.error(`Erro ao escrever no localStorage (${chave}):`, error);
    }
  }, [chave, valor]);
  
  return [valor, setValor];
}

// Uso do hook
function ConfiguracaoUsuario() {
  const [configuracoes, setConfiguracoes] = useLocalStorage('user_config', {
    tema: 'light',
    notificacoes: true,
    tamanhoFonte: 'medio'
  });
  
  const atualizarConfiguracao = (chave, valor) => {
    setConfiguracoes(prev => ({
      ...prev,
      [chave]: valor
    }));
  };
  
  return (
    <div>
      <h2>Configurações do Usuário</h2>
      
      <div>
        <label>
          Tema:
          <select
            value={configuracoes.tema}
            onChange={(e) => atualizarConfiguracao('tema', e.target.value)}
          >
            <option value="light">Claro</option>
            <option value="dark">Escuro</option>
            <option value="sistema">Sistema</option>
          </select>
        </label>
      </div>
      
      <div>
        <label>
          <input
            type="checkbox"
            checked={configuracoes.notificacoes}
            onChange={(e) => atualizarConfiguracao('notificacoes', e.target.checked)}
          />
          Ativar notificações
        </label>
      </div>
      
      <div>
        <label>
          Tamanho da fonte:
          <select
            value={configuracoes.tamanhoFonte}
            onChange={(e) => atualizarConfiguracao('tamanhoFonte', e.target.value)}
          >
            <option value="pequeno">Pequeno</option>
            <option value="medio">Médio</option>
            <option value="grande">Grande</option>
          </select>
        </label>
      </div>
    </div>
  );
}
```

### Gerenciando Cookies:

```jsx
// app/hooks/useCookies.js
'use client';
import { useState, useEffect } from 'react';
import Cookies from 'js-cookie';

export function useCookies(chave, valorInicial, opcoes = {}) {
  const [valor, setValor] = useState(() => {
    // Inicializar apenas no lado do cliente
    if (typeof window === 'undefined') {
      return valorInicial;
    }
    
    try {
      const cookieValor = Cookies.get(chave);
      return cookieValor ? JSON.parse(cookieValor) : valorInicial;
    } catch (error) {
      console.error(`Erro ao ler cookie (${chave}):`, error);
      return valorInicial;
    }
  });
  
  // Atualizar cookie quando o valor mudar
  useEffect(() => {
    if (typeof window === 'undefined') {
      return;
    }
    
    try {
      Cookies.set(chave, JSON.stringify(valor), opcoes);
    } catch (error) {
      console.error(`Erro ao escrever cookie (${chave}):`, error);
    }
  }, [chave, valor, opcoes]);
  
  return [valor, setValor];
}

// Uso para gerenciar preferências de consentimento de cookies
function BannerCookies() {
  const [consentimento, setConsentimento] = useCookies('cookie_consent', null, {
    expires: 365, // válido por 1 ano
    path: '/'
  });
  
  if (consentimento !== null) {
    return null; // Não mostra o banner se já tiver decisão
  }
  
  return (
    <div className="cookie-banner">
      <p>
        Este site usa cookies para melhorar sua experiência. 
        Ao continuar navegando, você concorda com nossa política de cookies.
      </p>
      <div>
        <button onClick={() => setConsentimento(true)}>
          Aceitar
        </button>
        <button onClick={() => setConsentimento(false)}>
          Rejeitar
        </button>
      </div>
    </div>
  );
}
```

## Escolhendo a Solução Certa

| Solução | Quando Usar | Complexidade | Vantagens |
|---------|-------------|--------------|-----------|
| useState/useReducer | Estado local simples | Baixa | Simplicidade, integração nativa |
| Context API | Estado global pequeno/médio | Média | Nativo do React, fácil setup |
| Zustand | Estado global médio | Média | Leve, simples, fácil de testar |
| Redux Toolkit | Estado complexo, grande | Alta | Poderoso, DevTools, middleware |
| TanStack Query | Estado de servidor | Média | Cache, invalidação automática |
| URL State | Filtros, paginação | Baixa | Compartilhável, preservado no histórico |
| localStorage/Cookies | Preferências do usuário | Baixa | Persistência entre sessões |

## Boas Práticas

1. **Combine Abordagens**: Use diferentes soluções para diferentes tipos de estado
   ```
   useState → Estado de UI local
   Context API → Temas, autenticação
   Zustand/Redux → Estado global complexo
   TanStack Query → Dados do servidor
   URL → Filtros e parâmetros de busca
   localStorage → Preferências persistentes
   ```

2. **Evite Prop Drilling**: Quando passar props por muitos níveis, considere Context API ou outra solução global

3. **Estado Servidor vs. Cliente**: Com App Router, aproveite a separação de componentes de servidor e cliente para reduzir o JavaScript enviado ao navegador

4. **Hidratação**: Cuidado com valores que dependem do navegador (ex: window) - use useEffect para lidar com hidratação

5. **Manutenção de Estado entre Navegações**: Use soluções como React Query para manter o estado da aplicação entre navegações

6. **Performance**: Evite re-renderizações desnecessárias usando memo, useMemo e useCallback quando apropriado

## Recursos Adicionais

- [Documentação do React sobre Gerenciamento de Estado](https://react.dev/learn/managing-state)
- [Zustand - Documentação Oficial](https://github.com/pmndrs/zustand)
- [Redux Toolkit - Documentação](https://redux-toolkit.js.org/)
- [TanStack Query - Documentação](https://tanstack.com/query/latest)
- [Next.js - Dados e Renderização](https://nextjs.org/docs/app/building-your-application/data-fetching)
- [Jotai - Gerenciamento de Estado Atômico](https://jotai.org/)