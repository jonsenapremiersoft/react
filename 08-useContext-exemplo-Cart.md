# Implementação de Carrinho de Compras com Context API

## Configuração Inicial

Primeiro, crie um novo projeto Next.js com TypeScript:

```bash
npx create-next-app@latest shopping-cart
cd shopping-cart
```

Selecione as seguintes opções durante a criação:
- TypeScript: Yes
- ESLint: No
- Tailwind CSS: Yes
- `src/` directory: Yes
- App Router: Yes

## Estrutura de Pastas

```
src/
  ├── app/
  │   ├── layout.tsx
  │   └── page.tsx
  ├── components/
  │   ├── ProductList.tsx
  │   └── Cart.tsx
  └── contexts/
      └── CartContext.tsx
```

## Implementação do Context API

Primeiro, vamos criar o Context que gerenciará nosso carrinho de compras. Crie o arquivo `src/contexts/CartContext.tsx`:

```typescript
import { createContext, useContext, useState, ReactNode } from 'react';

// Definindo as interfaces
interface Product {
  id: number;
  name: string;
  price: number;
  image: string;
}

interface CartItem extends Product {
  quantity: number;
}

interface CartContextType {
  items: CartItem[];
  addToCart: (product: Product) => void;
  removeFromCart: (productId: number) => void;
  updateQuantity: (productId: number, quantity: number) => void;
  clearCart: () => void;
  total: number;
}

// Criando o Context
const CartContext = createContext<CartContextType | undefined>(undefined);

// Provider Component
export function CartProvider({ children }: { children: ReactNode }) {
  const [items, setItems] = useState<CartItem[]>([]);

  // Função para adicionar item ao carrinho
  const addToCart = (product: Product) => {
    setItems(currentItems => {
      const existingItem = currentItems.find(item => item.id === product.id);
      
      if (existingItem) {
        return currentItems.map(item =>
          item.id === product.id
            ? { ...item, quantity: item.quantity + 1 }
            : item
        );
      }
      
      return [...currentItems, { ...product, quantity: 1 }];
    });
  };

  // Função para remover item do carrinho
  const removeFromCart = (productId: number) => {
    setItems(currentItems => 
      currentItems.filter(item => item.id !== productId)
    );
  };

  // Função para atualizar quantidade
  const updateQuantity = (productId: number, quantity: number) => {
    if (quantity < 1) return;
    
    setItems(currentItems =>
      currentItems.map(item =>
        item.id === productId
          ? { ...item, quantity }
          : item
      )
    );
  };

  // Função para limpar o carrinho
  const clearCart = () => {
    setItems([]);
  };

  // Calculando o total
  const total = items.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  );

  return (
    <CartContext.Provider 
      value={{ 
        items, 
        addToCart, 
        removeFromCart, 
        updateQuantity, 
        clearCart, 
        total 
      }}
    >
      {children}
    </CartContext.Provider>
  );
}

// Hook personalizado para usar o carrinho
export function useCart() {
  const context = useContext(CartContext);
  if (context === undefined) {
    throw new Error('useCart must be used within a CartProvider');
  }
  return context;
}
```

## Layout Principal

Atualize o arquivo `src/app/layout.tsx` para incluir o CartProvider:

```typescript
import { CartProvider } from '@/contexts/CartContext'

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>
        <CartProvider>
          {children}
        </CartProvider>
      </body>
    </html>
  )
}
```

## Lista de Produtos

Crie o componente `src/components/ProductList.tsx`:

```typescript
import { useCart } from '@/contexts/CartContext';

const products = [
  {
    id: 1,
    name: "Tênis Nike",
    price: 299.99,
    image: "/api/placeholder/200/200"
  },
  {
    id: 2,
    name: "Camiseta Adidas",
    price: 89.99,
    image: "/api/placeholder/200/200"
  },
  // Adicione mais produtos conforme necessário
];

export default function ProductList() {
  const { addToCart } = useCart();

  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 p-4">
      {products.map((product) => (
        <div 
          key={product.id}
          className="border rounded-lg p-4 shadow-sm hover:shadow-md transition-shadow"
        >
          <img 
            src={product.image}
            alt={product.name}
            className="w-full h-48 object-cover rounded-md"
          />
          <h3 className="mt-2 text-lg font-semibold">{product.name}</h3>
          <p className="text-gray-600">
            R$ {product.price.toFixed(2)}
          </p>
          <button
            onClick={() => addToCart(product)}
            className="mt-2 w-full bg-blue-500 text-white py-2 rounded-md hover:bg-blue-600 transition-colors"
          >
            Adicionar ao Carrinho
          </button>
        </div>
      ))}
    </div>
  );
}
```

