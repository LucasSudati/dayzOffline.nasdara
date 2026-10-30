# Guias de bunkers e transmissões

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
- **Começou com traço (`—`):** o dígito é **6, 7, 8, 9 ou 0**. Contar os traços iniciais: **1 traço = 6; 2 traços = 7; 3 traços = 8; 4 traços = 9; 5 traços = 0**. Exemplos: `— — · · ·` = **7**; `— — — — ·` = **9**; `— — — — —` = **0**.
- **Reconhecimento antecipado:** quando o sinal muda de ponto para traço (ou vice-versa), o dígito já pode ser reconhecido. Se os cinco sinais forem iguais, **cinco pontos = 5** e **cinco traços = 0**. Mesmo reconhecendo cedo, aguardar o fim do grupo antes de começar o próximo número.
- **Na transmissão do bunker:** separar os dígitos pela pausa entre grupos e manter a ordem. Conferir a mensagem repetida, especialmente quando houver ruído ou dúvida entre ponto e traço.

**Exercícios para treinar em dupla (respostas abaixo):**

**Treino 1:** `· · — — — / — — · · ·` — resposta: ||27||

**Treino 2:** `— — — — — / · · · · ·` — resposta: ||05||

**Treino 3:** `— · · · · / · · · — — / — — — · · / · — — — —` — resposta: ||6381||

> O macete serve para **Morse numérico**. Ponto e traço não são bits de duração igual: além da ordem dos sinais, as pausas ajudam a distinguir os dígitos.

### Relação com o ciclo dia/noite — hipótese observacional

Foi estimado em jogo um ciclo de **3 h de dia e 40 min de noite (220 minutos reais)**. Essa duração **não foi verificada nos arquivos da missão nem no servidor oficial**. Se a janela 17:45–06:00 corresponder aproximadamente aos 40 minutos reais observados, isso seria uma relação aproximada, não uma conversão universal: os multiplicadores de tempo diurno e noturno podem ser diferentes.

### Loot das caixas do evento de bunker

**Esta lista se refere à caixa de recompensa do evento, não ao loot comum das salas do bunker.**

**Itens relatados pela comunidade (ainda não confirmados integralmente por scripts):**
- Uma das opções de estojo de arma: **GunCase_Black** (relatado com SCAR-H) ou **GunCase_White** (relatado com SV98).
- Para a opção SCAR-H, há relatos de coronha, mira MK4 bege, dois carregadores de 20 tiros, munição .308 e bateria de 9 V.
- Para a opção SV98, há relatos de mira MK4 verde, dois carregadores de 10 tiros, munição traçante 7,62×54R e bateria de 9 V.
- Também são relatados colete porta-placas com bolsos e coldre, óculos de visão noturna com suporte, capacete balístico desértico e baterias de 9 V.

**Confirmado no arquivo vanilla `cfgspawnabletypes.xml`:** os estojos têm chaves associadas como carga com chance `1.0`: `GunCase_Black` → `GunCaseKeys_Blue`; `GunCase_White` → `GunCaseKeys_Red`; `GunCase_Orange` → `GunCaseKeys_Orange`. **Isso não demonstra que os três estojos apareçam na caixa do evento**; comprova apenas as associações no arquivo.

