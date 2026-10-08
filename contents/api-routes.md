<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# API Routes no Next.js

Uma das funcionalidades mais poderosas do Next.js é a capacidade de criar APIs dentro do mesmo projeto, eliminando a necessidade de um servidor separado. As API Routes permitem criar endpoints que funcionam como qualquer API RESTful, facilitando a integração entre frontend e backend em uma única base de código.

## Limitações de SPAs com Backend Separado

As **Single Page Applications (SPAs)** tradicionais geralmente requerem um backend separado, o que cria diversos desafios:

### Problemas Comuns:

1. **Complexidade de Configuração**:
   - **Dois projetos separados**: Frontend e backend em repositórios diferentes
   - **Configuração de CORS**: Necessário para comunicação entre domínios
   - **Deploy duplo**: Duas aplicações para gerenciar e hospedar
   - **Sincronização de versões**: Compatibilidade entre frontend e backend

2. **Problemas de Comunicação**:
   - **Latência de rede**: Requisições HTTP entre serviços separados
   - **Tratamento de erros complexo**: Falhas de rede e timeouts
   - **Autenticação distribuída**: Tokens e sessões entre serviços
   - **Debugging dificultado**: Rastreamento de bugs entre sistemas

3. **Exposição de APIs**:
   - **Endpoints públicos**: APIs expostas na internet
   - **Segurança adicional**: Rate limiting, autenticação, autorização
   - **Documentação necessária**: APIs precisam ser documentadas
   - **Versionamento complexo**: Manter compatibilidade entre versões

4. **Overhead de Desenvolvimento**:
   - **Tecnologias diferentes**: Linguagens e frameworks distintos
   - **Equipes separadas**: Frontend e backend podem ter equipes diferentes
   - **Testes integrados**: Necessidade de testar comunicação entre serviços
   - **Monitoramento duplo**: Logs e métricas de dois sistemas

## ✅ Vantagens das API Routes do Next.js

O Next.js resolve esses problemas com **API Routes integradas** que oferecem:

### Benefícios Principais:

1. **Arquitetura Unificada**:
   - **Monorepo**: Frontend e backend no mesmo projeto
   - **Tecnologia única**: JavaScript/TypeScript para tudo
   - **Deploy único**: Uma aplicação para gerenciar
   - **Versionamento sincronizado**: Frontend e backend sempre compatíveis

2. **Performance Otimizada**:
   - **Sem latência de rede**: Comunicação interna do servidor
   - **Compartilhamento de recursos**: Cache, conexões de banco, etc.
   - **Otimizações automáticas**: Next.js otimiza a comunicação interna
   - **Edge computing**: APIs podem rodar em edge locations

3. **Segurança Aprimorada**:
   - **APIs privadas**: Endpoints não expostos publicamente
   - **Proteção automática**: CSRF e outras proteções built-in
   - **Variáveis de ambiente**: Acesso seguro a secrets no servidor
   - **Autenticação integrada**: Sessões compartilhadas entre frontend e API

4. **Desenvolvimento Simplificado**:
   - **Hot reload**: Mudanças em APIs refletem instantaneamente
   - **Debugging unificado**: Logs e erros em um só lugar
   - **Tipagem compartilhada**: TypeScript entre frontend e backend
   - **Testes integrados**: Testar fluxos completos facilmente

5. **Deploy e Hospedagem**:
   - **Serverless por padrão**: APIs escalam automaticamente
   - **Edge functions**: Execução próxima aos usuários
   - **Zero configuração**: Deploy automático em plataformas como Vercel
   - **Monitoramento integrado**: Métricas unificadas

### Resultado:
**As API Routes do Next.js transformam o desenvolvimento full-stack em uma experiência unificada e otimizada**, eliminando a complexidade de gerenciar sistemas separados enquanto oferece performance e segurança superiores.

## Introdução às API Routes

O Next.js oferece dois sistemas para criar APIs:

1. **Pages Router API Routes**: o método tradicional, na pasta `/pages/api`
2. **App Router Route Handlers**: o método mais recente, na pasta `/app/api` (desde Next.js 13)

