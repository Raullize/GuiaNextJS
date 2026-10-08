<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=header"/>

# Otimização de Imagens no Next.js

A otimização de imagens é um aspecto crítico para o desempenho de aplicações web modernas. O Next.js oferece ferramentas poderosas para automatizar esse processo, garantindo que suas imagens sejam carregadas de forma eficiente e responsiva. Neste guia, vamos explorar como utilizar essas ferramentas para melhorar significativamente a experiência do usuário.

## Problemas de Imagens em SPAs Tradicionais

As **Single Page Applications (SPAs)** convencionais enfrentam diversos desafios relacionados ao gerenciamento de imagens:

### Limitações Comuns:

1. **Otimização Manual**:
   - **Formatos antigos**: JPEG, PNG sem conversão automática para WebP/AVIF
   - **Tamanhos fixos**: Uma imagem para todos os dispositivos
   - **Compressão manual**: Ferramentas externas para otimizar imagens
   - **Build time**: Processamento de imagens durante o build

2. **Performance Problemática**:
   - **Sem lazy loading**: Todas as imagens carregadas imediatamente
   - **CLS (Cumulative Layout Shift)**: Layout "pula" quando imagens carregam
   - **Bandwidth waste**: Imagens grandes em dispositivos pequenos
   - **Blocking rendering**: Imagens bloqueiam renderização da página

3. **Responsividade Limitada**:
   - **Breakpoints manuais**: Media queries para diferentes tamanhos
   - **Art direction**: Diferentes crops para mobile/desktop complexos
   - **Density handling**: Retina/high-DPI displays não otimizados
   - **Viewport units**: Imagens não se adaptam dinamicamente

4. **CDN e Caching**:
   - **CDN manual**: Configuração de CDN para imagens separadamente
   - **Cache headers**: Headers de cache configurados manualmente
   - **Invalidation**: Cache busting manual para novas versões
   - **Edge optimization**: Sem processamento nas edge locations

5. **Developer Experience**:
   - **Import complexo**: Webpack loaders para diferentes formatos
   - **Path management**: Caminhos relativos e absolutos confusos
   - **Hot reload**: Mudanças em imagens não refletem automaticamente
   - **Bundle analysis**: Impacto de imagens no bundle size não claro

## ✅ Vantagens da Otimização de Imagens Next.js

O Next.js revoluciona o gerenciamento de imagens com **otimizações automáticas e inteligentes**:

### Benefícios Principais:

1. **Otimização Automática**:
   - **Formatos modernos**: Conversão automática para WebP, AVIF
   - **Compressão inteligente**: Qualidade otimizada por tipo de imagem
   - **Responsive images**: Múltiplos tamanhos gerados automaticamente
   - **On-demand processing**: Imagens processadas quando solicitadas

2. **Performance Superior**:
   - **Lazy loading nativo**: Carregamento apenas quando visível
   - **CLS prevention**: Espaço reservado previne layout shift
   - **Priority loading**: Imagens above-the-fold carregadas primeiro
   - **Preload hints**: Resource hints automáticos para navegadores

3. **Responsividade Inteligente**:
   - **Device-aware**: Tamanho correto para cada dispositivo
   - **Density optimization**: Suporte automático para telas Retina
   - **Art direction**: Diferentes crops com `fill` e `object-fit`
   - **Viewport adaptation**: Imagens se adaptam ao container

4. **CDN e Edge Optimization**:
   - **Edge processing**: Imagens processadas nas edge locations
   - **Global CDN**: Distribuição automática via Vercel/outros CDNs
   - **Smart caching**: Cache otimizado por formato e tamanho
   - **Bandwidth saving**: Até 67% menos dados transferidos

5. **Developer Experience**:
   - **Import simples**: `import img from './image.jpg'` funciona nativamente
   - **TypeScript support**: Tipos automáticos para propriedades
   - **Hot reload**: Mudanças refletem instantaneamente
   - **Error handling**: Fallbacks automáticos para imagens quebradas

