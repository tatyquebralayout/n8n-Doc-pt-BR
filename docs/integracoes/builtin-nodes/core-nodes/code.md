---
title: Code Node
description: Guia completo sobre o Code Node no n8n, incluindo sintaxe JavaScript, exemplos práticos e boas práticas
sidebar_position: 1
keywords: [n8n, code node, javascript, programação, automação, lógica, customização]
---

# <ion-icon name="code-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Code Node

O **Code Node** é um dos nodes mais poderosos do n8n, permitindo executar código JavaScript customizado dentro de seus workflows. Ele oferece controle total sobre a lógica de processamento e manipulação de dados.

## O que é o Code Node?

O **Code Node** permite:

- **Executar código JavaScript** customizado
- **Manipular dados** de forma avançada
- **Criar lógica complexa** que não é possível com outros nodes
- **Integrar bibliotecas** JavaScript
- **Processar dados** em tempo real
- **Criar funções** reutilizáveis

### Quando Usar o Code Node

- **Transformações complexas** de dados
- **Validações avançadas** que outros nodes não suportam
- **Lógica de negócio** customizada
- **Integração** com APIs que requerem lógica específica
- **Processamento** de dados em lotes
- **Cálculos** matemáticos complexos

## Sintaxe Básica

### Estrutura do Code Node

```javascript
// Code Node - Estrutura básica
const dados = $json; // Dados de entrada

// Seu código JavaScript aqui
const resultado = processarDados(dados);

// Retornar dados processados
return { json: resultado };
```

### Acesso a Dados

```javascript
// Acessar dados do item atual
const dados = $json;

// Acessar dados de nodes específicos
const dadosAnteriores = $('Nome do Node').json;

// Acessar todos os items
const todosItems = $input.all();

// Acessar primeiro item
const primeiroItem = $input.first();

// Acessar item específico
const itemEspecifico = $input.item[0];
```

### Retorno de Dados

```javascript
// Retorno simples
return { json: { mensagem: "Sucesso" } };

// Retorno com múltiplos items
return [
  { json: { id: 1, nome: "Item 1" } },
  { json: { id: 2, nome: "Item 2" } }
];

// Retorno com dados binários
return {
  json: { dados: "texto" },
  binary: {
    arquivo: {
      data: "base64...",
      mimeType: "application/pdf"
    }
  }
};
```

## Exemplos Práticos

### 1. Transformação de Dados

```javascript
// Code Node - Transformação de dados
const dados = $json;

// Transformar dados de entrada
const dadosTransformados = {
  id: dados.id,
  nome_completo: `${dados.nome} ${dados.sobrenome}`,
  email_formatado: dados.email.toLowerCase(),
  idade_calculada: new Date().getFullYear() - new Date(dados.data_nascimento).getFullYear(),
  status: dados.ativo ? 'Ativo' : 'Inativo',
  timestamp: new Date().toISOString()
};

return { json: dadosTransformados };
```

### 2. Validação de Dados

```javascript
// Code Node - Validação avançada
const dados = $json;

// Função de validação
const validarDados = (dados) => {
  const erros = [];
  
  // Validar nome
  if (!dados.nome || dados.nome.length < 2) {
    erros.push('Nome deve ter pelo menos 2 caracteres');
  }
  
  // Validar email
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!emailRegex.test(dados.email)) {
    erros.push('Email inválido');
  }
  
  // Validar idade
  if (dados.idade < 18 || dados.idade > 120) {
    erros.push('Idade deve estar entre 18 e 120 anos');
  }
  
  return erros;
};

// Executar validação
const erros = validarDados(dados);

if (erros.length > 0) {
  return {
    json: {
      valido: false,
      erros: erros,
      dados_originais: dados
    }
  };
}

return {
  json: {
    valido: true,
    dados: dados,
    timestamp_validacao: new Date().toISOString()
  }
};
```

### 3. Processamento em Lotes

```javascript
// Code Node - Processamento em lotes
const items = $input.all();

// Processar cada item
const resultados = items.map(item => {
  const dados = item.json;
  
  // Aplicar lógica de negócio
  const resultado = {
    id: dados.id,
    nome: dados.nome,
    categoria: dados.valor > 1000 ? 'Premium' : 'Standard',
    desconto: dados.valor > 1000 ? 0.1 : 0.05,
    valor_final: dados.valor * (1 - (dados.valor > 1000 ? 0.1 : 0.05)),
    processado_em: new Date().toISOString()
  };
  
  return { json: resultado };
});

return resultados;
```

### 4. Integração com APIs

```javascript
// Code Node - Integração com API
const dados = $json;

// Função para fazer requisição
const fazerRequisicao = async (url, options) => {
  try {
    const response = await fetch(url, options);
    
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }
    
    return await response.json();
  } catch (error) {
    console.error('Erro na requisição:', error.message);
    throw error;
  }
};

// Processar dados
try {
  const resultado = await fazerRequisicao('https://api.exemplo.com/dados', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${dados.token}`
    },
    body: JSON.stringify({
      nome: dados.nome,
      email: dados.email,
      timestamp: new Date().toISOString()
    })
  });
  
  return {
    json: {
      sucesso: true,
      dados: resultado,
      processado_em: new Date().toISOString()
    }
  };
} catch (error) {
  return {
    json: {
      sucesso: false,
      erro: error.message,
      dados_originais: dados
    }
  };
}
```

### 5. Cálculos Matemáticos

```javascript
// Code Node - Cálculos financeiros
const dados = $json;

