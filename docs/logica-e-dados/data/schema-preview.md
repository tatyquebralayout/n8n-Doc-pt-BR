# Visualização de Schema

A visualização de schema é fundamental para entender a estrutura dos dados que fluem através dos workflows. Esta seção aborda como visualizar, interpretar e trabalhar com schemas de dados no n8n.

## Visão Geral

Schemas definem a estrutura, tipos e validação dos dados. No n8n, entender o schema dos dados é essencial para:

- **Mapeamento correto** de dados entre nodes
- **Validação de entrada** e saída
- **Debugging** de problemas de estrutura
- **Documentação** de workflows
- **Integração** com APIs externas

## Tipos de Schema

### Schema JSON

O formato mais comum para definição de schemas:

```json
{
  "type": "object",
  "properties": {
    "id": {
      "type": "integer",
      "description": "ID único do registro"
    },
    "nome": {
      "type": "string",
      "minLength": 2,
      "maxLength": 100,
      "description": "Nome completo da pessoa"
    },
    "email": {
      "type": "string",
      "format": "email",
      "description": "Endereço de email válido"
    },
    "idade": {
      "type": "integer",
      "minimum": 0,
      "maximum": 150,
      "description": "Idade em anos"
    },
    "ativo": {
      "type": "boolean",
      "description": "Status de atividade"
    },
    "endereco": {
      "type": "object",
      "properties": {
        "rua": { "type": "string" },
        "numero": { "type": "string" },
        "bairro": { "type": "string" },
        "cidade": { "type": "string" },
        "estado": { "type": "string" },
        "cep": { "type": "string", "pattern": "^\\d{5}-\\d{3}$" }
      },
      "required": ["rua", "cidade", "estado"]
    },
    "telefones": {
      "type": "array",
      "items": {
        "type": "string",
        "pattern": "^\\(\\d{2}\\) \\d{4,5}-\\d{4}$"
      },
      "description": "Lista de telefones"
    }
  },
  "required": ["nome", "email"]
}
```

### Schema de API

Schemas específicos para APIs REST:

```json
{
  "openapi": "3.0.0",
  "info": {
    "title": "API de Clientes",
    "version": "1.0.0",
    "description": "API para gerenciamento de clientes"
  },
  "paths": {
    "/clientes": {
      "get": {
        "summary": "Listar clientes",
        "responses": {
          "200": {
            "description": "Lista de clientes",
            "content": {
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/Cliente"
                  }
                }
              }
            }
          }
        }
      }
    }
  },
  "components": {
    "schemas": {
      "Cliente": {
        "type": "object",
        "properties": {
          "id": { "type": "integer" },
          "nome": { "type": "string" },
          "email": { "type": "string", "format": "email" },
          "cpf": { "type": "string", "pattern": "^\\d{3}\\.\\d{3}\\.\\d{3}-\\d{2}$" }
        }
      }
    }
  }
}
```

## Visualização no n8n

### Schema Preview

O n8n oferece visualização automática de schemas:

```javascript
// Acessar schema de um node
const schema = $node.previous.json.schema;

// Visualizar estrutura
console.log('Schema do node anterior:', JSON.stringify(schema, null, 2));

// Extrair tipos de dados
const tipos = extrairTipos(schema);
console.log('Tipos encontrados:', tipos);
```

### Ferramentas de Visualização

Use ferramentas externas para melhor visualização:

```javascript
// Gerar documentação do schema
const gerarDocumentacaoSchema = (schema) => {
  const doc = {
    titulo: 'Documentação do Schema',
    campos: [],
    exemplos: []
  };
  
  if (schema.properties) {
    Object.entries(schema.properties).forEach(([nome, prop]) => {
      doc.campos.push({
        nome: nome,
        tipo: prop.type,
        obrigatorio: schema.required?.includes(nome) || false,
        descricao: prop.description || '',
        validacoes: extrairValidacoes(prop)
      });
    });
  }
  
  return doc;
};

// Extrair validações
const extrairValidacoes = (propriedade) => {
  const validacoes = {};
  
  if (propriedade.minLength) validacoes.minLength = propriedade.minLength;
  if (propriedade.maxLength) validacoes.maxLength = propriedade.maxLength;
  if (propriedade.minimum) validacoes.minimum = propriedade.minimum;
  if (propriedade.maximum) validacoes.maximum = propriedade.maximum;
  if (propriedade.pattern) validacoes.pattern = propriedade.pattern;
  if (propriedade.enum) validacoes.enum = propriedade.enum;
  
  return validacoes;
};
```

