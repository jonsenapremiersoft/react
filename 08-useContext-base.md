# Hook useContext

## 1. O que é Context e por que usar?

Imagine que você tem uma árvore de componentes assim:

```typescript
<App>
  <Header>
    <Navigation>
      <UserProfile /> // Precisa dos dados do usuário
    </Navigation>
  </Header>
  <MainContent>
    <UserSettings /> // Também precisa dos dados do usuário
  </MainContent>
  <Footer>
    <UserLinks /> // Também precisa dos dados do usuário
  </Footer>
</App>
```

Sem Context, você precisaria passar os dados do usuário através de props por todos esses componentes, mesmo que os componentes intermediários não usem esses dados. Isso é chamado de "prop drilling" e pode tornar seu código difícil de manter.

## 2. Criando o Contexto

Vamos analisar a criação do contexto em detalhes:

```typescript
// contexts/UserContext.ts
import { createContext } from 'react';

// Primeiro, definimos o tipo dos dados que vamos compartilhar
type User = {
  id: string;
  name: string;
  email: string;
};

// Depois, definimos o tipo do contexto
type UserContextType = {
  user: User | null;
  setUser: (user: User | null) => void;
  isLoading: boolean;
};

// Criamos o contexto com um valor inicial undefined
export const UserContext = createContext<UserContextType | undefined>(undefined);
```

**Explicação:**
- `createContext`: Função do React que cria um novo contexto
- O tipo genérico `<UserContextType | undefined>` indica que nosso contexto pode ser do tipo UserContextType ou undefined
- O valor inicial `undefined` é importante para TypeScript, mas será substituído pelo Provider

## 3. Criando o Provider

O Provider é um componente que vai "fornecer" os dados para todos os componentes filhos:

```typescript
// contexts/UserProvider.tsx
'use client';

import { ReactNode, useState } from 'react';
import { UserContext } from './UserContext';

type UserProviderProps = {
  children: ReactNode;
};

export function UserProvider({ children }: UserProviderProps) {
  // Estado local que será compartilhado
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);

  // Podemos adicionar funções que manipulam o estado
  const loginUser = async (email: string, password: string) => {
    setIsLoading(true);
    try {
      // Lógica de login aqui
      const userData = await fetchUserData(email, password);
      setUser(userData);
    } finally {
      setIsLoading(false);
    }
  };

  // O value contém todos os dados e funções que queremos compartilhar
  const value = {
    user,
    setUser,
    isLoading,
    loginUser
  };

  return (
    <UserContext.Provider value={value}>
      {children}
    </UserContext.Provider>
  );
}
```

**Explicação:**
- `'use client'`: Necessário no Next.js 13+ para componentes que usam hooks
- `children`: Representa todos os componentes que serão envolvidos pelo Provider
- `useState`: Gerencia o estado que será compartilhado
- `value`: Objeto com todos os dados e funções que queremos disponibilizar

## 4. Criando um Hook Customizado

O hook customizado torna o uso do contexto mais seguro e conveniente:

```typescript
// hooks/useUser.ts
import { useContext } from 'react';
import { UserContext } from '@/contexts/UserContext';

export function useUser() {
  const context = useContext(UserContext);
  
  if (context === undefined) {
    throw new Error('useUser deve ser usado dentro de um UserProvider');
  }
  
  return context;
}
```

**Explicação:**
- O hook customizado encapsula a lógica de usar o contexto
- A verificação de `undefined` garante que o hook só seja usado dentro do Provider
- Retorna o contexto tipado corretamente, facilitando o uso com TypeScript

## 5. Configurando o Provider na Aplicação

```typescript
// app/layout.tsx
import { UserProvider } from '@/contexts/UserProvider';

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="pt-BR">
      <body>
        <UserProvider>
          {children}
        </UserProvider>
      </body>
    </html>
  );
}
```

**Explicação:**
- O Provider é colocado no layout para que todos os componentes da aplicação tenham acesso ao contexto
- Você pode ter múltiplos Providers aninhados se necessário

## 6. Usando o Contexto nos Componentes

```typescript
// components/UserProfile.tsx
'use client';

import { useUser } from '@/hooks/useUser';

export function UserProfile() {
  const { user, isLoading } = useUser();

  if (isLoading) {
    return <div>Carregando...</div>;
  }

  if (!user) {
    return <div>Usuário não logado</div>;
  }

  return (
    <div>
      <h2>Perfil do Usuário</h2>
      <p>Nome: {user.name}</p>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

**Explicação:**
- O componente usa o hook customizado `useUser` para acessar os dados
- Não precisa receber props para ter acesso aos dados do usuário
- Pode acessar tanto dados quanto funções do contexto

## Dicas Importantes:

1. **Performance**:
   - O Context não é otimizado para atualizações frequentes
   - Para dados que mudam muito, considere usar outras soluções como Redux ou Zustand

2. **Organização**:
   - Mantenha contextos separados para diferentes tipos de dados
   - Não coloque tudo em um único contexto global

3. **TypeScript**:
   - Sempre defina tipos explícitos para seus contextos
   - Use o hook customizado para ter melhor inferência de tipos

4. **Next.js**:
   - Lembre-se de usar 'use client' em componentes que usam hooks
   - O Provider geralmente vai no layout.tsx principal
