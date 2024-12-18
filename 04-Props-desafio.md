# Desafio:

1. Crie um array com uma lista de produtos
2. Utilize essa lista para renderizar os <ProductCard> na Home

```jsx
import React from 'react';

const ProductCard = ({ title, description, price, onSale }) => {
  return (
    <div className="max-w-sm rounded overflow-hidden shadow-lg p-4 bg-white">
      <h2 className="font-bold text-xl mb-2">{title}</h2>
      <p className="text-gray-700 text-base mb-4">{description}</p>
      <div className="flex justify-between items-center">
        <span className={`text-lg ${onSale ? 'text-red-600' : 'text-gray-900'}`}>
          R$ {price}
        </span>
        {onSale && (
          <span className="bg-red-100 text-red-800 text-xs font-medium px-2.5 py-0.5 rounded">
            Promoção
          </span>
        )}
      </div>
    </div>
  );
};

// Exemplo de uso:
const Home = () => (
  <div className="bg-gray-100 p-8">
    <ProductCard
      title="Notebook Pro"
      description="Notebook com 16GB RAM, SSD 512GB"
      price="3499,99"
      onSale={true}
    />
  </div>
);

export default Home;
```
