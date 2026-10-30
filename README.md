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

## Bunkers — transmissões, senhas e rotação (pesquisa em 30/10/2026)

O sistema é habilitado por `WorldsData.bunkerBroadcastEnabled: true` em [`cfggameplay.json`](cfggameplay.json). Os valores vanilla estão em [`cfgbunkerbroadcast.json`](cfgbunkerbroadcast.json), comparáveis à [configuração publicada pela Bohemia](https://github.com/BohemiaInteractive/DayZ-Central-Economy/blob/master/dayzOffline.nasdara/cfgbunkerbroadcast.json).

| Parâmetro | Valor | Significado e evidência |
|---|---|---|
| `numberOfActive` | `3` | Quantidade configurada de bunkers selecionados por lote. |
| `lockCodeLength` | `4` | Código de quatro caracteres. |
| `lockCodeMode` | `0` | Modo configurado; a interpretação exata deve ser conferida no script de fechaduras. |
| `batchLifetimeLength` | `720` | **720 minutos do jogo = 12 horas do jogo**. O código do agendador acumula a variação do relógio do mundo. |
| `reportInterval` | `10` | **10 minutos do jogo** entre tentativas de relatório. |
| `periods` | `17:45–06:00` | Janela do relógio **do jogo**, atravessando a meia-noite. Não é o horário de Brasília. |
| `dotDuration` / `dashDuration` | `0.5` / `1.5` | Duração configurada dos sinais de Morse em segundos. |
| `symbolGapDuration` / `wordGapDuration` | `0.4` / `2.2` | Intervalos configurados em segundos. |
| `pauseBetweenBunkers` | `6.0` | Pausa de seis segundos entre bunkers. |
| `messageRepetitions` / `bunkerRepetitions` | `1` / `0` | Valores configurados; sem assumir o significado especial do zero sem examinar a reprodução. |
| `proxies` | `23` posições | Pontos configurados para o sistema; não tratar cada proxy automaticamente como entrada física sem cruzar o mapa. |

### O que o código do agendador confirma

A classe [`BunkerBroadcastSchedulerSettings`](https://dayz.kgb.rest/d1/d14/_cfg_bunker_broadcast_json_data_8c_source.html) documenta explicitamente `batchLifetimeLength` e `reportInterval` em **minutos**. O método [`BunkerBroadcastScheduler::OnUpdate`](https://dayz.kgb.rest/d0/d92/class_bunker_broadcast_scheduler_a3a1ec085ac292888bcb66481bdd44caa.html) acumula minutos decorridos no relógio do mundo, seleciona novos bunkers quando o lote atinge 720 minutos e, **fora da janela ativa**, redefine `m_BatchSelected = false`. Durante a janela ativa, se não houver lote selecionado, o agendador tenta uma nova seleção após acumular cinco minutos do jogo. Isso sustenta uma **nova seleção possível ao começar outra noite**, mas não garante que o trio seja diferente do anterior ou que a troca aconteça exatamente às 17:45.

A janela 17:45–06:00 tem **12 h 15 min do jogo (735 minutos)**, ligeiramente mais longa que os 720 minutos do lote. Por isso, **não é correto afirmar que exista exatamente uma única seleção por noite** sem analisar o fluxo completo do agendador e suas condições de reinício.

### Recepção e decodificação — informações da comunidade

Guias recentes de jogadores relatam a frequência **80,0 MHz** (rádio pessoal ou rádio de base) e mensagens contendo **seis dígitos de referência de mapa seguidos de quatro dígitos de senha**. A frequência e o formato detalhado não aparecem no JSON e devem ser tratados como informações comunitárias, não como parâmetros comprovados por esse arquivo. Referências: [Skynet — Nasdara bunker finder](https://www.killfeed.com/nasdara/bunkers) e [guia de bunkers](https://dayzwiki101.com/nasdara-bunkers).

Para decodificar os números, usar Morse numérico: `0 -----`, `1 .----`, `2 ..---`, `3 ...--`, `4 ....-`, `5 .....`, `6 -....`, `7 --...`, `8 ---..`, `9 ----.`. Registrar separadamente a referência do mapa e a senha; zeros à esquerda são significativos. Ferramentas comunitárias podem ajudar, mas a leitura deve ser conferida por repetição do sinal.

### Relação com o ciclo dia/noite — hipótese observacional

Foi estimado em jogo um ciclo de **3 h de dia e 40 min de noite (220 minutos reais)**. Essa duração **não foi verificada nos arquivos da missão nem no servidor oficial**. Se a janela 17:45–06:00 corresponder aproximadamente aos 40 minutos reais observados, isso seria uma relação aproximada, não uma conversão universal: os multiplicadores de tempo diurno e noturno podem ser diferentes.

### O que ainda não está confirmado

- Algoritmo exato de sorteio, repetição ou exclusão de bunkers entre lotes.
- Quando os códigos são gerados e se permanecem válidos após reinício do servidor.
- Se o comportamento de servidores oficiais coincide integralmente com a configuração pública.
- Semântica precisa de `lockCodeMode: 0`, de `bunkerRepetitions: 0` e das coordenadas de cada proxy.
- Relatos sobre conteúdo de caixas, duração de abertura e frequência do rádio ainda precisam de verificação independente nos scripts ou em testes reproduzíveis.

**Critério da pesquisa:** distinguir parâmetros oficiais, implementação consultável em scripts e relatos comunitários. Não publicar senhas atuais ou horários reais de rotação como se fossem determinísticos sem observação do servidor.
