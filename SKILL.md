---
name: reunioes-consulta
description: Consulta e responde perguntas sobre reuniões salvas no OneDrive, buscando informações em resumos de reuniões do Teams. Use esta skill SEMPRE que o usuário perguntar sobre o conteúdo de reuniões passadas, quiser saber o que foi decidido, combinado ou discutido em alguma reunião, ou buscar informações específicas nos resumos salvos. Acione para frases como "o que foi decidido sobre X", "qual o combinado da reunião de Y", "quais são os próximos passos do time B2B", "tem alguma pendência sobre Z", "o que foi dito sobre", "me mostra os riscos levantados", "quando foi combinado que", ou qualquer variação de consulta sobre reuniões.
---

# Consulta de Reuniões

Busca informações nos resumos de reuniões salvos no OneDrive e responde perguntas com contexto preciso.

## Regra de escopo de busca

**Por padrão, busque apenas reuniões da semana atual** (segunda a domingo da semana em curso). Nunca varra reuniões fora desse intervalo sem autorização explícita do usuário.

Para calcular o intervalo da semana atual:
- `afterDateTime`: segunda-feira da semana atual às 00:00:00Z
- `beforeDateTime`: domingo da semana atual às 23:59:59Z

Se o usuário quiser buscar fora da semana atual (ex: "semana passada", "mês passado", "desde sempre"), **pergunte antes de executar**:
> "Essa busca vai além da semana atual. Confirma que quer pesquisar em reuniões anteriores também?"

Só amplie o escopo após confirmação explícita.

---

## Fluxo de execução

### 1. Entender a pergunta
Identifique:
- **O que** o usuário quer saber (decisão, combinado, próximo passo, risco, pendência, participante, regra de negócio, qualquer coisa)
- **Filtros** disponíveis: time específico (B2B, Projetos), período, nome de pessoa, palavra-chave
- **Escopo temporal**: semana atual (padrão) ou período ampliado (requer confirmação)

### 2. Buscar nos resumos salvos

Use a ferramenta `mcp__claude_ai_Microsoft_365__sharepoint_search` para buscar:

```
query: <termos-chave da pergunta do usuário>
folderName: Reuniões dos Times
fileType: docx
afterDateTime: <segunda-feira da semana atual>
beforeDateTime: <domingo da semana atual>
```

Se o usuário especificou um time, refine:
```
folderName: Reuniões dos Times/B2B   (ou /Projetos, etc.)
```

### 3. Ler o conteúdo dos arquivos relevantes

Use `mcp__claude_ai_Microsoft_365__read_resource` com os URIs retornados pela busca.

Leia quantos arquivos forem necessários para responder bem — em geral 1 a 3 já são suficientes. Se a busca retornar muitos resultados, priorize os mais recentes.

### 4. Responder com contexto

Responda de forma direta e contextualizada:
- Cite a data e o título da reunião de onde vem a informação
- Se a informação aparecer em múltiplas reuniões, consolide e mostre a evolução
- Se não encontrar nada relevante, diga claramente e sugira termos alternativos

### 5. Oferecer próximas ações

Ao final da resposta, ofereça:
- "Quer ver o resumo completo desta reunião?"
- "Quer resumir uma nova reunião?" → acione a skill `resumo-reunioes-teams`

---

## Tipos de consulta e como responder

**"O que foi decidido sobre X?"**
→ Busque na seção DECISÕES TOMADAS. Mostre a decisão, a reunião e a data.

**"Quais são os próximos passos do time Y?"**
→ Busque na seção PRÓXIMOS PASSOS filtrando pelo time. Liste os itens, responsáveis e prazos.

**"Tem alguma pendência aberta sobre Z?"**
→ Busque na seção PENDÊNCIAS. Se houver múltiplas reuniões, mostre a mais recente primeiro.

**"O que foi combinado com [nome]?"**
→ Busque nos COMBINADOS e PRÓXIMOS PASSOS pelo nome da pessoa. Consolide os itens.

**"Quais riscos foram levantados?"**
→ Busque em RISCOS E IMPEDIMENTOS. Agrupe por impacto (Alto → Médio → Baixo).

**"Quais regras de negócio foram definidas?"**
→ Busque em REGRAS DE NEGÓCIO. Liste por reunião e data.

**Consultas abertas** ("me conta o que rolou nas reuniões de B2B")
→ Busque as últimas 3 reuniões do time e faça um consolidado resumido.

---

## Estratégia de busca

Se a busca inicial não retornar resultados úteis, tente:
1. Termos mais genéricos (ex: "aprovação" em vez de "aprovação do contrato comercial")
2. Sem filtro de pasta (busca em todo SharePoint acessível)
3. Pergunte ao usuário: "Em qual time ou período seria essa reunião?"

Sempre prefira resultados de `Reuniões dos Times/` — são os resumos gerados pela skill. Outros arquivos no SharePoint podem ser irrelevantes.

---

## Acesso à pasta de resumos

Os resumos ficam na pasta `Reuniões dos Times` no OneDrive de quem os gera. Para que a busca funcione, o usuário que consulta precisa ter acesso a essa pasta.

**Se a busca não retornar nenhum resultado**, informe:
> "Não encontrei resumos de reuniões. Isso pode significar que:
> 1. Não há resumos salvos no período pesquisado, ou
> 2. A pasta `Reuniões dos Times` não está compartilhada com você.
>
> Peça ao responsável pelos resumos para compartilhar a pasta ou torná-la visível para toda a organização no OneDrive."

**Se a pasta for visível para a organização** (compartilhamento org-wide no SharePoint), qualquer colega autenticado no mesmo tenant consegue buscar e ler os arquivos sem configuração adicional.