## Schemas Comuns no Brasil

### Schema de Pessoa Física

```json
{
  "type": "object",
  "properties": {
    "nome": {
      "type": "string",
      "minLength": 3,
      "maxLength": 100,
      "description": "Nome completo"
    },
    "cpf": {
      "type": "string",
      "pattern": "^\\d{3}\\.\\d{3}\\.\\d{3}-\\d{2}$",
      "description": "CPF no formato XXX.XXX.XXX-XX"
    },
    "rg": {
      "type": "string",
      "description": "Número do RG"
    },
    "dataNascimento": {
      "type": "string",
      "format": "date",
      "description": "Data de nascimento (YYYY-MM-DD)"
    },
    "sexo": {
      "type": "string",
      "enum": ["M", "F", "O"],
      "description": "Sexo: M=Masculino, F=Feminino, O=Outro"
    },
    "estadoCivil": {
      "type": "string",
      "enum": ["Solteiro", "Casado", "Divorciado", "Viúvo", "União Estável"],
      "description": "Estado civil"
    },
    "endereco": {
      "type": "object",
      "properties": {
        "cep": {
          "type": "string",
          "pattern": "^\\d{5}-\\d{3}$",
          "description": "CEP no formato XXXXX-XXX"
        },
        "logradouro": { "type": "string" },
        "numero": { "type": "string" },
        "complemento": { "type": "string" },
        "bairro": { "type": "string" },
        "cidade": { "type": "string" },
        "estado": {
          "type": "string",
          "enum": ["AC", "AL", "AP", "AM", "BA", "CE", "DF", "ES", "GO", "MA", "MT", "MS", "MG", "PA", "PB", "PR", "PE", "PI", "RJ", "RN", "RS", "RO", "RR", "SC", "SP", "SE", "TO"]
        }
      },
      "required": ["cep", "logradouro", "numero", "bairro", "cidade", "estado"]
    },
    "contato": {
      "type": "object",
      "properties": {
        "telefone": {
          "type": "string",
          "pattern": "^\\(\\d{2}\\) \\d{4,5}-\\d{4}$"
        },
        "celular": {
          "type": "string",
          "pattern": "^\\(\\d{2}\\) \\d{5}-\\d{4}$"
        },
        "email": {
          "type": "string",
          "format": "email"
        }
      }
    }
  },
  "required": ["nome", "cpf", "dataNascimento"]
}
```

### Schema de Pessoa Jurídica

```json
{
  "type": "object",
  "properties": {
    "razaoSocial": {
      "type": "string",
      "minLength": 3,
      "maxLength": 150,
      "description": "Razão social da empresa"
    },
    "nomeFantasia": {
      "type": "string",
      "maxLength": 100,
      "description": "Nome fantasia"
    },
    "cnpj": {
      "type": "string",
      "pattern": "^\\d{2}\\.\\d{3}\\.\\d{3}/\\d{4}-\\d{2}$",
      "description": "CNPJ no formato XX.XXX.XXX/XXXX-XX"
    },
    "inscricaoEstadual": {
      "type": "string",
      "description": "Inscrição estadual"
    },
    "inscricaoMunicipal": {
      "type": "string",
      "description": "Inscrição municipal"
    },
    "dataAbertura": {
      "type": "string",
      "format": "date",
      "description": "Data de abertura da empresa"
    },
    "porte": {
      "type": "string",
      "enum": ["ME", "EPP", "Média", "Grande"],
      "description": "Porte da empresa"
    },
    "setor": {
      "type": "string",
      "description": "Setor de atuação"
    },
    "endereco": {
      "type": "object",
      "properties": {
        "cep": { "type": "string", "pattern": "^\\d{5}-\\d{3}$" },
        "logradouro": { "type": "string" },
        "numero": { "type": "string" },
        "complemento": { "type": "string" },
        "bairro": { "type": "string" },
        "cidade": { "type": "string" },
        "estado": { "type": "string" }
      }
    },
    "contato": {
      "type": "object",
      "properties": {
        "telefone": { "type": "string" },
        "email": { "type": "string", "format": "email" },
        "website": { "type": "string", "format": "uri" }
      }
    }
  },
  "required": ["razaoSocial", "cnpj"]
}
```

