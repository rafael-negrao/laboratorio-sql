# Exercicios

## 1. Lista de Clientes Ativos que Nunca Usaram PIX
**Contexto de Negócio:** Identificar clientes que realizaram pedidos mas nunca usaram PIX como forma de pagamento.

```sql
SELECT DISTINCT c.nome
  FROM Cliente c
  JOIN Pedido p ON c.id = p.cliente_id
  LEFT JOIN FormaPagamento fp ON p.forma_pagamento_id = fp.id AND fp.tipoFormaPagamento = 'PIX'
 WHERE fp.id IS NULL;
```

## 2. Comparação de Vendas de Produtos com a Média da Categoria
**Contexto de Negócio:** Verificar quais produtos estão vendendo acima da média de vendas de sua categoria.

```sql
CREATE VIEW CategoriaMediaVendas AS
SELECT pc.categoria_id, AVG(ip.quantidade) AS media_quantidade
FROM item_pedido ip
JOIN produto_categoria pc ON ip.produto_id = pc.produto_id
GROUP BY pc.categoria_id;

SELECT p.nome, pc.categoria_id, ip.quantidade
FROM Produto p
JOIN item_pedido ip ON p.id = ip.produto_id
JOIN produto_categoria pc ON p.id = pc.produto_id
JOIN CategoriaMediaVendas cmv ON pc.categoria_id = cmv.categoria_id
WHERE ip.quantidade > cmv.media_quantidade;
```

## 3. Total de Vendas por Transportadora
**Contexto de Negócio:** Calcular o total de vendas entregues por cada transportadora.

```sql
SELECT t.nome, SUM(ip.preco_unitario * ip.quantidade) AS total_vendas
FROM Transportadora t
JOIN Entrega e ON t.id = e.transportadora_id
JOIN Pedido p ON e.pedido_id = p.id
JOIN item_pedido ip ON p.id = ip.pedido_id
GROUP BY t.nome;
```

## 4. Clientes com Pedidos Superiores a R$ 1000
**Contexto de Negócio:** Identificar clientes cujos pedidos excederam R$ 1000.

```sql
SELECT c.nome, SUM(ip.preco_unitario * ip.quantidade) AS valor_total
FROM Cliente c
JOIN Pedido p ON c.id = p.cliente_id
JOIN item_pedido ip ON p.id = ip.pedido_id
GROUP BY c.nome
HAVING valor_total > 1000;
```

## 5. Produtos Nunca Vendidos
**Contexto de Negócio:** Encontrar produtos que nunca foram vendidos.

```sql
SELECT p.nome
FROM Produto p
LEFT JOIN item_pedido ip ON p.id = ip.produto_id
WHERE ip.produto_id IS NULL;
```

## 6. Consolidação de Pedidos por Data com Union All
**Contexto de Negócio:** Comparar a atividade de pedidos no primeiro e último dia do mês.

```sql
SELECT 'Primeiro Dia' AS dia, COUNT(*) AS num_pedidos
FROM Pedido
WHERE DAY(data_pedido) = 1
UNION ALL
SELECT 'Último Dia', COUNT(*)
FROM Pedido
WHERE DAY(data_pedido) = LAST_DAY(data_pedido);
```

## 7. Detalhes dos Pedidos Atrasados
**Contexto de Negócio:** Verificar detalhes dos pedidos que foram entregues após a data prevista.

```sql
SELECT p.id AS PedidoID, c.nome AS Cliente, t.nome AS Transportadora, e.data_prevista, e.data_entrega
FROM Pedido p
JOIN Cliente c ON p.cliente_id = c.id
JOIN Entrega e ON p.id = e.pedido_id
JOIN Transportadora t ON e.transportadora_id = t.id
WHERE e.data_entrega > e.data_prevista;
```
## 8. Frequência de Uso das Formas de Pagamento
**Contexto de Negócio:** Analisar a frequência de uso das diferentes formas de pagamento.

```sql
SELECT fp.tipoFormaPagamento, COUNT(*) AS num_usos
FROM Pedido p
JOIN FormaPagamento fp ON p.forma_pagamento_id = fp.id
GROUP BY fp.tipoFormaPagamento;
```

## 9. Relatório de Pedidos por Estado do Cliente
**Contexto de Negócio:** Gerar um relatório dos pedidos baseado no estado do cliente.

```sql
SELECT c.estado, COUNT(*) AS num_pedidos
FROM Cliente c
JOIN Pedido p ON c.id = p.cliente_id
GROUP BY c.estado;
```
