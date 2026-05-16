# Notas de Revisão Técnica — PCB do Painel Solar 1U

Este documento apoia a revisão interna do desenho preliminar da PCB do painel solar 1U do projeto ICARUS.

## Opções de desenho consideradas
- Duas células TJ3xSCA-4080 / TJ30SCA-4080 CIC por face.
- Abas de interligação orientadas para o exterior da placa.
- Associação elétrica das células numa string 2S.
- Região dedicada à interface de saída para o EPS.

## Pontos a validar antes do fecho do desenho
1. Conector e pinout finais.
2. Polaridade e geometria dos contactos das células.
3. Compatibilidade mecânica com a estrutura do CubeSat.
4. Margem de tensão do EPS para a configuração 2S.
5. Verificações finais de ERC, DRC e integração mecânica.

A versão atual deve ser tratada como base de revisão técnica e não como versão final para fabrico.
