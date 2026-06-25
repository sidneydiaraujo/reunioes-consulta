# reunioes-consulta

Skill do Claude Code que busca e responde perguntas sobre reuniões salvas no OneDrive. Faz parte de um ecossistema de três peças para gestão automática de reuniões:

| Peça | O que faz |
|---|---|
| **Rotina diária 13:45** (Claude Desktop) | Varre transcrições do Teams, salva resumos no OneDrive e envia rascunho no Gmail |
| **reunioes-consulta** (esta skill) | Consulta em linguagem natural os resumos e documentos RF salvos |
| **resumo-reunioes-teams** (skill manual) | Processa e salva uma reunião específica sob demanda |

---

## O que essa skill faz

Permite consultar em linguagem natural o conteúdo das reuniões salvas:

- O que foi **decidido** sobre determinado assunto
- Quais são os **combinados** e **próximos passos** do time
- Quais **pendências** estão em aberto
- Quais **riscos** foram levantados
- O que **fulano** ficou responsável de fazer
- **Requisitos Funcionais** de refinamentos: RF-XX, critérios de aceite, escopo, glossário

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
├── SKILL.md               # Definição da skill (lida pelo Claude)
├── rotina-diaria-1345.md  # Prompt da rotina diária para o Claude Desktop
└── README.md
```

Esta skill não possui scripts Python — toda a busca é feita via integração Microsoft 365 diretamente pelo Claude.

---

## Rotina diária automática (13:45 BRT)

O arquivo [`rotina-diaria-1345.md`](./rotina-diaria-1345.md) contém o prompt completo da rotina configurada no Claude Desktop para execução diária às 13:45 BRT. Cole o conteúdo na rotina do Claude Desktop para ativá-la.

### O que a rotina faz

1. **Calcula a janela de varredura:** 14:00 BRT do último dia útil anterior → 13:45 BRT de hoje
2. **Verifica o OneDrive** (fonte primária) — ignora reuniões que já têm resumo salvo
3. **Busca reuniões no calendário** com filtros (cancela, Diálogo de Inovação, organizadores bloqueados)
4. **Processa transcrições** do Teams por tipo:
   - Dailys e reuniões especiais → resumo estruturado (participantes, decisões, próximos passos)
   - Refinamentos / Iterações → documento de Requisito Funcional com 8 seções (template Thunders)
5. **Salva localmente no OneDrive** — sincronizado automaticamente para o SharePoint:

   ```
   C:\Users\sidne\OneDrive\Reuniões dos Times\
   ├── B2B\
   ├── Projetos\
   ├── Evolução\
   ├── B2B2C\
   ├── Refinamentos\
   └── Outros\
   ```

6. **Cria rascunho consolidado no Gmail** com as 3 seções (Dailys / Reuniões / Refinamentos)
7. **Exibe relatório final** com contagem de reuniões processadas, ignoradas e sem transcrição

### Integrações necessárias no Claude Desktop

- Microsoft 365 (leitura de calendário e transcrições)
- Gmail (criação de rascunho)
- Acesso ao sistema de arquivos local (gravação em `C:\Users\sidne\OneDrive\`)

---

## Integração com resumo-reunioes-teams

Esta skill é complementar à **resumo-reunioes-teams**:
- `resumo-reunioes-teams` → processa e salva uma reunião específica sob demanda
- `reunioes-consulta` → busca e responde perguntas sobre os resumos salvos
- **Rotina diária** → automatiza o ciclo completo sem intervenção manual

Instale as três peças para ter o fluxo completo de gestão de reuniões.
