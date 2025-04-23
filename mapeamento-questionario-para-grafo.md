# Mapeamento do Questionário para o Grafo de Conhecimento

Este documento demonstra como as respostas do [Questionário de Auto-Conhecimento](./questionario-perfil-overthinking.md) podem ser mapeadas para o grafo de conhecimento, criando uma representação estruturada do perfil cognitivo do usuário.

## Modelo de Conversão

O sistema de memória utilizará o seguinte modelo para converter as respostas em elementos do grafo:

### 1. Entidades Principais

O grafo de conhecimento começará com algumas entidades principais:

```javascript
// Entidade de Usuário (central)
{
  "name": "Usuario_[ID]",
  "entityType": "pessoa",
  "observations": ["Nome completo", "Data de criação do perfil"]
}

// Entidades de Domínios Cognitivos
{
  "name": "Pensamento_[ID]",
  "entityType": "dominio_cognitivo",
  "observations": ["Representa os padrões de pensamento do usuário"]
}

{
  "name": "Criatividade_[ID]",
  "entityType": "dominio_cognitivo",
  "observations": ["Representa os processos criativos do usuário"]
}

{
  "name": "Decisao_[ID]",
  "entityType": "dominio_cognitivo",
  "observations": ["Representa os processos de tomada de decisão do usuário"]
}

{
  "name": "Produtividade_[ID]",
  "entityType": "dominio_cognitivo",
  "observations": ["Representa os padrões de implementação e execução do usuário"]
}

{
  "name": "Emocional_[ID]",
  "entityType": "dominio_cognitivo",
  "observations": ["Representa os aspectos emocionais do usuário"]
}
```

### 2. Entidades Derivadas

Para cada seção do questionário, serão criadas entidades específicas:

```javascript
// Exemplos de entidades derivadas das respostas
{
  "name": "VelocidadeMental_[ID]",
  "entityType": "caracteristica_cognitiva",
  "observations": [
    "Nível: [resposta à escala 1-10]",
    "Aumenta quando: [resposta]", 
    "Diminui quando: [resposta]"
  ]
}

{
  "name": "EstiloPensamento_[ID]",
  "entityType": "caracteristica_cognitiva",
  "observations": [
    "Linear ou Ramificado: [resposta]",
    "Densidade de ideias simultâneas: [resposta]"
  ]
}

{
  "name": "ParalisiaPorAnalise_[ID]",
  "entityType": "desafio",
  "observations": [
    "Gatilhos principais: [resposta]",
    "Duração típica: [resposta]",
    "Impacto na produtividade: [resposta]"
  ]
}
```

### 3. Relações

As entidades serão conectadas através de relações direcionadas:

```javascript
// Conexões primárias com o usuário
{
  "from": "Usuario_[ID]",
  "to": "Pensamento_[ID]",
  "relationType": "possui"
}

{
  "from": "Usuario_[ID]",
  "to": "Criatividade_[ID]",
  "relationType": "possui"
}

// Conexões secundárias com características específicas
{
  "from": "Pensamento_[ID]",
  "to": "VelocidadeMental_[ID]",
  "relationType": "inclui"
}

{
  "from": "Pensamento_[ID]",
  "to": "EstiloPensamento_[ID]",
  "relationType": "caracterizado_por"
}

// Relações de causa e efeito
{
  "from": "ParalisiaPorAnalise_[ID]",
  "to": "Produtividade_[ID]",
  "relationType": "impacta"
}

// Relações entre entidades
{
  "from": "VelocidadeMental_[ID]",
  "to": "ParalisiaPorAnalise_[ID]",
  "relationType": "contribui_para"
}
```

## Exemplo de Mapeamento

Vejamos um exemplo prático de como as respostas a algumas perguntas específicas seriam convertidas:

### Exemplo 1: Respostas sobre Velocidade Mental

**Respostas hipotéticas:**
- Em uma escala de 1 a 10, quão rápido sua mente costuma funcionar no dia a dia? **8**
- Em quais momentos essa velocidade aumenta significativamente? **"Quando estou sob pressão de prazos ou em ambientes estimulantes como eventos sociais"**
- Em quais momentos você consegue desacelerar seus pensamentos? **"Durante meditação e ao me exercitar ao ar livre"**

**Conversão para o grafo:**

```javascript
// Entidade
{
  "name": "VelocidadeMental_001",
  "entityType": "caracteristica_cognitiva",
  "observations": [
    "Velocidade mental de base: 8/10",
    "Aumenta durante pressão de prazos",
    "Aumenta em ambientes sociais estimulantes",
    "Diminui durante prática de meditação",
    "Diminui durante exercícios ao ar livre"
  ]
}

// Relações
{
  "from": "Pensamento_001",
  "to": "VelocidadeMental_001",
  "relationType": "inclui"
}

{
  "from": "VelocidadeMental_001",
  "to": "ParalisiaPorAnalise_001",
  "relationType": "potencialmente_contribui_para"
}
```

### Exemplo 2: Respostas sobre Paralisia por Análise

**Respostas hipotéticas:**
- Quais tipos de decisões ou tarefas mais frequentemente causam paralisia por análise em você? **"Decisões de carreira e projetos criativos com muitas opções possíveis"**
- Você identifica padrões ou temas recorrentes nessas situações? **"Geralmente envolvem escolhas com consequências de longo prazo e opções mutuamente excludentes"**
- Quais são os primeiros sinais de que você está entrando em um estado de paralisia por análise? **"Começar a criar listas infinitas de prós e contras, e sentir tensão no peito"**

