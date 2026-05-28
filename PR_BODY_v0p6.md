## Resumo

ICARUS 1U Solar Panel PCB v0.6 — revisão interna. Esta branch fecha o trabalho técnico pré-CDR: topologia 2S corrigida, validação contra datasheets oficiais, análise térmica de sobretensão, decisão de design para v0.7, e DRC parity limpo no KiCad 10 (Windows).

## Mudanças principais

- **Correção crítica da topologia anti-série** SC1/SC2 — ver [`docs/CORRECAO_TOPOLOGIA_SERIE_v0p6.md`](docs/CORRECAO_TOPOLOGIA_SERIE_v0p6.md). Os dois contactos de verso estavam ambos em `SERIES_MID`, dando saída ≈ 0 V (anti-série). Renumeração de pads + move de `B.Cu` para `F.Cu` + reroute completo.
- **Validação contra datasheets oficiais** TJ30SCA-4080V4 e EPS I User Manual — ver [`docs/VALIDACAO_DATASHEETS_v0p6.md`](docs/VALIDACAO_DATASHEETS_v0p6.md). Parâmetros eléctricos e conector confirmados.
- **Análise quantitativa do risco `Voc_2S > 5.5 V` em frio** — ver [`docs/ANALISE_TERMICA_VOC_v0p6.md`](docs/ANALISE_TERMICA_VOC_v0p6.md). Pior caso BOL pós-eclipse: 6.18–6.26 V vs limite EPS de 5.5 V.
- **Decisão de mitigação para v0.7** — ver [`docs/DECISAO_TVS_v0p7.md`](docs/DECISAO_TVS_v0p7.md). Adoptar clamp activo TL431+BSS138 (precisão ±1 %) substituindo `D1`. TVS single-part comercial não cobre a janela [4.8, 5.4] V (investigação documentada).
- **Higiene de repo**: dead nets removidos, footprint dirs unificados sob `hardware/footprints/`, .gitignore para artefactos locais do KiCad, relatórios obsoletos do macOS apagados.

## Estado dos relatórios (KiCad 10.0 Windows)

- **ERC CLI**: 0 erros, 3 warnings (libs locais não resolvidas no contexto CLI — waiver formal em `test_logs/LIBRARY_WARNINGS_WAIVER_v0p6.md`).
- **DRC CLI parity**: 0 erros, 0 desconectados, 0 paridade esquemático↔PCB, 13 warnings (3x lib + 10x `lib_footprint_mismatch` — waived).
- **SVGs**: `reports/v0p6_top.svg`, `reports/v0p6_bottom.svg`, `reports/v0p6_fab.svg`.

## Acções bloqueantes para CDR (fora desta PR)

- [ ] Resposta da EnduroSat sobre natureza do limite 5.5 V (operating vs absolute max) — **opcional**; decisão de avançar com clamp em v0.7 não espera por esta resposta.
- [ ] Desenho de montagem CIC da AzurSpace (footprint da célula final).
- [ ] Drawing oficial Molex 53398-0471 (footprint final do conector).
- [ ] Validação mecânica 1U com células reais.
- [ ] Continuidade pino-a-pino do harness real.
- [ ] Revisão por segunda pessoa (Porto Space hardware lead).

## Próxima revisão (v0.7)

- Popular `D1` com clamp activo (TL431 + BSS138 + 3 resistores) conforme `docs/DECISAO_TVS_v0p7.md`.
- Re-layout local em torno de (120, 138).
- Re-validação ERC/DRC.

🤖 Generated with [Claude Code](https://claude.com/claude-code)
