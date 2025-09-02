# Usar Netlify Function para não expor as credenciais do app

## 1. Instalar as dependências

```bash
yarn add -D netlify-cli @netlify/functions

yarn add -D @types/node
```

## 2. Configurar variáveis de ambiente no Netlify

Defina as seguintes variáveis de ambiente nas configurações do projeto → Environment variables:

| Nome da variável    | Valor                   |
|---------------------|-------------------------|
| NF_BACKEND_URL      | https://seu-backend.com |
| NF_CLIENT_ID        | myclientid              |
| NF_CLIENT_SECRET    | myclientsecret          |
| NODE_VERSI0N        | 24                      |
| VITE_BACKEND_URL    | https://seu-backend.com |

## 3. Fazer as modificações no projeto

### 3.1.  Atualizar o arquivo `src/utils/system.ts`

- Deletar as referências para as credenciais. A código deve ficar como mostrado a seguir.

```js
export const CART_KEY = "com.devsuperior.dscommerce/Cart";
export const TOKEN_KEY = "com.devsuperior.dscommerce/Token";

export const BASE_URL = import.meta.env.VITE_BACKEND_URL ?? "http://localhost:8080";
```

### 3.2. Criar um arquivo `netlify.toml` na pasta raiz do projeto

```js
[build]
  command = "yarn build"
  publish = "dist"
  functions = "netlify/functions"
```

### 3.3. Criar o arquivo `netlify/functions/login.ts`

```js
import type { Handler } from '@netlify/functions';

const BASE_URL = process.env.NF_BACKEND_URL;
const CLIENT_ID = process.env.NF_CLIENT_ID;
const CLIENT_SECRET = process.env.NF_CLIENT_SECRET;

const TOKEN_PATH = '/oauth/token';

export const handler: Handler = async (event) => {
  try {
    if (event.httpMethod !== 'POST') {
      return { statusCode: 405, body: 'Method Not Allowed' };
    }

    if (!BASE_URL || !CLIENT_ID || !CLIENT_SECRET) {
      return { statusCode: 500, body: 'Missing server environment variables' };
    }

    // O body chega como string (x-www-form-urlencoded ou JSON).
    // Vamos aceitar ambos para conveniência.
    const contentType = event.headers['content-type'] || event.headers['Content-Type'] || '';
    let body: Record<string, unknown> = {};

    if (contentType.includes('application/json')) {
      body = JSON.parse(event.body || '{}');
    } else {
      // trata como x-www-form-urlencoded por padrão
      const params = new URLSearchParams(event.body || '');
      body = Object.fromEntries(params.entries());
    }

    const basic = Buffer.from(`${CLIENT_ID}:${CLIENT_SECRET}`).toString('base64');

    // Constrói corpo como application/x-www-form-urlencoded
    const formData = new URLSearchParams();
    for (const [k, v] of Object.entries(body)) {
      if (v !== undefined && v !== null) {
        formData.append(k, String(v));
      }
    }

    // Faz a requisição para o backend
    const resp = await fetch(`${BASE_URL}${TOKEN_PATH}`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Authorization': `Basic ${basic}`,
      },
      body: formData,
    });

    const text = await resp.text();
    let responseBody: string;
    let responseHeaders: Record<string, string>;

    try {
      responseBody = JSON.stringify(JSON.parse(text));
      responseHeaders = { 'Content-Type': 'application/json' };
    } catch {
      // Caso não seja JSON, devolve como texto
      responseBody = text;
      responseHeaders = { 'Content-Type': 'text/plain; charset=utf-8' };
    }

    return {
      statusCode: resp.status,
      headers: responseHeaders,
      body: responseBody,
    };
  } catch (err: any) {
    return {
      statusCode: 500,
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ error: 'proxy_error', message: err?.message ?? 'unknown_error' }),
    };
  }
};
```

### 3.4. Atualizar o arquivo `src/services/auth-service.ts`

- Atualize a função `loginRequest` conforme código a seguir
- Corrija os imports

```js
export function loginRequest(loginData: CredentialsDTO) {
  const headers = {
    "Content-Type": "application/x-www-form-urlencoded",
  };

  const requestBody = QueryString.stringify({
    ...loginData,
    grant_type: "password",
  });

  return axios({
    method: "POST",
    url: "/.netlify/functions/login",
    data: requestBody,
    headers,
    withCredentials: false
  });
}
```

## 4. Salvar um novo commit e enviar para o Github

```bash
git add .
git commit -m "Netlify function"
```
