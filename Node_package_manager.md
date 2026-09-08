# Gerenciadores de Pacotes do Node.js: npm, pnpm, npx, Yarn e Bun

## 1. npm (Node Package Manager)

O **gerenciador padrão** do Node.js, instalado automaticamente junto com ele. É o mais compatível e amplamente documentado.

### Comandos essenciais

```bash
# Iniciar um projeto (cria o package.json)
npm init
npm init -y          # pula as perguntas, usa valores padrão

# Instalar dependências
npm install                # instala tudo do package.json
npm install express        # instala e salva em "dependencies"
npm install -D jest        # instala como dependência de desenvolvimento
npm install -g typescript  # instala globalmente (na máquina)

# Remover e atualizar
npm uninstall lodash
npm update                 # atualiza pacotes dentro das versões permitidas

# Instalação limpa (CI/CD) — usa exatamente o que está no lockfile
npm ci

# Executar scripts do package.json
npm run dev
npm test

# Verificar pacotes desatualizados
npm outdated
```

### Características

| Aspecto | Detalhe |
|---|---|
| Lockfile | `package-lock.json` |
| Estrutura | `node_modules` **flat** (achatada) |
| Velocidade | Moderada (melhorou muito desde a v7) |
| Ponto fraco | Alto consumo de disco; permite "dependências fantasma" (usar pacotes que não estão declarados no `package.json`) |

---

## 2. pnpm (Performant npm)

Criado para resolver os principais problemas do npm: **velocidade e desperdício de disco**. Usa um **armazenamento global com links simbólicos/hard links**, então o mesmo pacote instalado em 50 projetos ocupa espaço em disco **apenas uma vez**.

### Instalação

```bash
npm install -g pnpm
# ou via script oficial:
curl -fsSL https://get.pnpm.io/install.sh | sh -
```

### Comandos essenciais (equivalência com npm)

| npm | pnpm |
|---|---|
| `npm install` | `pnpm install` |
| `npm install express` | `pnpm add express` |
| `npm install -D jest` | `pnpm add -D jest` |
| `npm install -g typescript` | `pnpm add -g typescript` |
| `npm uninstall lodash` | `pnpm remove lodash` |
| `npm run dev` | `pnpm dev` (executa scripts direto) |
| `npm ci` | `pnpm install --frozen-lockfile` |

### Exemplos práticos

```bash
# Ver por que um pacote está instalado (ótimo para debugging)
pnpm why react

# Limpar o store global
pnpm store prune

# Monorepo: rodar script em todos os pacotes do workspace
pnpm -r run build

# Filtrar por pacote específico no monorepo
pnpm --filter frontend dev
```

### Características

| Aspecto | Detalhe |
|---|---|
| Lockfile | `pnpm-lock.yaml` |
| Estrutura | `node_modules` com links simbólicos + store global em `~/.local/share/pnpm/store` |
| Velocidade | Muito rápida |
| Diferencial | **Dependências estritas** — você só acessa o que declarou; excelente suporte a **monorepos** (workspaces) |
| Usado por | Vite, Vue, Svelte, Astro, Prisma e muitos projetos modernos |

---

## 3. npx (Node Package Execute)

⚠️ **Atenção:** o npx **não é um gerenciador de pacotes** — é um **executor**. Ele vem junto com o npm (desde a v5.2) e serve para **executar pacotes sem instalá-los globalmente**.

### Casos de uso

```bash
# Criar projetos sem "poluir" a máquina com instalações globais
npx create-react-app meu-app
npx create-vite meu-app --template react
npx create-next-app@latest

# Rodar CLIs uma única vez
npx cowsay "Olá, mundo!"
npx http-server .              # sobe um servidor local na hora
npx json-server db.json        # cria uma API fake rapidamente

# Executar uma versão específica de um pacote
npx node@18 script.js

# Executar um pacote sem instalá-lo, passando flags
npx --yes prettier --write .   # --yes pula a confirmação de download

# Verificar qual binário será executado
npx --no-install eslint .      # só roda se já estiver instalado localmente
```

### Quando usar npx?

