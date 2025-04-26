<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# 🚀 Deploy e Hospedagem de Aplicações Next.js 🌐

Colocar sua aplicação Next.js em produção é o passo final do desenvolvimento. O Next.js oferece diversas opções de deploy que se adaptam às necessidades do seu projeto. Neste guia, vamos explorar as diferentes estratégias de deploy, plataformas de hospedagem e configurações necessárias para levar sua aplicação ao ar.

## 📋 Entendendo os Modelos de Renderização e Deploy

Antes de escolher onde hospedar sua aplicação Next.js, é importante entender os diferentes modelos de renderização que o framework suporta:

- 🖥️ **Server-Side Rendering (SSR)**: Páginas renderizadas no servidor a cada requisição
- 🏗️ **Static Site Generation (SSG)**: Páginas geradas durante o build
- 🔄 **Incremental Static Regeneration (ISR)**: Páginas estáticas que são regeneradas após um intervalo
- 🧩 **Client-Side Rendering (CSR)**: Conteúdo renderizado no navegador do usuário

A escolha da plataforma de hospedagem deve considerar qual modelo de renderização você utiliza em sua aplicação.

## ☁️ Plataformas de Hospedagem para Next.js

### Vercel (Recomendada)

A Vercel, empresa por trás do Next.js, oferece a melhor experiência para hospedar aplicações Next.js.

#### Vantagens da Vercel

- ✅ Suporte nativo a todos os recursos do Next.js
- ✅ Deploy automático a partir do GitHub, GitLab ou Bitbucket
- ✅ Previews para Pull Requests
- ✅ Análise de performance automática
- ✅ Integração com Analytics
- ✅ Certificados SSL automáticos
- ✅ Rede de CDN global

#### Como Fazer Deploy na Vercel

