---
title: Processamento de Dados
description: Nodes para manipular, transformar e processar dados no n8n
sidebar_position: 1
keywords: [n8n, dados, processamento, transformação, manipulação, agregação]
---

# <ion-icon name="analytics-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Processamento de Dados

Os nodes de **Processamento de Dados** são essenciais para manipular, transformar e organizar informações no n8n. Eles permitem que você prepare dados para uso em workflows, agrupe informações relacionadas e crie estruturas de dados otimizadas.

## Nodes Disponíveis

### <ion-icon name="add-circle-outline" style={{ fontSize: '20px', color: '#ea4b71' }}></ion-icon> Aggregate

O node **Aggregate** permite agrupar e agregar dados baseado em campos específicos, criando resumos e estatísticas.

**Principais funcionalidades:**
- Agrupar dados por campos
- Calcular somas, médias, contagens
- Encontrar valores mínimos e máximos
- Criar resumos estatísticos
- Combinar dados relacionados

**Casos de uso:**
- Calcular totais de vendas por período
- Agrupar dados de clientes por região
- Criar relatórios consolidados
- Analisar métricas de performance
- Processar dados de logs

[Ver documentação completa →](/integracoes/builtin-nodes/data-processing/aggregate)

### <ion-icon name="create-outline" style={{ fontSize: '20px', color: '#ea4b71' }}></ion-icon> Set

O node **Set** permite definir, modificar e remover campos nos dados, oferecendo controle total sobre a estrutura dos dados.

**Principais funcionalidades:**
- Definir novos campos
- Modificar valores existentes
- Remover campos desnecessários
- Renomear campos
- Aplicar transformações

**Casos de uso:**
- Padronizar formatos de dados
- Adicionar campos calculados
- Limpar dados de entrada
- Preparar dados para APIs
- Criar estruturas customizadas

[Ver documentação completa →](/integracoes/builtin-nodes/data-processing/set)

## Conceitos Fundamentais

### Estrutura de Dados no n8n

No n8n, os dados são organizados em **items** (itens), onde cada item representa uma entrada de dados. Cada item pode conter múltiplos campos com diferentes tipos de dados.

**Exemplo de estrutura:**
```json
{
  "id": 1,
  "nome": "João Silva",
  "email": "joao@exemplo.com",
  "idade": 30,
  "ativo": true,
  "data_cadastro": "2024-01-15T10:30:00Z"
}
```

### Tipos de Dados

O n8n suporta diversos tipos de dados:

- **String**: Texto
- **Number**: Números (inteiros e decimais)
- **Boolean**: Verdadeiro/falso
- **Object**: Objetos JSON
- **Array**: Listas de valores
- **Date**: Datas e timestamps
- **Binary**: Dados binários (arquivos)

### Fluxo de Dados

1. **Entrada**: Dados chegam de nodes anteriores
2. **Processamento**: Nodes de processamento manipulam os dados
3. **Saída**: Dados processados são enviados para nodes seguintes

## Operações Comuns

### Agrupamento de Dados

Agrupar dados relacionados para análise e processamento:

```javascript
// Exemplo: Agrupar vendas por vendedor
{
  "vendedor": "João",
  "vendas": [
    {"produto": "A", "valor": 100},
    {"produto": "B", "valor": 150}
  ],
  "total": 250
}
```

### Transformação de Campos

Modificar estrutura e valores dos dados:

```javascript
// Antes
{
  "primeiro_nome": "João",
  "ultimo_nome": "Silva",
  "data_nascimento": "1990-05-15"
}

// Depois
{
  "nome_completo": "João Silva",
  "idade": 33,
  "maior_idade": true
}
```

### Filtragem e Seleção

Selecionar apenas dados relevantes:

```javascript
// Filtrar apenas usuários ativos
if ($json.ativo === true) {
  return $json;
}
```

## Padrões de Processamento

### 1. Pipeline de Limpeza

```
Dados Brutos → Limpeza → Validação → Transformação → Dados Prontos
```

**Exemplo:**
1. **Set** node remove campos desnecessários
2. **Set** node padroniza formatos
3. **Set** node adiciona campos calculados
4. **Set** node valida dados obrigatórios

### 2. Agregação e Consolidação

```
Dados Detalhados → Agrupamento → Agregação → Resumo
```

**Exemplo:**
1. **Aggregate** node agrupa vendas por mês
2. **Aggregate** node calcula totais
3. **Set** node adiciona percentuais
4. **Set** node formata para relatório

### 3. Enriquecimento de Dados

```
Dados Básicos → Consulta Externa → Mesclagem → Dados Enriquecidos
```

