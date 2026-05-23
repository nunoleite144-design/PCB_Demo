# Release checklist v0.3 - ICARUS 1U solar panel PCB

Esta checklist define o minimo para considerar a PCB pronta para teste presencial com celulas reais. Nao significa flight-ready.

## Bloqueadores P0

- [ ] Pinout J1/harness confirmado: EPS pin 1 e 2 para PANEL_NEG.
- [ ] Pinout J1/harness confirmado: EPS pin 3 e 4 para PANEL_POS.
- [ ] PANEL_POS e PANEL_NEG sem curto antes de montar celulas.
- [ ] Polaridade das celulas reais confirmada por medicao.
- [ ] Serie 2S validada por medicao: Voc_total aproximadamente igual a Voc_SC1 + Voc_SC2.
- [ ] Teste com carga resistiva executado antes de ligar ao EPS.
- [ ] EPS ligado apenas depois de continuidade, polaridade e carga resistiva aprovadas.

## KiCad e layout

- [ ] Schematic presente e coerente com a PCB.
- [ ] ERC executado no KiCad.
- [ ] DRC executado no KiCad.
- [ ] Todos os warnings relevantes documentados.
- [ ] Footprint da celula validado com celula real.
- [ ] Conector de saida substituido por footprint real ou explicitamente marcado como placeholder.
- [ ] Silk screen identifica PV+, PV-, orientacao do cabo e configuracao 2S1P.
- [ ] Test pads acessiveis: PANEL_POS, PANEL_NEG, SERIES_MID e pontos por celula quando aplicavel.
- [ ] Se existir TVS, esta marcado como DNP por defeito.
- [ ] Configuracao 1S2P documentada como fallback, nao baseline.

## Mecanica

- [ ] Dimensoes da PCB confirmadas com estrutura 1U.
- [ ] Furos e keepouts validados.
- [ ] Celulas nao interferem com rails, parafusos ou estrutura.
- [ ] Harness tem raio de curvatura e alivio mecanico aceitaveis.
- [ ] Tabs das celulas nao ficam em esforco mecanico.

## Eletrico

- [ ] 2S1P documentado como baseline.
- [ ] Voc_2S = 5.40 V @ 25 C documentado.
- [ ] Vmp_2S = 4.74 V documentado.
- [ ] Imp aproximado = 0.54 A documentado.
- [ ] Pmp aproximado = 2.56 W documentado.
- [ ] Risco de Voc acima de 5.5 V abaixo de cerca de 16.7 C documentado.
- [ ] Over-voltage protection do EPS tratada como protecao, nao como modo nominal.

## Teste presencial

- [ ] Plano de teste impresso ou aberto no laptop.
- [ ] Tabela de continuidade preenchida.
- [ ] Fotos de montagem arquivadas.
- [ ] Medicoes de Voc por celula arquivadas.
- [ ] Medicoes do painel 2S arquivadas.
- [ ] Medicoes com carga arquivadas.
- [ ] Telemetria EPS arquivada se disponivel.
- [ ] Segunda pessoa reviu pinout antes da ligacao ao EPS.

## Criterio de release

A release v0.3 so pode ser marcada como pronta para teste presencial se todos os P0 estiverem cumpridos. Para fabricacao, todos os pontos de KiCad/layout, mecanica e eletrico devem estar cumpridos ou ter waiver documentado.
