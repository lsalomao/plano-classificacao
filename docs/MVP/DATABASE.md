# MVP Database - Estrutura e Dados

## Esquema do Banco de Dados

### Tabela: Rodadas

```sql
CREATE TABLE Rodadas (
    Id INTEGER PRIMARY KEY AUTOINCREMENT,
    NumeroRodada INTEGER NOT NULL,
    PontosPlanejados INTEGER NOT NULL,
    PontosConquistados INTEGER NULL,
    DataJogo DATETIME NULL,
    Adversario VARCHAR(100) NULL
);
```

## Script de Criação (SQLite)

```sql
-- Criação da tabela
CREATE TABLE IF NOT EXISTS Rodadas (
    Id INTEGER PRIMARY KEY AUTOINCREMENT,
    NumeroRodada INTEGER NOT NULL,
    PontosPlanejados INTEGER NOT NULL,
    PontosConquistados INTEGER NULL,
    DataJogo DATETIME NULL,
    Adversario VARCHAR(100) NULL
);

-- Índices para performance
CREATE INDEX IF NOT EXISTS IX_Rodadas_NumeroRodada ON Rodadas(NumeroRodada);
```

## Dados de Exemplo (38 rodadas do Brasileirão)

```sql
-- Inserção dos dados de exemplo
INSERT INTO Rodadas (NumeroRodada, PontosPlanejados, PontosConquistados, DataJogo, Adversario) VALUES
-- Rodadas já jogadas (com resultados)
(1, 1, 3, '2024-04-13 16:00:00', 'Cuiabá'),
(2, 2, 1, '2024-04-20 18:30:00', 'Palmeiras'),
(3, 4, 3, '2024-04-27 16:00:00', 'Criciúma'),
(4, 5, 0, '2024-05-04 19:00:00', 'Flamengo'),
(5, 6, 3, '2024-05-11 16:00:00', 'Goianiense'),
(6, 8, 1, '2024-05-18 18:30:00', 'Grêmio'),
(7, 10, 3, '2024-05-25 16:00:00', 'Vitória'),
(8, 11, 1, '2024-06-01 19:00:00', 'Corinthians'),
(9, 13, 3, '2024-06-08 16:00:00', 'Juventude'),
(10, 14, 0, '2024-06-15 18:30:00', 'Atlético-MG'),

-- Rodadas futuras (apenas planejamento)
(11, 16, NULL, NULL, 'Bahia'),
(12, 17, NULL, NULL, 'Fortaleza'),
(13, 19, NULL, NULL, 'Botafogo'),
(14, 20, NULL, NULL, 'Red Bull Bragantino'),
(15, 22, NULL, NULL, 'Vasco'),
(16, 23, NULL, NULL, 'Internacional'),
(17, 25, NULL, NULL, 'São Paulo'),
(18, 26, NULL, NULL, 'Athletico-PR'),
(19, 27, NULL, NULL, 'Fluminense'),
(20, 29, NULL, NULL, 'Cuiabá'),
(21, 30, NULL, NULL, 'Palmeiras'),
(22, 32, NULL, NULL, 'Criciúma'),
(23, 33, NULL, NULL, 'Flamengo'),
(24, 35, NULL, NULL, 'Goianiense'),
(25, 36, NULL, NULL, 'Grêmio'),
(26, 38, NULL, NULL, 'Vitória'),
(27, 39, NULL, NULL, 'Corinthians'),
(28, 41, NULL, NULL, 'Juventude'),
(29, 42, NULL, NULL, 'Atlético-MG'),
(30, 44, NULL, NULL, 'Bahia'),
(31, 45, NULL, NULL, 'Fortaleza'),
(32, 47, NULL, NULL, 'Botafogo'),
(33, 48, NULL, NULL, 'Red Bull Bragantino'),
(34, 50, NULL, NULL, 'Vasco'),
(35, 51, NULL, NULL, 'Internacional'),
(36, 53, NULL, NULL, 'São Paulo'),
(37, 54, NULL, NULL, 'Athletico-PR'),
(38, 56, NULL, NULL, 'Fluminense');
```

## Análise dos Dados de Exemplo

### Estatísticas Atuais (10 rodadas jogadas):
- **Pontos Conquistados**: 18 pontos
- **Pontos Planejados**: 14 pontos (até a rodada 10)
- **Percentual de Cumprimento**: 128.6% (acima da meta!)
- **Rodadas Jogadas**: 10 de 38

