# Exemplo de Implementação: Servidor de Memória com Grafo de Conhecimento

Este documento apresenta um exemplo simplificado de como implementar um servidor de memória baseado em grafo de conhecimento para uso com IA generativa.

## Estrutura Básica

A implementação se baseia em três componentes principais:
- Entidades (nós do grafo)
- Relações (arestas direcionadas)
- Observações (fatos armazenados nas entidades)

## Implementação em JavaScript

Abaixo está um exemplo de implementação simples em JavaScript:

```javascript
class KnowledgeGraph {
  constructor() {
    this.entities = {};  // Armazena todas as entidades
    this.relations = []; // Armazena todas as relações
  }

  // Criar uma nova entidade
  createEntity(name, entityType, observations = []) {
    if (this.entities[name]) {
      console.log(`Entidade ${name} já existe.`);
      return false;
    }
    
    this.entities[name] = {
      name,
      entityType,
      observations: [...observations]
    };
    
    return true;
  }

  // Criar múltiplas entidades
  createEntities(entitiesArray) {
    const results = [];
    
    for (const entity of entitiesArray) {
      const success = this.createEntity(
        entity.name,
        entity.entityType,
        entity.observations || []
      );
      
      results.push({
        name: entity.name,
        success
      });
    }
    
    return results;
  }

  // Criar uma relação entre entidades
  createRelation(fromEntity, toEntity, relationType) {
    // Verificar se as entidades existem
    if (!this.entities[fromEntity] || !this.entities[toEntity]) {
      console.log('Uma ou ambas entidades não existem.');
      return false;
    }
    
    // Verificar se a relação já existe
    const existingRelation = this.relations.find(
      r => r.from === fromEntity && 
           r.to === toEntity && 
           r.relationType === relationType
    );
    
    if (existingRelation) {
      console.log('Esta relação já existe.');
      return false;
    }
    
    // Adicionar a nova relação
    this.relations.push({
      from: fromEntity,
      to: toEntity,
      relationType
    });
    
    return true;
  }

  // Criar múltiplas relações
  createRelations(relationsArray) {
    const results = [];
    
    for (const relation of relationsArray) {
      const success = this.createRelation(
        relation.from,
        relation.to,
        relation.relationType
      );
      
      results.push({
        from: relation.from,
        to: relation.to,
        relationType: relation.relationType,
        success
      });
    }
    
    return results;
  }

  // Adicionar observações a uma entidade
  addObservations(entityName, observations) {
    if (!this.entities[entityName]) {
      console.log(`Entidade ${entityName} não existe.`);
      return false;
    }
    
    // Adicionar novas observações
    this.entities[entityName].observations = [
      ...this.entities[entityName].observations,
      ...observations
    ];
    
    return true;
  }

  // Buscar entidades e suas relações
  searchNodes(query) {
    const results = {
      entities: [],
      relations: []
    };
    
    // Busca case insensitive
    const queryLower = query.toLowerCase();
    
    // Buscar entidades que correspondam à consulta
    for (const [name, entity] of Object.entries(this.entities)) {
      if (
        name.toLowerCase().includes(queryLower) ||
        entity.entityType.toLowerCase().includes(queryLower) ||
        entity.observations.some(obs => 
          obs.toLowerCase().includes(queryLower)
        )
      ) {
        results.entities.push(entity);
      }
    }
    
    // Buscar relações entre as entidades encontradas
    const entityNames = results.entities.map(e => e.name);
    
    results.relations = this.relations.filter(
      r => entityNames.includes(r.from) && entityNames.includes(r.to)
    );
    
    return results;
  }

  // Ler entidades específicas pelo nome
  openNodes(names) {
    const results = {
      entities: [],
      relations: []
    };
    
    // Encontrar as entidades solicitadas
    for (const name of names) {
      if (this.entities[name]) {
        results.entities.push(this.entities[name]);
      }
    }
    
    // Encontrar relações entre as entidades solicitadas
    const entityNames = results.entities.map(e => e.name);
    
    results.relations = this.relations.filter(
      r => entityNames.includes(r.from) && entityNames.includes(r.to)
    );
    
    return results;
  }

  // Ler o grafo completo
  readGraph() {
    return {
      entities: Object.values(this.entities),
      relations: this.relations
    };
  }

  // Excluir entidades e suas relações
  deleteEntities(entityNames) {
    const results = [];
    
    for (const name of entityNames) {
      if (this.entities[name]) {
        // Excluir a entidade
        delete this.entities[name];
        
        // Excluir todas as relações associadas
        this.relations = this.relations.filter(
          r => r.from !== name && r.to !== name
        );
        
        results.push({
          name,
          success: true
        });
      } else {
        results.push({
          name,
          success: false
        });
      }
    }
    
    return results;
  }

  // Excluir observações específicas
  deleteObservations(deletions) {
    const results = [];
    
    for (const deletion of deletions) {
      const { entityName, observations } = deletion;
      
      if (!this.entities[entityName]) {
        results.push({
          entityName,
          success: false,
          reason: 'Entidade não encontrada'
        });
        continue;
      }
      
      // Filtrar as observações a serem mantidas
      this.entities[entityName].observations = 
        this.entities[entityName].observations.filter(
          obs => !observations.includes(obs)
        );
      
      results.push({
        entityName,
        success: true
      });
    }
    
    return results;
  }

  // Excluir relações específicas
  deleteRelations(relations) {
    const results = [];
    
    for (const relation of relations) {
      const { from, to, relationType } = relation;
      
      // Encontrar o índice da relação
      const index = this.relations.findIndex(
        r => r.from === from && 
             r.to === to && 
             r.relationType === relationType
      );
      
      if (index !== -1) {
        // Remover a relação
        this.relations.splice(index, 1);
        
        results.push({
          from,
          to,
          relationType,
          success: true
        });
      } else {
        results.push({
          from,
          to,
          relationType,
          success: false,
          reason: 'Relação não encontrada'
        });
      }
    }
    
    return results;
  }
}

// Exemplo de uso
function exemploUso() {
  const grafo = new KnowledgeGraph();
  
  // Criar entidades
  grafo.createEntities([
    {
      name: "IA_Generativa",
      entityType: "conceito",
      observations: [
        "Tecnologia que permite criar conteúdo novo",
        "Inclui modelos como GPT e Claude"
      ]
    },
    {
      name: "RAG",
      entityType: "técnica",
      observations: [
        "Retrieval-Augmented Generation",
        "Combina recuperação de informações com geração de texto"
      ]
    },
    {
      name: "KAG",
      entityType: "técnica",
      observations: [
        "Knowledge-Augmented Generation",
        "Integra conhecimento estruturado na geração de conteúdo"
      ]
    }
  ]);
  
  // Criar relações
  grafo.createRelations([
    {
      from: "RAG",
      to: "IA_Generativa",
      relationType: "é_aplicado_em"
    },
    {
      from: "KAG",
      to: "RAG",
      relationType: "evolui_de"
    }
  ]);
  
  // Adicionar observações
  grafo.addObservations("IA_Generativa", [
    "Utilizada em diversos setores como saúde, educação e marketing"
  ]);
  
  // Buscar informações
  const busca = grafo.searchNodes("conhecimento");
  console.log(JSON.stringify(busca, null, 2));
  
  // Ler entidades específicas
  const infoRAG = grafo.openNodes(["RAG", "KAG"]);
  console.log(JSON.stringify(infoRAG, null, 2));
  
  return grafo;
}

// Execute o exemplo
const grafoExemplo = exemploUso();
```

## Como Expandir Este Exemplo

Esta implementação básica pode ser expandida de várias maneiras:

1. **Adicionar persistência**: Implementar salvamento e carregamento do grafo em um banco de dados ou arquivo JSON.

2. **Otimizar buscas**: Adicionar índices para melhorar a performance de pesquisas em grafos grandes.

3. **Adicionar validação**: Implementar validação de tipos e formatos para entidades e relações.

4. **Interface REST API**: Expor as funcionalidades como uma API REST para integração com sistemas de IA.

5. **Visualização do grafo**: Implementar uma interface visual para navegar e editar o grafo.

## Integração com IA Generativa

Para integrar este grafo de conhecimento com um modelo de IA generativa:

1. Antes de cada interação, consulte o grafo para obter contexto relevante sobre o usuário
2. Durante a interação, identifique novas informações a serem armazenadas
3. Após a interação, atualize o grafo com as novas informações
4. Use as informações do grafo para personalizar futuras interações

Desta forma, o modelo pode manter contexto entre sessões e oferecer uma experiência mais personalizada e consistente.

---

Nota: Este é um exemplo simplificado para fins educacionais. Uma implementação em produção exigiria considerações adicionais sobre segurança, escalabilidade e privacidade de dados.
