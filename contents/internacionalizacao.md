<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# Internacionalização no Next.js

A internacionalização (i18n) permite que seu aplicativo Next.js seja acessível para usuários de diferentes idiomas e regiões. O Next.js oferece suporte integrado para internacionalização, facilitando a criação de aplicativos multilíngues. Neste guia, vamos explorar como implementar e gerenciar recursos de internacionalização em projetos Next.js.

## Conceitos Básicos

Antes de mergulharmos na implementação, vamos entender alguns conceitos importantes:

- **Locale**: Identificador para um conjunto de preferências de idioma e região (ex: pt-BR, en-US, es-ES)
- **Detecção de Idioma**: Identificar automaticamente o idioma preferido do usuário
- **Traduções**: Strings e mensagens em diferentes idiomas
- **Formatação**: Ajuste de datas, números e moedas para diferentes locales
- **Roteamento i18n**: Navegação entre diferentes versões de idioma do seu site

## Configurando i18n no Next.js

### Configuração no App Router (Next.js 13+)

No App Router, a internacionalização é implementada principalmente através do sistema de pastas:

#### 1. Estrutura de Pasta Baseada em Subpastas

```
app/
├── [lang]/
│   ├── page.js
│   ├── sobre/
│   │   └── page.js
│   └── produtos/
│       └── [...slug]/
│           └── page.js
└── layout.js
```

#### 2. Middleware para Detecção de Idioma

Crie um arquivo `middleware.js` na raiz do projeto:

```javascript
// middleware.js
import { NextResponse } from 'next/server';

// Locales suportados
const locales = ['pt-BR', 'en-US', 'es-ES'];
const defaultLocale = 'pt-BR';

// Função para verificar se o caminho já começa com um locale
function pathnameHasLocale(pathname) {
  return locales.some(
    locale => pathname.startsWith(`/${locale}/`) || pathname === `/${locale}`
  );
}

export function middleware(request) {
  const { pathname } = request.nextUrl;
  
  // Pular middleware para arquivos estáticos e rotas de API
  if (
    pathname.startsWith('/_next') ||
    pathname.startsWith('/api/') ||
    pathname.includes('/images/') ||
    pathname.includes('.') // arquivos com extensão
  ) {
    return NextResponse.next();
  }

  // Verificar se já temos um locale no caminho
  if (pathnameHasLocale(pathname)) {
    return NextResponse.next();
  }

  // Detectar o idioma preferido do usuário a partir do cabeçalho Accept-Language
  const acceptLanguage = request.headers.get('accept-language') || '';
  const preferredLocale = acceptLanguage
    .split(',')
    .map(lang => lang.split(';')[0].trim())
    .find(lang => locales.includes(lang)) || defaultLocale;

  // Redirecionar para o mesmo caminho com o locale preferido
  return NextResponse.redirect(
    new URL(`/${preferredLocale}${pathname === '/' ? '' : pathname}`, request.url)
  );
}

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|favicon.ico).*)'],
};
```

#### 3. Parâmetros Dinâmicos para Locale

```javascript
// app/[lang]/layout.js
export async function generateStaticParams() {
  return [
    { lang: 'pt-BR' },
    { lang: 'en-US' },
    { lang: 'es-ES' },
  ];
}

export default function Layout({ children, params }) {
  return (
    <html lang={params.lang}>
      <body>{children}</body>
    </html>
  );
}
```

### Configuração no Pages Router (Legacy)

Para projetos que usam o Pages Router, a configuração de i18n é feita no arquivo `next.config.js`:

```javascript
// next.config.js
module.exports = {
  i18n: {
    // Locales suportados
    locales: ['pt-BR', 'en-US', 'es-ES'],
    // Locale padrão
    defaultLocale: 'pt-BR',
    // Domínios específicos para cada locale (opcional)
    domains: [
      {
        domain: 'exemplo.com.br',
        defaultLocale: 'pt-BR',
      },
      {
        domain: 'example.com',
        defaultLocale: 'en-US',
      },
      {
        domain: 'ejemplo.com',
        defaultLocale: 'es-ES',
      },
    ],
    // Estratégia de detecção de locale
    localeDetection: true,
  },
};
```

## Gerenciamento de Traduções

Existem várias bibliotecas para gerenciar traduções em Next.js. Vamos explorar algumas das mais populares:

### 1. next-intl

Uma das soluções mais completas para internacionalização no Next.js:

#### Instalação

```bash
npm install next-intl
```

#### Configuração com App Router

```javascript
// messages/pt-BR.json
{
  "Index": {
    "title": "Bem-vindo ao nosso site",
    "subtitle": "Descubra nossos produtos incríveis",
    "cta": "Começar agora"
  },
  "Products": {
    "title": "Nossos Produtos",
    "notFound": "Produto não encontrado"
  }
}
```

```javascript
// app/[locale]/layout.js
import { NextIntlClientProvider } from 'next-intl';
import { notFound } from 'next/navigation';

export function generateStaticParams() {
  return [{ locale: 'pt-BR' }, { locale: 'en-US' }, { locale: 'es-ES' }];
}

export default async function LocaleLayout({ children, params: { locale } }) {
  let messages;
  try {
    messages = (await import(`../../messages/${locale}.json`)).default;
  } catch (error) {
    notFound();
  }

  return (
    <html lang={locale}>
      <body>
        <NextIntlClientProvider locale={locale} messages={messages}>
          {children}
        </NextIntlClientProvider>
      </body>
    </html>
  );
}
```

#### Uso nas Páginas

```javascript
// app/[locale]/page.js
import { useTranslations } from 'next-intl';

export default function Home() {
  const t = useTranslations('Index');
  
  return (
    <main>
      <h1>{t('title')}</h1>
      <p>{t('subtitle')}</p>
      <button>{t('cta')}</button>
    </main>
  );
}
```

### 2. react-i18next

Outra opção popular para gerenciar traduções:

#### Instalação

```bash
npm install react-i18next i18next
```

#### Configuração

```javascript
// i18n.js
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';

// Recursos de tradução
const resources = {
  'pt-BR': {
    translation: {
      'welcome': 'Bem-vindo ao nosso site',
      'about': 'Sobre nós',
      'contact': 'Contato'
    }
  },
  'en-US': {
    translation: {
      'welcome': 'Welcome to our website',
      'about': 'About us',
      'contact': 'Contact'
    }
  },
  'es-ES': {
    translation: {
      'welcome': 'Bienvenido a nuestro sitio',
      'about': 'Sobre nosotros',
      'contact': 'Contacto'
    }
  }
};

i18n
  .use(initReactI18next)
  .init({
    resources,
    lng: 'pt-BR', // idioma inicial
    fallbackLng: 'pt-BR',
    interpolation: {
      escapeValue: false // não necessário para React
    }
  });

export default i18n;
```

```javascript
// _app.js (Pages Router)
import '../i18n';
import { useRouter } from 'next/router';
import { useEffect } from 'react';
import i18n from '../i18n';

function MyApp({ Component, pageProps }) {
  const router = useRouter();
  
  useEffect(() => {
    if (router.locale) {
      i18n.changeLanguage(router.locale);
    }
  }, [router.locale]);
  
  return <Component {...pageProps} />;
}

export default MyApp;
```

#### Uso nas Páginas

```javascript
// pages/index.js
import { useTranslation } from 'react-i18next';

export default function Home() {
  const { t } = useTranslation();
  
  return (
    <div>
      <h1>{t('welcome')}</h1>
      <nav>
        <ul>
          <li>{t('about')}</li>
          <li>{t('contact')}</li>
        </ul>
      </nav>
    </div>
  );
}
```

## Formatação de Datas, Números e Moedas

Para formatar datas, números e moedas de acordo com o locale, podemos usar a API Intl nativa do JavaScript:

```javascript
// Formatação de data
function formatarData(data, locale) {
  return new Intl.DateTimeFormat(locale, {
    day: 'numeric',
    month: 'long',
    year: 'numeric'
  }).format(data);
}

// Formatação de números
function formatarNumero(numero, locale) {
  return new Intl.NumberFormat(locale).format(numero);
}

// Formatação de moeda
function formatarMoeda(valor, locale, moeda = 'BRL') {
  return new Intl.NumberFormat(locale, {
    style: 'currency',
    currency: moeda
  }).format(valor);
}

// Exemplo de uso
const data = new Date();
const numero = 1234567.89;
const preco = 99.99;

// Para pt-BR
console.log(formatarData(data, 'pt-BR')); // 15 de março de 2023
console.log(formatarNumero(numero, 'pt-BR')); // 1.234.567,89
console.log(formatarMoeda(preco, 'pt-BR')); // R$ 99,99

// Para en-US
console.log(formatarData(data, 'en-US')); // March 15, 2023
console.log(formatarNumero(numero, 'en-US')); // 1,234,567.89
console.log(formatarMoeda(preco, 'en-US', 'USD')); // $99.99
```