### Schema de Nota Fiscal

```json
{
  "type": "object",
  "properties": {
    "numero": {
      "type": "string",
      "description": "Número da nota fiscal"
    },
    "serie": {
      "type": "string",
      "description": "Série da nota fiscal"
    },
    "dataEmissao": {
      "type": "string",
      "format": "date-time",
      "description": "Data e hora de emissão"
    },
    "tipoOperacao": {
      "type": "string",
      "enum": ["Entrada", "Saída"],
      "description": "Tipo de operação"
    },
    "naturezaOperacao": {
      "type": "string",
      "description": "Natureza da operação"
    },
    "emitente": {
      "type": "object",
      "properties": {
        "cnpj": { "type": "string" },
        "razaoSocial": { "type": "string" },
        "endereco": { "type": "object" }
      }
    },
    "destinatario": {
      "type": "object",
      "properties": {
        "cpf": { "type": "string" },
        "cnpj": { "type": "string" },
        "nome": { "type": "string" },
        "endereco": { "type": "object" }
      }
    },
    "itens": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "codigo": { "type": "string" },
          "descricao": { "type": "string" },
          "quantidade": { "type": "number" },
          "valorUnitario": { "type": "number" },
          "valorTotal": { "type": "number" }
        }
      }
    },
    "totais": {
      "type": "object",
      "properties": {
        "subtotal": { "type": "number" },
        "icms": { "type": "number" },
        "ipi": { "type": "number" },
        "pis": { "type": "number" },
        "cofins": { "type": "number" },
        "total": { "type": "number" }
      }
    }
  },
  "required": ["numero", "dataEmissao", "emitente", "destinatario", "itens"]
}
```

## Validação de Schema

### Validação Automática

```javascript
// Validar dados contra schema
const validarContraSchema = (dados, schema) => {
  const Ajv = require('ajv');
  const ajv = new Ajv({ allErrors: true });
  
  const validate = ajv.compile(schema);
  const valido = validate(dados);
  
  return {
    valido: valido,
    erros: validate.errors || []
  };
};

// Exemplo de uso
const schemaCliente = {
  type: "object",
  properties: {
    nome: { type: "string", minLength: 3 },
    email: { type: "string", format: "email" },
    cpf: { type: "string", pattern: "^\\d{3}\\.\\d{3}\\.\\d{3}-\\d{2}$" }
  },
  required: ["nome", "email", "cpf"]
};

const cliente = {
  nome: "João Silva",
  email: "joao@email.com",
  cpf: "123.456.789-00"
};

const resultado = validarContraSchema(cliente, schemaCliente);
console.log('Validação:', resultado);
```

### Validação Customizada