1. Crie uma conta na [Vercel](https://vercel.com)
2. Conecte seu repositório Git (GitHub, GitLab, Bitbucket)
3. Selecione o repositório da sua aplicação Next.js
4. A Vercel detectará automaticamente o Next.js e aplicará as configurações adequadas
5. Clique em "Deploy"

```bash
# Ou use a CLI da Vercel
npm i -g vercel
vercel login
vercel
```

#### Configuração da Vercel

Você pode personalizar o comportamento do deploy criando um arquivo `vercel.json` na raiz do projeto:

```json
{
  "version": 2,
  "builds": [
    {
      "src": "next.config.js",
      "use": "@vercel/next"
    }
  ],
  "routes": [
    {
      "src": "/api/(.*)",
      "dest": "/api/$1"
    }
  ],
  "env": {
    "NEXT_PUBLIC_API_URL": "https://api.seuservico.com"
  },
  "regions": ["bru1", "gru1"],
  "headers": [
    {
      "source": "/fonts/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=31536000, immutable"
        }
      ]
    }
  ]
}
```

### Netlify

A Netlify é uma excelente alternativa para hospedar aplicações Next.js, especialmente para sites estáticos.

#### Como Fazer Deploy na Netlify

1. Crie uma conta na [Netlify](https://netlify.com)
2. Clique em "New site from Git"
3. Selecione seu repositório Git
4. Configurações de build:
   - Build command: `npm run build`
   - Publish directory: `.next`
5. Clique em "Deploy site"

#### Configuração do Netlify

Crie um arquivo `netlify.toml` na raiz do projeto:

```toml
[build]
  command = "npm run build"
  publish = ".next"

[build.environment]
  NETLIFY_NEXT_PLUGIN_SKIP = "true"

[[plugins]]
  package = "@netlify/plugin-nextjs"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

### AWS Amplify

AWS Amplify é uma solução completa para hospedar aplicações Next.js na infraestrutura da Amazon.

#### Como Fazer Deploy no AWS Amplify

1. Acesse o [AWS Amplify Console](https://console.aws.amazon.com/amplify)
2. Clique em "Create app" e selecione "Host web app"
3. Conecte seu repositório
4. Configure:
   - Framework: Next.js
   - Build command: `npm run build`
   - Output directory: `.next`
5. Clique em "Save and deploy"

```yaml
# amplify.yml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: .next
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
```

### Google Cloud Run

O Google Cloud Run permite executar aplicações Next.js em containers gerenciados.

#### Como Fazer Deploy no Google Cloud Run

1. Instale o [Google Cloud SDK](https://cloud.google.com/sdk)
2. Crie um Dockerfile para sua aplicação Next.js:

```dockerfile
# Dockerfile
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:18-alpine AS runner
WORKDIR /app
COPY --from=builder /app/next.config.js ./
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json

EXPOSE 3000
CMD ["npm", "start"]
```

3. Build e deploy da imagem:

```bash
# Construa a imagem Docker
docker build -t gcr.io/[PROJECT-ID]/nextjs-app .

# Faça upload para o Container Registry
docker push gcr.io/[PROJECT-ID]/nextjs-app

# Deploy no Cloud Run
gcloud run deploy nextjs-app --image gcr.io/[PROJECT-ID]/nextjs-app --platform managed
```

### Microsoft Azure

O Azure Static Web Apps e o Azure App Service são opções para hospedar aplicações Next.js.

#### Deploy no Azure Static Web Apps

```yaml
# .github/workflows/azure-static-web-apps.yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          app_location: "/"
          api_location: "api"
          output_location: ".next"
          app_build_command: "npm run build"
```

### DigitalOcean App Platform

DigitalOcean App Platform simplifica o deploy de aplicações Next.js.

#### Como Fazer Deploy no DigitalOcean App Platform

1. Crie uma conta no [DigitalOcean](https://digitalocean.com)
2. Vá para App Platform e clique em "Create App"
3. Conecte seu repositório
4. Selecione o branch e configure:
   - Type: Web Service
   - Environment: Node.js
   - Build Command: `npm run build`
   - Run Command: `npm start`
5. Clique em "Next" e configure recursos e variáveis de ambiente
6. Clique em "Create Resources"

## 📦 Self-Hosting com Docker

Para hospedar sua própria aplicação Next.js, você pode usar Docker:

```dockerfile
# Dockerfile para produção
FROM node:18-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

FROM node:18-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:18-alpine AS runner
WORKDIR /app
ENV NODE_ENV production

# Copy necessary files
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/next.config.js ./next.config.js

# Set user for security
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs
RUN chown -R nextjs:nodejs /app/.next
USER nextjs

EXPOSE 3000
CMD ["npm", "start"]
```

```yaml
# docker-compose.yml
version: '3'

services:
  nextjs:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgres://user:password@postgres:5432/db
      - NEXT_PUBLIC_API_URL=https://api.exemplo.com
    depends_on:
      - postgres
  
  postgres:
    image: postgres:14
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=db
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
```

## 🛠️ Configurações para Produção

### Environment Variables

Configure variáveis de ambiente para produção:

```bash
# .env.production
NEXT_PUBLIC_API_URL=https://api.seuservico.com
DATABASE_URL=postgres://user:password@localhost:5432/db
```

Para a Vercel, configure variáveis de ambiente no painel de controle ou:

```bash
vercel env add NEXT_PUBLIC_API_URL production
```

### HTTPS e SSL

A maioria das plataformas modernas fornece HTTPS automaticamente. Para servidores personalizados, considere usar Let's Encrypt com Certbot:

```bash
sudo apt-get update
sudo apt-get install certbot
sudo certbot certonly --standalone -d seu-dominio.com
```

### Otimizações de Performance

```javascript
// next.config.js
module.exports = {
  reactStrictMode: true,
  compress: true,
  poweredByHeader: false,
  optimizeFonts: true,
  images: {
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
    domains: ['images.exemplo.com'],
    formats: ['image/webp'],
  },
}
```

### Content Delivery Network (CDN)

Utilize CDNs para melhorar a performance:

```javascript
// next.config.js
module.exports = {
  assetPrefix: process.env.NODE_ENV === 'production' ? 'https://cdn.exemplo.com' : '',
}
```

## 🔄 Continuous Integration/Continuous Deployment (CI/CD)

### GitHub Actions para CI/CD

```yaml
# .github/workflows/ci.yml
name: CI/CD

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
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
      
    - name: Lint
      run: npm run lint
      
    - name: Run tests
      run: npm test
      
    - name: Build
      run: npm run build

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Deploy to Vercel
      uses: amondnet/vercel-action@v20
      with:
        vercel-token: ${{ secrets.VERCEL_TOKEN }}
        vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
        vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
        vercel-args: '--prod'
```

### GitLab CI/CD

```yaml
# .gitlab-ci.yml
stages:
  - test
  - build
  - deploy

variables:
  NODE_VERSION: "18"

cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/
    - .next/

test:
  stage: test
  image: node:${NODE_VERSION}
  script:
    - npm ci
    - npm run lint
    - npm test

build:
  stage: build
  image: node:${NODE_VERSION}
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - .next/

deploy:
  stage: deploy
  image: node:${NODE_VERSION}
  script:
    - npm i -g vercel
    - vercel --token ${VERCEL_TOKEN} --prod
  only:
    - main
```

## 🔍 Monitoramento e Logging

### Sentry para Monitoramento de Erros

```bash
npm install @sentry/nextjs
```

```javascript
// next.config.js
const { withSentryConfig } = require('@sentry/nextjs');

const moduleExports = {
  // suas configurações Next.js
};

const SentryWebpackPluginOptions = {
  // Opções do plugin Sentry
};

module.exports = withSentryConfig(moduleExports, SentryWebpackPluginOptions);
```

### Datadog para Monitoramento de Performance

```bash
npm install --save dd-trace
```

```javascript
// instrumentation.js (para Next.js 13+)
import ddTrace from 'dd-trace';

ddTrace.init({
  service: 'meu-app-nextjs',
  env: process.env.NODE_ENV,
});

export default function register() {
  // Inicialização opcional para Next.js
}
```

### Logtail para Gerenciamento de Logs

```bash
npm install @logtail/node @logtail/next
```

```javascript
// lib/logtail.js
import { Logtail } from '@logtail/node';

export const logtail = new Logtail(process.env.LOGTAIL_SOURCE_TOKEN);
```

## 🔒 Configurações de Segurança

### Content Security Policy (CSP)

```javascript
// next.config.js
const securityHeaders = [
  {
    key: 'Content-Security-Policy',
    value: `
      default-src 'self';
      script-src 'self' 'unsafe-eval' 'unsafe-inline' *.google-analytics.com;
      style-src 'self' 'unsafe-inline';
      img-src 'self' data: blob: *.google-analytics.com;
      font-src 'self';
      connect-src 'self' *.google-analytics.com;
      frame-src 'self';
    `.replace(/\s{2,}/g, ' ').trim()
  }
];

module.exports = {
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: securityHeaders,
      },
    ];
  },
};
```

### Headers de Segurança Adicionais

```javascript
// next.config.js
const securityHeaders = [
  {
    key: 'X-DNS-Prefetch-Control',
    value: 'on'
  },
  {
    key: 'Strict-Transport-Security',
    value: 'max-age=63072000; includeSubDomains; preload'
  },
  {
    key: 'X-XSS-Protection',
    value: '1; mode=block'
  },
  {
    key: 'X-Frame-Options',
    value: 'SAMEORIGIN'
  },
  {
    key: 'X-Content-Type-Options',
    value: 'nosniff'
  },
  {
    key: 'Referrer-Policy',
    value: 'origin-when-cross-origin'
  },
  {
    key: 'Permissions-Policy',
    value: 'camera=(), microphone=(), geolocation=()'
  }
];

