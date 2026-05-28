# Auditoria de cobre sob as celulas - ICARUS v0.6

Data: 2026-05-24

Estado: risco documentado para revisao interna. Nao aprovado para fabrico final nem para voo.

## Alteracao feita

- A rota principal `PANEL_NEG` em `B.Cu`, que antes passava pela lateral direita dentro da area nominal das celulas, foi desviada para o corredor exterior direito/inferior da PCB.
- As rotas dos test pads de `PANEL_NEG` e `PANEL_POS` foram desviadas pela margem esquerda/exterior para nao atravessarem a area nominal do corpo das celulas.

## Cobre que permanece sob a area nominal das celulas

| Net | Layer | Motivo | Estado |
|---|---|---|---|
| `SERIES_MID` | `B.Cu` | Interligacao dos pads de contacto da serie entre SC1 e SC2 | Pendente de validacao fisica |
| Pads de contacto das celulas | `F.Cu`/`B.Cu` conforme footprint | Contactos eletricos preliminares da celula | Pendente de drawing/celula real |

## Risco

- O footprint da celula ainda e preliminar.
- Nao esta validado se a face traseira/adesivo/isolamento da celula permite cobre mascarado sob a area ativa/corpo.
- Mesmo cobre coberto por solder mask pode ser insuficiente se houver abrasao, pressao mecanica, adesivo condutivo, tab desalinhada ou tolerancias de montagem.

## Validacao obrigatoria antes de fabrico

- Confirmar geometria real das tabs e contactos com drawing oficial ou celula real.
- Fazer dry-fit com celula real e material de fixacao previsto.
- Definir isolamento/adesivo compativel com a celula.
- Confirmar que nenhuma tab fica em contacto com cobre nao previsto.
- Rever se e necessario criar keepout absoluto de cobre sob a celula na proxima revisao.
