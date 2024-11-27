# Entendendo useEffect

O useEffect é um Hook fundamental do React que permite gerenciar efeitos colaterais em componentes funcionais. Efeitos colaterais são operações que podem afetar outros componentes e não podem ser realizadas durante a renderização, como:

- Requisições a APIs
- Modificações no DOM
- Gerenciamento de assinaturas (subscriptions)
- Manipulação de timers
- Logging
- Integração com bibliotecas externas

## Sintaxe Detalhada

```javascript
useEffect(() => {
  // Corpo do efeito: código que será executado
  
  return () => {
    // Função de limpeza (cleanup)
    // Executada antes do próximo efeito ou na desmontagem
  }
}, [arrayDeDependencias])
```

### Parâmetros Explicados

1. **Primeiro parâmetro**: Função que contém o código do efeito
2. **Segundo parâmetro**: Array de dependências que controla quando o efeito será executado

## Padrões de Execução

### 1. Execução em Toda Renderização
```javascript
function ComponenteExemplo() {
  useEffect(() => {
    console.log('Este efeito roda em toda renderização');
  }); // Sem segundo parâmetro
}
```

### 2. Execução Única (Montagem)
```javascript
function BuscaDadosInicial() {
  const [dados, setDados] = useState(null);

  useEffect(() => {
    async function buscarDados() {
      const response = await fetch('https://api.exemplo.com/dados');
      const data = await response.json();
      setDados(data);
    }

    buscarDados();
  }, []); // Array vazio = apenas na montagem
}
```

### 3. Execução Com Dependências
```javascript
function MonitoramentoUsuario() {
  const [usuario, setUsuario] = useState(null);
  const [nivel, setNivel] = useState('iniciante');

  useEffect(() => {
    console.log(`Nível do usuário alterado para: ${nivel}`);
    atualizarPerfilUsuario(usuario, nivel);
  }, [nivel, usuario]); // Executa quando nivel ou usuario mudam
}
```

## Cleanup (Limpeza) em Detalhes

O cleanup é crucial para:
- Prevenir memory leaks
- Cancelar requisições pendentes
- Limpar assinaturas
- Remover event listeners

```javascript
function ComponenteChat() {
  const [mensagens, setMensagens] = useState([]);

  useEffect(() => {
    // Estabelece conexão
    const conexao = criarConexaoChat();
    
    // Configura listener
    conexao.on('mensagem', (msg) => {
      setMensagens(prev => [...prev, msg]);
    });

    // Cleanup
    return () => {
      conexao.disconnect();
      console.log('Conexão encerrada e recursos limpos');
    };
  }, []);
}
```

## Exemplos Práticos Avançados

### 1. Gerenciamento de Título da Página
```javascript
function GerenciadorTitulo() {
  const [contador, setContador] = useState(0);
  const [usuario, setUsuario] = useState(null);

  // Efeito para título baseado no contador
  useEffect(() => {
    document.title = `Você clicou ${contador} vezes`;
  }, [contador]);

  // Efeito para título baseado no usuário
  useEffect(() => {
    if (usuario) {
      document.title = `Bem-vindo, ${usuario.nome}!`;
      return () => {
        document.title = 'React App';
      };
    }
  }, [usuario]);
}
```

### 2. Gerenciamento de Eventos com Cleanup
```javascript
function MonitorTeclado() {
  const [tecla, setTecla] = useState('');

  useEffect(() => {
    function handleKeyPress(e) {
      setTecla(e.key);
      console.log(`Tecla pressionada: ${e.key}`);
    }

    window.addEventListener('keypress', handleKeyPress);

    return () => {
      window.removeEventListener('keypress', handleKeyPress);
      console.log('Listener de teclado removido');
    };
  }, []);
}
```

### 3. Requisição com Cancelamento
```javascript
function BuscadorDados() {
  const [dados, setDados] = useState(null);
  const [carregando, setCarregando] = useState(true);
  const [erro, setErro] = useState(null);

  useEffect(() => {
    const abortController = new AbortController();

    async function buscarDados() {
      try {
        setCarregando(true);
        const response = await fetch('https://api.exemplo.com/dados', {
          signal: abortController.signal
        });
        const data = await response.json();
        setDados(data);
      } catch (error) {
        if (error.name === 'AbortError') {
          console.log('Requisição cancelada');
        } else {
          setErro(error.message);
        }
      } finally {
        setCarregando(false);
      }
    }

    buscarDados();

    return () => {
      abortController.abort();
    };
  }, []);
}
```

## Boas Práticas e Dicas

1. **Separação de Efeitos**: Divida efeitos por responsabilidade
```javascript
// Ruim
useEffect(() => {
  fetchDados();
  setupEventListeners();
  inicializarBiblioteca();
}, []);

// Bom
useEffect(() => { fetchDados(); }, []);
useEffect(() => { setupEventListeners(); }, []);
useEffect(() => { inicializarBiblioteca(); }, []);
```

2. **Uso de Funções Async**
```javascript
// Correto
useEffect(() => {
  async function fetchData() {
    const resultado = await getDados();
    setDados(resultado);
  }
  
  fetchData();
}, []);
```

3. **Dependências Corretas**
```javascript
function ExemploDependencias({ id, onUpdate }) {
  useEffect(() => {
    const dados = fetchDadosById(id);
    onUpdate(dados);
  }, [id, onUpdate]); // Inclua todas as dependências usadas
}
```

## Exercício Completo
```javascript
function GerenciadorTarefas() {
  const [tarefas, setTarefas] = useState([]);
  const [filtro, setFiltro] = useState('todas');
  const [carregando, setCarregando] = useState(true);

  // Efeito para carregar tarefas
  useEffect(() => {
    const abortController = new AbortController();

    async function carregarTarefas() {
      try {
        setCarregando(true);
        const response = await fetch('https://api.exemplo.com/tarefas', {
          signal: abortController.signal
        });
        const data = await response.json();
        setTarefas(data);
      } catch (error) {
        console.error('Erro ao carregar tarefas:', error);
      } finally {
        setCarregando(false);
      }
    }

    carregarTarefas();

    return () => {
      abortController.abort();
    };
  }, []);

  // Efeito para filtrar tarefas
  useEffect(() => {
    document.title = `${tarefas.length} tarefas - ${filtro}`;
    
    // Simula logging de análise
    console.log(`Filtro alterado para: ${filtro}`);
    
    return () => {
      console.log(`Limpando filtro: ${filtro}`);
    };
  }, [filtro, tarefas.length]);

  return (
    <div>
      <h1>Gerenciador de Tarefas</h1>
      <select value={filtro} onChange={e => setFiltro(e.target.value)}>
        <option value="todas">Todas</option>
        <option value="pendentes">Pendentes</option>
        <option value="concluidas">Concluídas</option>
      </select>
      
      {carregando ? (
        <p>Carregando tarefas...</p>
      ) : (
        <ul>
          {tarefas
            .filter(tarefa => filtro === 'todas' || tarefa.status === filtro)
            .map(tarefa => (
              <li key={tarefa.id}>{tarefa.titulo}</li>
            ))}
        </ul>
      )}
    </div>
  );
}
```

Este exemplo integra todos os conceitos discutidos e demonstra um caso de uso real do useEffect com:
- Múltiplos efeitos
- Gerenciamento de estado
- Limpeza de recursos
- Tratamento de erros
- Cancelamento de requisições
- Atualização do título da página
- Filtragem de dados
