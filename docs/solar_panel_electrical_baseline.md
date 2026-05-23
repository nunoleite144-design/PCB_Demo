# Baseline eletrico - painel solar ICARUS 1U

## Ambito

Este documento fixa a baseline eletrica para a PCB do painel solar 1U do CubeSat ICARUS e serve como referencia para layout, harness e testes de bancada.

## Componentes assumidos

Celula solar de referencia: TJ3xSCA-4080 Tipo 32.

| Parametro | Simbolo | Valor |
|---|---:|---:|
| Tensao em circuito aberto | Voc_cell | 2.70 V @ 25 C |
| Tensao no ponto de maxima potencia | Vmp_cell | 2.37 V @ 25 C |
| Densidade de corrente em curto-circuito | Jsc | 18.7 mA/cm2 |
| Densidade de corrente no ponto de maxima potencia | Jm | 17.9 mA/cm2 |
| Area ativa | A | 30.18 cm2 |
| Coeficiente termico de Voc | dVoc/dT | -6.0 mV/C |

EPS de referencia: EnduroSat EPS I.

Interface solar relevante:

| Caracteristica | Valor |
|---|---:|
| Canais solares | 3 |
| Conectores solares | 6 |
| Conversor por canal | MPPT step-up |
| Tensao maxima por canal | 5.5 V |
| Corrente maxima por canal | 1.8 A |
| Diodo de bloqueio | Integrado por conector solar |
| Protecao de sobretensao | Acima de 5.5 V |

## Configuracao baseline

A baseline recomendada e 2S1P: SC1 em serie com SC2. A corrente do painel e aproximadamente a corrente de uma celula; a tensao do painel e a soma das tensoes das duas celulas.

## Calculos a 25 C

Corrente no ponto de maxima potencia:

Imp = Jm x A = 17.9 mA/cm2 x 30.18 cm2 = 540.2 mA ~= 0.54 A

Potencia de uma celula:

Pmp_cell = Vmp_cell x Imp = 2.37 V x 0.540 A ~= 1.28 W

Para duas celulas em serie:

Voc_2S = 2 x 2.70 V = 5.40 V
Vmp_2S = 2 x 2.37 V = 4.74 V
Imp_2S ~= 0.54 A
Pmp_2S = 4.74 V x 0.540 A ~= 2.56 W

## Compatibilidade com EPS I

| Grandeza | Painel 2S1P ICARUS | EPS I tipico / limite |
|---|---:|---:|
| Tensao em MPP | ~= 4.74 V | tipico ~= 4.66 V |
| Corrente em MPP | ~= 0.54 A | tipico ~= 0.517 A; limite 1.8 A |
| Potencia em MPP | ~= 2.56 W | tipico ~= 2.6 W |
| Voc @ 25 C | 5.40 V | limite 5.5 V |

Conclusao: 2S1P e compativel como baseline de operacao MPPT.

## Risco de circuito aberto em frio

O coeficiente termico de Voc e negativo, logo Voc aumenta quando a temperatura desce.

Para duas celulas em serie:

dVoc_2S/dT = 2 x (-6.0 mV/C) = -12.0 mV/C

Modelo linear local:

Voc_2S(T) = 5.40 V + 0.012 V/C x (25 C - T)

Temperatura em que se atinge 5.5 V:

5.50 = 5.40 + 0.012 x (25 - T)
0.10 = 0.012 x (25 - T)
25 - T = 8.33
T ~= 16.7 C

Conclusao: em circuito aberto, abaixo de aproximadamente 16.7 C, o painel 2S pode ultrapassar 5.5 V.

Isto nao deve ser assumido como destrutivo porque o EPS I tem protecao de sobretensao. No entanto, nao deve ser usado como modo nominal, porque a atuacao da protecao pode impedir a colheita de energia nesse canal.

## Fallback

A configuracao 1S2P e eletricamente possivel, mas deve ser tratada como fallback, nao como baseline.

Vantagens de 1S2P: Voc menor e menor risco de sobretensao em frio.

Desvantagens de 1S2P: Vmp ~= 2.37 V, mais afastado da entrada solar tipica do EPS; corrente aproximadamente duplicada; maior dependencia do comportamento do MPPT boost a tensao mais baixa.

## Decisao tecnica atual

- Baseline: 2S1P.
- Fallback: 1S2P, so se teste termico/EPS rejeitar 2S1P.
- Nao montar TVS por defeito.
- Manter footprint TVS opcional como DNP, se incluido no layout.
- Confirmar pinout por continuidade antes de ligar ao EPS.
