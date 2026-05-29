# reunioes-consulta

Skill do Claude Code que busca e responde perguntas sobre reuniões salvas no OneDrive, consultando os resumos gerados pela skill **resumo-reunioes-teams**.

---

## O que essa skill faz

Permite consultar em linguagem natural o conteúdo das reuniões salvas:

- O que foi **decidido** sobre determinado assunto
- Quais são os **combinados** e **próximos passos** do time
- Quais **pendências** estão em aberto
- Quais **riscos** foram levantados
- O que **fulano** ficou responsável de fazer
- Qualquer conteúdo dos resumos de reuniões

Por padrão, busca apenas reuniões da semana atual. Para períodos anteriores, basta pedir explicitamente.

---

## Pré-requisitos

- [Claude Code](https://claude.ai/code) instalado
- Integração **Microsoft 365** habilitada no Claude Code (para acessar o SharePoint/OneDrive)
- Skill **resumo-reunioes-teams** instalada e com ao menos um resumo salvo no OneDrive

---

## Instalação

### 1. Clone o repositório na pasta de skills do Claude

**macOS / Linux:**
```bash
mkdir -p ~/.claude/skills
cd ~/.claude/skills
git clone https://github.com/sidneydiaraujo/reunioes-consulta
```

**Windows (PowerShell):**
```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills"
cd "$env:USERPROFILE\.claude\skills"
git clone https://github.com/sidneydiaraujo/reunioes-consulta
```

### 2. Habilite a integração Microsoft 365 no Claude Code

Esta skill usa o MCP do Microsoft 365 para buscar documentos no SharePoint/OneDrive.

No Claude Code, acesse as configurações de integrações e habilite **Microsoft 365**. Na primeira vez, o Claude vai solicitar autenticação.

### 3. Reinicie o Claude Code

Feche e reabra o Claude Code para carregar a skill.

---

## Como usar

```
"O que foi decidido sobre o campo CCEE na última reunião?"
"Quais são os próximos passos do time Projetos?"
"Tem alguma pendência aberta com o João?"
"Quais riscos foram levantados esta semana?"
"O que foi combinado sobre a integração com CCEE?"
"Me mostra o resumo das reuniões de B2B desta semana"
```

---

## Estrutura do projeto

```
reunioes-consulta/
├── SKILL.md    # Definição da skill (lida pelo Claude)
└── README.md
```

Esta skill não possui scripts Python — toda a busca é feita via integração Microsoft 365 diretamente pelo Claude.

---

## Integração com resumo-reunioes-teams

Esta skill é complementar à **resumo-reunioes-teams**:
- `resumo-reunioes-teams` → processa e salva os resumos no OneDrive
- `reunioes-consulta` → busca e responde perguntas sobre esses resumos

Instale as duas para ter o fluxo completo de gestão de reuniões.
