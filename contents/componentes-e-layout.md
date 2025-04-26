<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# 🧩 Componentes e Layout no Next.js 🔄

No Next.js, os componentes e layouts são fundamentais para criar interfaces reutilizáveis e manter uma estrutura consistente em toda a aplicação. Este guia explorará como trabalhar com componentes, criar layouts eficientes e aproveitar os recursos específicos do Next.js.

## 📋 Componentes no Next.js

Os componentes no Next.js seguem o modelo do React, mas com algumas funcionalidades adicionais específicas para o framework. No Next.js 13+ com App Router, temos dois tipos principais de componentes:

1. **Server Components** (padrão): Renderizados no servidor
2. **Client Components**: Renderizados no navegador do usuário

### Server Components

Por padrão, todos os componentes no App Router são Server Components:

```jsx
// app/componentes/Produto.jsx
async function buscarDadosProduto(id) {
  const res = await fetch(`https://api.exemplo.com/produtos/${id}`);
  return res.json();
}

export default async function Produto({ id }) {
  const produto = await buscarDadosProduto(id);
  
  return (
    <div className="produto-card">
      <h2>{produto.nome}</h2>
      <p>{produto.descricao}</p>
      <span>R$ {produto.preco.toFixed(2)}</span>
    </div>
  );
}
```

**Vantagens dos Server Components:**
- Menor JavaScript enviado ao cliente
- Acesso direto a recursos do servidor (banco de dados, sistema de arquivos)
- Busca de dados no servidor sem waterfalls
- Código sensível não exposto ao cliente
- Melhor SEO por renderização no servidor

### Client Components

Para componentes que precisam de interatividade, use a diretiva `'use client'` no topo do arquivo:

```jsx
'use client';

// app/componentes/Contador.jsx
import { useState } from 'react';

export default function Contador() {
  const [contador, setContador] = useState(0);
  
  return (
    <div className="contador">
      <p>Valor atual: {contador}</p>
      <button onClick={() => setContador(contador + 1)}>Incrementar</button>
      <button onClick={() => setContador(contador - 1)}>Decrementar</button>
    </div>
  );
}
```

**Quando usar Client Components:**
- Quando precisar de hooks do React (useState, useEffect, etc.)
- Para eventos interativos (onClick, onChange, etc.)
- Para usar efeitos e ciclo de vida
- Para APIs disponíveis apenas no navegador (localStorage, navigator, etc.)

## 🏗️ Padrões de Composição

### Dividindo Client e Server Components

Uma boa prática é manter a maior parte da aplicação como Server Components e usar Client Components apenas onde necessário:

```jsx
// app/produtos/[id]/page.jsx (Server Component)
import DetalheProduto from './DetalheProduto';
import BotaoComprar from './BotaoComprar';
import Avaliacoes from './Avaliacoes';

export default async function PaginaProduto({ params }) {
  const produto = await getProduto(params.id);
  
  return (
    <div className="pagina-produto">
      <DetalheProduto produto={produto} />
      {/* Componente cliente apenas onde precisamos de interatividade */}
      <BotaoComprar id={produto.id} preco={produto.preco} />
      <Avaliacoes produtoId={params.id} />
    </div>
  );
}
```

```jsx
// app/produtos/[id]/BotaoComprar.jsx
'use client';

import { useState } from 'react';

export default function BotaoComprar({ id, preco }) {
  const [adicionando, setAdicionando] = useState(false);
  
  const adicionarAoCarrinho = async () => {
    setAdicionando(true);
    // Lógica para adicionar ao carrinho
    await fetch('/api/carrinho', {
      method: 'POST',
      body: JSON.stringify({ id, quantidade: 1 })
    });
    setAdicionando(false);
  };
  
  return (
    <button 
      onClick={adicionarAoCarrinho} 
      disabled={adicionando}
      className="botao-comprar"
    >
      {adicionando ? 'Adicionando...' : `Comprar - R$ ${preco.toFixed(2)}`}
    </button>
  );
}
```

### Compartilhando Dados entre Componentes

Para compartilhar dados entre componentes, podemos usar:

1. **Props Drilling** (para hierarquias simples)
2. **Context API** (para estados globais em Client Components)
3. **React Query/SWR** (para dados remotos com cache e revalidação)

Exemplo com Context API:

```jsx
'use client';

