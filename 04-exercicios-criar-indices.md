## Exercício 2: Criação de Índices

### Adicionar um índice ao campo email da tabela cliente
```sql
CREATE INDEX idx_email ON cliente(email);
```
### Adicionar um índice ao campo nome da tabela produto
```sql
CREATE INDEX idx_nome_produto ON produto(nome);
```
