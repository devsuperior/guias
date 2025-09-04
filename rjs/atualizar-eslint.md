# Atualização do ESLint para corrigir imports automaticamente

## Explicando o problema

Anteriormente, os imports de tipos no TypeScript podiam ser feitos sem a palavra `type`, por exemplo:

```js
import { ProductDTO } from '../../models/product';

type Props = {
    product: ProductDTO;
}
```

Entretanto, agora é preciso acrescentar a palavra `type` no import:

```js
import { type ProductDTO } from '../../models/product';

type Props = {
    product: ProductDTO;
}
```

É possível manter um projeto atual sem a palavra `type` nesses imports, apenas mudando o valor da opção `verbatimModuleSyntax` do arquivo **tsconfig.app.json** para `false`: 

```js
{
  "compilerOptions": {
    "verbatimModuleSyntax": true,

...
```

Porém, é recomendado que essa opção seja mantida com o valor `true`, porque este é o padrão moderno e favorece projetos mais otimizados.

Assim, **é preciso fazer uma varredura em todo projeto para atualizar todos imports de tipos**. Isso pode ser feito de forma manual, ou de forma automática como vamos mostrar no passo a passo a seguir.

## Passo a passo para atualizar os imports de forma automática

### 1. Instale os seguintes pacotes

```bash
yarn add -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

```bash
yarn add -D eslint @eslint/js @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

### 2. Atualize o arquivo eslint.config.js

**eslint.config.js**

```js
import js from '@eslint/js'
import globals from 'globals'
import reactHooks from 'eslint-plugin-react-hooks'
import reactRefresh from 'eslint-plugin-react-refresh'
import tseslint from 'typescript-eslint'
import { globalIgnores } from 'eslint/config'
import tsParser from '@typescript-eslint/parser';
import tsPlugin from '@typescript-eslint/eslint-plugin';

export default tseslint.config([
  globalIgnores(['dist']),
  {
    files: ['**/*.{ts,tsx}'],
    extends: [
      js.configs.recommended,
      tseslint.configs.recommended,
      reactHooks.configs['recommended-latest'],
      reactRefresh.configs.vite,
    ],
    languageOptions: {
      ecmaVersion: 2020,
      globals: globals.browser,
      parser: tsParser,
      parserOptions: {
        ecmaVersion: 'latest',
        sourceType: 'module',
      },
    },
    plugins: {
      '@typescript-eslint': tsPlugin,
    },
    rules: {
      '@typescript-eslint/no-unused-vars': 'warn',
      '@typescript-eslint/no-unsafe-function-type': 'warn',
      '@typescript-eslint/no-explicit-any': 'warn',
      '@typescript-eslint/consistent-type-imports': ['error', { fixStyle: 'inline-type-imports' }],
    },
  },
])
```

### 3. Crie o arquivo eslint.config.mjs na pasta do projeto, com o seguinte conteúdo

**eslint.config.mjs**

```js
import js from '@eslint/js'
import tsParser from '@typescript-eslint/parser'
import tsPlugin from '@typescript-eslint/eslint-plugin'

export default [
  js.configs.recommended,
  {
    files: ['**/*.{ts,tsx}'],
    languageOptions: {
      parser: tsParser,
      parserOptions: {
        ecmaVersion: 'latest',
        sourceType: 'module',
      },
    },
    plugins: { '@typescript-eslint': tsPlugin },
    rules: {
      '@typescript-eslint/consistent-type-imports': ['error', { fixStyle: 'inline-type-imports' }],
    },
  },
]
```

### 4. Execute o ESLint na pasta do projeto

Apenas verificar o código:

```bash
yarn eslint "src/**/*.{ts,tsx}"
```

Corrigir o código:

```bash
yarn eslint "src/**/*.{ts,tsx}" --fix
```
