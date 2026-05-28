# Correcao da topologia de serie das celulas - ICARUS v0.6

Data: 2026-05-28

> **⚠️ SUPERSEDED em v0.7 (2026-05-28).** A v0.7 mudou para topologia paralela (1S2P) — ver [VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md](VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md). A net `SERIES_MID` deixou de existir; ambas as células partilham `PANEL_POS` e `PANEL_NEG`. Este documento mantem-se como história da correccao anti-serie que aplicamos na v0.6.

Estado: correcao de erro critico de topologia. Pre-validacao fisica. Nao aprovado para fabrico nem voo.

## Problema

O PCB v0.6 ligava as duas celulas em **anti-serie** (saida ~ 0 V):
- Os dois contactos de verso (ambos POSITIVOS) estavam ambos ligados a `SERIES_MID`.
- O parity DRC nao apanha isto porque so compara `numero de pad <-> net`, nao qual o pad fisico.
- No SC2 os numeros de pad estavam trocados (verso=1/NEG, tabs=2/POS), invertendo a celula.

Polaridade fisica fixa da celula (TJ30SCA-4080): **tabs da frente = NEG; verso/centro = POS**.

## Topologia correta (2S)

`PANEL_NEG` -[SC1]- `SERIES_MID` -[SC2]- `PANEL_POS` (tensoes somam, ~5.4 V).

| Celula | Pad fisico | Polaridade | Pad # | Net |
|---|---|---|---:|---|
| SC1 | tabs (frente) | NEG | 1 | `PANEL_NEG` |
| SC1 | centro (verso) | POS | 2 | `SERIES_MID` |
| SC2 | tabs (frente) | NEG | 1 | `SERIES_MID` |
| SC2 | centro (verso) | POS | 2 | `PANEL_POS` |

Regra de verificacao: `SERIES_MID` tem de ligar o **verso(+) de uma celula** as **tabs(-) da outra** (um pad grande + um grupo de tabs), nunca dois pads do mesmo tipo.

## Alteracoes aplicadas

- Placement mantido (SC1 a 0 graus, SC2 a 180 graus, tabs para fora).
- SC2: numeracao de pads corrigida para a convencao da biblioteca (tabs=1, centro=2) e nets reatribuidas: tabs -> `SERIES_MID`, centro -> `PANEL_POS`.
- Contactos de verso (SC1 e SC2) movidos de `B.Cu` para `F.Cu` (face do componente).
- Esquematico mantido (ja estava fisicamente correto apos P0.1).
- Routing refeito em conformidade (`SERIES_MID` liga centro de SC1 as tabs inferiores de SC2; `PANEL_POS` liga o centro de SC2 a J1).

## Por confirmar antes de fabrico

- Esquema real de contacto de verso (bond condutivo vs wrap-around) e geometria das tabs: desenho de montagem CIC do fornecedor.
- Folga entre celulas suficiente para a interligacao real.
- DRC GUI/CLI limpo apos reroute (ver relatorios em `reports/`).