## Componente do Carrinho

Crie o arquivo `src/components/Cart.tsx`:

```typescript
import { useCart } from '@/contexts/CartContext';

export default function Cart() {
  const { items, removeFromCart, updateQuantity, total, clearCart } = useCart();

  if (items.length === 0) {
    return (
      <div className="p-4 text-center">
        <p>Seu carrinho está vazio</p>
      </div>
    );
  }

  return (
    <div className="p-4">
      {items.map((item) => (
        <div 
          key={item.id}
          className="flex items-center justify-between border-b py-4"
        >
          <div className="flex items-center gap-4">
            <img 
              src={item.image}
              alt={item.name}
              className="w-16 h-16 object-cover rounded"
            />
            <div>
              <h4 className="font-medium">{item.name}</h4>
              <p className="text-gray-600">R$ {item.price.toFixed(2)}</p>
            </div>
          </div>
          
          <div className="flex items-center gap-4">
            <div className="flex items-center gap-2">
              <button
                onClick={() => updateQuantity(item.id, item.quantity - 1)}
                className="px-2 py-1 border rounded"
              >
                -
              </button>
              <span>{item.quantity}</span>
              <button
                onClick={() => updateQuantity(item.id, item.quantity + 1)}
                className="px-2 py-1 border rounded"
              >
                +
              </button>
            </div>
            
            <button
              onClick={() => removeFromCart(item.id)}
              className="text-red-500 hover:text-red-700"
            >
              Remover
            </button>
          </div>
        </div>
      ))}
      
      <div className="mt-4 flex justify-between items-center">
        <button
          onClick={clearCart}
          className="text-red-500 hover:text-red-700"
        >
          Limpar Carrinho
        </button>
        <div className="text-xl font-bold">
          Total: R$ {total.toFixed(2)}
        </div>
      </div>
    </div>
  );
}
```

## Página Principal

Atualize o arquivo `src/app/page.tsx`:

```typescript
import ProductList from '@/components/ProductList';
import Cart from '@/components/Cart';

export default function Home() {
  return (
    <main className="max-w-7xl mx-auto">
      <h1 className="text-3xl font-bold text-center my-8">
        Loja Virtual
      </h1>
      
      <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
        <div className="lg:col-span-2">
          <ProductList />
        </div>
        
        <div className="bg-gray-50 p-4 rounded-lg">
          <h2 className="text-xl font-bold mb-4">Carrinho de Compras</h2>
          <Cart />
        </div>
      </div>
    </main>
  );
}
```

## Explicação do Funcionamento

### Context API

O Context API é utilizado para gerenciar o estado global do carrinho. Principais funcionalidades:

1. **Estado do Carrinho**: Armazena um array de itens, cada um com quantidade.

2. **Funções de Manipulação**:
   - `addToCart`: Adiciona produtos ou incrementa quantidade
   - `removeFromCart`: Remove produtos do carrinho
   - `updateQuantity`: Atualiza a quantidade de um produto
   - `clearCart`: Limpa todo o carrinho
   - `total`: Calcula o valor total em tempo real

### Componentes

1. **ProductList**:
   - Exibe grid responsivo de produtos
   - Cada produto tem imagem, nome, preço e botão de adicionar
   - Usa Tailwind para layout e estilização

2. **Cart**:
   - Mostra itens no carrinho
   - Permite ajustar quantidade ou remover itens
   - Exibe total e opção de limpar carrinho
   - Layout responsivo com Tailwind

### Layout Responsivo

- Mobile: Uma coluna única
- Desktop: Grid de 3 colunas com carrinho na lateral
- Usa classes do Tailwind para responsividade

## Principais Funcionalidades

1. **Adição de Produtos**:
   - Verifica se produto já existe
   - Incrementa quantidade ou adiciona novo item

2. **Gerenciamento de Quantidade**:
   - Botões + e - para ajustar
   - Não permite quantidades negativas
   - Atualiza total automaticamente

3. **Remoção de Itens**:
   - Remove item específico
   - Opção de limpar carrinho inteiro

4. **Cálculo de Total**:
   - Atualizado em tempo real
   - Considera preço e quantidade de cada item
