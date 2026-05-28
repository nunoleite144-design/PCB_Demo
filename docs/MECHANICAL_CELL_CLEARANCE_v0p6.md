# Folga mecanica das celulas - ICARUS v0.6

Data: 2026-05-24

Estado: revisao interna pre-validacao fisica. Nao aprovado para fabrico final nem para voo.

## Referencia usada

- PCB nominal: 95.0 mm x 95.0 mm.
- `Edge.Cuts`: X 57.5..152.5 mm, Y 57.5..152.5 mm.
- Corpo nominal de cada celula: 80.15 mm x 40.15 mm.
- Mounting holes: H1 (62.5, 62.5), H2 (147.5, 62.5), H3 (62.5, 147.5), H4 (147.5, 147.5).
- Diametro nominal dos furos M3: 3.2 mm.

## Posicao das celulas na v0.6

| Item | Centro (mm) | Bounding box X (mm) | Bounding box Y (mm) |
|---|---:|---:|---:|
| SC1 | (105.000, 84.425) | 64.925 .. 145.075 | 64.350 .. 104.500 |
| SC2 | (105.000, 125.575) | 64.925 .. 145.075 | 105.500 .. 145.650 |

## Folgas nominais

| Verificacao | Resultado |
|---|---:|
| Distancia centro-a-centro SC1/SC2 no eixo Y | 41.150 mm |
| Gap nominal entre corpos SC1/SC2 | 1.000 mm |
| Folga minima ao Edge.Cuts esquerdo/direito | 7.425 mm |
| Folga minima ao Edge.Cuts superior/inferior | 6.850 mm |
| Folga aproximada entre canto de corpo de celula e edge de furo M3 mais proximo | 1.45 mm |
| Folga entre corpo de SC2 e courtyard de J1 | 2.20 mm |

## Notas

- Os mounting holes nao foram movidos.
- O gap de 1.0 mm e uma folga nominal CAD, nao uma validacao mecanica real.
- A distancia aos furos considera corpo nominal da celula e furo de 3.2 mm; falta validar tolerancias, tabs, adesivo, rails e stack-up mecanico 1U.
- A posicao do conector `J1` continua pendente de validacao com a arquitetura mecanica final e harness real.
