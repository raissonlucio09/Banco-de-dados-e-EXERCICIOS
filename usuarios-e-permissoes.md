# Usuários e Permissões (DCL)

O SQL também **controla o acesso** aos dados. Nunca use o `root` na aplicação!

## Criar usuário
```sql
CREATE USER 'app_loja'@'localhost' IDENTIFIED BY 'SenhaForte#123';
-- '%' = qualquer host
CREATE USER 'relatorios'@'%' IDENTIFIED BY 'OutraSenha#456';
```

## GRANT — conceder
```sql
GRANT SELECT, INSERT, UPDATE, DELETE ON loja.* TO 'app_loja'@'localhost';
GRANT SELECT ON loja.* TO 'relatorios'@'%';              -- só leitura
GRANT SELECT (id, nome) ON loja.cliente TO 'relatorios'@'%';  -- só algumas colunas
GRANT ALL PRIVILEGES ON loja.* TO 'admin_loja'@'localhost';
FLUSH PRIVILEGES;
```

## Ver permissões
```sql
SHOW GRANTS FOR 'app_loja'@'localhost';
```

## REVOKE — remover
```sql
REVOKE DELETE ON loja.* FROM 'app_loja'@'localhost';
```

## Apagar usuário / trocar senha
```sql
DROP USER 'relatorios'@'%';
ALTER USER 'app_loja'@'localhost' IDENTIFIED BY 'NovaSenha#789';
```

## Roles (MySQL 8+)
```sql
CREATE ROLE 'leitor';
GRANT SELECT ON loja.* TO 'leitor';
GRANT 'leitor' TO 'relatorios'@'%';
```

## Princípio do menor privilégio
Dê a cada usuário **só o que ele precisa**. Combine com **views** (arquivo views.md) para restringir colunas.

## Backup e restore (linha de comando)
```bash
mysqldump -u root -p loja > backup_loja.sql        # backup
mysql -u root -p loja < backup_loja.sql            # restore
```