```javascript
// Validações específicas para dados brasileiros
const validacoesBR = {
  cpf: (valor) => {
    if (!valor) return false;
    const cpf = valor.replace(/[^\d]/g, '');
    if (cpf.length !== 11) return false;
    if (/^(\d)\1{10}$/.test(cpf)) return false;
    
    let soma = 0;
    for (let i = 0; i < 9; i++) {
      soma += parseInt(cpf[i]) * (10 - i);
    }
    const digito1 = ((soma * 10) % 11) % 10;
    
    soma = 0;
    for (let i = 0; i < 10; i++) {
      soma += parseInt(cpf[i]) * (11 - i);
    }
    const digito2 = ((soma * 10) % 11) % 10;
    
    return parseInt(cpf[9]) === digito1 && parseInt(cpf[10]) === digito2;
  },
  
  cnpj: (valor) => {
    if (!valor) return false;
    const cnpj = valor.replace(/[^\d]/g, '');
    if (cnpj.length !== 14) return false;
    
    const pesos1 = [5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2];
    const pesos2 = [6, 5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2];
    
    let soma = 0;
    for (let i = 0; i < 12; i++) {
      soma += parseInt(cnpj[i]) * pesos1[i];
    }
    const digito1 = ((soma * 10) % 11) % 10;
    
    soma = 0;
    for (let i = 0; i < 13; i++) {
      soma += parseInt(cnpj[i]) * pesos2[i];
    }
    const digito2 = ((soma * 10) % 11) % 10;
    
    return parseInt(cnpj[12]) === digito1 && parseInt(cnpj[13]) === digito2;
  },
  
  cep: (valor) => {
    if (!valor) return false;
    return /^\d{5}-\d{3}$/.test(valor);
  },
  
  telefone: (valor) => {
    if (!valor) return false;
    return /^\(\d{2}\) \d{4,5}-\d{4}$/.test(valor);
  }
};
```

## Transformação de Schema

### Conversão de Formatos

```javascript
// Converter schema JSON para TypeScript
const converterParaTypeScript = (schema) => {
  let typescript = 'interface Dados {\n';
  
  if (schema.properties) {
    Object.entries(schema.properties).forEach(([nome, prop]) => {
      const tipo = converterTipo(prop);
      const obrigatorio = schema.required?.includes(nome) ? '' : '?';
      typescript += `  ${nome}${obrigatorio}: ${tipo};\n`;
    });
  }
  
  typescript += '}';
  return typescript;
};

const converterTipo = (propriedade) => {
  switch (propriedade.type) {
    case 'string':
      if (propriedade.enum) {
        return propriedade.enum.map(v => `'${v}'`).join(' | ');
      }
      return 'string';
    case 'integer':
    case 'number':
      return 'number';
    case 'boolean':
      return 'boolean';
    case 'array':
      return `${converterTipo(propriedade.items)}[]`;
    case 'object':
      return 'object';
    default:
      return 'any';
  }
};
```

### Geração de Exemplos

```javascript
// Gerar exemplos baseados no schema
const gerarExemplo = (schema) => {
  if (schema.type === 'object' && schema.properties) {
    const exemplo = {};
    
    Object.entries(schema.properties).forEach(([nome, prop]) => {
      exemplo[nome] = gerarValorExemplo(prop);
    });
    
    return exemplo;
  }
  
  return gerarValorExemplo(schema);
};

const gerarValorExemplo = (propriedade) => {
  switch (propriedade.type) {
    case 'string':
      if (propriedade.enum) {
        return propriedade.enum[0];
      }
      if (propriedade.format === 'email') {
        return 'exemplo@email.com';
      }
      if (propriedade.format === 'date') {
        return '2024-01-15';
      }
      return 'Texto de exemplo';
      
    case 'integer':
    case 'number':
      return propriedade.minimum || 0;
      
    case 'boolean':
      return true;
      
    case 'array':
      return [gerarValorExemplo(propriedade.items)];
      
    case 'object':
      return gerarExemplo(propriedade);
      
    default:
      return null;
  }
};
```

## Integração com APIs

### Schema de Resposta de API

```javascript
// Definir schema de resposta
const schemaRespostaAPI = {
  type: "object",
  properties: {
    success: { type: "boolean" },
    data: {
      type: "array",
      items: {
        type: "object",
        properties: {
          id: { type: "integer" },
          nome: { type: "string" },
          email: { type: "string", format: "email" },
          status: { type: "string", enum: ["ativo", "inativo"] }
        }
      }
    },
    pagination: {
      type: "object",
      properties: {
        page: { type: "integer" },
        limit: { type: "integer" },
        total: { type: "integer" },
        pages: { type: "integer" }
      }
    }
  }
};

// Validar resposta da API
const validarRespostaAPI = (resposta) => {
  return validarContraSchema(resposta, schemaRespostaAPI);
};
```

### Mapeamento de Schema

