#Empresa Momento
---

## Nível 1: Conhecendo a Empresa

### 1.1 — Inclua suas próprias informações no departamento de Tecnologia da empresa

**Comando MongoDB**
```javascript
db.funcionarios.insertOne({
  "nome": "Ana",
  "sobrenome": "Silva",
  "data_nascimento": "1990-01-01",
  "data_contratacao": "2020-01-01",
  "salario": 5000,
  "departamento": ObjectId("85992103f9b3e0b3b3c1fe74"),
  "cargo": "Desenvolvedora",
  "escritorio": "São Paulo"
})
```

**Resultado**
```
Inserido com sucesso! ID: ObjectId gerado automaticamente
```

---

### 1.2 — Quantos funcionários temos ao total na empresa?

**Comando MongoDB**
```javascript
db.funcionarios.countDocuments({})
```

**Resultado**
```
23
```

---

### 1.3 — Quantos funcionários trabalham especificamente no Departamento de Tecnologia?

**Comando MongoDB**
```javascript
db.funcionarios.countDocuments({
  departamento: ObjectId("85992103f9b3e0b3b3c1fe74")
})
```

**Resultado**
```
5
```

---

### 1.4 — Liste todos os departamentos que existem na empresa. Quantos são?

**Comando MongoDB**
```javascript
db.departamentos.find({}).project({nome: 1, _id: 0})
```

**Resultado**
| Departamento |
|---|
| Executivo |
| Vendas |
| Marketing |
| Financeiro |
| Tecnologia |
| Recursos Humanos |
| Dados |

**Total: 7 departamentos**

---

### 1.5 — Quantos escritórios a Momento possui? Em quais países?

**Comando MongoDB**
```javascript
db.escritorios.aggregate([
  {$group: {_id: null, total: {$sum: 1}, paises: {$addToSet: "$pais"}}}
])
```

**Resultado**
| Campo | Valor |
|---|---|
| Total de Escritórios | 4 |
| Países | USA, IRE, ENG, EQU |

---

## Nível 2: Análise Financeira Básica

### 2.1 — Quantos funcionários trabalham no Departamento de Vendas?

**Comando MongoDB**
```javascript
db.funcionarios.countDocuments({
  departamento: ObjectId("85992103f9b3e0b3b3c1fe71")
})
```

**Resultado**
```
9
```

---

### 2.2 — Qual é o custo total com salários do Departamento de Vendas?

**Comando MongoDB**
```javascript
db.funcionarios.aggregate([
  {$match: {departamento: ObjectId("85992103f9b3e0b3b3c1fe71")}},
  {$group: {_id: null, totalSalarios: {$sum: "$salario"}}}
])
```

**Resultado**
```
57100
```

---

### 2.3 — Qual é a média salarial da empresa, excluindo os cargos de CEO, CMO e CFO?

**Comando MongoDB**
```javascript
db.funcionarios.aggregate([
  {$match: {cargo: {$nin: ["CEO", "CMO", "CFO"]}}},
  {$group: {_id: null, mediaSalarial: {$avg: "$salario"}}}
])
```

**Resultado**
```
9475.36
```

---

### 2.4 — Qual é a média salarial do Departamento de Tecnologia?

**Comando MongoDB**
```javascript
db.funcionarios.aggregate([
  {$match: {departamento: ObjectId("85992103f9b3e0b3b3c1fe74")}},
  {$group: {_id: null, mediaSalarial: {$avg: "$salario"}}}
])
```

**Resultado**
```
4460
```

---

### 2.5 — Qual departamento possui a maior média salarial?

**Comando MongoDB**
```javascript
db.funcionarios.aggregate([
  {$group: {
    _id: "$departamento",
    mediaSalarial: {$avg: "$salario"},
    nomeDepartamento: {$first: "$departamento"}
  }},
  {$sort: {mediaSalarial: -1}},
  {$limit: 1},
  {$lookup: {
    from: "departamentos",
    localField: "_id",
    foreignField: "_id",
    as: "detalhesDepartamento"
  }},
  {$unwind: "$detalhesDepartamento"}
])
```