Ambos os sistemas permitem:
- Criar endpoints RESTful (GET, POST, PUT, DELETE, etc.)
- Acessar banco de dados diretamente
- Integrar com serviços de terceiros
- Manipular autenticação e autorização
- Proteger dados sensíveis (chaves de API, senhas, etc.)

## Pages Router API Routes

### Criação Básica

No sistema Pages Router, cada arquivo dentro da pasta `/pages/api` torna-se automaticamente um endpoint:

```jsx
// pages/api/hello.js
export default function handler(req, res) {
  res.status(200).json({ mensagem: 'Olá, mundo!' });
}
```

Este código cria um endpoint em `/api/hello` que responde com um JSON.

### Acessando Métodos HTTP

Você pode tratar diferentes métodos HTTP em um único endpoint:

```jsx
// pages/api/usuarios.js
export default function handler(req, res) {
  const { method } = req;

  switch (method) {
    case 'GET':
      // Buscar usuários
      res.status(200).json({ usuarios: ['João', 'Maria', 'Pedro'] });
      break;
    case 'POST':
      // Criar um novo usuário
      const { nome } = req.body;
      res.status(201).json({ mensagem: `Usuário ${nome} criado com sucesso!` });
      break;
    default:
      res.setHeader('Allow', ['GET', 'POST']);
      res.status(405).end(`Método ${method} não permitido`);
  }
}
```

### Parâmetros Dinâmicos

Assim como nas páginas, você pode usar rotas dinâmicas para APIs:

```jsx
// pages/api/produtos/[id].js
export default function handler(req, res) {
  const { id } = req.query;
  const { method } = req;

  switch (method) {
    case 'GET':
      // Buscar detalhes do produto com o ID específico
      res.status(200).json({ id, nome: `Produto ${id}`, preco: 99.99 });
      break;
    case 'PUT':
      // Atualizar produto
      res.status(200).json({ mensagem: `Produto ${id} atualizado` });
      break;
    case 'DELETE':
      // Excluir produto
      res.status(200).json({ mensagem: `Produto ${id} excluído` });
      break;
    default:
      res.setHeader('Allow', ['GET', 'PUT', 'DELETE']);
      res.status(405).end(`Método ${method} não permitido`);
  }
}
```

### Middleware para API Routes

Você pode criar middleware para processar a requisição antes de chegar ao handler principal:

```jsx
// middleware/logger.js
export function logger(handler) {
  return async (req, res) => {
    console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
    return handler(req, res);
  };
}

// pages/api/com-logger.js
import { logger } from '../../middleware/logger';

function handler(req, res) {
  res.status(200).json({ mensagem: 'API com logger' });
}

export default logger(handler);
```

## App Router Route Handlers

Com o App Router (Next.js 13+), as APIs são implementadas como Route Handlers:

### Criação Básica

```jsx
// app/api/hello/route.js
export async function GET() {
  return Response.json({ mensagem: 'Olá, mundo!' });
}
```

### Múltiplos Métodos HTTP

Cada método HTTP é definido como uma função separada:

```jsx
// app/api/usuarios/route.js
export async function GET() {
  const usuarios = ['João', 'Maria', 'Pedro'];
  return Response.json({ usuarios });
}

export async function POST(request) {
  const data = await request.json();
  // Lógica para criar um usuário
  return Response.json({ mensagem: `Usuário ${data.nome} criado!` }, { status: 201 });
}
```

### Parâmetros Dinâmicos

Usando pastas com nomes dinâmicos:

```jsx
// app/api/produtos/[id]/route.js
export async function GET(request, { params }) {
  const { id } = params;
  
  // Buscar produto do banco de dados
  const produto = {
    id,
    nome: `Produto ${id}`,
    preco: 99.99
  };
  
  return Response.json(produto);
}

export async function PUT(request, { params }) {
  const { id } = params;
  const data = await request.json();
  
  // Atualizar produto
  return Response.json({ mensagem: `Produto ${id} atualizado!` });
}

export async function DELETE(request, { params }) {
  const { id } = params;
  
  // Excluir produto
  return Response.json({ mensagem: `Produto ${id} excluído!` });
}
```

### Manipulando Cookies e Headers

