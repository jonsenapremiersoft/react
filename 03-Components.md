# React Components

## Entendendo Componentes Funcionais

Componentes são blocos de construção reutilizáveis que encapsulam HTML, CSS e lógica. Em React, utilizamos componentes funcionais - funções JavaScript que retornam JSX.

```jsx
function Welcome() {
  return <h1>Olá, mundo!</h1>;
}
```

## Pensando em Componentes

Ao desenvolver interfaces, divida-as em partes menores e independentes. Considere:

1. Responsabilidade única: cada componente deve fazer apenas uma coisa
2. Reutilização: identifique padrões repetitivos
3. Hierarquia: organize componentes em pais e filhos

Exemplo de decomposição:
```jsx
function App() {
  return (
    <div>
      <Header />
      <MainContent />
      <Footer />
    </div>
  );
}
```

## Criando Componentes Simples

Comece com componentes que apenas renderizam HTML:

```jsx
function Button() {
  return (
    <button className="btn">
      Clique aqui
    </button>
  );
}

function Card() {
  return (
    <div className="card">
      <h2>Título</h2>
      <p>Conteúdo do card</p>
      <Button />
    </div>
  );
}
```

## Trabalhando com Listas

Para renderizar listas em React, use o método `map` para transformar arrays em elementos JSX:

```jsx
function ProductList() {
  const products = [
    { id: 1, name: 'Notebook' },
    { id: 2, name: 'Mouse' },
    { id: 3, name: 'Teclado' }
  ];

  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  );
}
```

## A Importância do key


```jsx
import React from 'react';

const frutas = ['Maçã', 'Banana', 'Laranja'];

function ListaDeFrutas() {

return (
<ul>
{frutas.map((fruta) => ( <li>{fruta}</li> ))}
</ul>
);
}

export default ListaDeFrutas;
```

- Se você executar este código, o React exibirá um aviso no console:
```error
  Warning: Each child in a list should have a unique "key" prop.
```

### A prop `key` é um atributo especial que o React usa para rastrear quais itens em uma lista foram alterados, adicionados ou removidos. Isso ajuda o React a otimizar a renderização e atualizar apenas os elementos que realmente mudaram.

O atributo `key` é essencial por três razões:

  1. Identificação Única:** As `keys` fornecem uma identidade estável para cada elemento na lista.
  2. Desempenho Otimizado:** Com `keys`, o React minimiza operações de DOM, tornando a aplicação mais rápida.
  3. Evita Bugs:** Sem `keys` apropriadas, o React pode não renderizar corretamente os componentes, levando a comportamentos inesperados.

### Para resolver o aviso, adicione a prop `key` ao elemento da lista:
```jsx
import React from 'react';

const frutas = ['Maçã', 'Banana', 'Laranja'];

function ListaDeFrutas() {

return ( <ul>
 {frutas.map((fruta, index) => ( <li key={index}>{fruta}</li> ))}
 </ul> ); }

export default ListaDeFrutas;
```

### Exemplo do problema sem key:
```jsx
// ❌ Errado: Sem key
{items.map(item => <li>{item.name}</li>)}

// ✅ Correto: Com key
{items.map(item => <li key={item.id}>{item.name}</li>)}
```

**Nota:** Usar o índice do array (`index`) como `key` pode ser aceitável para listas estáticas, mas não é recomendado se a lista puder mudar de ordem ou se itens puderem ser adicionados/removidos. O ideal é usar um identificador único associado ao item.

### Exemplo usando IDs únicos:

```jsx
import React from 'react';
const frutas = [ { id: 1, nome: 'Maçã' }, { id: 2, nome: 'Banana' }, { id: 3, nome: 'Laranja' }, ];

function ListaDeFrutas() {
return (
<ul>
{frutas.map((fruta) => ( <li key={fruta.id}>{fruta.nome}</li> ))}
</ul> ); }

export default ListaDeFrutas;
```

**Sempre que você renderizar listas em React, lembre-se de:**

-   Usar `keys` únicas e estáveis.
-   Evitar usar índices como `keys` em listas dinâmicas.
-   Garantir que cada `key` corresponda a um único elemento.

### É bem comum acontecer de um desenvolvedor pensar o seguinte:

*Já que preciso passar um valor único para a key, então faz sentido eu usar um uuid generator direto na key do componente, assim eu garanto que toda vez ele vai ter um uuid diferente e único como no seguinte exemplo:*

```jsx
import React from 'react';
import { v4 as uuidv4 } from 'uuid';

const tarefas = ['Tarefa 1', 'Tarefa 2', 'Tarefa 3'];

function ListaDeTarefas() {
return (
<ul>
{tarefas.map((tarefa) => ( <li key={uuidv4()}>{tarefa}</li> ))}
</ul>
);
}
 export default ListaDeTarefas;
```

-   **Problema:**
   
    -   A cada renderização, cada `<li>` recebe uma nova `key`, fazendo com que o React trate todos os itens como novos elementos.
    -   Isso impede que o React reutilize componentes existentes, levando a re-renderizações desnecessárias.

#### **2. Impacto no Desempenho**

-   **Re-renderizações Desnecessárias:**
    -   Quando as `keys` mudam constantemente, o React não consegue otimizar a atualização do DOM.
    -   Cada elemento é destruído e recriado, o que pode afetar o desempenho, especialmente em listas grandes.

#### **3. Perda de Estado dos Componentes Filhos**

-   **Descarte do Estado Interno:**
   
    -   Se os componentes filhos mantêm algum estado interno, mudar a `key` força o React a desmontar e montar novamente esses componentes.
    -   Isso resulta na perda do estado interno, levando a comportamentos inesperados na aplicação.

#### **4. Melhores Práticas para Utilizar `keys`**

-   **`keys` Devem Ser Estáveis e Únicos:**
   
    -   Utilize identificadores que permaneçam consistentes entre as renderizações, como IDs provenientes do banco de dados.
    - -   **Evitar Usar Índices (index) como `keys` em Listas Dinâmicas:**
   
    -   Embora usar o índice (`index`) do array possa ser uma solução temporária para listas estáticas, em listas onde itens podem ser adicionados, removidos ou reordenados, isso pode levar a problemas semelhantes aos de usar `uuidv4()`.
-   **Quando Usar `uuidv4()`:**
   
    -   `uuidv4()` pode ser útil para gerar `keys` em situações onde os itens não têm um identificador único natural e a lista não é dinâmica.
    -   Mesmo assim, é preferível gerar `keys` uma vez e mantê-las estáveis, em vez de gerar uma nova `key` a cada renderização.

Uma solução interessante, caso sua API não retorne os ids ou uuids dos items é você fazer um map dos itens e adicionar o uuid em cada um quando você receber os dados da API. Desta forma você mantem os uuids consistentes e vai evitar dores de cabeça.
