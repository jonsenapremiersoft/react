# Entendendo o Gerenciamento de Estado no React com useState

## 1. O que é Estado?

O estado (state) em React é:
- Um objeto JavaScript que armazena dados dinâmicos
- Dados que podem mudar durante o ciclo de vida do componente
- Informações que, quando alteradas, causam uma nova renderização

### 1.1 Por que precisamos de Estado?

```jsx
// ❌ Abordagem INCORRETA - Variável comum
function Contador() {
  let contador = 0;
  
  const incrementar = () => {
    contador += 1; // Isso não causará re-renderização
    console.log(contador); // O valor muda, mas a UI não atualiza
  };

  return (
    <div>
      <p>Contagem: {contador}</p>
      <button onClick={incrementar}>Incrementar</button>
    </div>
  );
}

// ✅ Abordagem CORRETA - Usando useState
function Contador() {
  const [contador, setContador] = useState(0);
  
  const incrementar = () => {
    setContador(contador + 1); // Causa re-renderização e atualiza a UI
  };

  return (
    <div>
      <p>Contagem: {contador}</p>
      <button onClick={incrementar}>Incrementar</button>
    </div>
  );
}
```

## 2. Anatomia do useState

```jsx
const [estado, setEstado] = useState(valorInicial);
```

### 2.1 Desestruturação do Array
O useState retorna um array com exatamente 2 elementos:
```jsx
// Isso:
const [count, setCount] = useState(0);

// É o mesmo que:
const stateArray = useState(0);
const count = stateArray[0];     // Valor atual
const setCount = stateArray[1];  // Função atualizadora
```

### 2.2 Valor Inicial (Initial State)

O valor inicial pode ser de qualquer tipo:

```jsx
// Tipos primitivos
const [texto, setTexto] = useState("");
const [numero, setNumero] = useState(0);
const [booleano, setBooleano] = useState(false);

// Arrays
const [lista, setLista] = useState([]);

// Objetos
const [usuario, setUsuario] = useState({
  nome: "",
  email: ""
});

// Função inicializadora (lazy initial state)
const [estado, setEstado] = useState(() => {
  const valorComplexo = calcularValorInicial(); // Executado apenas na montagem
  return valorComplexo;
});
```

## 3. Como o Estado Funciona

### 3.1 Ciclo de Vida do Estado

```jsx
function ExemploCicloVida() {
  const [contador, setContador] = useState(0);

  console.log("1. Componente renderiza com contador =", contador);

  const incrementar = () => {
    console.log("2. Botão clicado, valor atual =", contador);
    setContador(contador + 1);
    console.log("3. setState chamado, próximo valor será =", contador + 1);
    // Note que o contador ainda não mudou aqui!
    console.log("4. Valor atual ainda é =", contador);
  };

  return (
    <div>
      <p>Contador: {contador}</p>
      <button onClick={incrementar}>Incrementar</button>
    </div>
  );
}
```

### 3.2 Atualizações em Lote (Batching)

```jsx
function ExemploBatching() {
  const [contador, setContador] = useState(0);

  const incrementarMultiplo = () => {
    // ❌ Não funciona como esperado
    setContador(contador + 1); // Agenda atualização 1
    setContador(contador + 1); // Agenda atualização 2
    setContador(contador + 1); // Agenda atualização 3
    // Todas as atualizações usam o mesmo valor base!

    // ✅ Forma correta usando updater function
    setContador(prev => prev + 1); // 0 -> 1
    setContador(prev => prev + 1); // 1 -> 2
    setContador(prev => prev + 1); // 2 -> 3
  };

  return (
    <button onClick={incrementarMultiplo}>+3</button>
  );
}
```

### 3.3 Atualizações de Objetos e Arrays

