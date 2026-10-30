# DayZ Nasdara — Consultas técnicas

Referência: arquivos de configuração da missão vanilla do mapa Nasdara. Os valores representam o repositório e não garantem comportamento idêntico em servidores oficiais ou modificados.

## Perguntas e respostas

**Versão do DayZ (estável, PC/Steam): 1.30.164200 — atualização de 21/10/2026.**  
**Versão experimental (PC/Steam): 1.30.164289 — atualização de 29/10/2026.**  
Fonte: [notas oficiais da versão estável](https://forums.dayz.com/index.php) e [notas oficiais da versão experimental](https://dayz.com/article/game-update/1-30-experimental-release/dayz-steam-patch-notes).

| Categoria | Item | Pergunta | Resposta | Fonte |
|---|---|---|---|---|
| Armazenamento / persistência | Gun Case (preta, laranja e branca) | Quanto tempo permanece no chão, aberta ou trancada? | O `lifetime` configurado é **14.400 segundos (4 horas)** para `GunCase_Black`, `GunCase_Orange` e `GunCase_White`. O XML não distingue estado aberto/trancado, nem comprova exceção por enterramento; é preciso verificar o comportamento do item no código do jogo. Não considerar permanente como caixa de madeira. | [db/types.xml](db/types.xml) |
| Itens / persistência | Chaves da Gun Case | Qual é o tempo configurado? | `GunCaseKeys_Blue`, `GunCaseKeys_Orange` e `GunCaseKeys_Red` também têm `lifetime` de **14.400 segundos (4 horas)**. | [db/types.xml](db/types.xml) |
| Armas / loot | SCAR-H (SCAR-17) | Pode aparecer no Túnel contaminado do Nasdara? | `SCARH`: `usage=ContaminatedArea`, `value=Unique`, `nominal=2`, `min=1`, `restock=3600 s`, `lifetime=14400 s`. A configuração é compatível com a zona contaminada do Túnel, mas não garante spawn em um ponto específico. `SCARH_Black` tem `nominal=0` (sem spawn nominal próprio). | [db/types.xml](db/types.xml), [mapgrouppos.xml](mapgrouppos.xml), [mapgroupproto.xml](mapgroupproto.xml) |
| Armas / loot | SCAR-L (SCAR-16) | Pode aparecer no Túnel contaminado do Nasdara? | `SCARL`: `usage=Military` e `MilitaryWest`, `value=Tier3`, `nominal=4`, `min=2`, `restock=0`, `lifetime=14400 s`. Não possui `usage=ContaminatedArea`; a busca indicada é em pontos militares Tier 3, sem comprovação de spawn no Túnel. `SCARL_Black` tem `nominal=0` (sem spawn nominal próprio). | [db/types.xml](db/types.xml), [mapgrouppos.xml](mapgrouppos.xml), [mapgroupproto.xml](mapgroupproto.xml) |

> Nota: o `lifetime` é um valor de referência do sistema de economia; interações, atualizações de persistência e regras do servidor podem alterar o momento efetivo de limpeza.

## Bunkers — transmissões, senhas e rotação (investigação em 30/10/2026)

A missão Nasdara habilita o sistema de rádio dos bunkers com `WorldsData.bunkerBroadcastEnabled: true` em [`cfggameplay.json`](cfggameplay.json). Os parâmetros abaixo vêm de [`cfgbunkerbroadcast.json`](cfgbunkerbroadcast.json); são **configurações**, não uma confirmação do algoritmo executado no servidor oficial.

| Parâmetro | Valor | Interpretação segura |
|---|---|---|
| `bunkerSettings.numberOfActive` | `3` | Quantidade configurada de bunkers ativos. Não identifica quais são. |
| `bunkerSettings.lockCodeLength` | `4` | Comprimento configurado do código de fechadura. |
| `bunkerSettings.lockCodeMode` | `0` | Modo numérico cuja semântica não está definida neste JSON. |
| `schedulerSettings.batchLifetimeLength` | `720` | Duração de um lote; **unidade e efeito sobre a seleção/códigos ainda não confirmados**. |
| `schedulerSettings.reportInterval` | `10` | Intervalo de relatório; unidade não explicitada no JSON. |
| `schedulerSettings.periods` | `1745–0600` | Janela de horário configurada; o arquivo não define por si só fuso, referência de relógio ou instante da rotação. |
| `playbackSettings.dotDuration` / `dashDuration` | `0.5` / `1.5` | Durações configuradas para ponto e traço. |
| `playbackSettings.symbolGapDuration` / `wordGapDuration` | `0.4` / `2.2` | Intervalos entre símbolos e palavras. |
| `playbackSettings.pauseBetweenBunkers` | `6.0` | Pausa configurada entre mensagens de bunkers. |
| `playbackSettings.messageRepetitions` / `bunkerRepetitions` | `1` / `0` | Valores brutos; a interpretação operacional de `0` depende da implementação. |
| `proxies` | `23` posições | Coordenadas de proxies; **não** equivalem necessariamente a 23 bunkers. |

**Hipótese a testar — relação com o ciclo dia/noite.** Foi relatada, como estimativa de observação no jogo, uma duração de aproximadamente **3 horas de dia e 40 minutos de noite**, totalizando **3 h 40 min (220 minutos reais)** por ciclo. Não há, nos arquivos acima, comprovação desses tempos nem de que `batchLifetimeLength: 720` corresponda a um dia do jogo. Se `720` estiver em minutos, seriam **12 horas**; se estiver em outra unidade, o resultado muda. Portanto, **não concluir** que a seleção de três bunkers ou suas senhas seja renovada a cada noite.

**Questões em aberto:** como os três bunkers são escolhidos (aleatoriamente ou em rotação fixa); quando a seleção é renovada; se os códigos mudam junto com o lote; o significado de `lockCodeMode: 0`; e a unidade dos parâmetros do agendador. Para responder, é necessário localizar os scripts que consomem essas configurações ou registrar transmissões em múltiplos ciclos no servidor oficial.