### Integração com next-intl

A biblioteca `next-intl` também oferece formatadores para datas, números e moedas:

```javascript
// app/[locale]/componentes/formatadores.js
'use client';
import { useFormatter } from 'next-intl';

export function FormatadoresExemplo() {
  const format = useFormatter();
  const data = new Date();
  const numero = 1234567.89;
  const preco = 99.99;
  
  return (
    <div>
      <p>Data: {format.dateTime(data, {
        year: 'numeric',
        month: 'long',
        day: 'numeric'
      })}</p>
      
      <p>Número: {format.number(numero)}</p>
      
      <p>Preço: {format.number(preco, {
        style: 'currency',
        currency: 'BRL'
      })}</p>
    </div>
  );
}
```

## Alternância de Idiomas

Vamos criar um seletor de idioma que funciona tanto no App Router quanto no Pages Router:

### Para App Router

```javascript
// app/[locale]/componentes/seletor-idioma.js
'use client';
import { usePathname, useRouter } from 'next/navigation';
import Link from 'next/link';

export default function SeletorIdioma() {
  const pathname = usePathname();
  const locales = ['pt-BR', 'en-US', 'es-ES'];
  const currentLocale = pathname.split('/')[1];
  
  // Obter o caminho sem o locale
  const pathnameWithoutLocale = pathname.split('/').slice(2).join('/');
  
  return (
    <div className="seletor-idioma">
      <ul>
        {locales.map((locale) => (
          <li key={locale}>
            <Link 
              href={`/${locale}/${pathnameWithoutLocale}`}
              className={locale === currentLocale ? 'ativo' : ''}
            >
              {locale === 'pt-BR' ? '🇧🇷 Português' : 
               locale === 'en-US' ? '🇺🇸 English' : '🇪🇸 Español'}
            </Link>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### Para Pages Router

```javascript
// components/seletor-idioma.js
import { useRouter } from 'next/router';
import Link from 'next/link';

