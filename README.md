# Course API

API REST desenvolvida com Spring Boot para gerenciamento de pedidos, produtos, categorias e usuários.

## Tecnologias

- Java 21
- Spring Boot 4.0.3
- Spring Data JPA
- Banco H2 (perfil de teste)
- PostgreSQL (produção)
- Maven

## Modelo de domínio

```
User ──< Order >── OrderItem >── Product >── Category
                      |
                   Payment
```

- **User** — cliente com nome, e-mail, telefone e senha
- **Order** — pedido vinculado a um cliente, com data e status
- **OrderItem** — item de pedido com quantidade e preço unitário
- **Product** — produto com nome, descrição, preço e imagem; pode pertencer a múltiplas categorias
- **Category** — categoria de produto
- **Payment** — pagamento associado a um pedido

### Status do pedido (`OrderStatus`)

| Código | Status            |
|--------|-------------------|
| 1      | WAITING_PAYMENT   |
| 2      | PAID              |
| 3      | SHIPPED           |
| 4      | DELIVERED         |
| 5      | CANCELED          |

## Endpoints

### Usuários `/users`

| Método | Rota          | Descrição                     |
|--------|---------------|-------------------------------|
| GET    | `/users`      | Lista todos os usuários       |
| GET    | `/users/{id}` | Busca usuário por ID          |
| POST   | `/users`      | Cria um novo usuário          |
| PUT    | `/users/{id}` | Atualiza dados do usuário     |
| DELETE | `/users/{id}` | Remove um usuário             |

### Pedidos `/orders`

| Método | Rota            | Descrição               |
|--------|-----------------|-------------------------|
| GET    | `/orders`       | Lista todos os pedidos  |
| GET    | `/orders/{id}`  | Busca pedido por ID     |

### Produtos `/products`

| Método | Rota              | Descrição                |
|--------|-------------------|--------------------------|
| GET    | `/products`       | Lista todos os produtos  |
| GET    | `/products/{id}`  | Busca produto por ID     |

### Categorias `/categories`

| Método | Rota                 | Descrição                   |
|--------|----------------------|-----------------------------|
| GET    | `/categories`        | Lista todas as categorias   |
| GET    | `/categories/{id}`   | Busca categoria por ID      |

## Como executar

### Pré-requisitos

- JDK 21+
- Maven 3.8+

### Rodando em modo de teste (H2)

O perfil `test` já está ativo por padrão em `application.properties` e sobe com dados de exemplo.

```bash
./mvnw spring-boot:run
```

A aplicação estará disponível em `http://localhost:8080`.

### Console H2

Com o perfil `test` ativo, o console do banco H2 fica disponível em:

```
http://localhost:8080/h2-console
```

| Campo    | Valor              |
|----------|--------------------|
| JDBC URL | `jdbc:h2:mem:testdb` |
| Username | `sa`               |
| Password | *(vazio)*          |

### Rodando com PostgreSQL

Crie um perfil (ex.: `application-dev.properties`) com as configurações do seu banco PostgreSQL e ative-o:

```properties
spring.profiles.active=dev
```

## Estrutura do projeto

```
src/main/java/com/gabriel/course/
├── config/          # Configurações (seed de dados de teste)
├── entities/        # Entidades JPA
│   ├── enums/       # Enum OrderStatus
│   └── pk/          # Chave composta de OrderItem
├── repositories/    # Interfaces Spring Data JPA
├── resources/       # Controllers REST
│   └── exceptions/  # Tratamento de erros HTTP
└── services/        # Regras de negócio
    └── exceptions/  # Exceções de domínio
```

## Tratamento de erros

Erros retornam um objeto padronizado:

```json
{
  "timestamp": "2024-01-01T00:00:00Z",
  "status": 404,
  "error": "Resource not found",
  "message": "...",
  "path": "/users/99"
}
```

| Exceção                    | HTTP |
|----------------------------|------|
| `ResourceNotFoundException`| 404  |
| `DatabaseException`        | 400  |