| Cenário | Vale a pena? |
|---|---|
| Scaffolding de projetos (`create-*`) | ✅ Sim — você usa uma vez e descarta |
| Testar uma CLI antes de adicionar ao projeto | ✅ Sim |
| Ferramenta usada diariamente | ❌ Prefira instalar como dependência de dev (`npm i -D`) para garantir a mesma versão para toda a equipe |

---

## 4. Yarn

Criado pelo Facebook em 2016 para suprir limitações do npm da época (hoje o npm alcançou boa parte das funcionalidades). Existem duas gerações:

- **Yarn Classic (1.x)** — em modo de manutenção
- **Yarn Berry (2+)** — moderno, com **Plug'n'Play (PnP)** que elimina a pasta `node_modules`

### Comandos essenciais

```bash
# Instalação do Yarn via corepack (recomendado, vem com o Node)
corepack enable
corepack prepare yarn@stable --activate

yarn                # equivale a yarn install
yarn add express    # adiciona dependência
yarn add -D jest    # dependência de dev
yarn remove lodash  # remove
yarn up             # atualiza (Berry)
yarn dev            # executa scripts sem precisar de "run"

# Equivalente ao npx
yarn dlx create-react-app meu-app
yarn create vite meu-app --template react
```

### Características

| Aspecto | Detalhe |
|---|---|
| Lockfile | `yarn.lock` |
| Diferencial (Berry) | PnP (sem `node_modules`), zero-installs (commit do cache no repositório), workspaces maduros |
| Ponto fraco | PnP pode gerar incompatibilidade com ferramentas antigas; curva de aprendizado maior |

---

## 5. Bun (runtime + gerenciador + bundler)

O mais recente (2022). Além de ser um **runtime JavaScript** concorrente do Node, inclui um gerenciador de pacotes **extremamente rápido**.

```bash
# Instalação
curl -fsSL https://bun.sh/install | bash

bun install              # instala dependências
bun add express          # adiciona dependência
bun add -d jest          # dependência de dev (-d minúsculo!)
bun remove lodash        # remove
bun run dev              # executa scripts
bun dev                  # atalho

# Equivalente ao npx
bunx create-vite meu-app
```

| Aspecto | Detalhe |
|---|---|
| Lockfile | `bun.lockb` (binário) / `bun.lock` (texto, versões recentes) |
| Velocidade | A mais rápida do mercado |
| Ponto fraco | Ecossistema mais jovem; nem todo pacote nativo é 100% compatível |

---

## 6. Tabela Comparativa Geral

| Critério | npm | pnpm | Yarn Berry | Bun |
|---|---|---|---|---|
| Vem com o Node | ✅ | ❌ | ❌ (via corepack) | ❌ |
| Velocidade de install | 🟡 Moderada | 🟢 Rápida | 🟢 Rápida | 🟣 Muito rápida |
| Economia de disco | 🔴 Alta repetição | 🟢 Store global | 🟡 Cache local | 🟡 Cache local |
| Dependências estritas | ❌ | ✅ | ⚙️ Configurável | ⚙️ Configurável |
| Monorepo (workspaces) | 🟡 Básico | 🟢 Excelente | 🟢 Excelente | 🟡 Bom |
| Executor tipo npx | `npx` | `pnpm dlx` / `pnpm x` | `yarn dlx` | `bunx` |
| Lockfile | `package-lock.json` | `pnpm-lock.yaml` | `yarn.lock` | `bun.lockb` |
| Maturidade | Alta | Alta | Alta | Em crescimento |

---

## 7. Exemplo Prático Completo: Mesmo Projeto nos 4 Gerenciadores

**Cenário:** criar uma API com Express e Jest para testes.

```bash
# ─── Com npm ───
mkdir minha-api && cd minha-api
npm init -y
npm install express
npm install -D jest
npx jest                       # executa os testes
npm run dev

# ─── Com pnpm ───
mkdir minha-api && cd minha-api
pnpm init
pnpm add express
pnpm add -D jest
pnpm jest
pnpm dev

# ─── Com Yarn ───
mkdir minha-api && cd minha-api
yarn init -y
yarn add express
yarn add -D jest
yarn jest
yarn dev

# ─── Com Bun ───
mkdir minha-api && cd minha-api
bun init
bun add express
bun add -d jest
bun jest
bun dev
```