### Distribuição de Pontos por Dificuldade:
- **Jogos "Fáceis"** (Cuiabá, Criciúma, Goianiense, Vitória, Juventude): 12/15 pontos (80%)
- **Jogos "Médios"** (Palmeiras, Grêmio, Corinthians): 2/9 pontos (22%)
- **Jogos "Difíceis"** (Flamengo, Atlético-MG): 0/6 pontos (0%)

## DataSeeder.cs (Para EF Core)

```csharp
public static class DataSeeder
{
    public static async Task SeedAsync(AppDbContext context)
    {
        // Verifica se já existem dados
        if (await context.Rodadas.AnyAsync())
            return;

        var rodadas = new List<Rodada>
        {
            // Rodadas já jogadas
            new() { NumeroRodada = 1, PontosPlanejados = 1, PontosConquistados = 3, DataJogo = new DateTime(2024, 4, 13, 16, 0, 0), Adversario = "Cuiabá" },
            new() { NumeroRodada = 2, PontosPlanejados = 2, PontosConquistados = 1, DataJogo = new DateTime(2024, 4, 20, 18, 30, 0), Adversario = "Palmeiras" },
            new() { NumeroRodada = 3, PontosPlanejados = 4, PontosConquistados = 3, DataJogo = new DateTime(2024, 4, 27, 16, 0, 0), Adversario = "Criciúma" },
            new() { NumeroRodada = 4, PontosPlanejados = 5, PontosConquistados = 0, DataJogo = new DateTime(2024, 5, 4, 19, 0, 0), Adversario = "Flamengo" },
            new() { NumeroRodada = 5, PontosPlanejados = 6, PontosConquistados = 3, DataJogo = new DateTime(2024, 5, 11, 16, 0, 0), Adversario = "Goianiense" },
            new() { NumeroRodada = 6, PontosPlanejados = 8, PontosConquistados = 1, DataJogo = new DateTime(2024, 5, 18, 18, 30, 0), Adversario = "Grêmio" },
            new() { NumeroRodada = 7, PontosPlanejados = 10, PontosConquistados = 3, DataJogo = new DateTime(2024, 5, 25, 16, 0, 0), Adversario = "Vitória" },
            new() { NumeroRodada = 8, PontosPlanejados = 11, PontosConquistados = 1, DataJogo = new DateTime(2024, 6, 1, 19, 0, 0), Adversario = "Corinthians" },
            new() { NumeroRodada = 9, PontosPlanejados = 13, PontosConquistados = 3, DataJogo = new DateTime(2024, 6, 8, 16, 0, 0), Adversario = "Juventude" },
            new() { NumeroRodada = 10, PontosPlanejados = 14, PontosConquistados = 0, DataJogo = new DateTime(2024, 6, 15, 18, 30, 0), Adversario = "Atlético-MG" },
            
            // Rodadas futuras (apenas planejamento)
            new() { NumeroRodada = 11, PontosPlanejados = 16, Adversario = "Bahia" },
            new() { NumeroRodada = 12, PontosPlanejados = 17, Adversario = "Fortaleza" },
            // ... continuar com todas as 38 rodadas
        };

        await context.Rodadas.AddRangeAsync(rodadas);
        await context.SaveChangesAsync();
    }
}
```

## Queries Úteis para Análise

```sql
-- Percentual de cumprimento atual
SELECT 
    ROUND(
        (CAST(SUM(PontosConquistados) AS FLOAT) / SUM(PontosPlanejados)) * 100, 
        1
    ) as PercentualCumprimento
FROM Rodadas 
WHERE PontosConquistados IS NOT NULL;

-- Rodadas com melhor e pior performance
SELECT 
    NumeroRodada,
    Adversario,
    PontosPlanejados,
    PontosConquistados,
    (PontosConquistados - PontosPlanejados) as Diferenca
FROM Rodadas 
WHERE PontosConquistados IS NOT NULL
ORDER BY Diferenca DESC;

-- Próximas 5 rodadas
SELECT 
    NumeroRodada,
    Adversario,
    PontosPlanejados
FROM Rodadas 
WHERE PontosConquistados IS NULL
ORDER BY NumeroRodada
LIMIT 5;
```

## Backup e Restore

```bash
# Backup do banco SQLite
cp database.db database_backup_$(date +%Y%m%d).db

# Restore
cp database_backup_20241201.db database.db
```

## Considerações de Performance

- **Índices**: Criados em `NumeroRodada` para ordenação rápida
- **Tamanho**: ~38 registros por temporada (muito pequeno)
- **Queries**: Todas as consultas são simples e rápidas
- **Crescimento**: Linear com o número de temporadas