```jsx
// app/api/login/route.js
import { cookies } from 'next/headers';

export async function POST(request) {
  const { usuario, senha } = await request.json();
  
  // Lógica de autenticação...
  const autenticado = verificarCredenciais(usuario, senha);
  
  if (autenticado) {
    // Criar cookie seguro de sessão
    cookies().set({
      name: 'session',
      value: 'token123',
      httpOnly: true,
      secure: process.env.NODE_ENV === 'production',
      maxAge: 60 * 60 * 24 * 7, // 1 semana
      path: '/',
    });
    
    return Response.json({ sucesso: true });
  }
  
  return Response.json(
    { sucesso: false, mensagem: 'Credenciais inválidas' },
    { status: 401 }
  );
}
```

### Redirecionamentos

```jsx
// app/api/redirecionamento/route.js
import { redirect } from 'next/navigation';

export async function GET(request) {
  redirect('/destino');
}
```

## Autenticação e Autorização

### JWT (JSON Web Tokens)

```jsx
// lib/jwt.js
import jwt from 'jsonwebtoken';

const JWT_SECRET = process.env.JWT_SECRET;

export function criarToken(payload) {
  return jwt.sign(payload, JWT_SECRET, { expiresIn: '7d' });
}

export function verificarToken(token) {
  try {
    return jwt.verify(token, JWT_SECRET);
  } catch (error) {
    return null;
  }
}

// app/api/auth/login/route.js
import { criarToken } from '@/lib/jwt';

export async function POST(request) {
  const { email, senha } = await request.json();
  
  // Verificar no banco de dados
  const usuario = await db.usuarios.findOne({ email });
  
  if (!usuario || !verificarSenha(senha, usuario.senha)) {
    return Response.json(
      { sucesso: false, mensagem: 'Email ou senha incorretos' },
      { status: 401 }
    );
  }
  
  // Criar token JWT
  const token = criarToken({ id: usuario.id, email: usuario.email });
  
  return Response.json({ sucesso: true, token });
}
```

### Middleware para Proteção de Rotas

```jsx
// middleware.js
import { NextResponse } from 'next/server';
import { verificarToken } from './lib/jwt';

export function middleware(request) {
  // Verificar se é uma rota de API protegida
  if (request.nextUrl.pathname.startsWith('/api/protegido')) {
    const token = request.headers.get('authorization')?.replace('Bearer ', '');
    
    if (!token) {
      return NextResponse.json(
        { error: 'Token não fornecido' },
        { status: 401 }
      );
    }
    
    const payload = verificarToken(token);
    
    if (!payload) {
      return NextResponse.json(
        { error: 'Token inválido ou expirado' },
        { status: 401 }
      );
    }
    
    // Adicionando o payload do token à requisição para uso posterior
    const requestHeaders = new Headers(request.headers);
    requestHeaders.set('x-usuario-id', payload.id);
    
    return NextResponse.next({
      request: {
        headers: requestHeaders,
      },
    });
  }
  
  return NextResponse.next();
}

export const config = {
  matcher: '/api/protegido/:path*',
};
```

## Integração com Banco de Dados

### MongoDB com Mongoose

```jsx
// lib/db.js
import mongoose from 'mongoose';

const MONGODB_URI = process.env.MONGODB_URI;

let cached = global.mongoose;

if (!cached) {
  cached = global.mongoose = { conn: null, promise: null };
}

export async function conectarDB() {
  if (cached.conn) {
    return cached.conn;
  }

  if (!cached.promise) {
    const opts = {
      bufferCommands: false,
    };

    cached.promise = mongoose.connect(MONGODB_URI, opts).then((mongoose) => {
      return mongoose;
    });
  }
  
  cached.conn = await cached.promise;
  return cached.conn;
}

// models/Produto.js
import mongoose from 'mongoose';

const ProdutoSchema = new mongoose.Schema({
  nome: { type: String, required: true },
  preco: { type: Number, required: true },
  descricao: String,
  estoque: { type: Number, default: 0 },
  dataCriacao: { type: Date, default: Date.now },
});

export default mongoose.models.Produto || mongoose.model('Produto', ProdutoSchema);

// app/api/produtos/route.js
import { conectarDB } from '@/lib/db';
import Produto from '@/models/Produto';

export async function GET(request) {
  await conectarDB();
  
  const produtos = await Produto.find({});
  
  return Response.json(produtos);
}

export async function POST(request) {
  await conectarDB();
  
  const data = await request.json();
  const produto = await Produto.create(data);
  
  return Response.json(produto, { status: 201 });
}
```

