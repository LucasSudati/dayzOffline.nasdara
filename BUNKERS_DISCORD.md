# Guia de bunkers e transmissões — versão para Discord

## Bunkers — transmissões, senhas e rotação (pesquisa em 30/10/2026)

O sistema é habilitado por `WorldsData.bunkerBroadcastEnabled: true` em [`cfggameplay.json`](https://github.com/LucasSudati/dayzOffline.nasdara/blob/main/cfggameplay.json). Os valores vanilla estão em [`cfgbunkerbroadcast.json`](https://github.com/LucasSudati/dayzOffline.nasdara/blob/main/cfgbunkerbroadcast.json), comparáveis à [configuração publicada pela Bohemia](https://github.com/BohemiaInteractive/DayZ-Central-Economy/blob/master/dayzOffline.nasdara/cfgbunkerbroadcast.json).

**Parâmetros da configuração:**
- 3 bunkers ativos por lote (`numberOfActive`).
- Senhas de 4 dígitos (`lockCodeLength`).
- Lote de 720 minutos do jogo; relatório a cada 10 minutos do jogo.
- Janela de transmissão das 17:45 às 06:00, horário do jogo.
- 23 proxies configurados.
- Ponto: 0,5 s; traço: 1,5 s; intervalo entre símbolos: 0,4 s; intervalo maior: 2,2 s; pausa entre bunkers: 6 s.
- `lockCodeMode = 0`, `messageRepetitions = 1`, `bunkerRepetitions = 0`; significados especiais ainda não confirmados.

### O que o código do agendador confirma

A classe [`BunkerBroadcastSchedulerSettings`](https://dayz.kgb.rest/d1/d14/_cfg_bunker_broadcast_json_data_8c_source.html) documenta explicitamente `batchLifetimeLength` e `reportInterval` em **minutos**. O método [`BunkerBroadcastScheduler::OnUpdate`](https://dayz.kgb.rest/d0/d92/class_bunker_broadcast_scheduler_a3a1ec085ac292888bcb66481bdd44caa.html) acumula minutos decorridos no relógio do mundo, seleciona novos bunkers quando o lote atinge 720 minutos e, **fora da janela ativa**, redefine `m_BatchSelected = false`. Durante a janela ativa, se não houver lote selecionado, o agendador tenta uma nova seleção após acumular cinco minutos do jogo. Isso sustenta uma **nova seleção possível ao começar outra noite**, mas não garante que o trio seja diferente do anterior ou que a troca aconteça exatamente às 17:45.

A janela 17:45–06:00 tem **12 h 15 min do jogo (735 minutos)**, ligeiramente mais longa que os 720 minutos do lote. Por isso, **não é correto afirmar que exista exatamente uma única seleção por noite** sem analisar o fluxo completo do agendador e suas condições de reinício.

### Recepção e decodificação — informações da comunidade

Guias recentes de jogadores relatam a frequência **80,0 MHz** (rádio pessoal ou rádio de base) e mensagens contendo **seis dígitos de referência de mapa seguidos de quatro dígitos de senha**. A frequência e o formato detalhado não aparecem no JSON e devem ser tratados como informações comunitárias, não como parâmetros comprovados por esse arquivo. Referências: [Skynet — Nasdara bunker finder](https://www.killfeed.com/nasdara/bunkers) e [guia de bunkers](https://dayzwiki101.com/nasdara-bunkers).

Para decodificar, cada **dígito numérico** é representado por **cinco sinais**. Na tabela, `·` é um sinal curto (**ponto**) e `—` é um sinal longo (**traço**). Os espaços entre grupos de cinco sinais foram acrescentados apenas para facilitar a leitura.

**Morse numérico (espaços entre todos os sinais):**

**0**: `— — — — —`
**1**: `· — — — —`
**2**: `· · — — —`
**3**: `· · · — —`
**4**: `· · · · —`
**5**: `· · · · ·`
**6**: `— · · · ·`
**7**: `— — · · ·`
**8**: `— — — · ·`
**9**: `— — — — ·`

**Exemplo ilustrativo (não é uma senha real):** código `2047` → `· · — — — / — — — — — / · · · · — / — — · · ·`. Para interpretar uma transmissão, identificar primeiro os grupos de cinco sinais, converter cada grupo em um dígito e preservar a ordem recebida.

**Atenção:** segundo os relatos comunitários citados acima, a mensagem contém uma referência de mapa de **seis dígitos** e uma senha de **quatro dígitos**. Registrar essas partes separadamente, sem descartar zeros à esquerda. Os espaços mostrados na tabela e no exemplo são uma convenção visual; não substituem a identificação das pausas reais do áudio.

### Decodificação rápida pelo ouvido — macete para jogar em grupo

Para identificar os números **sem anotar todos os sinais nem usar aplicativo**, ouvir o **primeiro sinal** e contar a sequência inicial. Cada número tem cinco sinais; os espaços abaixo são apenas visuais.

- **Começou com ponto (`·`):** o dígito é de **1 a 5**. Contar os pontos iniciais: `· · · — —` = **3**; `· · · · ·` = **5**.
- **Começou com traço (`—`):** o dígito é **6, 7, 8, 9 ou 0**. Contar os traços iniciais e subtrair de 10: `— — · · ·` = **8** (10 − 2); `— — — — ·` = **6** (10 − 4); `— — — — —` = **0**.
- **Reconhecimento antecipado:** quando o sinal muda de ponto para traço (ou vice-versa), o dígito já pode ser reconhecido. Se os cinco sinais forem iguais, **cinco pontos = 5** e **cinco traços = 0**. Mesmo reconhecendo cedo, aguardar o fim do grupo antes de começar o próximo número.
- **Na transmissão do bunker:** separar os dígitos pela pausa entre grupos e manter a ordem. Conferir a mensagem repetida, especialmente quando houver ruído ou dúvida entre ponto e traço.

**Exercícios para treinar em dupla (respostas abaixo):**

**Treino 1:** `· · — — — / — — · · ·` — resposta: ||28||

**Treino 2:** `— — — — — / · · · · ·` — resposta: ||05||

**Treino 3:** `— · · · · / · · · — — / — — — · · / · — — — —` — resposta: ||9371||

> O macete serve para **Morse numérico**. Ponto e traço não são bits de duração igual: além da ordem dos sinais, as pausas ajudam a distinguir os dígitos.

### Relação com o ciclo dia/noite — hipótese observacional

Foi estimado em jogo um ciclo de **3 h de dia e 40 min de noite (220 minutos reais)**. Essa duração **não foi verificada nos arquivos da missão nem no servidor oficial**. Se a janela 17:45–06:00 corresponder aproximadamente aos 40 minutos reais observados, isso seria uma relação aproximada, não uma conversão universal: os multiplicadores de tempo diurno e noturno podem ser diferentes.

### Loot das caixas do evento de bunker — levantamento pendente

Esta seção se refere **exclusivamente às caixas geradas pelo evento de bunker**, não ao loot militar comum encontrado no interior do bunker.

Os arquivos `types.xml`, `mapgroupproto.xml` e `mapgrouppos.xml` não bastam, isoladamente, para demonstrar a lista exata de itens possíveis dentro da caixa do evento. É necessário localizar a configuração ou o script que popula esse contêiner (ou registrar resultados de testes reproduzíveis). Por isso, **nenhuma lista de itens está sendo apresentada como confirmada** neste momento.

### O que ainda não está confirmado

- Algoritmo exato de sorteio, repetição ou exclusão de bunkers entre lotes.
- Quando os códigos são gerados e se permanecem válidos após reinício do servidor.
- Se o comportamento de servidores oficiais coincide integralmente com a configuração pública.
- Semântica precisa de `lockCodeMode: 0`, de `bunkerRepetitions: 0` e das coordenadas de cada proxy.
- Relatos sobre conteúdo de caixas, duração de abertura e frequência do rádio ainda precisam de verificação independente nos scripts ou em testes reproduzíveis.

**Critério da pesquisa:** distinguir parâmetros oficiais, implementação consultável em scripts e relatos comunitários. Não publicar senhas atuais ou horários reais de rotação como se fossem determinísticos sem observação do servidor.
