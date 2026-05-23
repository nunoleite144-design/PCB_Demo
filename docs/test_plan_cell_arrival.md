# Plano de teste - chegada das celulas solares ICARUS

## Objetivo

Validar as celulas TJ3xSCA-4080 Tipo 32, a PCB do painel solar e a ligacao ao EPS I antes de testes presenciais.

## Regras de seguranca

- Usar tapete ESD, pulseira ESD e luvas.
- Medir sempre polaridade antes de ligar ao EPS.
- Nao ligar ao EPS se PANEL_POS e PANEL_NEG estiverem em curto.
- Manter o painel coberto ate ao momento da medicao.
- Interromper o teste se houver aquecimento anormal, reset do EPS ou comportamento intermitente.

## Equipamento minimo

- Dois multimetros.
- Fonte de bancada com limitacao de corrente.
- Carga eletronica ou resistencias de potencia.
- Para painel 2S, usar resistencia proxima de 8.8 ohm; por exemplo 8.2 ohm ou 9.1 ohm, pelo menos 10 W.
- Termometro ou camara termica simples.
- Tapete e pulseira ESD.
- Kapton ou isolamento equivalente.
- Lupa ou microscopio.
- Harness EPS etiquetado.
- Laptop para telemetria EPS via USB ou ESPS I, se disponivel.
- Material opaco para cobrir rapidamente as celulas.

## Fase 0 - PCB sem celulas

| Passo | Acao | Criterio de aceitacao |
|---:|---|---|
| 0.1 | Inspecao visual da PCB | sem danos ou curto evidente |
| 0.2 | Medir PANEL_POS para PANEL_NEG | circuito aberto |
| 0.3 | Medir J1/harness pin 1 e 2 para PANEL_NEG | continuidade |
| 0.4 | Medir J1/harness pin 3 e 4 para PANEL_POS | continuidade |
| 0.5 | Medir pinos cruzados | aberto |
| 0.6 | Confirmar footprint das celulas com celula real sem soldar | alinhamento mecanico aceitavel |

## Fase 1 - Incoming inspection das celulas

Para cada celula:

| Passo | Acao | Registar |
|---:|---|---|
| 1.1 | Fotografar frente, verso e abas | fotos |
| 1.2 | Inspecao visual | fissuras, tabs, delaminacao, sujidade |
| 1.3 | Medir dimensoes principais | comprimento, largura, posicao das abas |
| 1.4 | Medir Voc sob iluminacao disponivel | tensao, temperatura, condicao de luz |
| 1.5 | Confirmar polaridade | marcacao fisica da celula |

Nota: em laboratorio terrestre, os valores nao vao igualar AM0. O objetivo e confirmar polaridade, integridade e consistencia relativa entre celulas.

## Fase 2 - Montagem parcial

| Passo | Acao | Criterio de aceitacao |
|---:|---|---|
| 2.1 | Dry-fit de SC1 | tabs chegam aos pads sem esforco mecanico |
| 2.2 | Fixar/soldar SC1 conforme processo definido | sem dano visivel |
| 2.3 | Medir Voc de SC1 | positivo e plausivel |
| 2.4 | Dry-fit de SC2 | tabs chegam aos pads sem esforco mecanico |
| 2.5 | Fixar/soldar SC2 | sem dano visivel |
| 2.6 | Medir Voc de SC2 | positivo e plausivel |
| 2.7 | Confirmar serie 2S | Voc_total aproximadamente soma das duas Voc individuais |

## Fase 3 - Teste com carga resistiva

Resistencia alvo aproximada:

Rmp = Vmp / Imp = 4.74 V / 0.54 A ~= 8.8 ohm

Usar 8.2 ohm ou 9.1 ohm com potencia igual ou superior a 10 W.

| Passo | Acao | Registar |
|---:|---|---|
| 3.1 | Painel iluminado, sem carga | Voc_total e temperatura |
| 3.2 | Ligar carga resistiva | V_load, I_load, P_load |
| 3.3 | Manter alguns minutos | estabilidade e temperatura |
| 3.4 | Mexer ligeiramente no harness sem forcar | ausencia de intermitencias |
| 3.5 | Cobrir parcialmente painel | resposta eletrica observada |

Criterios de aceitacao: sem aquecimento anormal, sem queda brusca de tensao, sem mau contacto, polaridade correta e potencia plausivel para a iluminacao disponivel.

## Fase 4 - Ligacao ao EPS I

Executar apenas depois das fases 0 a 3 estarem aprovadas.

| Passo | Acao | Criterio |
|---:|---|---|
| 4.1 | EPS em bancada e estado conhecido | sem falhas ativas |
| 4.2 | Painel coberto | sem geracao significativa |
| 4.3 | Confirmar continuidade do harness novamente | tabela aprovada |
| 4.4 | Ligar um unico painel a um unico conector solar | sem reset ou falha |
| 4.5 | Descobrir gradualmente o painel | telemetria solar plausivel |
| 4.6 | Ler tensao e corrente solar no EPS | valores coerentes com medicao externa |
| 4.7 | Registar comportamento do MPPT | sem ciclos anormais persistentes |

## Condicoes de paragem

Parar se ocorrer polaridade invertida, curto entre PANEL_POS e PANEL_NEG, leitura negativa com ponta vermelha em PANEL_POS, aquecimento anormal, reset do EPS, entrada repetida em protecao, corrente inesperadamente alta ou dano visual nas celulas.

## Dados a arquivar

Guardar em `test_logs/`: fotos da montagem, tabela de continuidade, medicoes de Voc por celula, medicoes do painel 2S, medicoes com carga, telemetria EPS se disponivel e conclusao do teste.