**Conversão para o grafo:**

```javascript
// Entidade
{
  "name": "ParalisiaPorAnalise_001",
  "entityType": "desafio",
  "observations": [
    "Ocorre principalmente em decisões de carreira",
    "Ocorre em projetos criativos com muitas opções",
    "Associada a escolhas com consequências de longo prazo",
    "Associada a opções mutuamente excludentes",
    "Sinal inicial: criação de listas excessivas de prós e contras",
    "Sinal inicial: tensão física no peito"
  ]
}

// Relações
{
  "from": "Usuario_001",
  "to": "ParalisiaPorAnalise_001",
  "relationType": "experimenta"
}

{
  "from": "ParalisiaPorAnalise_001",
  "to": "Decisao_001",
  "relationType": "impacta"
}

{
  "from": "ParalisiaPorAnalise_001",
  "to": "Criatividade_001",
  "relationType": "impacta"
}
```

### Exemplo 3: Respostas sobre Estratégias de Adaptação

**Respostas hipotéticas:**
- Quais estratégias você já tentou para lidar com a paralisia por análise? **"Definir prazos artificiais, pedir opinião de terceiros, técnica 5-4-3-2-1"**
- Quais funcionaram, mesmo que parcialmente? **"Definir prazos artificiais e a técnica 5-4-3-2-1"**
- Quais pioraram a situação? **"Pedir muitas opiniões diferentes de pessoas diferentes"**

**Conversão para o grafo:**

```javascript
// Entidades
{
  "name": "EstrategiaAdaptativa_001",
  "entityType": "estrategia",
  "observations": [
    "Nome: Definir prazos artificiais",
    "Eficácia: Parcialmente eficaz",
    "Contexto: Combate à paralisia por análise"
  ]
}

{
  "name": "EstrategiaAdaptativa_002",
  "entityType": "estrategia",
  "observations": [
    "Nome: Técnica 5-4-3-2-1",
    "Eficácia: Parcialmente eficaz",
    "Contexto: Combate à paralisia por análise"
  ]
}

{
  "name": "EstrategiaContraproducente_001",
  "entityType": "estrategia",
  "observations": [
    "Nome: Consultar múltiplas opiniões",
    "Eficácia: Contraproducente",
    "Contexto: Piora a paralisia por análise"
  ]
}

// Relações
{
  "from": "Usuario_001",
  "to": "EstrategiaAdaptativa_001",
  "relationType": "utiliza"
}

{
  "from": "Usuario_001",
  "to": "EstrategiaAdaptativa_002",
  "relationType": "utiliza"
}

{
  "from": "Usuario_001",
  "to": "EstrategiaContraproducente_001",
  "relationType": "evita"
}

{
  "from": "EstrategiaAdaptativa_001",
  "to": "ParalisiaPorAnalise_001",
  "relationType": "mitiga"
}

{
  "from": "EstrategiaAdaptativa_002",
  "to": "ParalisiaPorAnalise_001",
  "relationType": "mitiga"
}

{
  "from": "EstrategiaContraproducente_001",
  "to": "ParalisiaPorAnalise_001",
  "relationType": "intensifica"
}
```

## Evolução do Grafo ao Longo do Tempo

O grafo de conhecimento não é estático. À medida que o usuário interage com o sistema:

1. **Novas observações** são adicionadas às entidades existentes
2. **Novas entidades** são criadas para representar novos insights
3. **Novas relações** são estabelecidas entre entidades
4. **Padrões temporais** são identificados e registrados

Por exemplo, se o usuário mencionar durante uma conversa: "Hoje estou tendo muita dificuldade para decidir sobre o projeto A porque existem muitas opções e estou preocupado com o prazo final", o sistema poderia:

```javascript
// Adicionar nova observação
{
  "entityName": "ParalisiaPorAnalise_001",
  "observations": [
    "Episódio em 23/04/2025 relacionado ao Projeto A"
  ]
}

// Criar nova entidade
{
  "name": "ProjetoA",
  "entityType": "projeto",
  "observations": [
    "Mencionado como gerador de paralisia por análise",
    "Associado a múltiplas opções",
    "Possui prazo que gera preocupação"
  ]
}

// Criar novas relações
{
  "from": "ProjetoA",
  "to": "ParalisiaPorAnalise_001",
  "relationType": "desencadeia"
}

{
  "from": "Usuario_001",
  "to": "ProjetoA",
  "relationType": "trabalha_em"
}
```

## Benefícios deste Mapeamento

Este modelo de mapeamento oferece vários benefícios:

1. **Estruturação do conhecimento**: Transforma informações desestruturadas em uma representação formal e relacionável.

2. **Personalização crescente**: Quanto mais o usuário interage, mais rico e preciso se torna seu modelo cognitivo.

3. **Contextualização**: Permite que o sistema entenda o contexto específico de cada desafio ou característica.

4. **Diagnóstico de padrões**: Facilita a identificação de padrões recorrentes e gatilhos específicos.

5. **Suporte adaptativo**: Possibilita recomendar estratégias que funcionaram no passado para situações semelhantes.

---

**Nota importante**: Este mapeamento é dinâmico e se aprimora continuamente. As entidades, relações e observações são constantemente refinadas com base nas interações subsequentes, criando um modelo cada vez mais preciso da cognição do usuário.
