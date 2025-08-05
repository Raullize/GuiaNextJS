<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# 🧪 Testes em Next.js 🔍

Os testes são uma parte fundamental do desenvolvimento de software de qualidade. Ao testar sua aplicação Next.js, você garante que novos recursos não quebrem funcionalidades existentes e que seu código cumpra os requisitos esperados. Neste guia, vamos explorar as diferentes abordagens de testes para aplicações Next.js, incluindo testes unitários, de integração e end-to-end.

## 🚫 Desafios de Testes em SPAs Tradicionais

As **Single Page Applications (SPAs)** convencionais enfrentam diversos obstáculos relacionados a testes:

### Limitações Comuns:

1. **🔧 Configuração Complexa**:
   - **Setup manual**: Jest, Babel, Webpack configurados do zero
   - **Mocking complexo**: APIs, módulos e dependências mockadas manualmente
   - **Transpilação**: Configuração de TypeScript/JSX para testes
   - **Path mapping**: Aliases e imports relativos problemáticos

2. **🌐 Ambiente de Teste Limitado**:
   - **DOM simulation**: jsdom nem sempre reflete comportamento real
   - **Browser APIs**: localStorage, sessionStorage, fetch mockados
   - **Routing**: Navegação e URLs testadas artificialmente
   - **CSS-in-JS**: Estilos não renderizados nos testes

3. **🔄 Testes de Integração Fragmentados**:
   - **API mocking**: Endpoints mockados manualmente
   - **Estado global**: Redux, Context API difíceis de testar
   - **Side effects**: useEffect, timers, promises complexos
   - **Data fetching**: Chamadas assíncronas problemáticas

4. **🎭 E2E Testing Complexo**:
   - **Setup pesado**: Cypress, Playwright configurados separadamente
   - **Ambiente isolado**: Banco de dados e APIs de teste
   - **Flaky tests**: Testes instáveis por timing e async
   - **CI/CD integration**: Pipeline de testes complexo

5. **📊 Coverage e Reporting**:
   - **Configuração manual**: Istanbul, nyc configurados separadamente
   - **Relatórios básicos**: Coverage reports não integrados
   - **Thresholds**: Limites de cobertura não enforçados
   - **Visual regression**: Testes visuais não padronizados

## ✅ Vantagens do Sistema de Testes Next.js

O Next.js oferece **um ecossistema de testes integrado e otimizado**:

### Benefícios Principais:

1. **🚀 Configuração Zero**:
   - **Jest integrado**: Configuração automática com next/jest
   - **TypeScript support**: Suporte nativo sem configuração
   - **Path mapping**: Aliases (@/) funcionam automaticamente
   - **Environment variables**: .env.test carregado automaticamente

2. **🌐 Ambiente Realista**:
   - **Next.js runtime**: Testes rodam no mesmo ambiente da aplicação
   - **API Routes**: Endpoints testados como funções reais
   - **Middleware**: Lógica de middleware testável
   - **Image optimization**: Componente Image mockado adequadamente

3. **🔄 Testes de Integração Simplificados**:
   - **MSW integration**: Mock Service Worker para APIs
   - **Database testing**: Conexões de teste isoladas
   - **Server Components**: Testes de componentes do servidor
   - **Data fetching**: getServerSideProps, getStaticProps testáveis

4. **🎭 E2E Testing Integrado**:
   - **Playwright built-in**: Configuração automática para E2E
   - **Test database**: Isolamento automático de dados
   - **Preview deployments**: Testes em ambientes reais
   - **Visual regression**: Comparação de screenshots automática

5. **📊 Monitoring e Analytics**:
   - **Coverage reports**: Relatórios integrados ao build
   - **Performance testing**: Core Web Vitals nos testes
   - **Bundle analysis**: Impacto de mudanças no bundle size
   - **CI/CD integration**: GitHub Actions com templates prontos

6. **🛠️ Developer Experience**:
   - **Hot reload**: Testes re-executados automaticamente
   - **Error overlay**: Debugging visual de falhas
   - **Test isolation**: Cada teste roda em ambiente limpo
   - **Parallel execution**: Testes executados em paralelo

### Resultado:
**O Next.js transforma testes de uma tarefa complexa e propensa a erros em um processo integrado e eficiente**, garantindo qualidade de código com mínimo overhead de configuração.

