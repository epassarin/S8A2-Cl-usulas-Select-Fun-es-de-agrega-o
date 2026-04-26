# S8A2-Cl-usulas-Select-Fun-es-de-agrega-o
Cláusulas Select Funções de agregação | [SIS]ANO2C4B2S8A2
Com base no documento preparei este roteiro completo, **[SIS]ANO2C4B2S8A2AP.docx** e no contexto de ensino para o **Técnico em Desenvolvimento de Sistemas**,  esta atividade foca em **Funções de Agregação e Agrupamento** em uma única tabela, preparando o terreno para aulas futuras de JOINS.

Abaixo, o passo a passo configurado para o ambiente **VS Code com SQLite**.

---

# Guia de Aula Prática: Análise de Dados de Vendas (Parte 1)

## Etapa 1: Preparação do Ambiente de Laboratório
Antes de codificar, vocês precisam preparar o arquivo de banco de dados.

1.  No VS Code, crie um arquivo chamado `analise_vendas.sql`, para isso pressione `Ctrl+Shift+P` e selecione **SQLite: New Query**.
2.  Se não instalou dormiu no ponto, ficou jogando ou fazendo coisas desnecessária, nao tem instalado.

**Pra quem não instalaou segue routeiro logo abaixo e os plugin que deve instalar**


## Etapa 2: Construção da Infraestrutura (DDL e DML)
O primeiro passo é criar a tabela conforme o contexto da atividade e inserir dados fictícios para que os cálculos façam sentido.

### Bloco 1: Criação da Tabela
```sql
CREATE TABLE vendas (
    id_venda INTEGER PRIMARY KEY AUTOINCREMENT,
    produto TEXT NOT NULL,
    quantidade INTEGER NOT NULL,
    valor_unitario REAL NOT NULL,
    data_venda DATE NOT NULL
);
```

**Explicação:** "Aqui estamos definindo o 'molde' dos nossos dados. Usamos `REAL` para valores decimais (dinheiro) e `DATE` para as datas. O `AUTOINCREMENT` garante que cada venda receba um número único automaticamente."

### Bloco 2: Inserção de Dados de Teste
*Dica: Como o documento menciona "mês passado", vamos inserir dados relativos ao mês anterior à data atual.*

```sql
INSERT INTO vendas (produto, quantidade, valor_unitario, data_venda) VALUES 
('Mouse Optico', 10, 50.00, '2024-03-15'),
('Teclado Mecânico', 5, 200.00, '2024-03-20'),
('Monitor 24', 2, 900.00, '2024-03-22'),
('Mouse Optico', 2, 50.00, '2024-03-25'),
('Webcam HD', 8, 150.00, '2024-04-01'); -- Venda deste mês (para testar o filtro)
```

---

## Etapa 3: RESOLUÇÃO DETALAHDA DAS ETAPAS DA ATIVIDADE

Agora, aplicaremos as cláusulas SQL para extrair as respostas solicitadas no documento.

### Passo 1: Quantas vendas foram realizadas no mês passado?

```sql
SELECT COUNT(*) AS total_vendas_mes_passado
FROM vendas
WHERE data_venda BETWEEN '2024-03-01' AND '2024-03-31';
```
**Explicação Técnica:** "A função `COUNT(*)` conta todas as linhas que passam pelo filtro. O `BETWEEN` delimita o período exato do mês passado. O `AS` cria um apelido amigável para a coluna de resultado."

### Passo 2: Qual foi o valor total de vendas de cada produto?
```sql
SELECT produto, SUM(quantidade * valor_unitario) AS faturamento_por_produto
FROM vendas
GROUP BY produto;
```

**Explicação Técnica:** "Aqui multiplicamos a quantidade pelo valor unitário dentro da função `SUM`. O `GROUP BY produto` é essencial: ele diz ao SQL para separar as vendas por nome de produto antes de somar."

### Passo 3: Qual foi o produto mais vendido em quantidade?
```sql
SELECT produto, SUM(quantidade) AS total_qtd
FROM vendas
GROUP BY produto
ORDER BY total_qtd DESC
LIMIT 1;
```
**Explicação Técnica:** "Agrupamos por produto e somamos as quantidades. O `ORDER BY DESC` coloca o maior número no topo, e o `LIMIT 1` isola apenas o vencedor."

### Passo 4: Qual foi o valor médio das vendas por dia no mês passado?
```sql
SELECT data_venda, AVG(quantidade * valor_unitario) AS media_diaria
FROM vendas
WHERE data_venda BETWEEN '2024-03-01' AND '2024-03-31'
GROUP BY data_venda;
```
**Explicação Técnica:** "A função `AVG` (Average) calcula a média. Agrupando por `data_venda`, o SQL calcula a média financeira de todos os pedidos ocorridos em cada dia específico."

### Passo 5: Qual foi a venda de maior valor e a de menor valor no mês passado?
```sql
SELECT MAX(quantidade * valor_unitario) AS maior_venda,
       MIN(quantidade * valor_unitario) AS menor_venda
FROM vendas
WHERE data_venda BETWEEN '2024-03-01' AND '2024-03-31';
```
**Explicação Técnica:** "As funções `MAX` e `MIN` percorrem todos os cálculos de (qtd * valor) no período filtrado e retornam os valores extremos."

---

## Resultado Final Esperado (O que deve aparecer no VS Code)

Se o aluno executar o script corretamente, o resultado visual (Output) no VS Code para o **Passo 2** (Faturamento por Produto), por exemplo, deve ser algo como:

| produto | faturamento_por_produto |
| :--- | :--- |
| Monitor 24 | 1800.0 |
| Mouse Optico | 600.0 |
| Teclado Mecânico | 1000.0 |

---

### Dicas de Ouro:
1.  **Diferença de Agregadores:**  `COUNT` conta registros (linhas), enquanto `SUM` soma valores numéricos dentro dessas linhas.
2.  **O Perigo do GROUP BY:** Se esquecerem o `GROUP BY` no Passo 2, o SQL tentará somar tudo em uma única linha, o que causará um erro de lógica ou exibirá apenas o primeiro produto com a soma de todos os outros.
3.  **Filtros de Data:** Em bancos reais (MySQL/PostgreSQL), usamos `DATE_SUB(CURDATE(), INTERVAL 1 MONTH)`, mas no SQLite, para fins didáticos, datas fixas em formato string (`'YYYY-MM-DD'`) são mais fáceis para o primeiro contato.