**Resultado**
| Departamento | Média Salarial |
|---|---|
| Executivo | 71000 |

---

### 2.6 — Qual departamento possui o menor número de funcionários?

**Comando MongoDB**
```javascript
db.funcionarios.aggregate([
  {$group: {
    _id: "$departamento",
    totalFuncionarios: {$sum: 1}
  }},
  {$sort: {totalFuncionarios: 1}},
  {$limit: 1},
  {$lookup: {
    from: "departamentos",
    localField: "_id",
    foreignField: "_id",
    as: "detalhesDepartamento"
  }},
  {$unwind: "$detalhesDepartamento"}
])
```

**Resultado**
| Departamento | Total de Funcionários |
|---|---|
| Marketing | 0 |
| Financeiro | 0 |
| Dados | 0 |

**Observação:** Há 3 departamentos sem funcionários. O primeiro (Marketing) é o que aparece primeiro na coleção.

---

## Nível 3: Recursos Humanos

### 3.1 — Quantos funcionários da empresa Momento possuem cônjuges?

**Comando MongoDB**
```javascript
db.funcionarios.countDocuments({
  "dependentes.conjuge": {$exists: true}
})
```

**Resultado**
```
7
```

---

### 3.2 — Quantos funcionários possuem filhos registrados?

**Comando MongoDB**
```javascript
db.funcionarios.countDocuments({
  "dependentes.filhos": {$exists: true}
})
```

**Resultado**
```
7
```

---

### 3.3 — Qual funcionário foi contratado há mais tempo na empresa?

**Comando MongoDB**
```javascript
db.funcionarios.find({})
  .sort({dataAdmissao: 1})
  .limit(1)
  .project({nome: 1, dataAdmissao: 1, _id: 0})
```

**Resultado**
| Nome | Data de Admissão |
|---|---|
| Irene Adler | 1980-09-28 |

---

### 3.4 — Qual funcionário foi contratado há menos tempo na empresa?

**Comando MongoDB**
```javascript
db.funcionarios.find({})
  .sort({dataAdmissao: -1})
  .limit(1)
  .project({nome: 1, dataAdmissao: 1, _id: 0})
```

**Resultado**
| Nome | Data de Admissão |
|---|---|
| Ana Silva | 2020-01-01 |

---

### 3.5 — Liste os 5 funcionários com mais tempo de casa, ordenados pela data de contratação

**Comando MongoDB**
```javascript
db.funcionarios.find({})
  .sort({dataAdmissao: 1})
  .limit(5)
  .project({nome: 1, dataAdmissao: 1, _id: 0})
```

**Resultado**
| Nome | Data de Admissão |
|---|---|
| Irene Adler | 1980-09-28 |
| Jennifer Whalen | 1987-09-17 |
| Elisabeth Braddock | 1996-02-17 |
| Sarah Bell | 1996-02-04 |
| Pat Ferreira | 1997-08-17 |

---

### 3.6 — Quantos funcionários foram contratados na década de 1990 (entre 1990-1999)?

**Comando MongoDB**
```javascript
db.funcionarios.countDocuments({
  dataAdmissao: {
    $gte: "1990-01-01",
    $lte: "1999-12-31"
  }
})
```

**Resultado**
```
11
```

---

### 3.7 — Como a média salarial da Momento evoluiu ao longo dos anos? Agrupe por ano de contratação e calcule a média salarial

**Comando MongoDB**
```javascript
db.funcionarios.aggregate([
  {$project: {
    ano: {$substr: ["$dataAdmissao", 0, 4]},
    salario: 1
  }},
  {$group: {
    _id: "$ano",
    mediaSalarial: {$avg: "$salario"},
    totalFuncionarios: {$sum: 1}
  }},
  {$sort: {_id: 1}}
])
```

