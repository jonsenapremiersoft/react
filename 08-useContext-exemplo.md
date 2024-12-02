# Implementando um Theme Switcher com useContext

## 0. Inicie o projeto

```bash
npx create-next-app@latest exemplo-usecontext --typescript --tailwind --app --src-dir
```

## 1. Crie o contexto do tema

```typescript
// contexts/ThemeContext.tsx
'use client'

import { createContext, useContext, useState, ReactNode } from 'react'

type Theme = 'light' | 'dark'

interface ThemeContextType {
  theme: Theme
  toggleTheme: () => void
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined)

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<Theme>('light')

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light')
  }

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      <div className={theme}>
        {children}
      </div>
    </ThemeContext.Provider>
  )
}

export function useTheme() {
  const context = useContext(ThemeContext)
  if (context === undefined) {
    throw new Error('useTheme must be used within a ThemeProvider')
  }
  return context
}
```

## 2. Configure o layout raiz

```typescript
// app/layout.tsx
import { ThemeProvider } from '@/contexts/ThemeContext'
import './globals.css'

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>
        <ThemeProvider>
          {children}
        </ThemeProvider>
      </body>
    </html>
  )
}
```

## 3. Atualize seu arquivo globals.css

```css
/* app/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  .light {
    --bg-primary: #ffffff;
    --text-primary: #000000;
  }
  
  .dark {
    --bg-primary: #1a1a1a;
    --text-primary: #ffffff;
  }
}

body {
  background-color: var(--bg-primary);
  color: var(--text-primary);
}
```

## 4. Crie um componente de botão para alternar o tema

```typescript
// components/ThemeToggle.tsx
'use client'

import { useTheme } from '@/contexts/ThemeContext'

export function ThemeToggle() {
  const { theme, toggleTheme } = useTheme()

  return (
    <button 
      onClick={toggleTheme}
      className="px-4 py-2 rounded-lg bg-blue-500 text-white hover:bg-blue-600 transition-colors"
    >
      {theme === 'light' ? '🌙' : '☀️'}
    </button>
  )
}
```

## 5. Use o tema na página principal

```typescript
// app/page.tsx
import { ThemeToggle } from '@/components/ThemeToggle'

export default function Home() {
  return (
    <main className="min-h-screen p-8">
      <div className="max-w-2xl mx-auto">
        <div className="flex justify-between items-center mb-8">
          <h1 className="text-2xl font-bold">Theme Switcher Example</h1>
          <ThemeToggle />
        </div>
        
        <div className="p-6 rounded-lg border border-gray-200 dark:border-gray-700">
          <p>Este conteúdo se adapta ao tema atual!</p>
        </div>
      </div>
    </main>
  )
}
```

## Feature:

**Persistência do Tema**

Implemente a persistência do tema usando `localStorage` para que a preferência do usuário seja mantida mesmo após recarregar a página. Você precisará:

1. Modificar o `ThemeContext` para verificar o localStorage ao inicializar
2. Atualizar o localStorage quando o tema mudar
3. Adicionar um efeito para sincronizar o tema com o localStorage

Dicas:
- Use `useEffect` para lidar com o localStorage
- Lembre-se que o localStorage só está disponível no cliente
- Considere usar um valor padrão caso não haja tema salvo

Bônus:
- Implemente uma animação suave na transição entre temas