module.exports = {
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: securityHeaders,
      },
    ];
  },
};
```

## 🌍 Estratégias de Implantação

### Blue-Green Deployment

O Blue-Green Deployment permite alternar entre duas versões idênticas do seu aplicativo:

```bash
# Configurando com Vercel Teams
vercel deploy --prod --name app-blue
# Atualize o DNS para apontar para a nova versão
vercel alias set app-blue.vercel.app seu-dominio.com
```

### Canary Releases

Implante gradualmente novas versões para uma porcentagem do tráfego:

```javascript
// Se estiver usando um proxy como Nginx
// /etc/nginx/sites-available/default
server {
    listen 80;
    server_name seu-dominio.com;

    location / {
        # 90% do tráfego vai para a versão estável
        split_clients "${remote_addr}${http_user_agent}" $variant {
            90%     stable;
            *       canary;
        }

        if ($variant = stable) {
            proxy_pass http://stable-version:3000;
        }
        if ($variant = canary) {
            proxy_pass http://canary-version:3000;
        }
    }
}
```

## 🧠 Melhores Práticas para Deploy

1. **Automatize o processo de build e deploy** - Use CI/CD para evitar erros manuais
2. **Use variáveis de ambiente** - Não hardcode segredos ou configurações específicas do ambiente
3. **Separe ambientes** - Mantenha ambientes de desenvolvimento, staging e produção
4. **Mantenha o controle de versões** - Use tags ou releases para rastrear versões
5. **Faça backups regulares** - Especialmente para dados críticos
6. **Monitore sua aplicação** - Configure alertas para problemas de performance ou erros
7. **Implemente rollbacks rápidos** - Tenha um plano para reverter rapidamente em caso de problemas
8. **Teste em produção-like** - Teste em um ambiente similar ao de produção antes do deploy
9. **Otimize assets** - Minifique JS, CSS e otimize imagens
10. **Use CDN** - Distribua conteúdo estático via CDN para melhor performance

## 🔥 Soluções para Problemas Comuns

### 1. Erro 500 Após Deploy

Verifique:
- Variáveis de ambiente configuradas corretamente
- Permissões de acesso a bancos de dados ou APIs externas
- Logs de erro do servidor

### 2. Problemas com Static Generation

```javascript
// next.config.js
module.exports = {
  // Para projetos maiores, aumente o timeout do build
  staticPageGenerationTimeout: 120
}
```

### 3. API Routes não Funcionando

Verifique se seu provedor de hospedagem suporta serverless functions ou API routes do Next.js. Na Vercel, elas funcionam automaticamente.

### 4. Páginas 404 em Rotas Dinâmicas após Deploy

Verifique se as páginas dinâmicas estão sendo pré-renderizadas corretamente:

```javascript
// pages/posts/[slug].js
export async function getStaticPaths() {
  // Certifique-se de retornar todos os caminhos necessários
  const paths = getAllPostSlugs();
  return {
    paths,
    fallback: 'blocking' // Ou true ou false, dependendo do caso
  };
}
```

### 5. Erros com Dependências Nativas

Algumas dependências podem precisar de compilação. Garanta que o ambiente de build tenha as ferramentas necessárias:

```dockerfile
# Dockerfile
FROM node:18-alpine AS deps
# Instale ferramentas de compilação se necessário
RUN apk add --no-cache libc6-compat python3 make g++
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
```

## 🔍 Recursos Adicionais

- [Documentação Oficial de Deploy do Next.js](https://nextjs.org/docs/deployment)
- [Guia de Deploy na Vercel](https://vercel.com/docs/concepts/next.js/overview)
- [Configuração de CI/CD com GitHub Actions](https://docs.github.com/en/actions/guides/building-and-testing-nodejs)
- [Configurando SSL com Let's Encrypt](https://letsencrypt.org/getting-started/)
- [Otimizando Performance Web](https://web.dev/performance-optimizing-content-efficiency/)

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/> 