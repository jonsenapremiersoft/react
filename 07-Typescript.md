# Introdução ao TypeScript no React

## Estrutura do Projeto (criado com create-next-app)

```
my-next-app/
├── src/
│   ├── app/                 # Diretório do App Router
│   │   ├── page.tsx        # Página inicial
│   │   ├── layout.tsx      # Layout raiz
│   │   └── globals.css     # Estilos globais
│   └── components/         # Componentes reutilizáveis
├── public/                 # Arquivos estáticos
└── package.json           # Dependências e scripts
```

## Componentes com TypeScript

### 1. Props Tipadas

```tsx
// Exemplo de um componente de perfil de usuário

// Define a interface das props
interface ProfileProps {
  name: string;
  age: number;
  email: string;
  role?: 'admin' | 'user'; // Union type - propriedade opcional
}

// Componente usando a interface
const Profile = ({ name, age, email, role = 'user' }: ProfileProps) => {
  return (
    <div>
      <h2>{name}</h2>
      <p>Idade: {age}</p>
      <p>Email: {email}</p>
      <p>Cargo: {role}</p>
    </div>
  );
};
```

Explicação:
- Interface define o contrato das props
- `?` indica propriedade opcional
- Union types (`'admin' | 'user'`) permitem valores específicos
- Valor padrão definido na desestruturação

### 2. Hooks Modernos com TypeScript

```tsx
// Exemplo de um contador com useState e useEffect
import { useState, useEffect } from 'react';

const Counter = () => {
  // TypeScript infere o tipo automaticamente pelo valor inicial
  const [count, setCount] = useState(0);
  
  // Tipo explícito quando o valor inicial é null
  const [user, setUser] = useState<string | null>(null);

  // useEffect com cleanup function tipada
  useEffect(() => {
    const timer = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);

    // Cleanup function
    return () => clearInterval(timer);
  }, []);

  return <div>Count: {count}</div>;
};
```

Explicação:
- TypeScript infere tipos automaticamente quando possível
- Use tipos explícitos para valores null/undefined
- Cleanup functions previnem memory leaks

### 3. Manipulação de Eventos

```tsx
// Exemplo de formulário com eventos tipados
const Form = () => {
  // Event handler tipado para formulário
  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    // Lógica aqui
  };

  // Event handler tipado para input
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    console.log(e.target.value);
  };

  // Event handler tipado para botão
  const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
    console.log('Clicado!');
  };

  return (
    <form onSubmit={handleSubmit}>
      <input onChange={handleChange} />
      <button onClick={handleClick}>Enviar</button>
    </form>
  );
};
```

Explicação:
- Eventos têm tipos específicos (FormEvent, ChangeEvent, MouseEvent)
- HTMLElements também são tipados (HTMLFormElement, HTMLInputElement)
- Previne erros comuns de digitação e propriedades


## Exemplo Prático: Lista de Tarefas

```tsx
// types.ts
interface Task {
  id: number;
  title: string;
  completed: boolean;
  priority: 'low' | 'medium' | 'high';
}

// TaskList.tsx
import { useState } from 'react';

const TaskList = () => {
  const [tasks, setTasks] = useState<Task[]>([]);
  const [newTask, setNewTask] = useState('');
  const [priority, setPriority] = useState<Task['priority']>('medium');

  const addTask = () => {
    if (newTask.trim()) {
      const task: Task = {
        id: Date.now(),
        title: newTask,
        completed: false,
        priority
      };
      setTasks([...tasks, task]);
      setNewTask('');
    }
  };

  const toggleTask = (id: number) => {
    setTasks(tasks.map(task =>
      task.id === id ? { ...task, completed: !task.completed } : task
    ));
  };

  return (
    <div>
      <input
        value={newTask}
        onChange={(e) => setNewTask(e.target.value)}
        placeholder="Nova tarefa"
      />
      <select 
        value={priority} 
        onChange={(e) => setPriority(e.target.value as Task['priority'])}
      >
        <option value="low">Baixa</option>
        <option value="medium">Média</option>
        <option value="high">Alta</option>
      </select>
      <button onClick={addTask}>Adicionar</button>
      
      <ul>
        {tasks.map(task => (
          <li 
            key={task.id}
            style={{ 
              textDecoration: task.completed ? 'line-through' : 'none',
              color: task.priority === 'high' ? 'red' : 'inherit'
            }}
            onClick={() => toggleTask(task.id)}
          >
            {task.title}
          </li>
        ))}
      </ul>
    </div>
  );
};
```

Explicação do exercício:
- Interface Task define estrutura da tarefa
- useState com tipos específicos para cada estado
- Funções tipadas para manipulação de tarefas
- Event handlers tipados para inputs
- Estilização condicional baseada em propriedades tipadas

Este exemplo demonstra:
- Gerenciamento de estado complexo
- Manipulação de arrays tipados
- Eventos de usuário
- Renderização condicional
- Tipos personalizados
- Union types para prioridades

Sugestões para expandir o exercício:
1. Adicionar filtros por status
2. Implementar ordenação por prioridade
3. Adicionar persistência com localStorage
4. Criar componentes separados para itens da lista
5. Adicionar data de criação/conclusão
