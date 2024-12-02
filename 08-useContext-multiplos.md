# Como usar dois Providers do `createContext` no `layout.tsx`

Se você precisa usar dois ou mais **providers** do `createContext` em seu `layout.tsx`, pode aninhá-los ao redor dos seus `children`. Aqui está uma explicação completa:

---

## 1. Criando os Contextos

### `ThemeContext`
Este contexto gerencia o tema (`light` ou `dark`):

```tsx
import { createContext, useState, useContext, ReactNode } from "react";

type Theme = "light" | "dark";

interface ThemeContextType {
  theme: Theme;
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<Theme>("light");

  const toggleTheme = () => setTheme((prev) => (prev === "light" ? "dark" : "light"));

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error("useTheme must be used within a ThemeProvider");
  }
  return context;
}
```

### `AuthContext`
Este contexto gerencia a autenticação:

```tsx
import { createContext, useContext, useState, ReactNode } from "react";

interface AuthContextType {
  isAuthenticated: boolean;
  login: () => void;
  logout: () => void;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export function AuthProvider({ children }: { children: ReactNode }) {
  const [isAuthenticated, setIsAuthenticated] = useState(false);

  const login = () => setIsAuthenticated(true);
  const logout = () => setIsAuthenticated(false);

  return (
    <AuthContext.Provider value={{ isAuthenticated, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (context === undefined) {
    throw new Error("useAuth must be used within an AuthProvider");
  }
  return context;
}
```

## 2. Aninhando os Providers no layout.tsx

Você pode combinar os dois providers diretamente no componente de layout:

```jsx
import { ReactNode } from "react";
import { ThemeProvider } from "@/contexts/ThemeContext";
import { AuthProvider } from "@/contexts/AuthContext";

export default function RootLayout({ children }: { children: ReactNode }) {
  return (
    <html lang="en">
      <body>
        {/* Aninhando os providers */}
        <ThemeProvider>
          <AuthProvider>{children}</AuthProvider>
        </ThemeProvider>
      </body>
    </html>
  );
}
```

## 3. Usando os Contextos nos Componentes

Agora você pode acessar os valores dos contextos usando os hooks useTheme e useAuth:

```jsx
import { useTheme } from "@/contexts/ThemeContext";
import { useAuth } from "@/contexts/AuthContext";

export function SomeComponent() {
  const { theme, toggleTheme } = useTheme();
  const { isAuthenticated, login, logout } = useAuth();

  return (
    <div>
      <p>Current Theme: {theme}</p>
      <button onClick={toggleTheme}>Toggle Theme</button>

      <p>Authenticated: {isAuthenticated ? "Yes" : "No"}</p>
      <button onClick={isAuthenticated ? logout : login}>
        {isAuthenticated ? "Logout" : "Login"}
      </button>
    </div>
  );
}
```

## 4. Criando um Componente de Agregação de Providers (Opcional)

Se você tiver muitos contextos, pode criar um componente de agregação para simplificar:

### Componente de agregação
```jsx
function AppProviders({ children }: { children: ReactNode }) {
  return (
    <ThemeProvider>
      <AuthProvider>{children}</AuthProvider>
    </ThemeProvider>
  );
}
```

### Usando o agregador no `layout.tsx`

```jsx
export default function RootLayout({ children }: { children: ReactNode }) {
  return (
    <html lang="en">
      <body>
        <AppProviders>{children}</AppProviders>
      </body>
    </html>
  );
}
```
