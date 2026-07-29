# Review Filmes

Aplicação web para catálogo e avaliação de filmes, construída com **ASP.NET Core 8 MVC** e banco de dados **PostgreSQL**.

## Funcionalidades

- Exibição de catálogo de filmes com slide e thumbnail
- Página de detalhes do filme (título, resumo, lançamento, categoria, elenco, direção)
- Cadastro de reviews (avaliação) por usuários
- Exposição de métricas via Prometheus (`/metrics`)

## Arquitetura

```
src/
├── Review-Filmes.Domain/           # Modelos de domínio (Filme, Review)
├── Review-Filmes.Web/              # Aplicação MVC (Controllers, Views, Repository, EF Core)
├── Review-Filmes.Test.Unit/        # Testes unitários (NUnit + Moq)
├── Review-Filmes.Test.Integration/ # Testes de integração (PostgreSQL real)
└── Review-Filmes.Test.EndToEnd/    # Testes E2E (Selenium + ChromeDriver)
```

## Pré-requisitos

- [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- [Docker](https://www.docker.com/) e Docker Compose
- PostgreSQL (ou use o Docker Compose abaixo)

## Executando com Docker Compose

```bash
cd src
docker compose up --build
```

A aplicação ficará disponível em: `http://localhost:8080`

## Executando localmente (sem Docker)

1. Suba o PostgreSQL:
   ```bash
   docker run -e POSTGRES_USER=review -e POSTGRES_PASSWORD=Passw0rd2023! -e POSTGRES_DB=review -p 5432:5432 postgres
   ```

2. Configure a connection string em `src/Review-Filmes.Web/appsettings.json`:
   ```json
   "ConnectionStrings": {
     "DefaultConnection": "Host=localhost;Database=review;Username=review;Password=Passw0rd2023!"
   }
   ```

3. Execute a aplicação:
   ```bash
   cd src/Review-Filmes.Web
   dotnet run
   ```

> As migrations são aplicadas automaticamente na inicialização da aplicação.

## Executando os Testes

```bash
# Testes unitários
dotnet test src/Review-Filmes.Test.Unit/

# Testes de integração (requer PostgreSQL configurado)
dotnet test src/Review-Filmes.Test.Integration/

# Testes E2E (requer aplicação rodando + ChromeDriver)
dotnet test src/Review-Filmes.Test.EndToEnd/
```

## Deploy no Kubernetes

```bash
kubectl apply -f k8s/deployment.yaml
```

O arquivo `k8s/deployment.yaml` inclui os deployments de PostgreSQL e da aplicação web.

## Stack Tecnológica

| Camada         | Tecnologia                          |
|----------------|-------------------------------------|
| Framework      | ASP.NET Core 8 MVC                  |
| Banco de dados | PostgreSQL + Entity Framework Core  |
| ORM            | EF Core com Npgsql                  |
| Testes         | NUnit, Moq, Selenium                |
| Container      | Docker + Docker Compose             |
| Orquestração   | Kubernetes                          |
| Monitoramento  | Prometheus (`prometheus-net`)       |