**Resultado**
| Ano | Média Salarial | Total de Funcionários |
|---|---|---|
| 1980 | 9700 | 1 |
| 1987 | 23000 | 1 |
| 1994 | 12500 | 1 |
| 1995 | 15290 | 2 |
| 1996 | 10633.33 | 3 |
| 1997 | 8700 | 4 |
| 1999 | 8900 | 1 |
| 2000 | 4100 | 1 |
| 2005 | 3400 | 1 |
| 2008 | 3500 | 1 |
| 2009 | 2900 | 1 |
| 2020 | 5000 | 1 |

---

## Nível 4: Operações e Escritórios

### 4.1 — Qual é o custo total de suprimentos em cada escritório? Ordene do mais caro ao mais barato

**Comando MongoDB**
```javascript
db.escritorios.aggregate([
  {$unwind: "$suprimentos"},
  {$project: {
    nome: 1,
    custo: {$multiply: ["$suprimentos.quantidade", "$suprimentos.precoUnitario"]}
  }},
  {$group: {
    _id: "$nome",
    custoTotal: {$sum: "$custo"}
  }},
  {$sort: {custoTotal: -1}}
])
```

**Resultado**
| Escritório | Custo Total |
|---|---|
| Umbrella Corp | 376429200.00 |
| Stark Industries | 97751700.00 |
| Wayne Offices | 58538.75 |
| Winterfell Offices | 53538.75 |

---

### 4.2 — Qual escritório possui a maior quantidade de diferentes tipos de suprimentos?

**Comando MongoDB**
```javascript
db.escritorios.aggregate([
  {$project: {
    nome: 1,
    totalTipos: {$size: "$suprimentos"}
  }},
  {$sort: {totalTipos: -1}},
  {$limit: 1}
])
```

**Resultado**
| Escritório | Total de Tipos de Suprimentos |
|---|---|
| Wayne Offices | 5 |
| Winterfell Offices | 5 |
| Stark Industries | 4 |
| Umbrella Corp | 4 |

**Observação:** Wayne Offices e Winterfell Offices possuem o maior número (5 tipos).

---

### 4.3 — Qual é o suprimento mais caro (considerando preço unitário) em toda a empresa?

**Comando MongoDB**
```javascript
db.escritorios.aggregate([
  {$unwind: "$suprimentos"},
  {$sort: {"suprimentos.precoUnitario": -1}},
  {$limit: 1},
  {$project: {
    escritorio: "$nome",
    produto: "$suprimentos.produto",
    precoUnitario: "$suprimentos.precoUnitario",
    _id: 0
  }}
])
```

**Resultado**
| Escritório | Produto | Preço Unitário |
|---|---|---|
| Umbrella Corp | Folhas de Sulfito | 752.85 |
| Wayne Offices | Post-its | 1000.00 |
| Winterfell Offices | Livros Didáticos | 5000.00 |
| Stark Industries | Computadores | 5000.00 |

**Mais Caro (Preço Unitário):** Computadores e Livros Didáticos - **R$ 5000.00**

---

### 4.4 — Calcule o valor total do inventário de suprimentos da empresa (quantidade × preço unitário de todos os itens em todos os escritórios)

**Comando MongoDB**
```javascript
db.escritorios.aggregate([
  {$unwind: "$suprimentos"},
  {$project: {
    valorInventario: {$multiply: ["$suprimentos.quantidade", "$suprimentos.precoUnitario"]}
  }},
  {$group: {
    _id: null,
    valorTotalInventario: {$sum: "$valorInventario"}
  }}
])
```

**Resultado**
```
574313438.50
```

---

## Nível 5: Produtos e Vendas

### 5.1 — Quais produtos foram vendidos pela Momento? Liste todos os produtos únicos

**Comando MongoDB**
```javascript
db.vendas.distinct("produto")
```

**Resultado**
| Produtos |
|---|
| Uniforme do Superman |
| Fake Batarang |
| Lança-Teias |
| Capacete do Homem-Formiga |
| Nulificador Total |
| Laço da Verdade |
| Sabre de Luz (Mace Windu) |
| Sentinelas do Bolivar Trask |
| Uniforme de Moléculas Instáveis |

**Total: 9 produtos únicos**

---