6. **Monitoring e Analytics**:
   - **Core Web Vitals**: LCP otimizado automaticamente
   - **Bundle analysis**: Impacto de imagens no bundle size
   - **Performance insights**: Métricas de carregamento de imagens
   - **A/B testing**: Diferentes estratégias de otimização

### Resultado:
**O componente Image do Next.js transforma otimização de imagens de um processo manual e propenso a erros em uma solução automática e inteligente**, oferecendo performance superior com zero configuração.

## O Componente Image do Next.js

O Next.js fornece um componente `Image` otimizado que estende a tag HTML `<img>` tradicional com recursos avançados:

- **Otimização automática**: Converte imagens para formatos modernos como WebP e AVIF
- **Redimensionamento responsivo**: Entrega o tamanho certo para cada dispositivo
- **Lazy loading nativo**: Carrega imagens apenas quando entram no viewport
- **Evita CLS (Cumulative Layout Shift)**: Reserva espaço antes do carregamento
- **CDN configurável**: Permite servir imagens de diferentes origens

### Importação e Uso Básico

```jsx
import Image from 'next/image';

export default function Produto() {
  return (
    <div>
      <h1>Produto Destaque</h1>
      <Image
        src="/images/produto.jpg"  // Caminho relativo à pasta `public`
        alt="Descrição do produto"
        width={500}
        height={300}
      />
    </div>
  );
}
```

### Importando Imagens Locais

Para imagens dentro do seu projeto, você pode importá-las diretamente:

```jsx
import Image from 'next/image';
import produtoImagem from '@/public/images/produto.jpg';

export default function Produto() {
  return (
    <div>
      <h1>Produto Destaque</h1>
      <Image
        src={produtoImagem}  // Objeto importado
        alt="Descrição do produto"
        placeholder="blur"   // Usa a versão em baixa resolução enquanto carrega
      />
    </div>
  );
}
```

## Configurações Avançadas

### Imagens Responsivas

```jsx
import Image from 'next/image';

export default function Hero() {
  return (
    <div className="relative h-[50vh] w-full">
      <Image
        src="/images/banner.jpg"
        alt="Banner da página inicial"
        fill               // Preenche o container pai
        sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
        style={{
          objectFit: 'cover',  // Cobre o container mantendo a proporção
        }}
        priority          // Carrega com alta prioridade (para imagens above the fold)
      />
    </div>
  );
}
```

### Quality e Formatos

```jsx
<Image
  src="/images/produto.jpg"
  alt="Descrição do produto"
  width={800}
  height={600}
  quality={85}           // Qualidade da imagem (0-100)
  formats={['avif', 'webp']}  // Prioriza estes formatos
/>
```

### Placeholder durante o Carregamento

```jsx
<Image
  src="/images/produto-grande.jpg"
  alt="Produto"
  width={1200}
  height={800}
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,iVBORw0KGgo..."  // Base64 de uma versão pequena da imagem
/>
```

Para imagens importadas, o placeholder blur é gerado automaticamente:

```jsx
import Image from 'next/image';
import produtoImagem from '@/public/images/produto.jpg';

// ...
<Image
  src={produtoImagem}
  alt="Produto"
  placeholder="blur"  // Gerado automaticamente para imagens importadas
/>
```

### Estilos e Classes

```jsx
<Image
  src="/images/produto.jpg"
  alt="Produto"
  width={500}
  height={300}
  className="rounded-lg shadow-md hover:opacity-90 transition-opacity"
  style={{ 
    maxWidth: '100%',
    height: 'auto' 
  }}
/>
```

## Imagens de Domínios Externos

Para utilizar imagens de origens externas, você precisa configurar os domínios permitidos no `next.config.js`:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  images: {
    domains: ['exemplo.com', 'cdn.exemplo.com', 'images.unsplash.com'],
  },
};

module.exports = nextConfig;
```

Então você pode usar:

```jsx
<Image
  src="https://images.unsplash.com/photo-1234567890"
  alt="Imagem do Unsplash"
  width={800}
  height={600}
