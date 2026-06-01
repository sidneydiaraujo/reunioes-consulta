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
- Integração **Microsoft 365** habilitada no Claude Code
- Acesso à pasta `Reuniões dos Times` no OneDrive de quem gera os resumos (veja abaixo)

---

## Acesso à pasta de resumos

Esta skill busca os resumos salvos por quem usa a **resumo-reunioes-teams**. Para que a busca funcione, você precisa ter acesso à pasta `Reuniões dos Times` no OneDrive dessa pessoa.

### Opção A — Pasta visível para toda a organização (recomendado)

A pasta `Reuniões dos Times` já está compartilhada com toda a organização Thunders em modo de visualização:

> 📁 [Reuniões dos Times](https://thunderscombr-my.sharepoint.com/:f:/g/personal/sidney_silva_thunders_com_br/IgCLlEtMLkIGQIfrp_O8VP63AW3gpu1VJDuDA10Ihl_zpP4)

Qualquer colega autenticado no tenant Thunders já tem acesso — **sem precisar solicitar nada**. Basta instalar a skill e conectar o Microsoft 365.

### Opção B — Compartilhamento individual

Se a pasta não for visível para a organização, peça ao dono da pasta para compartilhá-la diretamente com você:
1. Dono: clique com botão direito em `Reuniões dos Times` → **Compartilhar**
2. Adicione seu email com permissão de **visualização**
3. Depois do compartilhamento, a busca da skill já encontra os arquivos

### O que acontece sem acesso

Se você instalar a skill sem ter acesso à pasta, as buscas não retornarão resultados. O Claude informará que não encontrou arquivos e sugerirá verificar o compartilhamento.

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

No Claude Code, acesse as configurações de integrações e habilite **Microsoft 365**. Na primeira vez, o Claude vai solicitar autenticação com sua conta corporativa.

### 3. Verifique o acesso à pasta

Confirme com quem gera os resumos que você tem acesso à pasta `Reuniões dos Times` (Opção A ou B acima).

### 4. Reinicie o Claude Code

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
