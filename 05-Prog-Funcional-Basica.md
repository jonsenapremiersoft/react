# Conceitos básicos de Programação Funcional

## Introdução

A programação funcional é um paradigma de programação que forma a base de muitos conceitos modernos no React, especialmente os Hooks. Vamos entender os princípios fundamentais antes de mergulharmos nos Hooks.

## Imutabilidade

A imutabilidade é um dos pilares da programação funcional. Significa que uma vez que um valor é criado, ele não pode ser alterado.

### Por que é importante?

- Previsibilidade do código
- Facilita o rastreamento de mudanças
- Evita efeitos colaterais indesejados
- Melhora a performance em frameworks reativos como React

### Exemplo prático:

```javascript
// ❌ Abordagem mutável
const numeros = [1, 2, 3];
numeros.push(4); // Modificando o array original

// ✅ Abordagem imutável
const numeros = [1, 2, 3];
const novosNumeros = [...numeros, 4]; // Criando um novo array
```

Com objetos:

```javascript
// ❌ Abordagem mutável
const usuario = { nome: 'João', idade: 25 };
usuario.idade = 26;

// ✅ Abordagem imutável
const usuario = { nome: 'João', idade: 25 };
const novoUsuario = { ...usuario, idade: 26 };
```

## Pure Functions (Funções Puras)

Uma função pura é aquela que:
1. Sempre retorna o mesmo resultado para os mesmos argumentos
2. Não possui efeitos colaterais (side effects)
3. Não depende de estado externo

### Exemplo de função pura:

```javascript
// ✅ Função pura
function somar(a, b) {
    return a + b;
}

// ❌ Função impura
let total = 0;
function somarAoTotal(valor) {
    total += valor; // Depende de estado externo
    return total;
}
```

### Por que funções puras são importantes?

- Facilitam testes unitários
- São previsíveis e confiáveis
- Podem ser facilmente compostas
- Permitem memoização e otimizações

## Aplicação Prática no React

Vamos ver como esses conceitos se aplicam no React:

```javascript
// ❌ Componente com estado mutável
function ContadorRuim() {
    let contador = 0;
    
    const incrementar = () => {
        contador++; // Mutação direta
    };
    
    return <button onClick={incrementar}>{contador}</button>;
}

// ✅ Componente funcional puro com estado imutável
function ContadorBom() {
    const [contador, setContador] = React.useState(0);
    
    const incrementar = () => {
        setContador(contador + 1); // Criando novo estado
    };
    
    return <button onClick={incrementar}>{contador}</button>;
}
```

## Benefícios para o React

Entender esses conceitos é crucial porque:

1. O React é construído em torno de princípios funcionais
2. Os Hooks são baseados em imutabilidade
3. Componentes React devem se comportar como funções puras
4. Facilita o entendimento do fluxo de dados unidirecional

## Exercício Prático

Tente refatorar este código para seguir os princípios funcionais:

```javascript
// Código original
let itens = [];

function adicionarItem(item) {
    itens.push(item);
    return itens;
}

// Sua vez de refatorar para uma versão imutável e pura!
```

## Conclusão

Esses conceitos fundamentais da programação funcional são essenciais para entender como o React funciona, especialmente quando começarmos a trabalhar com Hooks.