/>
```

### Usando Remote Patterns

Para mais controle sobre quais URLs são permitidas:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: '**.exemplo.com',  // Permite qualquer subdomínio
        port: '',
        pathname: '/images/**',      // Apenas imagens neste caminho
      },
    ],
  },
};

module.exports = nextConfig;
```

## Carregamento sob Demanda

### Prioridade

Para imagens importantes acima da dobra (visíveis sem rolagem):

```jsx
<Image
  src="/images/hero.jpg"
  alt="Banner principal"
  width={1920}
  height={1080}
  priority  // Carrega imediatamente, ideal para LCP (Largest Contentful Paint)
/>
```

### Lazy Loading

O comportamento padrão é lazy load, mas você pode controlá-lo:

```jsx
<Image
  src="/images/conteudo.jpg"
  alt="Conteúdo"
  width={800}
  height={600}
  loading="lazy"  // Padrão
/>
```

## Otimização para Imagens Dinâmicas

### Usando Imagens de CMS ou API

Para imagens que vêm de um CMS, API ou banco de dados:

```jsx
export default function Produtos({ produtos }) {
  return (
    <div className="grid grid-cols-3 gap-4">
      {produtos.map(produto => (
        <div key={produto.id} className="produto-card">
          <Image
            src={produto.imagemUrl}
            alt={produto.nome}
            width={300}
            height={300}
            style={{ objectFit: 'contain' }}
          />
          <h3>{produto.nome}</h3>
          <p>{produto.preco}</p>
        </div>
      ))}
    </div>
  );
}
```

### Usando Image Loader Personalizado

Para integrar com serviços de imagem como Cloudinary, Imgix, etc:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  images: {
    loader: 'custom',
    loaderFile: './my-image-loader.js',
  },
};

module.exports = nextConfig;
```

```javascript
// my-image-loader.js
export default function myImageLoader({ src, width, quality }) {
  return `https://exemplo.com/resize?url=${src}&w=${width}&q=${quality || 75}`
}
```

```jsx
import Image from 'next/image';

export default function MeuComponente() {
  return (
    <Image
      src="imagem.jpg"
      width={500}
      height={300}
      alt="Descrição"
    />
  );
}
```

## Galerias e Carrosséis de Imagens

Para galerias ou carrosséis, combine o componente Image com bibliotecas como Swiper ou react-slick:

```jsx
'use client';

import Image from 'next/image';
import { Swiper, SwiperSlide } from 'swiper/react';
import 'swiper/css';

export default function GaleriaProduto({ imagens }) {
  return (
    <Swiper
      spaceBetween={10}
      slidesPerView={1}
      navigation
      pagination={{ clickable: true }}
    >
      {imagens.map((imagem, index) => (
        <SwiperSlide key={index}>
          <div className="relative h-[400px] w-full">
            <Image
              src={imagem.url}
              alt={imagem.alt || `Imagem ${index + 1}`}
              fill
              style={{ objectFit: 'contain' }}
            />
          </div>
        </SwiperSlide>
      ))}
    </Swiper>
  );
}
```

## Efeitos e Transformações

### Imagens com Hover Effects

```jsx
'use client';

import Image from 'next/image';
import { useState } from 'react';

export default function ImagemInterativa({ src, alt }) {
  const [isHovered, setIsHovered] = useState(false);
  
  return (
    <div 
      className="relative overflow-hidden rounded-lg"
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      <Image
        src={src}
        alt={alt}
        width={400}
        height={300}
        className={`transition-transform duration-300 ${isHovered ? 'scale-110' : 'scale-100'}`}
      />
      {isHovered && (
        <div className="absolute inset-0 bg-black bg-opacity-40 flex items-center justify-center">
          <span className="text-white text-xl font-bold">Ver Detalhes</span>
        </div>
      )}
    </div>
  );
}
```

### Imagens com Filtros CSS

```jsx
<Image
  src="/images/produto.jpg"
  alt="Produto com filtro"
  width={500}
  height={300}
  className="filter hover:brightness-110 hover:contrast-125 transition-filter duration-300"