// Funções de cálculo
const calcularJurosCompostos = (principal, taxa, tempo) => {
  return principal * Math.pow(1 + taxa / 100, tempo);
};

const calcularParcelas = (valor, numParcelas, taxaJuros) => {
  const taxaMensal = taxaJuros / 100 / 12;
  const valorParcela = valor * (taxaMensal * Math.pow(1 + taxaMensal, numParcelas)) / 
                      (Math.pow(1 + taxaMensal, numParcelas) - 1);
  return valorParcela;
};

// Aplicar cálculos
const resultado = {
  valor_original: dados.valor,
  juros_compostos: calcularJurosCompostos(dados.valor, dados.taxa, dados.tempo),
  valor_parcela: calcularParcelas(dados.valor, dados.num_parcelas, dados.taxa_juros),
  total_parcelas: dados.valor * (1 + dados.taxa_juros / 100),
  calculado_em: new Date().toISOString()
};

return { json: resultado };
```

### 6. Manipulação de Arrays

```javascript
// Code Node - Manipulação de arrays
const dados = $json;

// Funções de manipulação
const filtrarDados = (array, criterio) => {
  return array.filter(item => {
    switch (criterio.tipo) {
      case 'valor':
        return item.valor >= criterio.min && item.valor <= criterio.max;
      case 'categoria':
        return criterio.categorias.includes(item.categoria);
      case 'data':
        return new Date(item.data) >= new Date(criterio.data_inicio) &&
               new Date(item.data) <= new Date(criterio.data_fim);
      default:
        return true;
    }
  });
};

const agruparPorCategoria = (array) => {
  return array.reduce((acc, item) => {
    if (!acc[item.categoria]) {
      acc[item.categoria] = [];
    }
    acc[item.categoria].push(item);
    return acc;
  }, {});
};

const calcularEstatisticas = (array) => {
  const valores = array.map(item => item.valor);
  return {
    total: valores.length,
    soma: valores.reduce((sum, val) => sum + val, 0),
    media: valores.reduce((sum, val) => sum + val, 0) / valores.length,
    maximo: Math.max(...valores),
    minimo: Math.min(...valores)
  };
};

// Aplicar manipulações
const dadosFiltrados = filtrarDados(dados.items, dados.criterio);
const dadosAgrupados = agruparPorCategoria(dadosFiltrados);
const estatisticas = calcularEstatisticas(dadosFiltrados);

return {
  json: {
    dados_originais: dados.items.length,
    dados_filtrados: dadosFiltrados.length,
    agrupados: dadosAgrupados,
    estatisticas: estatisticas,
    processado_em: new Date().toISOString()
  }
};
```

## Funções Avançadas

### 1. Funções Assíncronas

```javascript
// Code Node - Funções assíncronas
const processarDadosAssincrono = async (dados) => {
  // Simular processamento assíncrono
  await new Promise(resolve => setTimeout(resolve, 1000));
  
  return {
    ...dados,
    processado: true,
    timestamp: new Date().toISOString()
  };
};

// Executar função assíncrona
const resultado = await processarDadosAssincrono($json);
return { json: resultado };
```

### 2. Tratamento de Erros

```javascript
// Code Node - Tratamento de erros
const processarComTratamento = async (dados) => {
  try {
    // Operação que pode falhar
    const resultado = await operacaoRiscosa(dados);
    
    return {
      sucesso: true,
      dados: resultado,
      timestamp: new Date().toISOString()
    };
  } catch (error) {
    console.error('Erro no processamento:', error.message);
    
    return {
      sucesso: false,
      erro: error.message,
      dados_originais: dados,
      timestamp: new Date().toISOString()
    };
  }
};

const resultado = await processarComTratamento($json);
return { json: resultado };
```

### 3. Cache e Memória

```javascript
// Code Node - Cache simples
const cache = new Map();

const processarComCache = (chave, dados) => {
  // Verificar cache
  if (cache.has(chave)) {
    const dadosCache = cache.get(chave);
    const agora = new Date();
    const diferenca = agora - dadosCache.timestamp;
    
    // Cache válido por 5 minutos
    if (diferenca < 5 * 60 * 1000) {
      return {
        ...dadosCache.dados,
        origem: 'cache',
        timestamp_cache: dadosCache.timestamp
      };
    }
  }
  
  // Processar dados
  const resultado = processarDados(dados);
  
  // Salvar no cache
  cache.set(chave, {
    dados: resultado,
    timestamp: new Date()
  });
  
  return {
    ...resultado,
    origem: 'processamento',
    timestamp_processamento: new Date().toISOString()
  };
};