**Fontes:** [configuração vanilla](https://github.com/LucasSudati/dayzOffline.nasdara/blob/main/cfgspawnabletypes.xml) e [relato comunitário sobre recompensas](https://dayzwiki101.com/nasdara-bunkers). O conjunto exato, as probabilidades e o script que preenche a caixa ainda precisam ser verificados.


**Verificação adicional (30/10/2026):** a busca no repositório público `BohemiaInteractive/DayZ-Central-Economy` por `BunkerReward`, `BunkerLoot` e `BunkerSupply` não retornou uma definição de tabela de recompensas. Nos XMLs examinados (`types.xml`, `mapgroupproto.xml` e `mapgrouppos.xml`), há registros de estojos/chaves e de construções de bunker, mas não uma lista explícita do conteúdo da caixa do evento. Os arquivos de posições e protótipos descrevem construções e pontos de loot, não comprovam os prêmios especiais. A identificação do script de geração da recompensa continua pendente.

### O que ainda não está confirmado

- Algoritmo exato de sorteio, repetição ou exclusão de bunkers entre lotes.
- Quando os códigos são gerados e se permanecem válidos após reinício do servidor.
- Se o comportamento de servidores oficiais coincide integralmente com a configuração pública.
- Semântica precisa de `lockCodeMode: 0`, de `bunkerRepetitions: 0` e das coordenadas de cada proxy.
- Relatos sobre conteúdo de caixas, duração de abertura e frequência do rádio ainda precisam de verificação independente nos scripts ou em testes reproduzíveis.

**Critério da pesquisa:** distinguir parâmetros oficiais, implementação consultável em scripts e relatos comunitários. Não publicar senhas atuais ou horários reais de rotação como se fossem determinísticos sem observação do servidor.

---

## Versão para Discord — seis mensagens independentes

Copiar apenas o texto de cada bloco, sem o título de separação. Cada mensagem tem menos de 2.000 caracteres. A versão técnica completa permanece acima.

### Mensagem 1

**DAYZ NASDARA | 1/6 — BUNKERS**
Configuração vanilla: 3 bunkers ativos por lote, senha de 4 dígitos, 23 posições de referência configuradas. Um lote dura 720 minutos do jogo (12 horas do jogo), e o relatório ocorre a cada 10 minutos do jogo. A transmissão funciona entre 17:45 e 06:00, no relógio do DayZ, não no horário de Brasília.
A janela dura 735 minutos do jogo, 15 a mais que o lote. Portanto, não há garantia de exatamente uma troca por noite nem de três bunkers diferentes a cada noite.
Fonte: cfgbunkerbroadcast.json (vanilla).

### Mensagem 2

**DAYZ NASDARA | 2/6 — RÁDIO**
Sinais Morse: ponto (·) = 0,5 s; traço (—) = 1,5 s; intervalo entre sinais = 0,4 s; intervalo maior = 2,2 s; pausa entre bunkers = 6 s.
A frequência 80,0 MHz e o formato de 6 dígitos de localização + 4 de senha são relatos da comunidade, não valores comprovados no JSON consultado.
Anota a sequência completa, incluindo zeros iniciais, e separa os blocos conforme as pausas.
Fonte: cfgbunkerbroadcast.json; relatos comunitários identificados no guia completo.

### Mensagem 3

**DAYZ NASDARA | 3/6 — MORSE NUMÉRICO**
0: — — — — —
1: · — — — —
2: · · — — —
3: · · · — —
4: · · · · —
5: · · · · ·
6: — · · · ·
7: — — · · ·
8: — — — · ·
9: — — — — ·
Cada número usa 5 sinais. Exemplo ilustrativo: 2047 = · · — — — / — — — — — / · · · · — / — — · · ·.
Os espaços e barras acima são apenas para facilitar a leitura.

### Mensagem 4

**DAYZ NASDARA | 4/6 — MACETE PARA DECODIFICAR**
Se o número começa com ponto (·), conta os pontos iniciais: 1 a 5 pontos correspondem aos números 1 a 5.
Se começa com traço (—), conta os traços iniciais: 1 traço = 6; 2 = 7; 3 = 8; 4 = 9; 5 = 0.
Exemplos: · · · — — = 3; — — — · · = 8; — — — — — = 0.
O número pode ser reconhecido quando o sinal muda, mas espera os cinco sinais terminarem para identificar o próximo.

### Mensagem 5

**DAYZ NASDARA | 5/6 — TREINO MORSE**
Tenta decodificar antes de abrir a resposta.
Treino 1: · · — — — / — — · · · → ||27||
Treino 2: — — — — — / · · · · · → ||05||
Treino 3: — · · · · / · · · — — / — — — · · / · — — — — → ||6381||
No Discord, os símbolos || ocultam a resposta até alguém clicar. A transmissão real exige ouvir também as pausas entre grupos.

### Mensagem 6

**DAYZ NASDARA | 6/6 — GUN CASE E RECOMPENSAS**
Nos arquivos vanilla, existem GunCase_Black, GunCase_White e GunCase_Orange. As chaves associadas são, respectivamente, azul, vermelha e laranja. O cfgspawnabletypes.xml atribui chance 1.0 (100%) à chave correspondente como carga da caixa; isso NÃO significa 100% de chance de ganhar a caixa no evento.
O types.xml registra nominal = 0 para as três GunCase: não há geração normal alvo pela economia central. Isso não impede que um evento ou script as crie.
Relatos ainda NÃO confirmados do evento: estojo preto com SCAR-H; estojo branco com SV98; miras MK4, carregadores, munições, baterias, colete, NVG e capacete.
SCAR-H tem nominal 2 e uso ContaminatedArea/Unique no types.xml; SV98 tem nominal 0 e categoria lootdispatch. Esses valores NÃO são probabilidades de recompensa.
**Chance de receber SCAR-H ou SV98 no evento: desconhecida.** Para calcular porcentagens, precisamos da tabela de pesos ou do script que escolhe as recompensas. types.xml, mapgroupproto.xml e mapgrouppos.xml não fornecem essa tabela.