export default function SeletorIdioma() {
  const router = useRouter();
  const { pathname, asPath, query } = router;
  
  const locales = router.locales || ['pt-BR', 'en-US', 'es-ES'];
  
  return (
    <div className="seletor-idioma">
      <ul>
        {locales.map((locale) => (
          <li key={locale}>
            <Link 
              href={{ pathname, query }} 
              as={asPath}
              locale={locale}
              className={locale === router.locale ? 'ativo' : ''}
            >
              {locale === 'pt-BR' ? '🇧🇷 Português' : 
               locale === 'en-US' ? '🇺🇸 English' : '🇪🇸 Español'}
            </Link>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## SEO para Sites Multilíngues

Para um bom SEO em sites multilíngues, é importante incluir as tags apropriadas:

### Links Hreflang no App Router

```javascript
// app/[locale]/layout.js
export default function Layout({ children, params }) {
  const { locale } = params;
  const locales = ['pt-BR', 'en-US', 'es-ES'];
  const pathname = '/'; // Obtenha dinamicamente o caminho atual
  
  const alternateLanguages = {};
  locales.forEach((lang) => {
    alternateLanguages[lang] = `https://meusite.com/${lang}${pathname}`;
  });
  
  return (
    <html lang={locale}>
      <head>
        {/* Outros metadados */}
        {locales.map((lang) => (
          <link
            key={lang}
            rel="alternate"
            hrefLang={lang}
            href={alternateLanguages[lang]}
          />
        ))}
        <link
          rel="alternate"
          hrefLang="x-default"
          href={`https://meusite.com/pt-BR${pathname}`}
        />
      </head>
      <body>{children}</body>
    </html>
  );
}
```

### Links Hreflang no Pages Router

```javascript
// pages/_app.js
import Head from 'next/head';
import { useRouter } from 'next/router';

function MyApp({ Component, pageProps }) {
  const router = useRouter();
  const { locale, locales, asPath } = router;
  
  return (
    <>
      <Head>
        {locales.map((lang) => (
          <link
            key={lang}
            rel="alternate"
            hrefLang={lang}
            href={`https://meusite.com/${lang}${asPath}`}
          />
        ))}
        <link
          rel="alternate"
          hrefLang="x-default"
          href={`https://meusite.com/pt-BR${asPath}`}
        />
      </Head>
      <Component {...pageProps} />
    </>
  );
}

export default MyApp;
```

## URL Multilíngues

Diferentes estratégias para URLs multilíngues:

### 1. Subpath Routing (Recomendado)

```
https://meusite.com/pt-BR/produtos
https://meusite.com/en-US/products
https://meusite.com/es-ES/productos
```

### 2. Domain Routing

```
https://meusite.com.br/ (pt-BR)
https://mysite.com/ (en-US)
https://misitio.com/ (es-ES)
```

Para implementar Domain Routing no Pages Router:

```javascript
// next.config.js
module.exports = {
  i18n: {
    locales: ['pt-BR', 'en-US', 'es-ES'],
    defaultLocale: 'pt-BR',
    domains: [
      {
        domain: 'meusite.com.br',
        defaultLocale: 'pt-BR',
      },
      {
        domain: 'mysite.com',
        defaultLocale: 'en-US',
      },
      {
        domain: 'misitio.com',
        defaultLocale: 'es-ES',
      },
    ],
  },
};
```

## Considerações de Design para Interfaces Multilíngues

Ao criar interfaces para diferentes idiomas, é importante considerar:

1. **Espaço para texto**: Alguns idiomas precisam de mais espaço que outros
2. **Direção de texto**: Suporte para idiomas RTL (right-to-left) como árabe e hebraico
3. **Formatação de datas e números**: Diferentes formatos para diferentes regiões
4. **Pluralização**: Regras diferentes em cada idioma

### Exemplo de Pluralização com next-intl

```javascript
// messages/pt-BR.json
{
  "Cart": {
    "items": {
      "one": "{count} item no carrinho",
      "other": "{count} itens no carrinho"
    }
  }
}
```

```javascript
// app/[locale]/carrinho/page.js
'use client';
import { useTranslations } from 'next-intl';

export default function CarrinhoPage() {
  const t = useTranslations('Cart');
  const itemCount = 5;
  
  return (
    <div>
      <h1>Carrinho de Compras</h1>
      <p>{t('items', { count: itemCount })}</p>
    </div>
  );
}
```

## Melhores Práticas

1. **Separar conteúdo de código**: Mantenha as traduções em arquivos JSON separados
2. **Usar ferramentas de gestão de traduções**: Ferramentas como POEditor, Phrase ou Crowdin
3. **Implementar fallback para traduções ausentes**: Sempre ter um texto de backup
4. **Testar em diferentes idiomas**: Verificar quebras de layout e outros problemas
5. **Considerar traduções baseadas em contexto**: Traduções diferentes para a mesma palavra dependendo do contexto
6. **Automatizar exportação/importação de traduções**: Facilitar o trabalho dos tradutores

## Recursos e Ferramentas Úteis

- **Bibliotecas de i18n**:
  - [next-intl](https://next-intl-docs.vercel.app/)
  - [react-i18next](https://react.i18next.com/)
  - [next-translate](https://github.com/vinissimus/next-translate)
  - [lingui](https://lingui.js.org/)

- **Ferramentas de Gestão de Traduções**:
  - [Phrase](https://phrase.com/)
  - [Crowdin](https://crowdin.com/)
  - [POEditor](https://poeditor.com/)
  - [Lokalise](https://lokalise.com/)

- **Documentação Oficial**:
  - [Internacionalização no App Router](https://nextjs.org/docs/app/building-your-application/routing/internationalization)
  - [Internacionalização no Pages Router](https://nextjs.org/docs/pages/building-your-application/routing/internationalization)

## Exemplo Completo de Aplicação Multilíngue

### Estrutura de Pastas (App Router)

```
app/
├── [locale]/
│   ├── page.js
│   ├── sobre/
│   │   └── page.js
│   ├── produtos/
│   │   └── page.js
│   └── componentes/
│       ├── cabecalho.js
│       ├── rodape.js
│       └── seletor-idioma.js
├── layout.js
├── middleware.js
├── i18n.js
└── not-found.js
messages/
├── pt-BR.json
├── en-US.json
└── es-ES.json
```

Implementando esse sistema de internacionalização, seu aplicativo Next.js estará pronto para atender usuários de diferentes regiões e culturas, proporcionando uma experiência localizada e personalizada.

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/>