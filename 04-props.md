# Entendendo Props no React

Props (propriedades) são a maneira principal de passar dados entre componentes React. São fundamentais para criar componentes reutilizáveis e manter o fluxo de dados organizado.

## O Que São Props?

Props são argumentos passados para componentes React, similar aos atributos HTML. Elas permitem que um componente pai passe dados para seus filhos.

```jsx
// Componente pai passa props
function App() {
  return <Welcome name="Maria" age={25} active={true} />;
}

// Componente filho recebe props
function Welcome(props) {
  return (
    <div>
      <h1>Olá, {props.name}</h1>
      <p>Idade: {props.age}</p>
      <p>Status: {props.active ? 'Ativo' : 'Inativo'}</p>
    </div>
  );
}
```

## Características Importantes das Props

1. **Somente Leitura**: Props são imutáveis. Um componente nunca deve modificar suas próprias props.

2. **Tipos de Dados**: Props podem receber:
   ```jsx
   <Component 
     text="string"           // Strings
     number={42}            // Números
     isActive={true}        // Booleanos
     user={{ id: 1 }}      // Objetos
     items={[1, 2, 3]}     // Arrays
     onClick={handleClick}  // Funções
   />
   ```

## Desestruturação de Props

A desestruturação torna o código mais limpo e legível:

```jsx
// Sem desestruturação
function Profile(props) {
  return <p>{props.name} tem {props.age} anos</p>;
}

// Com desestruturação
function Profile({ name, age }) {
  return <p>{name} tem {age} anos</p>;
}
```

## Props Children

`children` é uma prop especial que permite passar elementos JSX como filhos:

```jsx
function Card({ title, children }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      {children}
    </div>
  );
}

// Uso
function App() {
  return (
    <Card title="Meu Card">
      <p>Este é o conteúdo dentro do card</p>
      <button>Clique aqui</button>
    </Card>
  );
}
```

## Valores Padrão

Defina valores padrão para props opcionais:

```jsx
function Button({ text = "Clique", type = "primary" }) {
  return <button className={type}>{text}</button>;
}
```

## Validação com PropTypes

PropTypes ajuda a evitar erros validando o tipo das props:

```jsx
import PropTypes from 'prop-types';

function User({ name, age, email }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Idade: {age}</p>
      <p>Email: {email}</p>
    </div>
  );
}

User.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number,
  email: PropTypes.string.isRequired
};
```

## Boas Práticas

1. **Nomeie props claramente**: Use nomes descritivos que indiquem o propósito
2. **Mantenha componentes puros**: Não modifique props dentro do componente
3. **Documente props**: Comente o propósito de cada prop importante


## Componentes Puros no React

Um componente puro é aquele que, para as mesmas props e state, sempre renderiza o mesmo resultado. São previsíveis e não causam efeitos colaterais.

## Características

```jsx
// Componente Puro ✅
function PureGreeting({ name }) {
 return <h1>Olá, {name}</h1>;
}

// Componente Impuro ❌
function ImpureGreeting({ name }) {
 // Modifica props (nunca faça isso)
 name = name.toUpperCase();
 
 // Depende de variável externa
 const random = Math.random();
 
 return <h1>Olá, {name} {random}</h1>;
}
```

