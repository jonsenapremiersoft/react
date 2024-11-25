# JSX

## O que é JSX?

JSX (JavaScript XML) é uma extensão sintática para JavaScript que permite escrever código semelhante a HTML diretamente em JavaScript. É uma das características mais fundamentais do React, pois fornece uma maneira declarativa e intuitiva de descrever a interface do usuário.

Por baixo dos panos, JSX é convertido em objetos JavaScript puros através de um processo chamado transpilação, geralmente feito pelo Babel. 

### Exemplos práticos:

```jsx
// JSX simples
const greeting = <h1>Olá Mundo!</h1>;

// JSX com expressões JavaScript
const name = "Maria";
const greeting = <h1>Olá, {name}!</h1>;

// JSX com múltiplos elementos
const profile = (
  <div>
    <h1>Perfil</h1>
    <img src="avatar.jpg" alt="Foto de perfil" />
    <p>Nome: {name}</p>
  </div>
);
```

## Como JSX Funciona

### 1. Processo de Compilação

Quando você escreve JSX, o Babel o transforma em chamadas `React.createElement()`. Vamos ver um exemplo detalhado:

```jsx
// Seu código JSX
const element = (
  <div className="container">
    <h1>Título</h1>
    <p>Parágrafo</p>
  </div>
);

// É transformado em:
const element = React.createElement(
  'div',
  { className: 'container' },
  React.createElement('h1', null, 'Título'),
  React.createElement('p', null, 'Parágrafo')
);
```

### 2. Estrutura Interna

Cada elemento JSX é convertido em um objeto com esta estrutura:

```javascript
{
  type: 'div',
  props: {
    className: 'container',
    children: [
      {
        type: 'h1',
        props: { children: 'Título' }
      },
      {
        type: 'p',
        props: { children: 'Parágrafo' }
      }
    ]
  }
}
```

## React.createElement vs JSX

### Usando React.createElement

```javascript
// Sem JSX - Verboso e difícil de ler
const element = React.createElement(
  'div',
  { className: 'profile-card' },
  React.createElement('img', { src: 'avatar.jpg', className: 'profile-image' }),
  React.createElement(
    'div',
    { className: 'profile-info' },
    React.createElement('h2', null, 'João Silva'),
    React.createElement('p', null, 'Desenvolvedor Frontend')
  )
);
```

### Usando JSX

```jsx
// Com JSX - Claro e intuitivo
const element = (
  <div className="profile-card">
    <img src="avatar.jpg" className="profile-image" />
    <div className="profile-info">
      <h2>João Silva</h2>
      <p>Desenvolvedor Frontend</p>
    </div>
  </div>
);
```

## Diferenças Detalhadas entre HTML e JSX

### 1. Atributos de Classe

```jsx
// HTML
<div class="container">

// JSX
<div className="container">

// JSX com múltiplas classes
<div className={`container ${isActive ? 'active' : ''}`}>
```

### 2. Eventos

```jsx
// HTML
<button onclick="handleClick()">

// JSX - Eventos em camelCase e funções diretas
<button onClick={handleClick}>

// JSX com funções inline
<button onClick={(e) => {
  e.preventDefault();
  console.log('Clicado!');
}}>
```

### 3. Estilização

```jsx
// HTML
<div style="background-color: blue; margin-top: 20px;">

// JSX - Objeto de estilos
<div style={{
  backgroundColor: 'blue', // Propriedades em camelCase
  marginTop: '20px',      // Valores como strings quando têm unidades
  display: 'flex',
  justifyContent: 'center'
}}>

// JSX - Combinando estilos dinâmicos
<div style={{
  ...baseStyles,
  ...conditionalStyles,
  backgroundColor: isActive ? 'blue' : 'gray'
}}>
```

### 4. Expressões JavaScript

```jsx
// Condicionais
<div>
  {isLoggedIn && <UserProfile />}
  {isAdmin ? <AdminPanel /> : <UserPanel />}
</div>

// Loops e Mapeamentos
<ul>
  {items.map(item => (
    <li key={item.id}>
      {item.name} - {item.price}
    </li>
  ))}
</ul>

// Expressões Complexas
<div>
  {(() => {
    switch (status) {
      case 'loading': return <Loading />;
      case 'error': return <Error />;
      default: return <Content />;
    }
  })()}
</div>
```

### 5. Outras Diferenças Importantes

```jsx
// Tags vazias precisam ser fechadas
<input type="text" />
<img src="image.jpg" alt="descrição" />

// For em labels
<label htmlFor="name">  // não 'for' como em HTML

// Atributos data-*
<div data-testid="test">  // mantém o mesmo formato do HTML

// Comentários em JSX
{/* Comentário JSX */}
```