## 📋 Por que Testar?

Testar sua aplicação Next.js traz diversos benefícios:

- ✅ **Detecção precoce de bugs**: Encontre problemas antes que eles cheguem à produção
- 🛡️ **Proteção contra regressões**: Garanta que novas alterações não quebrem funcionalidades existentes
- 📝 **Documentação viva**: Testes servem como documentação de como o código deve funcionar
- 🔄 **Facilita refatorações**: Refatore com confiança, sabendo que os testes detectarão problemas
- 👥 **Colaboração em equipe**: Permite que diferentes desenvolvedores trabalhem no mesmo código com segurança

## 🧩 Tipos de Testes

### Testes Unitários

Testes unitários focam em pequenas partes isoladas do código, como funções e componentes.

### Testes de Integração

Testes de integração verificam como diferentes partes do sistema funcionam juntas.

### Testes End-to-End (E2E)

Testes E2E simulam interações reais de usuários do início ao fim de um fluxo.

## 🛠️ Ferramentas de Teste para Next.js

### Jest

Jest é o framework de testes mais popular para aplicações JavaScript e React.

#### Configuração do Jest

```javascript
// jest.config.js
const nextJest = require('next/jest');

const createJestConfig = nextJest({
  // Caminho para o app Next.js
  dir: './',
});

// Configuração personalizada do Jest
const customJestConfig = {
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  moduleNameMapper: {
    // Mapeamento de aliases
    '^@/components/(.*)$': '<rootDir>/components/$1',
    '^@/pages/(.*)$': '<rootDir>/pages/$1',
  },
  testEnvironment: 'jest-environment-jsdom',
  collectCoverage: true,
  collectCoverageFrom: [
    '**/*.{js,jsx,ts,tsx}',
    '!**/*.d.ts',
    '!**/node_modules/**',
    '!**/.next/**',
    '!jest.config.js',
    '!next.config.js',
  ],
};

// exporta a configuração
module.exports = createJestConfig(customJestConfig);
```

```javascript
// jest.setup.js
// Importar bibliotecas necessárias para configuração dos testes
import '@testing-library/jest-dom';
```

### React Testing Library

React Testing Library é uma biblioteca para testar componentes React de forma mais próxima a como os usuários interagem com sua aplicação.

### Cypress

Cypress é uma ferramenta de testes end-to-end que permite simular interações de usuários no navegador.

## 📊 Testando Componentes React

### Teste de Componente Básico

```jsx
// components/Button.js
export function Button({ onClick, children }) {
  return (
    <button 
      onClick={onClick}
      className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
    >
      {children}
    </button>
  );
}
```

```jsx
// __tests__/components/Button.test.js
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from '@/components/Button';

describe('Button Component', () => {
  it('renderiza o texto do botão corretamente', () => {
    render(<Button>Clique Aqui</Button>);
    expect(screen.getByText('Clique Aqui')).toBeInTheDocument();
  });

  it('chama a função onClick quando clicado', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Clique Aqui</Button>);
    
    fireEvent.click(screen.getByText('Clique Aqui'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

### Testando Componentes com Hooks

```jsx
// components/Counter.js
'use client';

import { useState } from 'react';

export function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p data-testid="count">Contagem: {count}</p>
      <button onClick={() => setCount(count + 1)}>Incrementar</button>
      <button onClick={() => setCount(count - 1)}>Decrementar</button>
    </div>
  );
}
```

```jsx
// __tests__/components/Counter.test.js
import { render, screen, fireEvent } from '@testing-library/react';
import { Counter } from '@/components/Counter';

describe('Counter Component', () => {
  it('inicia com contagem zero', () => {
    render(<Counter />);
    expect(screen.getByTestId('count')).toHaveTextContent('Contagem: 0');
  });

  it('incrementa a contagem quando o botão é clicado', () => {
    render(<Counter />);
    fireEvent.click(screen.getByText('Incrementar'));
    expect(screen.getByTestId('count')).toHaveTextContent('Contagem: 1');
  });

  it('decrementa a contagem quando o botão é clicado', () => {
    render(<Counter />);
    fireEvent.click(screen.getByText('Decrementar'));
    expect(screen.getByTestId('count')).toHaveTextContent('Contagem: -1');
  });
});
```

## 🔄 Testando API Routes no Next.js

```jsx
// pages/api/user.js
export default function handler(req, res) {
  if (req.method === 'GET') {
    // Simulação de dados de usuário
    res.status(200).json({ id: 1, name: 'John Doe', email: 'john@example.com' });
  } else {
    res.setHeader('Allow', ['GET']);
    res.status(405).end(`Method ${req.method} Not Allowed`);
  }
}
```

```javascript
// __tests__/pages/api/user.test.js
import { createMocks } from 'node-mocks-http';
import handler from '@/pages/api/user';

