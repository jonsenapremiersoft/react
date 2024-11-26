# Introdução aos Hooks no React

Os Hooks são uma adição ao React a partir da versão 16.8 que revolucionou a forma como escrevemos componentes. Eles nos permitem usar estado e outros recursos do React sem escrever componentes de classe.

### Por que Hooks foram criados?

Antes dos Hooks, tínhamos dois problemas principais:

1. Era difícil reutilizar lógica de estado entre componentes
2. Componentes complexos se tornavam difíceis de entender
3. Classes confundiam tanto pessoas quanto máquinas

### O que são Hooks?

Hooks são funções especiais que permitem você "conectar-se" aos recursos do React. O nome "Hook" vem do inglês, significando "gancho", justamente porque eles se "engancham" nos recursos do React.

### Regras básicas dos Hooks:

1. Só podemos chamar Hooks no nível mais alto do componente
2. Só podemos chamar Hooks dentro de componentes funcionais React ou dentro de outros Hooks customizados
3. Não podemos chamar Hooks dentro de condições, loops ou funções aninhadas

### Exemplo básico:

```jsx
import React, { useState } from 'react';

function ExemploHook() {
  const [contador, setContador] = useState(0);

  return (
    <div>
      <p>Você clicou {contador} vezes</p>
      <button onClick={() => setContador(contador + 1)}>
        Clique aqui
      </button>
    </div>
  );
}
```

### Benefícios dos Hooks:

1. Código mais limpo e conciso
2. Melhor reutilização de lógica
3. Mais fácil de testar
4. Menos código boilerplate
5. Melhor composição de funcionalidades