**Exemplo:**
1. **HTTP Request** busca dados de cliente
2. **Set** node mescla informações
3. **Set** node adiciona campos calculados
4. **Set** node valida dados completos

## Exemplos Práticos

### Exemplo 1: Processamento de Vendas

**Dados de entrada:**
```json
[
  {
    "vendedor": "João",
    "produto": "Produto A",
    "quantidade": 2,
    "preco_unitario": 50.00,
    "data": "2024-01-15"
  },
  {
    "vendedor": "Maria",
    "produto": "Produto B",
    "quantidade": 1,
    "preco_unitario": 100.00,
    "data": "2024-01-15"
  }
]
```

**Processamento:**
1. **Set** node adiciona campo `valor_total`
2. **Aggregate** node agrupa por vendedor
3. **Set** node calcula comissão (10%)
4. **Set** node formata para relatório

**Resultado:**
```json
[
  {
    "vendedor": "João",
    "total_vendas": 100.00,
    "comissao": 10.00,
    "quantidade_vendas": 1
  },
  {
    "vendedor": "Maria",
    "total_vendas": 100.00,
    "comissao": 10.00,
    "quantidade_vendas": 1
  }
]
```

### Exemplo 2: Limpeza de Dados de Clientes

**Dados de entrada:**
```json
{
  "nome": "joão silva",
  "email": "joao@email.com",
  "telefone": "(11) 99999-9999",
  "cpf": "123.456.789-00",
  "endereco": "Rua das Flores, 123 - São Paulo/SP"
}
```

**Processamento:**
1. **Set** node padroniza nome (primeira letra maiúscula)
2. **Set** node remove formatação do telefone
3. **Set** node remove formatação do CPF
4. **Set** node separa endereço em campos
5. **Set** node valida email

**Resultado:**
```json
{
  "nome": "João Silva",
  "email": "joao@email.com",
  "telefone": "11999999999",
  "cpf": "12345678900",
  "logradouro": "Rua das Flores",
  "numero": "123",
  "cidade": "São Paulo",
  "estado": "SP",
  "email_valido": true
}
```

### Exemplo 3: Análise de Logs

**Dados de entrada:**
```json
[
  {
    "timestamp": "2024-01-15T10:30:00Z",
    "level": "ERROR",
    "message": "Falha na conexão",
    "user_id": 123
  },
  {
    "timestamp": "2024-01-15T10:31:00Z",
    "level": "INFO",
    "message": "Conexão restaurada",
    "user_id": 123
  }
]
```

**Processamento:**
1. **Aggregate** node agrupa por nível de log
2. **Aggregate** node conta ocorrências por hora
3. **Set** node adiciona percentuais
4. **Set** node identifica padrões

## Boas Práticas

### Estruturação de Dados

1. **Use nomes consistentes** para campos
2. **Padronize formatos** de data e hora
3. **Valide dados obrigatórios** antes do processamento
4. **Documente transformações** complexas
5. **Mantenha backup** dos dados originais

### Performance

1. **Processe dados em lotes** quando possível
2. **Use filtros** para reduzir volume de dados
3. **Otimize agregações** com índices apropriados
4. **Evite processamento desnecessário**
5. **Monitore uso de memória**

### Manutenção

1. **Teste transformações** com dados reais
2. **Implemente validações** robustas
3. **Configure alertas** para falhas
4. **Documente regras de negócio**
5. **Mantenha versões** das transformações

## Troubleshooting

### Problemas Comuns

#### Dados corrompidos
- Valide formato dos dados de entrada
- Verifique encoding (UTF-8)
- Implemente tratamento de erros
- Use logs para debug

#### Performance lenta
- Otimize agregações
- Use filtros apropriados
- Processe em lotes menores
- Monitore uso de recursos

#### Campos ausentes
- Implemente valores padrão
- Valide dados obrigatórios
- Use operadores de coalescência
- Documente dependências

### Debug

1. **Use o node Debug Helper** para inspecionar dados
2. **Configure logging detalhado**
3. **Teste com dados de exemplo**
4. **Valide cada etapa do processamento**
5. **Monitore métricas de performance**

## Próximos Passos

- [Expressões n8n](/logica-e-dados/expressoes) - Usar dados dinâmicos
- [Nodes de Lógica](/integracoes/builtin-nodes/logic-control/index) - Controlar fluxo
- [Nodes Core](/integracoes/builtin-nodes/core-nodes/index) - Operações básicas
- [Manipulação de Dados](/logica-e-dados/data/index) - Conceitos avançados
- [Tratamento de Erros](/logica-e-dados/flow-logic/error-handling) - Lidar com falhas
