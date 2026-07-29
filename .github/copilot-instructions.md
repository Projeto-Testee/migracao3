# Copilot Instructions — Review Filmes

## Contexto do projeto

Aplicação ASP.NET Core 8 MVC para catálogo e review de filmes. Usa PostgreSQL como banco de dados, EF Core (Npgsql) para ORM e Prometheus para métricas expostas em `/metrics`.

## Estrutura do projeto

- `Review-Filmes.Domain` — modelos de domínio (`Filme`, `Review`). **Sem dependências de infraestrutura.**
- `Review-Filmes.Web` — aplicação web MVC. Contém controllers, views Razor, repositórios, contexto EF Core (`AppDbContext`) e migrations.
- `Review-Filmes.Test.Unit` — testes unitários com NUnit + Moq; sem dependências externas.
- `Review-Filmes.Test.Integration` — testes com banco PostgreSQL real; connection string lida de `appsettings.json` ou variável de ambiente `ConnectionStrings__DefaultConnection`.
- `Review-Filmes.Test.EndToEnd` — testes Selenium com ChromeDriver headless; URL base configurada em `appsettings.json` ou variável `BASE_URL`.

## Convenções de código

- **Idioma**: nomes de classes, métodos e variáveis em **inglês**; comentários e mensagens de log em **português (pt-BR)**.
- Usar **injeção de dependência via construtor** (sem service locator ou acesso direto a `IServiceProvider`).
- Repositórios acessados exclusivamente via interfaces (`IFilmeRepository`, `IReviewRepository`).
- **Nunca** acessar `AppDbContext` diretamente nos controllers; sempre usar o repositório correspondente.
- Migrations geradas com `dotnet ef migrations add` e aplicadas automaticamente no startup via `db.Database.Migrate()`.
- Não adicionar lógica de negócio nos controllers; regras pertencem ao domínio (`Review-Filmes.Domain`).
- Preferir **ViewModels tipados** em vez de `ViewBag` ou `ViewData`.

## Padrões de teste

- **Unitários**: usar `Mock<IFilmeRepository>` e `Mock<IReviewRepository>` com `MockBehavior.Strict`; configurar apenas os métodos necessários com `.Setup(...)`.
- **Integração**: criar dados no `[SetUp]` usando `_context.Filmes.Add(...)` seguido de `_context.SaveChanges()`; usar `EnsureCreated()` para garantir o schema.
- **E2E**: usar `WebDriverWait` para aguardar elementos dinâmicos; **nunca** usar `Thread.Sleep`.
- Framework de testes: **NUnit** com os atributos `[Test]`, `[SetUp]` e `[TearDown]`.

## Banco de dados

- Provider: **Npgsql** (PostgreSQL).
- Connection string local: `Host=localhost;Database=review;Username=review;Password=Passw0rd2023!`
- Connection string Docker Compose: `Host=postgres;Database=review;Username=review;Password=Passw0rd2023!`
- Variáveis sensíveis devem ser passadas por variáveis de ambiente; **nunca commitar credenciais**.

## Docker e Kubernetes

- Dockerfile usa **multi-stage build**: `mcr.microsoft.com/dotnet/sdk:8.0` para build e `aspnet:8.0` para runtime.
- Porta exposta: **8080**.
- `docker-compose.yml` (em `src/`) sobe a aplicação e o PostgreSQL juntos.
- Manifests Kubernetes estão em `k8s/deployment.yaml` (inclui deployment do PostgreSQL e da aplicação).
- Deploy local usa runner self-hosted + Kind (cluster: `laboratorio`); imagem carregada com `kind load docker-image`.

## O que evitar

- Não duplicar migrations — verificar se já existe antes de criar uma nova.
- Não usar `Thread.Sleep` em testes; usar `WebDriverWait` ou `Task.Delay` com cancelamento.
- Não adicionar pacotes NuGet sem verificar se já existe equivalente no projeto.
- Não usar credenciais hardcoded em código-fonte ou arquivos de configuração versionados.