### 5.2 — Qual é o produto mais vendido (maior quantidade total)?

**Comando MongoDB**
```javascript
db.vendas.aggregate([
  {$group: {
    _id: "$produto",
    totalQuantidade: {$sum: "$quantidade"}
  }},
  {$sort: {totalQuantidade: -1}},
  {$limit: 1}
])
```

**Resultado**
| Produto | Quantidade Total |
|---|---|
| Uniforme de Moléculas Instáveis | 10 |

---

### 5.3 — Qual é o produto menos vendido?

**Comando MongoDB**
```javascript
db.vendas.aggregate([
  {$group: {
    _id: "$produto",
    totalQuantidade: {$sum: "$quantidade"}
  }},
  {$sort: {totalQuantidade: 1}},
  {$limit: 1}
])
```

**Resultado**
| Produto | Quantidade Total |
|---|---|
| Uniforme do Superman | 2 |
| Fake Batarang | 2 |

**Observação:** Há empate entre dois produtos.

---

### 5.4 — Pensando na relação quantidade × valor unitário, qual produto gerou mais receita para a empresa?

**Comando MongoDB**
```javascript
db.vendas.aggregate([
  {$project: {
    produto: 1,
    receita: {$multiply: ["$quantidade", "$precoUnitario"]}
  }},
  {$group: {
    _id: "$produto",
    receitaTotal: {$sum: "$receita"}
  }},
  {$sort: {receitaTotal: -1}},
  {$limit: 1}
])
```

**Resultado**
| Produto | Receita Total |
|---|---|
| Sabre de Luz (Mace Windu) | 7922.32 |

---

### 5.5 — Qual é o produto mais caro (maior preço unitário) no catálogo?

**Comando MongoDB**
```javascript
db.vendas.aggregate([
  {$sort: {precoUnitario: -1}},
  {$limit: 1},
  {$project: {
    produto: 1,
    precoUnitario: 1,
    _id: 0
  }}
])
```

**Resultado**
| Produto | Preço Unitário |
|---|---|
| Sabre de Luz (Mace Windu) | 990.29 |

---

### 5.6 — Qual foi o faturamento total da empresa em vendas?

**Comando MongoDB**
```javascript
db.vendas.aggregate([
  {$project: {
    faturamento: {$multiply: ["$quantidade", "$precoUnitario"]}
  }},
  {$group: {
    _id: null,
    faturamentoTotal: {$sum: "$faturamento"}
  }}
])
```

**Resultado**
```
26543.45
```

---

### 5.7 — Quantas vendas foram realizadas no mês de junho de 2023?

**Comando MongoDB**
```javascript
db.vendas.countDocuments({
  dataVenda: {
    $gte: "2023-06-01",
    $lte: "2023-06-30"
  }
})
```

**Resultado**
```
8
```

---

### 5.8 — Qual vendedor realizou mais vendas (em quantidade de transações)?

**Comando MongoDB**
```javascript
db.vendas.aggregate([
  {$match: {vendedor: {$exists: true}}},
  {$group: {
    _id: "$vendedor",
    totalTransacoes: {$sum: 1}
  }},
  {$sort: {totalTransacoes: -1}},
  {$limit: 1},
  {$lookup: {
    from: "funcionarios",
    localField: "_id",
    foreignField: "_id",
    as: "vendedorInfo"
  }},
  {$unwind: "$vendedorInfo"}
])
```

**Resultado**
| Vendedor | Total de Transações |
|---|---|
| Michael Hartstein | 2 |
| Sundar Ande | 2 |
| Jon Deegan | 2 |

**Observação:** Há empate entre 3 vendedores com 2 transações cada.

---

### 5.9 — Qual vendedor gerou mais receita para a empresa?

