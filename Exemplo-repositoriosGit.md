Vou guiar você na criação de um projeto Next.js com TypeScript e Tailwind, incluindo um template básico.

# 1. Criar o Projeto

```bash
npx create-next-app@latest my-nextjs-template --typescript --tailwind --eslint
cd my-nextjs-template
```

# 2. Estrutura de Pastas

```
src/
  ├── components/
  │   ├── layout/
  │   │   ├── Header.tsx
  │   │   ├── Footer.tsx
  │   │   └── Main.tsx
  │   └── ui/
  ├── pages/
  │   ├── _app.tsx
  │   └── index.tsx
  └── styles/
      └── globals.css
```

# 3. Componentes do Layout

Header.tsx:
```typescript
export default function Header() {
  return (
    <header className="bg-gray-800 text-white p-4">
      <nav className="container mx-auto flex justify-between items-center">
        <h1 className="text-xl font-bold">Meu Site</h1>
        <ul className="flex gap-4">
          <li><a href="/" className="hover:text-gray-300">Home</a></li>
          <li><a href="/about" className="hover:text-gray-300">Sobre</a></li>
        </ul>
      </nav>
    </header>
  );
}
```

Footer.tsx:
```typescript
export default function Footer() {
  return (
    <footer className="bg-gray-800 text-white p-4 mt-auto">
      <div className="container mx-auto text-center">
        <p>&copy; {new Date().getFullYear()} Meu Site. Todos os direitos reservados.</p>
      </div>
    </footer>
  );
}
```

Main.tsx:
```typescript
interface MainProps {
  children: React.ReactNode;
}

export default function Main({ children }: MainProps) {
  return (
    <main className="container mx-auto px-4 py-8 flex-grow">
      {children}
    </main>
  );
}
```

# 4. Configurar _app.tsx

```typescript
import '@/styles/globals.css';
import type { AppProps } from 'next/app';
import Header from '@/components/layout/Header';
import Footer from '@/components/layout/Footer';
import Main from '@/components/layout/Main';

export default function App({ Component, pageProps }: AppProps) {
  return (
    <div className="min-h-screen flex flex-col">
      <Header />
      <Main>
        <Component {...pageProps} />
      </Main>
      <Footer />
    </div>
  );
}
```

# 5. Página Inicial (index.tsx)

```typescript
export default function Home() {
  return (
    <div className="space-y-4">
      <h1 className="text-3xl font-bold">Bem-vindo!</h1>
      <p>Este é um template básico para seu projeto Next.js.</p>
    </div>
  );
}
```

# Desafio

Crie uma página que consuma a API pública do GitHub:
1. Crie um arquivo `pages/github.tsx`
2. Use `fetch` e `useEffect` para buscar seus repositórios públicos
3. Exiba os dados em cards usando Tailwind
4. Adicione um link no Header

Exemplo da implementação:

```typescript
import { useEffect, useState } from 'react';

interface Repository {
  id: number;
  name: string;
  description: string;
  html_url: string;
}

export default function GitHub() {
  const [repos, setRepos] = useState<Repository[]>([]);

  useEffect(() => {
    fetch('https://api.github.com/users/SEU_USERNAME/repos')
      .then(res => res.json())
      .then(data => setRepos(data));
  }, []);

  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
      {repos.map(repo => (
        <div key={repo.id} className="p-4 border rounded-lg shadow hover:shadow-md">
          <h2 className="text-xl font-bold">{repo.name}</h2>
          <p className="text-gray-600">{repo.description}</p>
          <a href={repo.html_url} className="text-blue-500 hover:underline">
            Ver no GitHub
          </a>
        </div>
      ))}
    </div>
  );
}
```

Execute `npm run dev` e acesse http://localhost:3000 para ver seu projeto.

## Observações
- Ao criar 'github.tsx' na pasta 'pages', é só direcionar o link para '/github'.

```jsx
<li>
  <Link href="/github" className="hover:text-gray-300">
    Repositórios
  </Link>
</li>
```

### Sobre o funcionamento do '_app.tsx'

O `_app.tsx` é o componente raiz do Next.js que envolve todas as páginas. Vamos analisar suas props:

```typescript
function App({ Component, pageProps }: AppProps) {
  // ...
}
```

- `Component`: A página atual que está sendo renderizada (o componente correspondente ao URL)
- `pageProps`: Props que são pré-carregadas para a página através de `getStaticProps` ou `getServerSideProps`

Quando você navega para `/about`, por exemplo:
- `Component` será o conteúdo de `pages/about.tsx`
- Este conteúdo é renderizado dentro do `<Main>` em seu layout
- O `Header` e `Footer` permanecem consistentes em todas as páginas

O fluxo de renderização é:
```
_app.tsx
  └── Header
  └── Main
      └── Component (página atual)
  └── Footer
```