describe('User API Endpoint', () => {
  it('retorna dados do usuário para método GET', async () => {
    const { req, res } = createMocks({
      method: 'GET',
    });

    await handler(req, res);

    expect(res._getStatusCode()).toBe(200);
    expect(JSON.parse(res._getData())).toEqual({
      id: 1,
      name: 'John Doe',
      email: 'john@example.com',
    });
  });

  it('retorna erro 405 para métodos não permitidos', async () => {
    const { req, res } = createMocks({
      method: 'POST',
    });

    await handler(req, res);

    expect(res._getStatusCode()).toBe(405);
  });
});
```

## 🧪 Testando Server Components e Client Components

### Testando Server Components

```jsx
// app/components/UserProfile.jsx (Server Component)
export default async function UserProfile({ userId }) {
  // Em um componente real, você buscaria dados do usuário
  const user = await fetchUser(userId);
  
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
      <p>Membro desde: {new Date(user.createdAt).toLocaleDateString()}</p>
    </div>
  );
}

// Função mock para buscar usuário
async function fetchUser(id) {
  // Simulação de busca de dados
  return {
    id,
    name: 'Maria Silva',
    email: 'maria@example.com',
    createdAt: '2023-01-15',
  };
}
```

```jsx
// __tests__/app/components/UserProfile.test.jsx
import { render, screen } from '@testing-library/react';
import UserProfile from '@/app/components/UserProfile';

// Mock da função fetchUser
jest.mock('@/app/components/UserProfile', () => {
  const originalModule = jest.requireActual('@/app/components/UserProfile');
  return {
    __esModule: true,
    ...originalModule,
    default: async ({ userId }) => {
      const user = {
        id: userId,
        name: 'Maria Silva',
        email: 'maria@example.com',
        createdAt: '2023-01-15',
      };
      
      return (
        <div>
          <h2>{user.name}</h2>
          <p>{user.email}</p>
          <p>Membro desde: {new Date(user.createdAt).toLocaleDateString()}</p>
        </div>
      );
    },
  };
});

describe('UserProfile Server Component', () => {
  it('renderiza o perfil do usuário corretamente', async () => {
    render(await UserProfile({ userId: 1 }));
    
    expect(screen.getByText('Maria Silva')).toBeInTheDocument();
    expect(screen.getByText('maria@example.com')).toBeInTheDocument();
    expect(screen.getByText(/Membro desde:/)).toBeInTheDocument();
  });
});
```

### Testando Client Components

```jsx
// app/components/LoginForm.jsx (Client Component)
'use client';

import { useState } from 'react';

