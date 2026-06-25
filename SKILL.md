---
name: reunioes-consulta
description: Consulta e responde perguntas sobre reuniões salvas no OneDrive, buscando informações em resumos de reuniões do Teams e documentos de Requisito Funcional gerados em Refinamentos. Use esta skill SEMPRE que o usuário perguntar sobre o conteúdo de reuniões passadas, quiser saber o que foi decidido, combinado ou discutido em alguma reunião, ou buscar informações específicas nos resumos salvos. Acione também para perguntas sobre requisitos funcionais, histórias refinadas, critérios de aceite, regras de negócio ou escopo de funcionalidades. Exemplos: "o que foi decidido sobre X", "qual o combinado da reunião de Y", "quais são os próximos passos do time B2B", "tem alguma pendência sobre Z", "o que foi dito sobre", "me mostra os riscos levantados", "quando foi combinado que", "qual o requisito funcional do refinamento de W", "o que ficou fora do escopo no refinamento de X", "quais RF foram levantados para Y".
---

# Consulta de Reuniões

Busca informações nos resumos de reuniões e documentos de Requisito Funcional salvos no OneDrive e responde perguntas com contexto preciso.

## Tipos de documento disponíveis

Existem dois tipos de documento na pasta `Reuniões dos Times`:

**1. Resumos de reunião** — gerados para Dailys e demais reuniões (Planning, Review, Alinhamento, Retrospectiva, etc.)
Contêm: decisões, combinados, próximos passos, pendências, riscos, participantes.

**2. Requisitos Funcionais** — gerados automaticamente para reuniões de Refinamento
Seguem o template Thunders com 7 seções: Visão Geral, Declaração do Problema, Contexto Técnico, Requisitos Funcionais (RF-XX), Fora do Escopo, Glossário e Referências.
Salvos em: `Reuniões dos Times/Refinamentos`

---

## Regra de escopo de busca

**Por padrão, busque apenas reuniões da semana atual** (segunda a domingo da semana em curso). Nunca varre reuniões fora desse intervalo sem autorização explícita do usuário.

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
- **O que** o usuário quer saber (decisão, combinado, próximo passo, risco, pendência, requisito funcional, critério de aceite, regra de negócio, escopo, glossário)
- **Tipo de documento**: resumo de reunião ou Requisito Funcional de refinamento
- **Filtros** disponíveis: time específico (B2B, Projetos), funcionalidade, período, nome de pessoa, palavra-chave
- **Escopo temporal**: semana atual (padrão) ou período ampliado (requer confirmação)

### 2. Escolher a pasta de busca

| Tipo de pergunta | Pasta |
|---|---|
| Sobre dailys, planning, review, alinhamento, retrospectiva | `Reuniões dos Times` |
| Sobre requisitos, RF, refinamento, critério de aceite, escopo | `Reuniões dos Times/Refinamentos` |
| Dúvida sobre o tipo | Busque nas duas pastas |

### 3. Buscar nos documentos salvos

Use `mcp__claude_ai_Microsoft_365__sharepoint_search` com:

```
query: <termos-chave da pergunta do usuário>
folderName: Reuniões dos Times        (ou /Refinamentos se for sobre requisitos)
fileType: docx
afterDateTime: <segunda-feira da semana atual>
beforeDateTime: <domingo da semana atual>
```

Se o usuário especificou um time, refine:
```
folderName: Reuniões dos Times/B2B   (ou /Projetos, etc.)
```

### 4. Ler o conteúdo dos arquivos relevantes

Use `mcp__claude_ai_Microsoft_365__read_resource` com os URIs retornados pela busca.

Leia quantos arquivos forem necessários para responder bem — em geral 1 a 3 já são suficientes. Se a busca retornar muitos resultados, priorize os mais recentes.

### 5. Responder com contexto

Responda de forma direta e contextualizada:
- Cite a data e o título da reunião ou do Requisito Funcional de onde vem a informação
- Se a informação aparecer em múltiplos documentos, consolide e mostre a evolução
- Se não encontrar nada relevante, diga claramente e sugira termos alternativos

### 6. Oferecer próximas ações

Ao final da resposta, ofereça:
- "Quer ver o documento completo desta reunião/requisito?"
- "Quer resumir uma nova reunião?" → acione a skill `resumo-reunioes-teams`

---

## Tipos de consulta e como responder

### Consultas sobre reuniões (Dailys, Planning, Review, etc.)

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

**Consultas abertas** ("me conta o que rolou nas reuniões de B2B")
→ Busque as últimas 3 reuniões do time e faça um consolidado resumido.

---

### Consultas sobre Requisitos Funcionais (Refinamentos)

**"Qual o requisito funcional de [funcionalidade]?"**
→ Busque em `Reuniões dos Times/Refinamentos`. Apresente o documento completo com todas as seções RF-XX.

**"O que foi definido no refinamento de [história/módulo]?"**
→ Busque pelo nome da funcionalidade. Mostre: Visão Geral, Requisitos RF-XX e Próximos Passos.

**"Quais RF foram levantados para [funcionalidade]?"**
→ Busque na seção REQUISITOS FUNCIONAIS do documento. Liste cada RF-XX com sua descrição.

**"O que ficou fora do escopo no refinamento de X?"**
→ Busque na seção FORA DO ESCOPO. Liste os itens excluídos.

**"Qual o critério de aceite / regra de negócio de [funcionalidade]?"**
→ Busque nas seções CONTEXTO TÉCNICO e REQUISITOS FUNCIONAIS. Consolide as regras encontradas.

**"Tem alguma pendência do refinamento de X?"**
→ Busque na seção PENDÊNCIAS ANTES DA IMPLEMENTAÇÃO. Mostre os itens com responsável.

**"O que significa [sigla] no contexto do refinamento?"**
→ Busque na seção GLOSSÁRIO DE SIGLAS do documento.

**"Quais refinamentos aconteceram essa semana?"**
→ Busque em `Reuniões dos Times/Refinamentos` pelo intervalo da semana. Liste os documentos encontrados com título, data e módulo.

---

## Estratégia de busca

Se a busca inicial não retornar resultados úteis, tente:
1. Termos mais genéricos (ex: "medição" em vez de "rateio de consumo ACL")
2. Busque nas duas pastas (`Reuniões dos Times` e `Reuniões dos Times/Refinamentos`)
3. Sem filtro de pasta (busca em todo SharePoint acessível)
4. Pergunte ao usuário: "Em qual time, módulo ou período seria essa reunião/requisito?"

Sempre prefira resultados de `Reuniões dos Times/` — são os documentos gerados pelas skills. Outros arquivos no SharePoint podem ser irrelevantes.

---

## Acesso à pasta de resumos

Os resumos e requisitos ficam na pasta `Reuniões dos Times` (e subpasta `Refinamentos`) no OneDrive de quem os gera.

**Se a busca não retornar nenhum resultado**, informe:
> "Não encontrei documentos de reuniões. Isso pode significar que:
> 1. Não há resumos ou requisitos salvos no período pesquisado, ou
> 2. A pasta `Reuniões dos Times` não está compartilhada com você.
>
> Peça ao responsável pelos resumos para compartilhar a pasta ou torná-la visível para toda a organização no OneDrive."

**Se a pasta for visível para a organização** (compartilhamento org-wide no SharePoint), qualquer colega autenticado no mesmo tenant consegue buscar e ler os arquivos sem configuração adicional.