Em todos os casos, o `package.json` resultante é praticamente idêntico — **os gerenciadores são intercambiáveis**, mas cada um gera seu próprio lockfile (não misture lockfiles no mesmo repositório).

---

## 8. Qual Escolher? (Recomendações)

| Situação | Recomendação |
|---|---|
| Iniciante / máximo de compatibilidade / tutoriais | **npm** |
| Monorepos, economia de disco, projetos modernos (Vite, Vue) | **pnpm** ✅ (escolha mais popular atualmente em novos projetos) |
| Equipes que já usam / precisam de PnP | **Yarn** |
| Projetos novos priorizando performance máxima / usar como runtime | **Bun** |

**Dica de ouro:** em times, padronize um único gerenciador e use o campo `"packageManager"` no `package.json` para forçá-lo:

```json
{
  "packageManager": "pnpm@9.0.0"
}
```

---

# Instalação Local vs Global: O Que Acontece de Verdade

## 1. Os Três "Lugares" Onde um Pacote Pode Existir

Quando falamos de pacotes Node.js, o conteúdo pode viver em três contextos distintos:

| Contexto | Onde fica | O que significa |
|---|---|---|
| **Local (projeto)** | `./node_modules` do projeto | Disponível apenas para o código desse projeto |
| **Cache/Store** | `~/.npm` (npm) ou store do pnpm | Apenas **conteúdo baixado em cache**, para não baixar de novo da internet |
| **Global** | Diretório específico da máquina | Binários disponíveis no **PATH** — funcionam em qualquer terminal |

⚠️ O erro conceitual mais comum é **confundir cache com instalação global**. Cache é só uma cópia dos arquivos para economizar download — não torna nenhum comando executável globalmente.

---

## 2. O Que "Global" Realmente Significa

Instalar com `-g` significa colocar os **binários** do pacote em um diretório que está no **PATH do sistema**. Exemplo prático com o TypeScript:

```bash
# ─── Instalação LOCAL ───
cd meu-projeto
npm install -D typescript
# tsc NÃO funciona se você digitar "tsc" em qualquer pasta...

tsc                       # ❌ command not found (fora do projeto)
npx tsc                   # ✅ funciona — o npx busca em ./node_modules/.bin
npm run build             # ✅ funciona — scripts do package.json enxergam o .bin local

# ─── Instalação GLOBAL ───
npm install -g typescript
cd /qualquer/outra/pasta
tsc --version             # ✅ funciona em QUALQUER lugar do terminal
```

**Você pode verificar esses locais:**

```bash
npm root -g         # mostra onde ficam os pacotes globais do npm
# ex: /usr/local/lib/node_modules  (Linux/Mac)
# ex: C:\Users\Você\AppData\Roaming\npm\node_modules  (Windows)

npm ls -g --depth=0 # lista tudo que está instalado globalmente

npm ls typescript   # verifica se o pacote existe no projeto atual
```

---

## 3. A Nuance do pnpm: O Store Global

O pnpm realmente tem um "componente global" que recebe **todo pacote baixado, mesmo em instalações locais**:

```bash
pnpm add express        # instalação LOCAL no projeto...
pnpm store path         # ...mas o conteúdo foi parar no store global também!
# ex: /home/você/.local/share/pnpm/store/v10
```

**Como isso funciona?**

```
┌─────────────────────────────────────────────────────┐
│  STORE GLOBAL do pnpm (armazenamento centralizado)  │
│  ~//.local/share/pnpm/store                         │
│                                                     │
│  [express@4.19.2]  [react@18.3.1]  [lodash@4.17.21] │
│   (arquivos reais, guardados UMA única vez)         │
└──────────────┬──────────────────────────────────────┘
               │  hard links (não é cópia!)
               ▼
┌─────────────────────────────────────────────────────┐
│  node_modules do PROJETO (instalação local)         │
│                                                     │
│  ./node_modules/express  → aponta para o store      │
│                                                     │
│  ⚠️ NÃO ocupa espaço em disco duplicado,           │
│     mas só existe DENTRO deste projeto              │
└─────────────────────────────────────────────────────┘
```

