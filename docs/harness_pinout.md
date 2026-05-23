# Pinout e verificacao do harness - ICARUS solar panel para EnduroSat EPS I

## Objetivo

Evitar ligacao invertida entre a PCB do painel solar e a entrada solar do EnduroSat EPS I.

## Pinout obrigatorio no lado EPS I

| Pino do conector solar EPS I | Funcao eletrica | Net ICARUS |
|---:|---|---|
| 1 | negativo | PANEL_NEG |
| 2 | negativo | PANEL_NEG |
| 3 | positivo | PANEL_POS |
| 4 | positivo | PANEL_POS |

Regra: pinos 1 e 2 do EPS sao sempre negativos; pinos 3 e 4 sao sempre positivos.

## Estado do PCB v0.2

A revisao inicial tinha o conector de saida marcado como placeholder. O pinout tem de ser verificado contra a orientacao real do conector, do cabo e do lado de encaixe no EPS.

Nenhum cabo deve ser ligado ao EPS antes de cumprir a tabela de continuidade abaixo.

## Procedimento de continuidade obrigatorio

Executar com multimetro em modo continuidade ou resistencia baixa, com o painel nao iluminado ou coberto.

| Teste | Ponto A | Ponto B | Resultado esperado |
|---:|---|---|---|
| 1 | EPS pin 1 | PCB PANEL_NEG | continuidade |
| 2 | EPS pin 2 | PCB PANEL_NEG | continuidade |
| 3 | EPS pin 3 | PCB PANEL_POS | continuidade |
| 4 | EPS pin 4 | PCB PANEL_POS | continuidade |
| 5 | EPS pin 1 | PCB PANEL_POS | aberto |
| 6 | EPS pin 2 | PCB PANEL_POS | aberto |
| 7 | EPS pin 3 | PCB PANEL_NEG | aberto |
| 8 | EPS pin 4 | PCB PANEL_NEG | aberto |
| 9 | PANEL_POS | PANEL_NEG | aberto, exceto leitura atraves das celulas sob iluminacao |

## Criterio de aceitacao

O harness so pode ser usado com o EPS se todos os testes acima forem cumpridos e registados.

Registo minimo:

- data;
- pessoa que mediu;
- foto do cabo ligado ao painel;
- foto do cabo do lado EPS;
- tabela de continuidade preenchida;
- polaridade medida com voltimetro em circuito aberto.

## Teste de polaridade antes de ligar ao EPS

Com o painel iluminado:

- ponta vermelha do multimetro em PANEL_POS;
- ponta preta em PANEL_NEG;
- a leitura deve ser positiva.

Se a leitura for negativa, parar. Nao ligar ao EPS.

## Nota sobre espelhamento do conector

A numeracao dos pinos depende da orientacao do conector, da vista usada no datasheet e da orientacao do cabo. Por isso, a silk screen e o esquematico nao substituem a medicao de continuidade no cabo real.