// app/providers/CarrinhoProvider.jsx
import { createContext, useContext, useState } from 'react';

const CarrinhoContext = createContext({});

export function CarrinhoProvider({ children }) {
  const [itens, setItens] = useState([]);
  
  const adicionarItem = (produto) => {
    setItens(prev => [...prev, produto]);
  };
  
  const removerItem = (id) => {
    setItens(prev => prev.filter(item => item.id !== id));
  };
  
  return (
    <CarrinhoContext.Provider value={{ itens, adicionarItem, removerItem }}>
      {children}
    </CarrinhoContext.Provider>
  );
}

export const useCarrinho = () => useContext(CarrinhoContext);
```

```jsx
// app/layout.jsx
import { CarrinhoProvider } from './providers/CarrinhoProvider';

export default function RootLayout({ children }) {
  return (
    <html lang="pt-BR">
      <body>
        <CarrinhoProvider>
          {children}
        </CarrinhoProvider>
      </body>
    </html>
  );
}
```

## 📐 Layouts no Next.js

Os layouts permitem compartilhar UI entre múltiplas páginas. Com o App Router, os layouts são muito mais poderosos e eficientes.

### Root Layout (obrigatório)

Cada aplicação precisa de um layout raiz em `app/layout.js`:

```jsx
// app/layout.jsx
import './globals.css';
import Header from '@/components/Header';
import Footer from '@/components/Footer';

export const metadata = {
  title: 'Minha Loja Online',
  description: 'Os melhores produtos para você!',
};

export default function RootLayout({ children }) {
  return (
    <html lang="pt-BR">
      <body>
        <Header />
        <main className="container">
          {children}
        </main>
        <Footer />
      </body>
    </html>
  );
}
```

### Layouts Aninhados

Você pode criar layouts específicos para seções da aplicação:

```jsx
// app/dashboard/layout.jsx
import Sidebar from '@/components/dashboard/Sidebar';

export default function DashboardLayout({ children }) {
  return (
    <div className="dashboard-container">
      <Sidebar />
      <div className="dashboard-content">
        {children}
      </div>
    </div>
  );
}
```

### Layouts Paralelos

No App Router, você pode ter múltiplos layouts para a mesma rota usando slots:

```jsx
// app/@sidebar/page.jsx
export default function Sidebar() {
  return <div className="sidebar">Sidebar Content</div>;
}

// app/@main/page.jsx
export default function Main() {
  return <div className="main-content">Main Content</div>;
}

// app/layout.jsx
export default function Layout({ sidebar, main }) {
  return (
    <div className="flex">
      <div className="w-1/4">{sidebar}</div>
      <div className="w-3/4">{main}</div>
    </div>
  );
}
```

## 🔄 Templates vs Layouts

Templates são semelhantes aos layouts, mas reinicializam o estado quando o usuário navega entre rotas:

```jsx
// app/template.jsx
export default function Template({ children }) {
  return (
    <div className="template">
      {/* Este componente é recriado a cada navegação */}
      {children}
    </div>
  );
}
```

**Quando usar Templates:**
- Para efeitos de transição de página
- Para animações de entrada/saída
- Quando quiser resetar o estado entre navegações
- Para comportamentos que dependem de hooks de efeito na montagem

## 📱 Componentes Específicos do Next.js

O Next.js oferece componentes específicos para otimizar a experiência de desenvolvimento:

### Link Component

```jsx
import Link from 'next/link';

export default function Navbar() {
  return (
    <nav>
      <Link href="/">Início</Link>
      <Link href="/produtos">Produtos</Link>
      <Link 
        href="/blog/2023/post-recente" 
        prefetch={false} // Desativa pré-carregamento
      >
        Blog
      </Link>
    </nav>
  );
}
```

### Image Component

```jsx
import Image from 'next/image';

export default function Produto({ produto }) {
  return (
    <div className="produto">
      <Image
        src={produto.imagem}
        alt={produto.nome}
        width={500}
        height={300}
        priority={true} // Carrega com prioridade
        quality={85} // Qualidade da imagem
        placeholder="blur" // Mostra um placeholder durante o carregamento
        blurDataURL="data:image/png;base64,..." // Miniatura para o placeholder
      />
      <h2>{produto.nome}</h2>
    </div>
  );
}
```

### Script Component

```jsx
import Script from 'next/script';