**Então o express foi "adicionado ao gerenciador global"?**

Em parte sim, em parte não — e a distinção é crucial:

| Aspecto | Store global do pnpm | Instalação global (`pnpm add -g`) |
|---|---|---|
| Conteúdo dos arquivos | ✅ Fica lá (uma vez só) | ✅ Fica lá |
| Projeto pode importar (`import express...`) | ✅ Sim, **apenas neste projeto** | ❌ Não — global serve para CLIs |
| Binário disponível no PATH (qualquer terminal) | ❌ **Não** | ✅ Sim |
| Aparece em `pnpm ls -g` | ❌ Não | ✅ Sim |

O store do pnpm é apenas um **repositório de conteúdo (cache inteligente)**. Ele economiza disco, mas não "expõe" o pacote para a sua máquina. Instalar `express` localmente em 10 projetos diferentes com pnpm resulta em **uma única cópia física** dos arquivos — mas cada projeto precisa da sua própria declaração no `package.json` e dos seus próprios links para usar.

Isso é justamente o **oposto** da instalação global: `-g` serve para ferramentas de linha de comando que você quer usar em qualquer pasta (como `typescript`, `nodemon`, `pm2`), **sem** vínculo com nenhum projeto.

---

## 4. E o npx? Onde Ele Encaixa?

O `npx` tem um comportamento próprio, com uma ordem de resolução:

```bash
npx jest
```

O npx procura nesta ordem:

1. **`./node_modules/.bin/`** do projeto → achou? Executa daí (não instala nada)
2. **Pacotes globais** → achou? Executa daí
3. **Não achou em nenhum lugar?** → baixa **temporariamente** para um cache próprio (`~/.npm/_npx`), executa e **não instala em lugar nenhum permanentemente** — nem no projeto, nem globalmente

```bash
# Demonstração:
npx cowsay "Teste"
# 🐮 Executa normalmente (baixou temporariamente)

cd meu-projeto
npm ls cowsay        # ❌ (empty) — NÃO foi adicionado ao projeto
npm ls -g cowsay     # ❌ — NÃO foi instalado globalmente
```

O equivalente no pnpm é `pnpm dlx`, e no Bun é `bunx` — mesmo comportamento de execução temporária.

---

## 5. Resumo Visual: O Que Acontece com Cada Comando

Usando o pacote fictício `ferramenta-cli` como exemplo:

| Comando | `node_modules` do projeto | `package.json` | Store/Cache | PATH global |
|---|---|---|---|---|
| `npm install ferramenta-cli` | ✅ | ✅ em `dependencies` | ✅ cache | ❌ |
| `npm install -g ferramenta-cli` | ❌ | ❌ | ✅ cache | ✅ |
| `npx ferramenta-cli` | ❌ | ❌ | ⚠️ cache temporário | ❌ |
| `pnpm add ferramenta-cli` | ✅ (via hard link) | ✅ | ✅ **store global** | ❌ |
| `pnpm add -g ferramenta-cli` | ❌ | ❌ | — | ✅ |

---

## 6. Regra Prática

> **Instalação global (`-g`)** = ferramentas de terminal que você usa em qualquer lugar e que não pertencem a um projeto específico (ex: `typescript`, `nodemon`, `vercel`).
>
> **Instalação local (sem `-g`)** = tudo que seu projeto importa no código (`express`, `react`, `jest`). Mesmo que o pnpm guarde os arquivos no store global, o pacote só é "utilizável" dentro dos projetos que o declaram.

**Dica extra:** uma tendência moderna é evitar instalações globais até para ferramentas de CLI — instalando-as como dependência de desenvolvimento (`-D`) no projeto. Isso garante que **toda a equipe use exatamente a mesma versão** da ferramenta, versionada junto com o código no repositório.