```javascript
// Mapear dados entre schemas diferentes
const mapearSchema = (dados, schemaOrigem, schemaDestino) => {
  const mapeamento = {
    'cliente.nome': 'nome',
    'cliente.email': 'email',
    'cliente.telefone': 'contato.telefone',
    'endereco.cep': 'localizacao.cep',
    'endereco.cidade': 'localizacao.cidade'
  };
  
  const dadosMapeados = {};
  
  Object.entries(mapeamento).forEach(([origem, destino]) => {
    const valor = obterValorPorCaminho(dados, origem);
    if (valor !== undefined) {
      definirValorPorCaminho(dadosMapeados, destino, valor);
    }
  });
  
  return dadosMapeados;
};

const obterValorPorCaminho = (objeto, caminho) => {
  return caminho.split('.').reduce((obj, prop) => obj?.[prop], objeto);
};

const definirValorPorCaminho = (objeto, caminho, valor) => {
  const props = caminho.split('.');
  const ultimaProp = props.pop();
  const obj = props.reduce((o, prop) => {
    if (!o[prop]) o[prop] = {};
    return o[prop];
  }, objeto);
  obj[ultimaProp] = valor;
};
```

## Ferramentas de Visualização

### JSON Schema Viewer

```javascript
// Componente para visualizar schema
const SchemaViewer = ({ schema }) => {
  const renderizarPropriedade = (nome, prop, nivel = 0) => {
    const indentacao = '  '.repeat(nivel);
    
    return (
      <div key={nome} style={{ marginLeft: nivel * 20 }}>
        <strong>{nome}</strong>: {prop.type}
        {prop.description && <span> - {prop.description}</span>}
        {prop.enum && <span> ({prop.enum.join(', ')})</span>}
        {prop.properties && (
          <div>
            {Object.entries(prop.properties).map(([subNome, subProp]) =>
              renderizarPropriedade(subNome, subProp, nivel + 1)
            )}
          </div>
        )}
      </div>
    );
  };
  
  return (
    <div className="schema-viewer">
      <h3>Schema de Dados</h3>
      {schema.properties && Object.entries(schema.properties).map(([nome, prop]) =>
        renderizarPropriedade(nome, prop)
      )}
    </div>
  );
};
```

### Documentação Automática

```javascript
// Gerar documentação do schema
const gerarDocumentacao = (schema) => {
  const doc = {
    titulo: 'Documentação do Schema',
    descricao: schema.description || '',
    campos: [],
    exemplos: []
  };
  
  if (schema.properties) {
    Object.entries(schema.properties).forEach(([nome, prop]) => {
      doc.campos.push({
        nome: nome,
        tipo: prop.type,
        obrigatorio: schema.required?.includes(nome) || false,
        descricao: prop.description || '',
        formato: prop.format || '',
        validacoes: extrairValidacoes(prop)
      });
    });
  }
  
  // Gerar exemplo
  doc.exemplos.push(gerarExemplo(schema));
  
  return doc;
};
```

## Boas Práticas

### Design de Schema

- **Use nomes descritivos** para propriedades
- **Documente sempre** com descrições claras
- **Defina validações** apropriadas
- **Mantenha consistência** entre schemas relacionados
- **Use tipos específicos** quando possível

### Validação

- **Valide dados de entrada** sempre
- **Use validações customizadas** para regras de negócio
- **Teste com dados reais** e edge cases
- **Monitore erros** de validação
- **Documente regras** de validação

### Performance

- **Cache schemas** frequentemente usados
- **Otimize validações** para grandes volumes
- **Use validação lazy** quando apropriado
- **Monitore tempo** de validação
- **Considere validação parcial** para dados grandes

## Recursos Adicionais

### Ferramentas Úteis

- **JSON Schema Validator**: Validação online de schemas
- **JSON Schema Generator**: Geração automática de schemas
- **Swagger Editor**: Editor visual para OpenAPI
- **JSON Schema Viewer**: Visualização de schemas

### Bibliotecas

- **Ajv**: Validador JSON Schema para JavaScript
- **jsonschema**: Validador Python
- **json-schema**: Validador Java
- **JSON.NET**: Validador .NET

---

**Próximo**: [Filtros de Dados](./data-filtering) - Filtre e processe dados eficientemente 