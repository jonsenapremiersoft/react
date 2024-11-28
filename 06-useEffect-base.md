# Hook useEffect

## O que é o useEffect?
O `useEffect` é um Hook fundamental do React que permite executar "efeitos colaterais" em componentes funcionais. Efeitos colaterais são operações que podem afetar outros componentes e não podem ser realizadas durante a renderização, como:
- Chamadas a APIs
- Modificações no DOM
- Subscrições em serviços
- Manipulação de timers
- Logging
- Manipulação de eventos do navegador

## Por que precisamos do useEffect?
Em componentes de classe, usávamos métodos de ciclo de vida como `componentDidMount`, `componentDidUpdate` e `componentWillUnmount`. O `useEffect` combina esses três em um único Hook, tornando mais simples gerenciar o ciclo de vida do componente.

## Sintaxe Detalhada
```typescript
useEffect(() => {
  // 1. Este é o efeito principal
  // Executa após a renderização

  return () => {
    // 2. Esta é a função de cleanup
    // Executa antes do efeito rodar novamente ou quando o componente desmonta
  };
}, [ /* 3. Array de dependências */ ]);
```

## Ciclo de Vida Detalhado

### 1. Montagem (componentDidMount)
```typescript
interface UserData {
  name: string;
  email: string;
}

const UserProfile = () => {
  const [userData, setUserData] = useState<UserData | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchUserData = async () => {
      try {
        setIsLoading(true);
        const response = await fetch('https://api.example.com/user');
        const data = await response.json();
        setUserData(data);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Erro ao carregar dados');
      } finally {
        setIsLoading(false);
      }
    };

    fetchUserData();
  }, []); // Array vazio = executa apenas na montagem

  if (isLoading) return <div>Carregando...</div>;
  if (error) return <div>Erro: {error}</div>;
  if (!userData) return <div>Nenhum dado encontrado</div>;

  return (
    <div>
      <h1>{userData.name}</h1>
      <p>{userData.email}</p>
    </div>
  );
};
```

### 2. Atualização (componentDidUpdate)
```typescript
interface TimerProps {
  startCount: number;
  step: number;
}

const Timer = ({ startCount, step }: TimerProps) => {
  const [count, setCount] = useState(startCount);
  const [isRunning, setIsRunning] = useState(false);

  // Efeito reage a mudanças em isRunning e step
  useEffect(() => {
    let intervalId: NodeJS.Timeout;

    if (isRunning) {
      intervalId = setInterval(() => {
        setCount(prevCount => prevCount + step);
      }, 1000);

      console.log(`Timer iniciado com step de ${step}`);
    }

    // Cleanup: limpa o intervalo quando isRunning muda ou componente desmonta
    return () => {
      if (intervalId) {
        clearInterval(intervalId);
        console.log('Timer parado');
      }
    };
  }, [isRunning, step]); // Dependências: isRunning e step

  return (
    <div>
      <h2>Contador: {count}</h2>
      <button onClick={() => setIsRunning(!isRunning)}>
        {isRunning ? 'Parar' : 'Iniciar'}
      </button>
    </div>
  );
};
```

### 3. Desmontagem e Limpeza (componentWillUnmount)
```typescript
interface ChatProps {
  roomId: string;
}

const ChatRoom = ({ roomId }: ChatProps) => {
  const [messages, setMessages] = useState<string[]>([]);

  useEffect(() => {
    // Simula conexão com websocket
    console.log(`Conectando ao chat da sala ${roomId}`);
    
    const connection = new WebSocket(`ws://exemplo.com/chat/${roomId}`);
    
    connection.onmessage = (event) => {
      setMessages(prev => [...prev, event.data]);
    };

    // Função de cleanup
    return () => {
      console.log(`Desconectando da sala ${roomId}`);
      connection.close();
      setMessages([]); // Limpa mensagens ao trocar de sala
    };
  }, [roomId]); // Reconecta quando roomId muda

  return (
    <div>
      <h3>Sala: {roomId}</h3>
      <ul>
        {messages.map((msg, index) => (
          <li key={index}>{msg}</li>
        ))}
      </ul>
    </div>
  );
};
```

## Padrões Avançados

### 1. Debounce de Inputs
```typescript
interface SearchProps {
  onSearch: (term: string) => void;
}

const SearchInput = ({ onSearch }: SearchProps) => {
  const [searchTerm, setSearchTerm] = useState('');

  useEffect(() => {
    const timeoutId = setTimeout(() => {
      if (searchTerm.trim()) {
        console.log(`Buscando por: ${searchTerm}`);
        onSearch(searchTerm);
      }
    }, 500); // Espera 500ms após última digitação

    return () => {
      clearTimeout(timeoutId); // Cancela timeout anterior
    };
  }, [searchTerm, onSearch]);

  return (
    <input
      type="text"
      value={searchTerm}
      onChange={(e) => setSearchTerm(e.target.value)}
      placeholder="Buscar..."
    />
  );
};
```

### 2. Event Listeners
```typescript
const ScrollTracker = () => {
  const [scrollY, setScrollY] = useState(0);
  const [isScrollingUp, setIsScrollingUp] = useState(false);

  useEffect(() => {
    let lastScrollY = window.scrollY;

    const handleScroll = () => {
      const currentScrollY = window.scrollY;
      setScrollY(currentScrollY);
      setIsScrollingUp(currentScrollY < lastScrollY);
      lastScrollY = currentScrollY;
    };

    // Adiciona throttle para melhor performance
    const throttledHandleScroll = _.throttle(handleScroll, 100);

    window.addEventListener('scroll', throttledHandleScroll);

    return () => {
      window.removeEventListener('scroll', throttledHandleScroll);
      throttledHandleScroll.cancel(); // Limpa throttle
    };
  }, []);

  return (
    <div className="scroll-info">
      <p>Posição Y: {scrollY}px</p>
      <p>Direção: {isScrollingUp ? '⬆️ Subindo' : '⬇️ Descendo'}</p>
    </div>
  );
};
```

## Regras e Boas Práticas

1. **Separação de Responsabilidades**:
```typescript
const UserDashboard = () => {
  // ✅ Separe efeitos por responsabilidade
  useEffect(() => {
    // Efeito para dados do usuário
  }, []);

  useEffect(() => {
    // Efeito para notificações
  }, []);

  // ❌ Não misture responsabilidades
  useEffect(() => {
    // Carregar dados do usuário
    // E também lidar com notificações
    // E também fazer mais alguma coisa...
  }, []);
};
```

2. **Evite Dependências Desnecessárias**:
```typescript
const ExampleComponent = () => {
  const [data, setData] = useState<string[]>([]);

  // ❌ Evite
  useEffect(() => {
    const transformedData = data.map(item => item.toUpperCase());
    setData(transformedData);
  }, [data]); // Isso causará loop infinito!

  // ✅ Correto
  const transformData = () => {
    const transformedData = data.map(item => item.toUpperCase());
    setData(transformedData);
  };

  // Use o effect apenas quando necessário
  useEffect(() => {
    // Alguma operação assíncrona necessária
  }, []);
};
```

3. **Tipagem Correta**:
```typescript
interface FetchResponse<T> {
  data: T;
  status: number;
}

interface User {
  id: number;
  name: string;
}

const UserLoader = () => {
  // Especifique tipos explicitamente
  const [user, setUser] = useState<User | null>(null);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        const response = await fetch('/api/user');
        const { data }: FetchResponse<User> = await response.json();
        setUser(data);
      } catch (err) {
        setError(err instanceof Error ? err : new Error('Erro desconhecido'));
      }
    };

    fetchUser();
  }, []);

  return (
    // JSX
  );
};
```
