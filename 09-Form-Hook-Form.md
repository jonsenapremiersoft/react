## Cadastro de Usuário com Validação e Persistência: Reack Hook Form + Zod

**Descrição**:  
- Crie uma aplicação simples onde o usuário pode preencher um formulário de cadastro com nome, email, senha e data de nascimento.
 O formulário será validado usando **Zod**, terá feedback visual para erros, carregamento simulado, e os dados serão salvos localmente no **localStorage**.

---

### **Requisitos Funcionais**
1. **Formulário com validação de dados**:
   - Nome: obrigatório, entre 3 e 50 caracteres.
   - Email: formato válido.
   - Senha: obrigatório, pelo menos 6 caracteres.
   - Data de Nascimento: maior de 13 anos.

2. **Feedback ao usuário**:
   - Mostrar mensagens de erro diretamente abaixo dos campos.
   - Mostrar um indicador de carregamento (spinner) ao enviar.

3. **Persistência**:
   - Salvar os dados no `localStorage` ao enviar.
   - Carregar os dados salvos ao recarregar a página.

4. **Estilização**:
   - Usar **Tailwind CSS** para criar um design responsivo e moderno.

---

### **Passo a Passo**

#### 1. **Setup do Projeto**
Execute os comandos para criar e configurar o projeto:
```bash
npx create-next-app@latest user-registration --ts --use-npm --app  --tailwind
cd user-registration
npm install react-hook-form zod @hookform/resolvers postcss autoprefixer
```

#### 2. **Estrutura do Projeto**
Crie os diretórios necessários:
```
/app
  /form
    page.tsx
/components
  Form.tsx
  Spinner.tsx
/utils
  schema.ts
  storage.ts
```

#### 3. **Validação com Zod**
Crie o arquivo `/utils/schema.ts`:
```typescript
import { z } from 'zod';

export const userSchema = z.object({
  name: z.string().min(3, 'O nome deve ter pelo menos 3 caracteres.').max(50, 'O nome pode ter no máximo 50 caracteres.'),
  email: z.string().email('Insira um email válido.'),
  password: z.string().min(6, 'A senha deve ter pelo menos 6 caracteres.'),
  birthDate: z.string().refine((date) => {
    const age = new Date().getFullYear() - new Date(date).getFullYear();
    return age >= 13;
  }, 'Você deve ter pelo menos 13 anos.'),
});

export type UserFormData = z.infer<typeof userSchema>;
```

#### 4. **Persistência no localStorage**
Crie o arquivo `/utils/storage.ts`:
```typescript
export const saveToLocalStorage = (key: string, value: any) => {
  localStorage.setItem(key, JSON.stringify(value));
};

export const loadFromLocalStorage = <T>(key: string): T | null => {
  const data = localStorage.getItem(key);
  return data ? JSON.parse(data) : null;
};
```

#### 5. **Componente do Formulário**
Crie `/components/Form.tsx`:
```tsx
'use client';

import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { userSchema, UserFormData } from '@/utils/schema';
import { saveToLocalStorage } from '@/utils/storage';
import { useState } from 'react';
import Spinner from './Spinner';

export default function Form() {
  const [loading, setLoading] = useState(false);
  const [success, setSuccess] = useState(false);

  const { register, handleSubmit, formState: { errors } } = useForm<UserFormData>({
    resolver: zodResolver(userSchema),
  });

  const onSubmit = (data: UserFormData) => {
    setLoading(true);
    setTimeout(() => {
      saveToLocalStorage('userData', data);
      setLoading(false);
      setSuccess(true);
    }, 1500);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4 max-w-md mx-auto">
      <div>
        <label className="block mb-1">Nome</label>
        <input
          {...register('name')}
          className="w-full border rounded p-2"
          placeholder="Seu nome"
        />
        {errors.name && <p className="text-red-500">{errors.name.message}</p>}
      </div>

      <div>
        <label className="block mb-1">Email</label>
        <input
          {...register('email')}
          type="email"
          className="w-full border rounded p-2"
          placeholder="Seu email"
        />
        {errors.email && <p className="text-red-500">{errors.email.message}</p>}
      </div>

      <div>
        <label className="block mb-1">Senha</label>
        <input
          {...register('password')}
          type="password"
          className="w-full border rounded p-2"
          placeholder="Sua senha"
        />
        {errors.password && <p className="text-red-500">{errors.password.message}</p>}
      </div>

      <div>
        <label className="block mb-1">Data de Nascimento</label>
        <input
          {...register('birthDate')}
          type="date"
          className="w-full border rounded p-2"
        />
        {errors.birthDate && <p className="text-red-500">{errors.birthDate.message}</p>}
      </div>

      <button
        type="submit"
        className="w-full bg-blue-500 text-white rounded p-2"
        disabled={loading}
      >
        {loading ? <Spinner /> : 'Cadastrar'}
      </button>

      {success && <p className="text-green-500 mt-2">Cadastro realizado com sucesso!</p>}
    </form>
  );
}
```

#### 6. **Componente de Spinner**
Crie `/components/Spinner.tsx`:
```tsx
export default function Spinner() {
  return (
    <div
      className="inline-block w-6 h-6 border-4 border-white border-t-transparent rounded-full animate-spin"
      role="status"
    >
      <span className="sr-only">Carregando...</span>
    </div>
  )
}
```

#### 7. **Página Principal**
Crie `/app/form/page.tsx`:
```tsx
import Form from '@/components/Form';

export default function FormPage() {
  return (
    <main className="min-h-screen flex items-center justify-center bg-gray-100">
      <Form />
    </main>
  );
}
```

---
