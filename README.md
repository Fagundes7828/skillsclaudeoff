# opensquad — Visão Geral do Projeto

> README técnico gerado a partir da análise de `package.json`, `CLAUDE.md`, `SECURITY.md`, `CONTRIBUTING.md`, `CHANGELOG.md` e `eslint.config.js`.

## O que é

**opensquad** é um framework de orquestração multi-agente, distribuído como pacote npm (`npx opensquad init`), que permite criar "squads" de agentes de IA especializados para automatizar produção de conteúdo, análise de dados e outros fluxos de trabalho — direto de dentro da IDE (Claude Code, Cursor, VS Code + Copilot, Codex, Gemini CLI, Qwen Code, Trae, Open Code, Antigravity).

- **Versão atual:** 0.1.15
- **Licença:** MIT
- **Autor/mantenedor:** Renato Asse ([@renatoasse](https://github.com/renatoasse))
- **Requisito:** Node.js >= 20.0.0
- **Runtime:** ESM (`"type": "module"`), CLI via bin `opensquad`

## Estrutura de diretórios

```
_opensquad/                     núcleo do framework (não editar manualmente)
_opensquad/_memory/             contexto persistente (empresa, preferências)
_opensquad/_browser_profile/    sessões/cookies do Playwright (gitignored)
_opensquad/config/              configuração (ex: playwright.config.json)
_opensquad/core/best-practices/ catálogo de guias de best practices
skills/                         skills instaladas (integrações, scripts, prompts)
squads/                         squads criados pelo usuário
squads/{name}/_investigations/  análises de perfis feitas pelo Sherlock
squads/{name}/output/           conteúdo/arquivos gerados
templates/                      templates canônicos usados por init/update
dashboard/                      Escritório Virtual (interface 2D em Phaser)
bin/, src/, agents/, skills/    código-fonte e agentes do pacote
```

## Componentes principais

| Componente | Papel |
|---|---|
| **Opensquad (skill/comando)** | Ponto de entrada — `/opensquad` |
| **Architect (Atlas)** | Cria e modifica squads, faz perguntas, projeta o pipeline |
| **Sherlock** | Investiga perfis de referência (Instagram, YouTube, X, LinkedIn) via navegador headless (Playwright) |
| **Pipeline Runner** | Executa squads automaticamente, respeitando checkpoints |
| **Dashboard (Escritório Virtual)** | Interface 2D (Phaser) que mostra os agentes trabalhando em tempo real |

## Dependências (`package.json`)

**Runtime:**
- `@inquirer/checkbox`, `@inquirer/input`, `@inquirer/select` — prompts de CLI interativos

**Dev:**
- `eslint` + `@eslint/js` + `globals` — lint (config em `eslint.config.js`, cobrindo `src/`, `bin/`, `tests/`)

**Scripts npm:**
- `test` → `node --test tests/*.test.js`
- `lint` → `eslint src/ bin/ tests/`
- `version` → grava a versão do pacote em `templates/_opensquad/.opensquad-version` e adiciona ao git

## Modelo de segurança (`SECURITY.md`)

- **Sessões de navegador:** cookies persistidos só com consentimento explícito, salvos localmente em `_opensquad/_browser_profile/` (gitignored). Navegador roda headless e pode acessar qualquer URL, não só as referências fornecidas.
- **Hospedagem de imagens:** upload para imgBB usando chave de API própria do usuário (`IMGBB_API_KEY`) — nenhum upload acontece sem configuração explícita.
- **Checkpoints:** são instruções em nível de prompt no pipeline do agente, não guardas programáticas — o enforcement real depende da IDE host (Claude Code, Cursor, etc.).
- **Skills:** sem verificação criptográfica (não são assinadas/hasheadas). Tipos com escopo de execução crescente:
  - `prompt` — só influencia comportamento
  - `script` — executa bash com permissões completas do usuário local
  - `mcp` — conecta a servidores externos, recebe tokens do `.env`
  - `hybrid` — combinação de script + MCP
- Skills da comunidade passam por code review antes de entrar no catálogo oficial; skills de terceiros devem ser revisadas manualmente antes do uso.
- **Consumo de tokens:** cada execução consome tokens pagos (Claude Code, API OpenAI); Sherlock e geração de imagem são as operações mais custosas.

## Como contribuir (`CONTRIBUTING.md`)

Filosofia central: **"verticalizar, não complicar"** — expandir via skills, agents, squads e guias de best practices, mantendo o núcleo (`_opensquad/core/`) simples e sem dependências pesadas (sem banco de dados, sem Docker, sem infra adicional).

**Aceito:**
- Novas skills (integrações com plataformas/APIs — principal tipo de contribuição)
- Guias de best practices (copywriting, design, SEO, etc.)
- Novos agents e squad templates
- Bug fixes, melhorias de performance, documentação, i18n (`src/locales/`: en, pt-BR, es), testes

**Não aceito (via issue para discutir exceções):**
- Trocar o sistema de arquivos por banco de dados
- Adicionar dependências de infraestrutura
- Reescrever `_opensquad/core/`
- Expandir demais a árvore de dependências
- Funcionalidades de desenvolvimento de software genérico (fora do escopo de automação de negócios)

**Fluxo de criação de skill:**
```
skills/sua-skill/
  SKILL.md          (obrigatório — frontmatter YAML + instruções)
  scripts/          (opcional)
  references/       (opcional)
  assets/           (opcional)
```
Checklist de segurança ao submeter: sem credenciais hardcoded, sem comandos destrutivos sem confirmação, sem upload de dados sem aviso no `SKILL.md`, sem escrita fora de `squads/<nome>/output/`.

## Histórico recente (`CHANGELOG.md`)

- **0.1.15** (2026-04-10): suporte a Gemini CLI, Qwen Code e Trae como IDEs.
- **0.1.14** (2026-03-30): skill de e-mail via Resend; ajustes de README e `.gitignore`.
- **0.1.13** (2026-03-30): release grande de segurança/transparência — `SECURITY.md`, aviso de custo de tokens, consentimento para sessões de navegador, dashboard reescrito de PixiJS para Phaser, refatoração do Sherlock em extratores por plataforma.
- **0.1.9–0.1.12**: evolução do dashboard (isométrico, sprites 48x48), sistema de memória por squad, CI com GitHub Actions.
- **0.1.0** (2026-02-23): release inicial — CLI, Architect, Pipeline Runner, squad de Instagram, templates de prompt.

## Notas

Este documento consolida o que já está descrito de forma dispersa em `README.md`, `SECURITY.md` e `CONTRIBUTING.md` — não substitui o README bilíngue de marketing do projeto, que continua sendo a porta de entrada oficial para novos usuários.
