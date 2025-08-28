# ReactJS Professional

## Atualizar 

```bash
sudo apt update
```

## Curl, Git, build-essential

Instalar:

```
sudo apt install -y curl git build-essential
```

Conferir Git:

```
git --version
```

> **Atenção**: é necessário configurar seu Git, inclusive o acesso ao Github via SSH. Caso precise de ajuda, siga nosso tutorial:

[![Image](https://img.youtube.com/vi/_hZf1teRFNg/mqdefault.jpg "Vídeo no Youtube")](https://youtu.be/_hZf1teRFNg)


## NVM (Node Version Manager)

Instalar:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
```

Conferir instalação:

```bash
command -v nvm
```

## Node.js

Instalar:

```bash
nvm install 24
```

Garantir que a versão em uso seja a desejada:

```bash
nvm use 24
```

Conferir:

```bash
node -v
```

## Corepack e Yarn

Instalar Corepack:

```bash
npm i -g corepack
```

Ativar Corepack:

```bash
corepack enable
```

Ativar Yarn:

```bash
corepack prepare yarn@stable --activate
```

Conferir:

```bash
yarn -v
```

