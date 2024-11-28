# Exemplo useEffect: Contador



```tsx
import { useEffect, useState } from 'react';

const SavedCounter = () => {
  const [count, setCount] = useState<number>(() => {
    // Inicializa o estado com o valor salvo no localStorage, se existir
    const savedCount = localStorage.getItem('savedCount');
    return savedCount ? parseInt(savedCount) : 0;
  });

  // useEffect para salvar o valor no localStorage sempre que count mudar
  useEffect(() => {
    localStorage.setItem('savedCount', count.toString());
    
    // Log para demonstração do efeito
    console.log('Valor salvo:', count);
  }, [count]);

  const handleIncrement = () => setCount(prev => prev + 1);
  const handleDecrement = () => setCount(prev => prev - 1);
  const handleReset = () => setCount(0);

  return (
    <div className="flex flex-col items-center justify-center min-h-screen bg-gray-100 p-4">
      <div className="bg-white rounded-lg shadow-md p-6 space-y-4">
        <h1 className="text-2xl font-bold text-center text-gray-800">
          Contador com Persistência
        </h1>
        
        <div className="text-center">
          <p className="text-lg mb-2">Contagem Atual:</p>
          <p className="text-4xl font-bold text-blue-600">{count}</p>
        </div>
        
        <div className="flex gap-2 justify-center">
          <button
            onClick={handleDecrement}
            className="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600 transition-colors"
          >
            -1
          </button>
          
          <button
            onClick={handleReset}
            className="px-4 py-2 bg-gray-500 text-white rounded hover:bg-gray-600 transition-colors"
          >
            Reset
          </button>
          
          <button
            onClick={handleIncrement}
            className="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600 transition-colors"
          >
            +1
          </button>
        </div>
      </div>
    </div>
  );
};

export default SavedCounter;

```

Vamos analisar este exemplo passo a passo:

1. **Configuração Inicial**
   - Primeiro, você precisa criar um novo projeto Next.js com TypeScript e Tailwind:
   ```bash
   npx create-next-app@latest meu-contador --typescript --tailwind
   cd meu-contador
   ```

2. **Estrutura do Componente**
   - Crie um novo arquivo em `app/page.tsx` (ou onde preferir na sua estrutura)
   - Importamos os hooks necessários do React: `useState` e `useEffect`

3. **Estado do Componente**
   - Usamos o `useState` com TypeScript especificando o tipo `number`
   - A inicialização do estado é feita com uma função (lazy initialization) que:
     - Verifica se existe um valor salvo no localStorage
     - Converte o valor para número se existir, ou usa 0 como padrão

4. **UseEffect**
   - O hook `useEffect` é usado para salvar o valor no localStorage
   - O array de dependências `[count]` garante que o efeito só roda quando `count` muda
   - Incluí um `console.log` para fins didáticos - você pode ver no console quando o efeito é executado

5. **Handlers**
   - `handleIncrement`: Incrementa o contador usando o padrão de callback para garantir o valor mais atual
   - `handleDecrement`: Decrementa o contador
   - `handleReset`: Reseta o contador para 0

6. **Interface do Usuário**
   - Usa classes do Tailwind para estilização
   - Layout centralizado com flex
   - Card com sombra contendo:
     - Título
     - Exibição do valor atual
     - Botões para controle

Para testar o funcionamento:

1. Execute o projeto:
   ```bash
   npm run dev
   ```

2. Abra o navegador e:
   - Teste os botões de incremento e decremento
   - Abra o console do navegador (F12) para ver os logs do useEffect
   - Recarregue a página e observe que o valor persiste
   - Abra uma nova aba e veja que o valor é compartilhado

**Pontos importantes sobre o useEffect:**

1. **Quando ele executa:**
   - Na montagem inicial do componente
   - Sempre que o valor de `count` mudar
   - Isso garante que o localStorage sempre está sincronizado

2. **Por que precisamos do array de dependências:**
   - Se não incluíssemos `[count]`, o efeito rodaria em toda renderização
   - Com `[]` vazio, rodaria apenas na montagem
   - Com `[count]`, roda apenas quando necessário

3. **Boas práticas demonstradas:**
   - Uso de TypeScript para type safety
   - Lazy initialization do estado
   - Funções de handler separadas para melhor organização
   - Uso de classes do Tailwind de forma organizada

Este exemplo demonstra um caso de uso real do useEffect: sincronização com um sistema externo (localStorage). É um padrão comum em aplicações React e serve como boa base para entender os conceitos fundamentais do hook.

Quer que eu explique algum aspecto específico em mais detalhes?