/>
```

## Imagens em Diferentes Resoluções

### Art Direction com Picture

Para casos avançados de art direction (imagens diferentes para dispositivos diferentes):

```jsx
export default function ResponsiveHero() {
  return (
    <>
      {/* Imagem para mobile */}
      <div className="block md:hidden relative h-[300px]">
        <Image
          src="/images/hero-mobile.jpg"
          alt="Banner"
          fill
          style={{ objectFit: 'cover' }}
        />
      </div>
      
      {/* Imagem para tablet */}
      <div className="hidden md:block lg:hidden relative h-[400px]">
        <Image
          src="/images/hero-tablet.jpg"
          alt="Banner"
          fill
          style={{ objectFit: 'cover' }}
        />
      </div>
      
      {/* Imagem para desktop */}
      <div className="hidden lg:block relative h-[500px]">
        <Image
          src="/images/hero-desktop.jpg"
          alt="Banner"
          fill
          style={{ objectFit: 'cover' }}
        />
      </div>
    </>
  );
}
```

## Integrações Populares

### Cloudinary

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  images: {
    domains: ['res.cloudinary.com'],
  },
};

module.exports = nextConfig;
```

```jsx
<Image
  src="https://res.cloudinary.com/demo/image/upload/c_crop,g_face,h_400,w_400/r_max/c_scale,w_200/f_auto/exemplo.jpg"
  alt="Imagem do Cloudinary"
  width={200}
  height={200}
/>
```

### Contentful

```jsx
function getImageUrl(imageField) {
  return `https:${imageField.fields.file.url}`;
}

export default function PostContentful({ post }) {
  return (
    <article>
      <h1>{post.fields.title}</h1>
      <div className="relative h-[400px] w-full">
        <Image
          src={getImageUrl(post.fields.featuredImage)}
          alt={post.fields.featuredImage.fields.description || post.fields.title}
          fill
          style={{ objectFit: 'cover' }}
        />
      </div>
      <div>{post.fields.content}</div>
    </article>
  );
}
```

## Melhores Práticas

1. **Sempre defina width e height**: Previne Cumulative Layout Shift (CLS)
2. **Use `fill` para imagens que devem se adaptar ao container**: Combine com `position: relative` no container
3. **Defina o atributo `sizes`**: Otimiza o cache para diferentes tamanhos de tela
4. **Use `priority` para imagens LCP**: Melhora o Core Web Vitals
5. **Utilize `placeholder="blur"`**: Melhora a experiência de carregamento
6. **Escolha o formato correto**: WebP e AVIF são mais eficientes que JPEG/PNG
7. **Não desative a otimização**: A menos que realmente necessário
8. **Use SVGO para otimizar SVGs**: Reduz o tamanho dos arquivos SVG

## Solução de Problemas Comuns

### Imagens não aparecem

Verifique:
- Os domínios estão configurados para imagens externas
- As dimensões (width/height) estão definidas corretamente
- O caminho da imagem está correto
- Para imagens locais, estão na pasta `public`

### Layout shift (CLS)

Solução:
- Defina width e height explicitamente
- Use o atributo `fill` com um container de tamanho fixo
- Defina `style={{ objectFit: 'contain' }}` ou `cover`

### Imagens distorcidas

Solução:
- Use `objectFit: 'contain'` para manter a proporção sem cortes
- Use `objectFit: 'cover'` para cobrir o container sem distorção
- Use aspect ratio no container para manter a proporção correta

## Recursos Adicionais

- [Documentação oficial do Next.js Image](https://nextjs.org/docs/app/building-your-application/optimizing/images)
- [API Reference do componente Image](https://nextjs.org/docs/app/api-reference/components/image)
- [Guia de otimização Vercel](https://vercel.com/docs/concepts/image-optimization)

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=00A0D2&height=120&section=footer"/>