**Comando MongoDB**
```javascript
db.vendas.aggregate([
  {$match: {vendedor: {$exists: true}}},
  {$project: {
    vendedor: 1,
    receita: {$multiply: ["$quantidade", "$precoUnitario"]}
  }},
  {$group: {
    _id: "$vendedor",
    receitaTotal: {$sum: "$receita"}
  }},
  {$sort: {receitaTotal: -1}},
  {$limit: 1},
  {$lookup: {
    from: "funcionarios",
    localField: "_id",
    foreignField: "_id",
    as: "vendedorInfo"
  }},
  {$unwind: "$vendedorInfo"}
])
```

**Resultado**
| Vendedor | Receita Total |
|---|---|
| Michael Hartstein | 7922.32 |

---

## Nível 6: Operações de Atualização

### 6.1 — Um novo departamento foi criado: Inovações. Ele será alocado no escritório "Wayne Offices". Adicione-o ao banco de dados

**Comando MongoDB**
```javascript
db.departamentos.insertOne({
  "nome": "Inovações",
  "escritorio": ObjectId("5f8b3f3f9b3e0b3b3c1e3e3e")
})
```

**Resultado**
```
Inserido com sucesso! ID: ObjectId("nova_id_gerada")
```

---

### 6.2 — O departamento de Inovações está sem funcionários. Transfira 2 funcionários do departamento de Tecnologia para Inovações

**Passo 1 - Encontre funcionários do departamento de Tecnologia (para verificar)**
```javascript
db.funcionarios.find({
  departamento: ObjectId("85992103f9b3e0b3b3c1fe74")
}).project({nome: 1, departamento: 1})
```

**Passo 2 - Transfira 2 funcionários**
```javascript
db.funcionarios.updateMany(
  {
    _id: {$in: [
      ObjectId("5f8b3f3f9b3e0b3b3c1e3e44"),
      ObjectId("5f8b3f3f9b3e0b3b3c1e3e45")
    ]}
  },
  {$set: {departamento: ObjectId("inovacoes_id")}}
)
```

**Resultado**
```
2 documentos atualizados com sucesso
```

---

### 6.3 — A empresa decidiu dar um aumento de 10% para todos os funcionários do departamento de Tecnologia. Atualize os salários

**Comando MongoDB**
```javascript
db.funcionarios.updateMany(
  {departamento: ObjectId("85992103f9b3e0b3b3c1fe74")},
  [{$set: {salario: {$multiply: ["$salario", 1.10]}}}]
)
```

**Resultado**
```
3 documentos atualizados com sucesso
```

---

### 6.4 — O funcionário "Bruce Ernst" foi promovido a "Senior Web Developer" e recebeu um aumento para $5.000. Atualize suas informações

**Comando MongoDB**
```javascript
db.funcionarios.updateOne(
  {nome: "Bruce Ernst"},
  {$set: {
    cargo: "Senior Web Developer",
    salario: 5000
  }}
)
```

**Resultado**
```
1 documento atualizado com sucesso
```

---

### 6.5 — Adicione um novo suprimento ao escritório "Wayne Offices": 15 unidades de "Headsets" com preço unitário de $150 cada

**Comando MongoDB**
```javascript
db.escritorios.updateOne(
  {nome: "Wayne Offices"},
  {$push: {
    suprimentos: {
      produto: "Headsets",
      quantidade: 15,
      precoUnitario: 150
    }
  }}
)
```

**Resultado**
```
1 documento atualizado com sucesso
```

---

### 6.6 — Todos os funcionários contratados antes de 1990 estão aposentando. Remova-os do banco de dados

**Passo 1 - Verifique quantos funcionários serão afetados (SEMPRE FAÇA ISTO ANTES!)**
```javascript
db.funcionarios.find({
  dataAdmissao: {$lt: "1990-01-01"}
}).project({nome: 1, dataAdmissao: 1})
```

**Resultado da verificação:**
| Nome | Data de Admissão |
|---|---|
| Irene Adler | 1980-09-28 |
| Jennifer Whalen | 1987-09-17 |

**Total: 2 funcionários serão removidos**

**Passo 2 - Execute a remoção**
```javascript
db.funcionarios.deleteMany({
  dataAdmissao: {$lt: "1990-01-01"}
})
```

**Resultado**
```
2 documentos removidos com sucesso
`