### PostgreSQL com Prisma

```jsx
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Usuario {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  nome      String
  senha     String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

// lib/prisma.js
import { PrismaClient } from '@prisma/client';

const prisma = global.prisma || new PrismaClient();

if (process.env.NODE_ENV === 'development') global.prisma = prisma;

export default prisma;

// app/api/usuarios/route.js
import prisma from '@/lib/prisma';
import bcrypt from 'bcrypt';

export async function GET() {
  const usuarios = await prisma.usuario.findMany({
    select: {
      id: true,
      email: true,
      nome: true,
      createdAt: true,
    },
  });
  
  return Response.json(usuarios);
}

export async function POST(request) {
  const data = await request.json();
  
  // Hash da senha
  const senhaHash = await bcrypt.hash(data.senha, 10);
  
  try {
    const usuario = await prisma.usuario.create({
      data: {
        ...data,
        senha: senhaHash,
      },
      select: {
        id: true,
        email: true,
        nome: true,
      },
    });
    
    return Response.json(usuario, { status: 201 });
  } catch (error) {
    if (error.code === 'P2002') {
      return Response.json(
        { erro: 'Email já cadastrado' },
        { status: 400 }
      );
    }
    
    return Response.json(
      { erro: 'Erro ao criar usuário' },
      { status: 500 }
    );
  }
}
```

## Chamando API Routes do Cliente

### Com fetch

```jsx
'use client';

import { useState } from 'react';

export default function FormularioUsuario() {
  const [nome, setNome] = useState('');
  const [email, setEmail] = useState('');
  const [senha, setSenha] = useState('');
  const [mensagem, setMensagem] = useState('');
  
  async function handleSubmit(e) {
    e.preventDefault();
    
    try {
      const res = await fetch('/api/usuarios', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ nome, email, senha }),
      });
      
      const data = await res.json();
      
      if (!res.ok) {
        throw new Error(data.erro || 'Ocorreu um erro');
      }
      
      setMensagem('Usuário criado com sucesso!');
      setNome('');
      setEmail('');
      setSenha('');
    } catch (error) {
      setMensagem(`Erro: ${error.message}`);
    }
  }
  
  return (
    <form onSubmit={handleSubmit}>
      {/* Campos do formulário */}
      <div>{mensagem}</div>
      <button type="submit">Cadastrar</button>
    </form>
  );
}
```

### Com SWR

```jsx
'use client';

import useSWR, { mutate } from 'swr';

const fetcher = (...args) => fetch(...args).then(res => res.json());

export default function ListaUsuarios() {
  const { data, error, isLoading } = useSWR('/api/usuarios', fetcher);
  
  async function excluirUsuario(id) {
    try {
      await fetch(`/api/usuarios/${id}`, {
        method: 'DELETE',
      });
      
      // Revalidar os dados
      mutate('/api/usuarios');
    } catch (error) {
      console.error('Erro ao excluir:', error);
    }
  }
  
  if (isLoading) return <div>Carregando...</div>;
  if (error) return <div>Erro ao carregar usuários</div>;
  
  return (
    <ul>
      {data.map(usuario => (
        <li key={usuario.id}>
          {usuario.nome} ({usuario.email})
          <button onClick={() => excluirUsuario(usuario.id)}>Excluir</button>
        </li>
      ))}
    </ul>
  );
}
```

## Testes de API Routes

### Com Jest e Supertest

