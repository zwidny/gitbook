# 常用sql命令

## 项目前期比较有用的命令
1. 数据库创建
   ```sql
   create database if not exists starlight default character set utf8mb4 collate utf8mb4_unicode_ci;
   ```

1. Create User
   ```sql
   CREATE USER 'starlightor'@'%' IDENTIFIED BY 'starlightor';
   ```

1. 授权
   ```sql
   GRANT ALL PRIVILEGES ON starlight.* to 'starlightor'@'%';
   ```