const resultado = processarComCache($json.id, $json);
return { json: resultado };
```

### 4. Validação de Schema

```javascript
// Code Node - Validação de schema
const validarSchema = (dados, schema) => {
  const erros = [];
  
  for (const [campo, regras] of Object.entries(schema)) {
    const valor = dados[campo];
    
    // Verificar se campo existe
    if (regras.obrigatorio && (valor === undefined || valor === null || valor === '')) {
      erros.push(`Campo '${campo}' é obrigatório`);
      continue;
    }
    
    if (valor !== undefined && valor !== null) {
      // Verificar tipo
      if (regras.tipo && typeof valor !== regras.tipo) {
        erros.push(`Campo '${campo}' deve ser do tipo ${regras.tipo}`);
      }
      
      // Verificar tamanho mínimo
      if (regras.min && valor.length < regras.min) {
        erros.push(`Campo '${campo}' deve ter pelo menos ${regras.min} caracteres`);
      }
      
      // Verificar tamanho máximo
      if (regras.max && valor.length > regras.max) {
        erros.push(`Campo '${campo}' deve ter no máximo ${regras.max} caracteres`);
      }
      
      // Verificar regex
      if (regras.pattern && !regras.pattern.test(valor)) {
        erros.push(`Campo '${campo}' não atende ao padrão esperado`);
      }
    }
  }
  
  return erros;
};

// Schema de validação
const schema = {
  nome: { obrigatorio: true, tipo: 'string', min: 2, max: 100 },
  email: { 
    obrigatorio: true, 
    tipo: 'string', 
    pattern: /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  },
  idade: { obrigatorio: true, tipo: 'number', min: 18, max: 120 }
};

const erros = validarSchema($json, schema);

if (erros.length > 0) {
  return {
    json: {
      valido: false,
      erros: erros,
      dados: $json
    }
  };
}

return {
  json: {
    valido: true,
    dados: $json,
    validado_em: new Date().toISOString()
  }
};
```

## Boas Práticas

### 1. Estrutura do Código

```javascript
// Code Node - Estrutura organizada
const dados = $json;

// 1. Validação de entrada
const validarEntrada = (dados) => {
  if (!dados || typeof dados !== 'object') {
    throw new Error('Dados de entrada inválidos');
  }
  return dados;
};

// 2. Funções de processamento
const processarDados = (dados) => {
  // Lógica de processamento
  return dados;
};

// 3. Validação de saída
const validarSaida = (resultado) => {
  if (!resultado) {
    throw new Error('Resultado inválido');
  }
  return resultado;
};

// 4. Execução principal
try {
  const dadosValidados = validarEntrada(dados);
  const resultado = processarDados(dadosValidados);
  const resultadoValidado = validarSaida(resultado);
  
  return { json: resultadoValidado };
} catch (error) {
  console.error('Erro no processamento:', error.message);
  
  return {
    json: {
      erro: error.message,
      dados_originais: dados,
      timestamp: new Date().toISOString()
    }
  };
}
```

### 2. Performance

```javascript
// Code Node - Otimizações de performance
const processarEficiente = (dados) => {
  // Usar Map para busca eficiente
  const cache = new Map();
  
  // Processar em lotes
  const resultados = [];
  const tamanhoLote = 100;
  
  for (let i = 0; i < dados.length; i += tamanhoLote) {
    const lote = dados.slice(i, i + tamanhoLote);
    const resultadoLote = processarLote(lote, cache);
    resultados.push(...resultadoLote);
  }
  
  return resultados;
};
```

### 3. Logging

```javascript
// Code Node - Logging estruturado
const logEstruturado = (nivel, mensagem, dados) => {
  const log = {
    nivel: nivel,
    mensagem: mensagem,
    timestamp: new Date().toISOString(),
    dados: dados
  };
  
  console.log(JSON.stringify(log, null, 2));
};

// Usar logging
logEstruturado('INFO', 'Iniciando processamento', $json);
```

## Troubleshooting

### Problemas Comuns

#### Código não executa
- Verifique sintaxe JavaScript
- Confirme se não há erros de referência
- Teste com código simples
- Verifique logs de erro

#### Performance lenta
- Otimize loops e operações
- Use cache quando possível
- Processe em lotes
- Monitore uso de memória

#### Dados não aparecem
- Verifique se está retornando dados
- Confirme estrutura do retorno
- Use Debug Helper
- Teste com dados simples

### Debug

1. **Use console.log** para debug
2. **Teste com dados simples**
3. **Verifique Execution History**
4. **Use Debug Helper**
5. **Monitore performance**

## Próximos Passos

- [Expressões n8n](/logica-e-dados/expressoes) - Usar expressões JavaScript
- [Tratamento de Erros](/logica-e-dados/flow-logic/error-handling) - Lidar com falhas
- [Debugging](/logica-e-dados/flow-logic/debugging) - Técnicas de debug
- [HTTP Request](/integracoes/builtin-nodes/http-requests/http-request) - Integrar com APIs
- [Data Processing](/integracoes/builtin-nodes/data-processing/index) - Processar dados