```jsx
function ExemploAtualizacoes() {
  const [usuario, setUsuario] = useState({
    nome: "João",
    idade: 25,
    preferencias: {
      tema: "escuro",
      notificacoes: true
    }
  });

  // ❌ Mutação direta - NUNCA FAÇA ISSO
  const atualizacaoIncorreta = () => {
    usuario.idade = 26; // Não causa re-renderização
    usuario.preferencias.tema = "claro"; // Mutação aninhada
    setUsuario(usuario); // O React não detecta a mudança
  };

  // ✅ Atualização imutável correta
  const atualizacaoCorreta = () => {
    // Atualização rasa
    setUsuario({
      ...usuario,
      idade: 26
    });

    // Atualização profunda
    setUsuario({
      ...usuario,
      preferencias: {
        ...usuario.preferencias,
        tema: "claro"
      }
    });
  };

  const [tarefas, setTarefas] = useState([
    { id: 1, texto: "Tarefa 1" },
    { id: 2, texto: "Tarefa 2" }
  ]);

  // ✅ Operações corretas com arrays
  const operacoesArray = () => {
    // Adicionar
    setTarefas([...tarefas, { id: 3, texto: "Tarefa 3" }]);

    // Remover
    setTarefas(tarefas.filter(tarefa => tarefa.id !== 2));

    // Atualizar
    setTarefas(tarefas.map(tarefa =>
      tarefa.id === 1
        ? { ...tarefa, texto: "Tarefa Atualizada" }
        : tarefa
    ));
  };
}
```

## 4. Exemplo Prático: Carrinho de Compras

```jsx
function CarrinhoDeCompras() {
  const [carrinho, setCarrinho] = useState([]);
  const [total, setTotal] = useState(0);

  const adicionarProduto = (produto) => {
    // Verifica se o produto já existe no carrinho
    const produtoExistente = carrinho.find(item => item.id === produto.id);

    if (produtoExistente) {
      // Atualiza a quantidade se já existir
      setCarrinho(carrinho.map(item =>
        item.id === produto.id
          ? { ...item, quantidade: item.quantidade + 1 }
          : item
      ));
    } else {
      // Adiciona novo produto se não existir
      setCarrinho([...carrinho, { ...produto, quantidade: 1 }]);
    }

    // Atualiza o total
    setTotal(prevTotal => prevTotal + produto.preco);
  };

  const removerProduto = (produtoId) => {
    const produto = carrinho.find(item => item.id === produtoId);
    
    if (produto.quantidade > 1) {
      // Diminui a quantidade
      setCarrinho(carrinho.map(item =>
        item.id === produtoId
          ? { ...item, quantidade: item.quantidade - 1 }
          : item
      ));
    } else {
      // Remove o produto
      setCarrinho(carrinho.filter(item => item.id !== produtoId));
    }

    // Atualiza o total
    setTotal(prevTotal => prevTotal - produto.preco);
  };

  return (
    <div>
      <h2>Carrinho ({carrinho.length} itens)</h2>
      {carrinho.map(item => (
        <div key={item.id}>
          <span>{item.nome} x {item.quantidade}</span>
          <button onClick={() => removerProduto(item.id)}>-</button>
          <button onClick={() => adicionarProduto(item)}>+</button>
        </div>
      ))}
      <p>Total: R$ {total.toFixed(2)}</p>
    </div>
  );
}
```

## 5. Dicas Avançadas e Melhores Práticas

### 5.1 Lazy Initial State
```jsx
// ❌ Cálculo pesado em toda renderização
const [estado, setEstado] = useState(calcularValorComplexo());

// ✅ Cálculo executado apenas na montagem
const [estado, setEstado] = useState(() => calcularValorComplexo());
```

### 5.2 Derivando Estado
```jsx
// ❌ Estado redundante
const [itens, setItens] = useState([]);
const [quantidadeItens, setQuantidadeItens] = useState(0);

const adicionarItem = (item) => {
  setItens([...itens, item]);
  setQuantidadeItens(quantidadeItens + 1); // Redundante!
};

// ✅ Estado derivado
const [itens, setItens] = useState([]);
const quantidadeItens = itens.length; // Calculado automaticamente

const adicionarItem = (item) => {
  setItens([...itens, item]); // Quantidade atualiza automaticamente
};
```

### 5.3 Atualizações Assíncronas
```jsx
function ExemploAssincrono() {
  const [dados, setDados] = useState(null);
  const [erro, setErro] = useState(null);
  const [carregando, setCarregando] = useState(false);

  const buscarDados = async () => {
    setCarregando(true);
    setErro(null);
    
    try {
      const response = await fetch('api/dados');
      const data = await response.json();
      setDados(data);
    } catch (error) {
      setErro(error.message);
    } finally {
      setCarregando(false);
    }
  };

  return (
    <div>
      {carregando && <p>Carregando...</p>}
      {erro && <p>Erro: {erro}</p>}
      {dados && <pre>{JSON.stringify(dados, null, 2)}</pre>}
      <button onClick={buscarDados}>Buscar Dados</button>
    </div>
  );
}
```