export default function LoginForm({ onSubmit }) {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    
    if (!email || !password) {
      setError('Preencha todos os campos');
      return;
    }
    
    setError('');
    onSubmit({ email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      {error && <p className="text-red-500">{error}</p>}
      
      <div className="mb-4">
        <label htmlFor="email">Email</label>
        <input
          id="email"
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          className="border p-2 w-full"
          data-testid="email-input"
        />
      </div>
      
      <div className="mb-4">
        <label htmlFor="password">Senha</label>
        <input
          id="password"
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          className="border p-2 w-full"
          data-testid="password-input"
        />
      </div>
      
      <button 
        type="submit"
        className="bg-blue-500 text-white p-2 rounded"
      >
        Entrar
      </button>
    </form>
  );
}
```

```jsx
// __tests__/app/components/LoginForm.test.jsx
import { render, screen, fireEvent } from '@testing-library/react';
import LoginForm from '@/app/components/LoginForm';

describe('LoginForm Client Component', () => {
  it('exibe erro quando o formulário é enviado vazio', () => {
    const handleSubmit = jest.fn();
    render(<LoginForm onSubmit={handleSubmit} />);
    
    fireEvent.click(screen.getByText('Entrar'));
    
    expect(screen.getByText('Preencha todos os campos')).toBeInTheDocument();
    expect(handleSubmit).not.toHaveBeenCalled();
  });
  
  it('chama onSubmit com os dados corretos quando o formulário é preenchido', () => {
    const handleSubmit = jest.fn();
    render(<LoginForm onSubmit={handleSubmit} />);
    
    fireEvent.change(screen.getByTestId('email-input'), {
      target: { value: 'usuario@example.com' },
    });
    
    fireEvent.change(screen.getByTestId('password-input'), {
      target: { value: 'senha123' },
    });
    
    fireEvent.click(screen.getByText('Entrar'));
    
    expect(handleSubmit).toHaveBeenCalledWith({
      email: 'usuario@example.com',
      password: 'senha123',
    });
    expect(screen.queryByText('Preencha todos os campos')).not.toBeInTheDocument();
  });
});
```

## 🔍 Testes de Integração com Next.js

Os testes de integração verificam como diferentes partes da sua aplicação trabalham juntas.

```jsx
// __tests__/integration/todo-list.test.jsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import TodoList from '@/components/TodoList';
import { TodoProvider } from '@/context/TodoContext';

describe('Integração: Lista de Tarefas', () => {
  it('permite adicionar, marcar e excluir tarefas', async () => {
    render(
      <TodoProvider>
        <TodoList />
      </TodoProvider>
    );
    
    // Adicionar uma nova tarefa
    fireEvent.change(screen.getByPlaceholderText('Adicionar tarefa...'), {
      target: { value: 'Comprar leite' },
    });
    fireEvent.click(screen.getByText('Adicionar'));
    
    // Verificar se a tarefa foi adicionada
    expect(screen.getByText('Comprar leite')).toBeInTheDocument();
    
    // Marcar como concluída
    fireEvent.click(screen.getByText('Comprar leite'));
    
    // Verificar se foi marcada como concluída
    await waitFor(() => {
      expect(screen.getByText('Comprar leite')).toHaveClass('line-through');
    });
    
    // Remover a tarefa
    fireEvent.click(screen.getByTestId('delete-button'));
    
    // Verificar se foi removida
    await waitFor(() => {
      expect(screen.queryByText('Comprar leite')).not.toBeInTheDocument();
    });
  });
});
```

## 🌐 Testes End-to-End com Cypress

Os testes E2E simulam interações reais de usuários em um navegador.

### Configuração do Cypress

```javascript
// cypress.config.js
import { defineConfig } from 'cypress';

export default defineConfig({
  e2e: {
    setupNodeEvents(on, config) {
      // implementar event listeners aqui
    },
    baseUrl: 'http://localhost:3000',
  },
});
```

### Escrevendo Testes E2E

```javascript
// cypress/e2e/home.cy.js
describe('Página Inicial', () => {
  beforeEach(() => {
    cy.visit('/');
  });

  it('exibe o título correto', () => {
    cy.get('h1').should('contain', 'Bem-vindo ao Meu App Next.js');
  });

  it('navega para a página About ao clicar no link', () => {
    cy.get('nav').contains('Sobre').click();
    cy.url().should('include', '/sobre');
    cy.get('h1').should('contain', 'Sobre Nós');
  });
});
```

```javascript
// cypress/e2e/login.cy.js
describe('Formulário de Login', () => {
  beforeEach(() => {
    cy.visit('/login');
  });

  it('exibe erro quando o formulário é enviado vazio', () => {
    cy.get('button[type="submit"]').click();
    cy.get('.text-red-500').should('be.visible');
  });

  it('faz login com sucesso usando credenciais válidas', () => {
    cy.get('#email').type('usuario@exemplo.com');
    cy.get('#password').type('senha123');
    cy.get('button[type="submit"]').click();
    
    // Redireciona para o dashboard após login
    cy.url().should('include', '/dashboard');
    cy.get('h1').should('contain', 'Dashboard');
  });
});
```

## 🧠 Mocks e Stubs

### Mockando Fetch e API Calls

```javascript
// __tests__/utils/api.test.js
import { fetchUserData } from '@/utils/api';

// Mock global fetch
global.fetch = jest.fn();

describe('API Utils', () => {
  beforeEach(() => {
    // Limpar mocks entre testes
    jest.clearAllMocks();
  });

  it('busca dados do usuário corretamente', async () => {
    const mockUser = { id: 1, name: 'Carlos Silva' };
    
    // Configurar o mock para retornar dados específicos
    fetch.mockResolvedValueOnce({
      ok: true,
      json: async () => mockUser,
    });

    const result = await fetchUserData(1);
    
    expect(result).toEqual(mockUser);
    expect(fetch).toHaveBeenCalledWith('/api/users/1');
  });

  it('lança erro quando a requisição falha', async () => {
    fetch.mockResolvedValueOnce({
      ok: false,
      status: 404,
      statusText: 'Not Found',
    });

    await expect(fetchUserData(999)).rejects.toThrow('Erro 404: Not Found');
    expect(fetch).toHaveBeenCalledWith('/api/users/999');
  });
});
```

### Mockando Módulos e Componentes

```javascript
// __tests__/components/UserDashboard.test.jsx
import { render, screen } from '@testing-library/react';
import UserDashboard from '@/components/UserDashboard';
import * as api from '@/utils/api';

// Mock do módulo de API
jest.mock('@/utils/api', () => ({
  fetchUserData: jest.fn(),
  fetchUserPosts: jest.fn(),
}));

describe('UserDashboard Component', () => {
  beforeEach(() => {
    // Mock das funções de API para cada teste
    api.fetchUserData.mockResolvedValue({
      id: 1,
      name: 'Juliana Costa',
      email: 'juliana@example.com',
    });
    
    api.fetchUserPosts.mockResolvedValue([
      { id: 1, title: 'Primeiro Post', content: 'Conteúdo...' },
      { id: 2, title: 'Segundo Post', content: 'Mais conteúdo...' },
    ]);
  });

  it('renderiza dados do usuário e posts', async () => {
    render(<UserDashboard userId={1} />);
    
    // Esperar que os dados carregados apareçam na tela
    expect(await screen.findByText('Juliana Costa')).toBeInTheDocument();
    expect(await screen.findByText('Primeiro Post')).toBeInTheDocument();
    expect(await screen.findByText('Segundo Post')).toBeInTheDocument();
    
    // Verificar se as funções de API foram chamadas corretamente
    expect(api.fetchUserData).toHaveBeenCalledWith(1);
    expect(api.fetchUserPosts).toHaveBeenCalledWith(1);
  });
});
```

## 📈 Cobertura de Testes

A cobertura de testes mede quais partes do seu código estão sendo testadas.

### Executando Testes com Cobertura

```bash
npm test -- --coverage
```

### Configurando Thresholds de Cobertura

```javascript
// jest.config.js
module.exports = {
  // ... outras configurações
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80,
    },
  },
};
```

## 🚀 Integração Contínua (CI) com Testes

### GitHub Actions

```yaml
# .github/workflows/tests.yml
name: Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
          
      - name: Install dependencies
        run: npm ci
        
      - name: Run unit and integration tests
        run: npm test -- --coverage
        
      - name: Build project
        run: npm run build
        
      - name: Start Next.js
        run: npm run start & npx wait-on http://localhost:3000
        
      - name: Run E2E tests
        run: npm run cypress:run
```

## 🧠 Melhores Práticas para Testes em Next.js

1. **Comece com testes unitários** - São mais rápidos e fáceis de manter
2. **Use data-testid para identificar elementos** - Evite testar baseado em classes de estilo
3. **Mantenha testes isolados** - Um teste não deve depender de outro
4. **Mock de dependências externas** - API calls, localStorage, etc.
5. **Teste os fluxos principais** - Foque nos fluxos críticos do usuário
6. **Evite testar detalhes de implementação** - Teste comportamentos, não como algo é implementado
7. **Mantenha testes simples e legíveis** - Testes são também documentação
8. **Configure CI para executar testes automaticamente** - Pegue problemas cedo
9. **Combine diferentes tipos de teste** - Unitários, integração e E2E
10. **Atualize testes conforme o código evolui** - Testes desatualizados são inúteis

## 🔍 Recursos Adicionais

- [Documentação de Testes do Next.js](https://nextjs.org/docs/testing)
- [Jest - Framework de Testes JavaScript](https://jestjs.io/)
- [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
- [Cypress - Framework de Testes E2E](https://www.cypress.io/)
- [Testing Playground - Ferramenta para ajudar com queries de teste](https://testing-playground.com/)

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/>