```jsx
// __tests__/api/produtos.test.js
import { createMocks } from 'node-mocks-http';
import produtosHandler from '@/pages/api/produtos';

describe('API de Produtos', () => {
  test('GET retorna lista de produtos', async () => {
    const { req, res } = createMocks({
      method: 'GET',
    });
    
    await produtosHandler(req, res);
    
    expect(res._getStatusCode()).toBe(200);
    expect(JSON.parse(res._getData())).toEqual(
      expect.objectContaining({
        produtos: expect.any(Array),
      })
    );
  });
  
  test('POST cria um novo produto', async () => {
    const { req, res } = createMocks({
      method: 'POST',
      body: {
        nome: 'Produto Teste',
        preco: 99.99,
      },
    });
    
    await produtosHandler(req, res);
    
    expect(res._getStatusCode()).toBe(201);
    expect(JSON.parse(res._getData())).toEqual(
      expect.objectContaining({
        id: expect.any(String),
        nome: 'Produto Teste',
      })
    );
  });
});
```

## Integração com Serviços Externos

### Envio de E-mails

```jsx
// app/api/contato/route.js
import nodemailer from 'nodemailer';

export async function POST(request) {
  const { nome, email, mensagem } = await request.json();
  
  // Configurar o transporter
  const transporter = nodemailer.createTransport({
    host: process.env.EMAIL_HOST,
    port: process.env.EMAIL_PORT,
    secure: true,
    auth: {
      user: process.env.EMAIL_USER,
      pass: process.env.EMAIL_PASS,
    },
  });
  
  try {
    await transporter.sendMail({
      from: `"Site" <${process.env.EMAIL_FROM}>`,
      to: process.env.EMAIL_TO,
      subject: `Nova mensagem de contato - ${nome}`,
      text: mensagem,
      html: `
        <p><strong>Nome:</strong> ${nome}</p>
        <p><strong>Email:</strong> ${email}</p>
        <p><strong>Mensagem:</strong></p>
        <p>${mensagem}</p>
      `,
    });
    
    return Response.json({ sucesso: true });
  } catch (error) {
    console.error('Erro ao enviar email:', error);
    return Response.json(
      { sucesso: false, mensagem: 'Erro ao enviar mensagem' },
      { status: 500 }
    );
  }
}
```

### Integração com Gateway de Pagamento

```jsx
// app/api/pagamento/route.js
import Stripe from 'stripe';

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY);

export async function POST(request) {
  const { itens, emailCliente } = await request.json();
  
  try {
    // Criar uma checkout session
    const session = await stripe.checkout.sessions.create({
      payment_method_types: ['card'],
      line_items: itens.map(item => ({
        price_data: {
          currency: 'brl',
          product_data: {
            name: item.nome,
            images: [item.imagem],
          },
          unit_amount: Math.round(item.preco * 100), // Em centavos
        },
        quantity: item.quantidade,
      })),
      mode: 'payment',
      success_url: `${process.env.NEXT_PUBLIC_URL}/sucesso?session_id={CHECKOUT_SESSION_ID}`,
      cancel_url: `${process.env.NEXT_PUBLIC_URL}/carrinho`,
      customer_email: emailCliente,
    });
    
    return Response.json({ sessionId: session.id, url: session.url });
  } catch (error) {
    console.error('Erro no pagamento:', error);
    return Response.json(
      { erro: 'Erro ao processar pagamento' },
      { status: 500 }
    );
  }
}
```

## Melhores Práticas

1. **Separação de Preocupações**: Mantenha a lógica de negócio separada dos handlers
2. **Validação de Dados**: Valide sempre os dados recebidos nas requisições
3. **Tratamento de Erros**: Implemente tratamento apropriado para diferentes tipos de erro
4. **Caching**: Utilize estratégias de cache para melhorar a performance
5. **Segurança**: Proteja suas rotas com autenticação e autorização quando necessário
6. **Rate Limiting**: Implemente limites de requisição para prevenir abusos
7. **CORS**: Configure corretamente para permitir apenas origens confiáveis
8. **Logs**: Mantenha logs para depuração e monitoramento

## Recursos Adicionais

- [Documentação oficial de API Routes](https://nextjs.org/docs/pages/building-your-application/routing/api-routes)
- [Documentação de Route Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers)
- [Exemplos de API Routes no repositório Next.js](https://github.com/vercel/next.js/tree/canary/examples/api-routes)

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/>