# Sistema Classificação Planejada

## O que é o Sistema
O **Sistema Classificação Planejada** é uma ferramenta de planejamento estratégico para clubes de futebol baseada no conceito utilizado pelos próprios treinadores: buscar aproximadamente **72 pontos** para brigar pelo título do campeonato.

A ideia é simples: cada clube planeja onde pode ou não perder pontos, estabelecendo metas de pontuação por rodada considerando a dificuldade dos adversários que enfrentará.

## Como Funciona

O sistema permite que você:

1. **Defina metas de pontos** para cada rodada baseado na dificuldade dos jogos
2. **Registre os resultados reais** conforme os jogos acontecem
3. **Acompanhe o percentual** de cumprimento das metas estabelecidas
4. **Compense resultados** ruins em jogos difíceis com bons resultados em jogos mais fáceis

## Exemplo Prático

Um time com 10 pontos que enfrentou Grêmio, Corinthians, Palmeiras e Atlético-MG fora de casa **não tem os mesmos 10 pontos** de quem enfrentou Cuiabá, Criciúma e Goianiense. 

É isso que a classificação planejada indica: **a dificuldade do caminho** percorrido até aquele momento do campeonato.

## Princípios Importantes

- **Não faz previsões**: O sistema não tenta adivinhar resultados
- **Independência**: A planilha de cada time não tem relação com a dos outros
- **Flexibilidade**: Perdas em jogos "fáceis" podem ser compensadas em jogos "difíceis"
- **Foco no acumulado**: O importante é chegar próximo da meta total, não seguir à risca cada rodada

## Perguntas Frequentes

**Todos os times chegam a 72 pontos se cumprirem as metas?**  
Sim, essa é a base do planejamento.

**Alguns times podem perder clássicos e outros não?**  
Sim, porque alguns têm 2 clássicos por ano, outros têm 6.

**Se meu time perder um jogo que era para ganhar, já era?**  
Não. Você compensa com outro jogo que "não era para ganhar".

**Qual o objetivo final?**  
Chegar na rodada X com pontuação próxima ou superior ao planejado para aquela rodada. O percentual mostra o quanto seu time fez de pontos perto do que **deveria** ter feito até ali para brigar pelos 72 pontos.

---

## Documentação Técnica

### Arquitetura do Sistema
- [**ARCHITECTURE_FRONTEND.md**](ARCHITECTURE_FRONTEND.md) - Boas práticas e diretrizes para desenvolvimento em Vue.js
- [**ARCHITECTURE_BACKEND.md**](ARCHITECTURE_BACKEND.md) - Padrões e convenções para API em .NET (C#)

### Funcionalidades Detalhadas
- [**Definição de Metas de Pontos por Rodada**](features/definicao_metas_pontos_por_rodada.md) - Sistema para estabelecer objetivos por rodada
- [**Registro dos Resultados Reais**](features/registro_resultados_reais.md) - Controle dos pontos efetivamente conquistados
- [**Acompanhamento do Percentual de Cumprimento**](features/acompanhamento_percentual_cumprimento.md) - Dashboard com progresso e gráficos
- [**Flexibilidade para Compensação de Resultados**](features/flexibilidade_compensacao_resultados.md) - Sistema que permite compensar resultados

---

*Este sistema oferece uma visão clara e objetiva do desempenho do time em relação ao planejamento estratégico, auxiliando na tomada de decisões informadas para alcançar os objetivos no campeonato.*