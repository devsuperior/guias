# Estrutura do projeto ReactJS + Vite

## Pastas

### .vscode

Configurações do VS Code específicas do projeto. 

### .yarn

Infraestrutura do Yarn Berry. Costuma conter subpastas como:

* `releases/` (o binário do Yarn utilizado pelo projeto)
* `plugins/` (plugins do Yarn)
* `sdks/` (SDKs do TS/ESLint para o editor)
* `cache/` (cache dos pacotes — opcional versionar)
* `patches/` (patches aplicados a dependências)
* `unplugged/` (pacotes “desempacotados” quando necessário)
  Regra geral de versionamento: **versione** `releases/`, `plugins/`, `sdks/`, `patches/` e **não** versione `cache/` e `unplugged/`.

### public

Arquivos estáticos copiados para o build (ficam disponíveis em `/`). Ideal para `favicon`, `robots.txt`, imagens que não precisam passar pelo pipeline do Vite. 

### src

Código-fonte da aplicação (componentes, estilos, hooks, rotas etc.). 

## Arquivos

### .gitignore

Padrões a serem ignorados pelo Git. 

### .pnp.cjs

Runtime do **Plug’n’Play** do Yarn. Ele substitui `node_modules` e mapeia cada import para o pacote correto, garantindo instalações determinísticas e rápidas. Quando você executa scripts via `yarn`, o Yarn injeta esse runtime para a resolução de módulos.

### .pnp.loader.mjs

Loader ESM para o Node. Complementa o PnP quando módulos são carregados em modo ESM. Em termos práticos, você não edita esse arquivo; ele permite que imports ESM funcionem sob PnP.

### README.md

Guia do projeto (como rodar, buildar, estruturar, etc). 

### eslint.config.js

Configuração "flat" do ESLint (v9+). Aqui vivem as regras, plugins (por ex. `@typescript-eslint`, `eslint-plugin-react`), overrides, ignores etc. Com Yarn PnP, rode o ESLint via:

```
yarn eslint .
```

### index.html

Este é o ponto de entrada (entry point) da aplicação Vite. Diferente de bundlers clássicos, o Vite parte deste HTML. 

### package.json

Manifesta o projeto:

* `"scripts"`: `dev`, `build`, `preview`, `lint`, etc.
* `"dependencies"`: `react`, `react-dom` (v19), bibliotecas da app
* `"devDependencies"`: `vite`, `@vitejs/plugin-react` (ou `-swc`), TypeScript, ESLint…
* `"type": "module"` é comum em templates modernos
* `"packageManager": "yarn@3.x"` ajuda ferramentas a usarem a versão correta
  Dica: com PnP, **sempre** use `yarn add`/`yarn remove` (não misture npm/pnpm).

### tsconfig.app.json

Config do TypeScript voltada ao **código do app** (browser). 

### tsconfig.json

Arquivo "raiz" que define opções comuns e **referencia** os outros `tsconfig`s. O TS usa essas referências para compilar partes diferentes com opções diferentes.

### tsconfig.node.json

Config do TS para **arquivos Node** do projeto (por exemplo, `vite.config.ts`, scripts). 

### vite.config.ts

Configuração do Vite.

### yarn.lock

Lockfile do Yarn. Garante que todo mundo instale **exatamente** as mesmas versões de dependências. Com Berry + PnP, ele substitui o uso tradicional de `node_modules`.
