# MVP Backend - ASP.NET Core API

## Estrutura do Projeto

```
src/
 ├── Controllers/
 │   └── DashboardController.cs     # Endpoint principal
 ├── Models/
 │   ├── Rodada.cs                  # Entidade principal
 │   └── DashboardResponse.cs       # DTO de resposta
 ├── Data/
 │   ├── AppDbContext.cs            # Context do EF Core
 │   └── DataSeeder.cs              # Dados iniciais
 ├── Services/
 │   ├── IDashboardService.cs       # Interface
 │   └── DashboardService.cs        # Lógica de negócio
 └── Program.cs                     # Configuração da aplicação
```

## Modelos de Dados

### Rodada.cs
```csharp
public class Rodada
{
    public int Id { get; set; }
    public int NumeroRodada { get; set; }
    public int PontosPlanejados { get; set; }
    public int? PontosConquistados { get; set; }
    public DateTime? DataJogo { get; set; }
    public string? Adversario { get; set; }
    public bool JogoRealizado => PontosConquistados.HasValue;
}
```

### DashboardResponse.cs
```csharp
public class DashboardResponse
{
    public List<RodadaDto> Rodadas { get; set; } = new();
    public EstatisticasDto Estatisticas { get; set; } = new();
}

public class RodadaDto
{
    public int NumeroRodada { get; set; }
    public int PontosPlanejados { get; set; }
    public int? PontosConquistados { get; set; }
    public string? Adversario { get; set; }
    public DateTime? DataJogo { get; set; }
    public bool JogoRealizado { get; set; }
}

public class EstatisticasDto
{
    public int TotalPontosConquistados { get; set; }
    public int TotalPontosPlanejados { get; set; }
    public decimal PercentualCumprimento { get; set; }
    public int RodadasJogadas { get; set; }
    public int TotalRodadas { get; set; }
}
```

## Controller

### DashboardController.cs
```csharp
[ApiController]
[Route("api/[controller]")]
public class DashboardController : ControllerBase
{
    private readonly IDashboardService _dashboardService;

    public DashboardController(IDashboardService dashboardService)
    {
        _dashboardService = dashboardService;
    }

    [HttpGet]
    public async Task<ActionResult<DashboardResponse>> GetDashboard()
    {
        try
        {
            var dashboard = await _dashboardService.GetDashboardDataAsync();
            return Ok(dashboard);
        }
        catch (Exception ex)
        {
            return StatusCode(500, new { message = "Erro interno do servidor" });
        }
    }
}
```

## Serviços

### IDashboardService.cs
```csharp
public interface IDashboardService
{
    Task<DashboardResponse> GetDashboardDataAsync();
}
```

### DashboardService.cs
```csharp
public class DashboardService : IDashboardService
{
    private readonly AppDbContext _context;

    public DashboardService(AppDbContext context)
    {
        _context = context;
    }

    public async Task<DashboardResponse> GetDashboardDataAsync()
    {
        var rodadas = await _context.Rodadas
            .OrderBy(r => r.NumeroRodada)
            .ToListAsync();

        var rodadasDto = rodadas.Select(r => new RodadaDto
        {
            NumeroRodada = r.NumeroRodada,
            PontosPlanejados = r.PontosPlanejados,
            PontosConquistados = r.PontosConquistados,
            Adversario = r.Adversario,
            DataJogo = r.DataJogo,
            JogoRealizado = r.JogoRealizado
        }).ToList();

        var estatisticas = CalcularEstatisticas(rodadas);

        return new DashboardResponse
        {
            Rodadas = rodadasDto,
            Estatisticas = estatisticas
        };
    }

    private EstatisticasDto CalcularEstatisticas(List<Rodada> rodadas)
    {
        var rodadasJogadas = rodadas.Where(r => r.JogoRealizado).ToList();
        
        var totalConquistados = rodadasJogadas.Sum(r => r.PontosConquistados ?? 0);
        var totalPlanejados = rodadasJogadas.Sum(r => r.PontosPlanejados);
        
        var percentual = totalPlanejados > 0 
            ? (decimal)totalConquistados / totalPlanejados * 100 
            : 0;

        return new EstatisticasDto
        {
            TotalPontosConquistados = totalConquistados,
            TotalPontosPlanejados = totalPlanejados,
            PercentualCumprimento = Math.Round(percentual, 1),
            RodadasJogadas = rodadasJogadas.Count,
            TotalRodadas = rodadas.Count
        };
    }
}
```

## Configuração (Program.cs)

```csharp
var builder = WebApplication.CreateBuilder(args);

// Services
builder.Services.AddControllers();
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlite(builder.Configuration.GetConnectionString("DefaultConnection")));

builder.Services.AddScoped<IDashboardService, DashboardService>();

// CORS para desenvolvimento
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowFrontend", policy =>
    {
        policy.WithOrigins("http://localhost:3000", "http://localhost:5173")
              .AllowAnyHeader()
              .AllowAnyMethod();
    });
});

var app = builder.Build();

// Pipeline
if (app.Environment.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}

app.UseCors("AllowFrontend");
app.UseRouting();
app.MapControllers();

// Seed inicial dos dados
using (var scope = app.Services.CreateScope())
{
    var context = scope.ServiceProvider.GetRequiredService<AppDbContext>();
    await DataSeeder.SeedAsync(context);
}

app.Run();
```

## Banco de Dados (AppDbContext.cs)

```csharp
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

    public DbSet<Rodada> Rodadas { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Rodada>(entity =>
        {
            entity.HasKey(e => e.Id);
            entity.Property(e => e.NumeroRodada).IsRequired();
            entity.Property(e => e.PontosPlanejados).IsRequired();
            entity.Property(e => e.Adversario).HasMaxLength(100);
        });
    }
}
```

## Docker

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src
COPY ["*.csproj", "./"]
RUN dotnet restore
COPY . .
RUN dotnet build -c Release -o /app/build

FROM build AS publish
RUN dotnet publish -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "ClassificacaoPlanjada.Api.dll"]
```

## Comandos Úteis

```bash
# Executar em desenvolvimento
dotnet run

# Criar migração
dotnet ef migrations add InitialCreate

# Aplicar migração
dotnet ef database update

# Build para produção
dotnet publish -c Release
```