# DayZ Nasdara — Consultas técnicas

Referência: arquivos de configuração da missão vanilla do mapa Nasdara. Os valores representam o repositório e não garantem comportamento idêntico em servidores oficiais ou modificados.

## Perguntas e respostas

**Versão do DayZ (estável, PC/Steam): 1.30.164200 — atualização de 21/10/2026.**  
**Versão experimental (PC/Steam): 1.30.164289 — atualização de 29/10/2026.**  
Fonte: [notas oficiais da versão estável](https://forums.dayz.com/topic/266388-dayz-update-130/) e [notas oficiais da versão experimental](https://dayz.com/article/game-update/1-30-experimental-release/dayz-steam-patch-notes).

| Categoria | Item | Pergunta | Resposta | Fonte |
|---|---|---|---|---|
| Armazenamento / persistência | Gun Case (preta, laranja e branca) | Quanto tempo permanece no chão, aberta ou trancada? | O `lifetime` configurado é **14.400 segundos (4 horas)** para `GunCase_Black`, `GunCase_Orange` e `GunCase_White`. O XML não distingue estado aberto/trancado, nem comprova exceção por enterramento; é preciso verificar o comportamento do item no código do jogo. Não considerar permanente como caixa de madeira. | [db/types.xml](db/types.xml) |
| Itens / persistência | Chaves da Gun Case | Qual é o tempo configurado? | `GunCaseKeys_Blue`, `GunCaseKeys_Orange` e `GunCaseKeys_Red` também têm `lifetime` de **14.400 segundos (4 horas)**. | [db/types.xml](db/types.xml) |
| Armas / loot | SCAR-H (SCAR-17) | Pode aparecer no Túnel contaminado do Nasdara? | `SCARH`: `usage=ContaminatedArea`, `value=Unique`, `nominal=2`, `min=1`, `restock=3600 s`, `lifetime=14400 s`. A configuração é compatível com a zona contaminada do Túnel, mas não garante spawn em um ponto específico. `SCARH_Black` tem `nominal=0` (sem spawn nominal próprio). | [db/types.xml](db/types.xml), [mapgrouppos.xml](mapgrouppos.xml), [mapgroupproto.xml](mapgroupproto.xml) |
| Armas / loot | SCAR-L (SCAR-16) | Pode aparecer no Túnel contaminado do Nasdara? | `SCARL`: `usage=Military` e `MilitaryWest`, `value=Tier3`, `nominal=4`, `min=2`, `restock=0`, `lifetime=14400 s`. Não possui `usage=ContaminatedArea`; a busca indicada é em pontos militares Tier 3, sem comprovação de spawn no Túnel. `SCARL_Black` tem `nominal=0` (sem spawn nominal próprio). | [db/types.xml](db/types.xml), [mapgrouppos.xml](mapgrouppos.xml), [mapgroupproto.xml](mapgroupproto.xml) |

> Nota: o `lifetime` é um valor de referência do sistema de economia; interações, atualizações de persistência e regras do servidor podem alterar o momento efetivo de limpeza.