export default function Layout({ children }) {
  return (
    <>
      <Script
        src="https://analytics.example.com/script.js"
        strategy="lazyOnload" // Carrega depois que tudo estiver pronto
        onLoad={() => console.log('Script carregado')}
      />
      {children}
    </>
  );
}
```

## 🎭 Padrões de UI Avançados

### Modais e Overlays

```jsx
'use client';

import { useState } from 'react';
import { createPortal } from 'react-dom';

export function Modal({ isOpen, onClose, children }) {
  if (!isOpen) return null;
  
  return createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={e => e.stopPropagation()}>
        <button className="modal-close" onClick={onClose}>×</button>
        {children}
      </div>
    </div>,
    document.body
  );
}

export function ModalTrigger({ buttonText, children }) {
  const [isOpen, setIsOpen] = useState(false);
  
  return (
    <>
      <button onClick={() => setIsOpen(true)}>{buttonText}</button>
      <Modal isOpen={isOpen} onClose={() => setIsOpen(false)}>
        {children}
      </Modal>
    </>
  );
}
```

### Componentes Suspense

```jsx
// app/page.jsx
import { Suspense } from 'react';
import ProdutosCarregando from '@/components/ProdutosCarregando';
import ListaProdutos from '@/components/ListaProdutos';

export default function HomePage() {
  return (
    <div>
      <h1>Nossa Loja</h1>
      <Suspense fallback={<ProdutosCarregando />}>
        <ListaProdutos />
      </Suspense>
    </div>
  );
}
```

### Error Boundaries

```jsx
'use client';

// app/error.jsx
import { useEffect } from 'react';

export default function Error({ error, reset }) {
  useEffect(() => {
    console.error(error);
  }, [error]);

  return (
    <div className="error-container">
      <h2>Algo deu errado!</h2>
      <p>{error.message || 'Ocorreu um erro na aplicação'}</p>
      <button onClick={() => reset()}>Tentar novamente</button>
    </div>
  );
}
```

## 💅 Estilização de Componentes

O Next.js suporta múltiplas abordagens de estilização:

### CSS Modules

```css
/* components/Button.module.css */
.button {
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
  font-weight: bold;
}

.primary {
  background-color: #0070f3;
  color: white;
}

.secondary {
  background-color: #f5f5f5;
  color: #333;
}
```

```jsx
import styles from './Button.module.css';

export default function Button({ children, variant = 'primary' }) {
  return (
    <button className={`${styles.button} ${styles[variant]}`}>
      {children}
    </button>
  );
}
```

### Tailwind CSS

```jsx
export default function Card({ title, content }) {
  return (
    <div className="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition-shadow">
      <h3 className="text-xl font-bold mb-2 text-gray-800">{title}</h3>
      <p className="text-gray-600">{content}</p>
    </div>
  );
}
```

### CSS-in-JS (com Styled Components)

```jsx
'use client';

import styled from 'styled-components';

const StyledButton = styled.button`
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
  font-weight: bold;
  background-color: ${props => props.primary ? '#0070f3' : '#f5f5f5'};
  color: ${props => props.primary ? 'white' : '#333'};
`;

export default function Button({ children, primary }) {
  return (
    <StyledButton primary={primary}>
      {children}
    </StyledButton>
  );
}
```

## 🧠 Melhores Práticas

1. **Mantenha a maioria como Server Components**: Use Client Components apenas onde necessário
2. **Mova a interatividade para baixo**: Coloque a diretiva `'use client'` no nível mais baixo possível
3. **Prefira composição**: Divida grandes componentes em componentes menores e reutilizáveis
4. **Use layouts para UI compartilhada**: Evite repetir código em múltiplas páginas
5. **Optimize imagens**: Sempre use o componente Image para melhor performance
6. **Coloque lógica de busca de dados em funções separadas**: Melhora a organização e facilita testes
7. **Mantenha a estrutura consistente**: Adote padrões para nomes e organização de componentes

## 🔗 Recursos Adicionais

- [Documentação oficial de Componentes no Next.js](https://nextjs.org/docs/app/building-your-application/rendering/composition-patterns)
- [Documentação de Layouts](https://nextjs.org/docs/app/building-your-application/routing/pages-and-layouts)
- [UI Component Libraries para Next.js](https://nextjs.org/docs/app/building-your-application/styling/css-in-js)

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/> 