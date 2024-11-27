# Exemplos de useState

---

## O que é `useState`?

O `useState` é um **hook** que retorna um par de valores:
1. O valor atual do estado.
2. Uma função para atualizar esse valor.

A sintaxe básica é:

```javascript
const [state, setState] = useState(initialValue);
```

- `state`: o valor atual do estado.
- `setState`: função que atualiza o estado.
- `initialValue`: o valor inicial do estado (pode ser de qualquer tipo).

Agora, vamos explorar exemplos práticos com diferentes tipos de dados.

---

## 1. Trabalhando com `string`

### Exemplo: Atualizar uma mensagem de boas-vindas

```javascript
import React, { useState } from "react";

function StringExample() {
  const [message, setMessage] = useState("Bem-vindo!");

  return (
    <div>
      <h1>{message}</h1>
      <button onClick={() => setMessage("Olá, visitante!")}>
        Alterar Mensagem
      </button>
    </div>
  );
}

export default StringExample;
```

**Descrição:** Aqui o estado inicial é uma `string`. O botão altera a mensagem usando a função `setMessage`.

---

## 2. Trabalhando com `number`

### Exemplo: Contador simples

```javascript
import React, { useState } from "react";

function NumberExample() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>Contador: {count}</h1>
      <button onClick={() => setCount(count + 1)}>Incrementar</button>
      <button onClick={() => setCount(count - 1)}>Decrementar</button>
    </div>
  );
}

export default NumberExample;
```

**Descrição:** O estado inicial é um número (`0`). O botão atualiza o contador, incrementando ou decrementando.

---

## 3. Trabalhando com `boolean`

### Exemplo: Alternar entre dois estados

```javascript
import React, { useState } from "react";

function BooleanExample() {
  const [isOn, setIsOn] = useState(false);

  return (
    <div>
      <h1>{isOn ? "Ligado" : "Desligado"}</h1>
      <button onClick={() => setIsOn(!isOn)}>Alternar</button>
    </div>
  );
}

export default BooleanExample;
```

**Descrição:** Aqui usamos um `boolean` para alternar entre dois estados ("Ligado" e "Desligado").

---

## 4. Trabalhando com `array`

### Exemplo: Lista de tarefas

```javascript
import React, { useState } from "react";

function ArrayExample() {
  const [tasks, setTasks] = useState(["Tarefa 1", "Tarefa 2"]);

  const addTask = () => {
    setTasks([...tasks, `Tarefa ${tasks.length + 1}`]);
  };

  return (
    <div>
      <h1>Lista de Tarefas</h1>
      <ul>
        {tasks.map((task, index) => (
          <li key={index}>{task}</li>
        ))}
      </ul>
      <button onClick={addTask}>Adicionar Tarefa</button>
    </div>
  );
}

export default ArrayExample;
```

**Descrição:** O estado é um `array` de strings. O botão adiciona uma nova tarefa ao final do array.

---

## 5. Trabalhando com `object`

### Exemplo: Atualizar informações de perfil

```javascript
import React, { useState } from "react";

function ObjectExample() {
  const [profile, setProfile] = useState({
    name: "João",
    age: 25,
  });

  const updateName = () => setProfile({ ...profile, name: "Maria" });
  const updateAge = () => setProfile({ ...profile, age: profile.age + 1 });

  return (
    <div>
      <h1>Perfil</h1>
      <p>Nome: {profile.name}</p>
      <p>Idade: {profile.age}</p>
      <button onClick={updateName}>Alterar Nome</button>
      <button onClick={updateAge}>Aumentar Idade</button>
    </div>
  );
}

export default ObjectExample;
```

**Descrição:** O estado é um `object`. Usamos o operador spread (`...`) para atualizar campos específicos sem perder os outros.

---

## 6. Trabalhando com estado inicial gerado dinamicamente

### Exemplo: Criar estado inicial com base em cálculo

```javascript
import React, { useState } from "react";

function DynamicInitialState() {
  const [randomNumber, setRandomNumber] = useState(() => Math.floor(Math.random() * 100));

  const generateNewNumber = () => setRandomNumber(Math.floor(Math.random() * 100));

  return (
    <div>
      <h1>Número Aleatório: {randomNumber}</h1>
      <button onClick={generateNewNumber}>Gerar Novo Número</button>
    </div>
  );
}

export default DynamicInitialState;
```

**Descrição:** Usamos uma função para calcular o estado inicial dinamicamente (`Math.random`).

---

## Dicas Importantes

1. **Não modifique o estado diretamente.** Sempre use a função de atualização fornecida pelo `useState`.
   ```javascript
   // Correto:
   setState(newState);
   // Errado:
   state = newState; // Isso não atualiza o estado!
   ```
2. **Estados complexos podem usar o operador spread para preservar valores.**
   ```javascript
   setState({ ...state, newProperty: value });
   ```
3. **Para estados derivados, considere usar `useReducer`.** Quando o estado ficar muito complexo, `useReducer` pode ser uma alternativa.

---
