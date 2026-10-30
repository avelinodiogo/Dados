# Orbe · Seu universo de estudos

Site estático para seguir um ciclo de estudos, manter um cronograma semanal fixo, cronometrar horas líquidas, agendar revisões e registrar notas. Não precisa instalar dependências nem compilar.

## Publicar

O site é estático: na Vercel, publique a pasta inteira, sem comando de build (não há `package.json` nem dependências). Arquivos do site:

- `index.html`, `login.html`, `manifest.webmanifest`, `sw.js`
- `firebase.js` (o projeto do Firebase), `cloud.js` (a nuvem do app) e `login.js` (a tela de entrada)
- `study-core.js`, `app.js`, `tracker.js`, `reviews.js`, `colors.js`, `progress.js`, `cycle-model.js`, `cycle.js`, `sound.js`, `focus.js`, `welcome.js`, `today.js`, `team.js`, `appearance.js`, `guide.js`, `simulator.js`, `space.js`
- `tracker.css`, `reviews.css`, `progress.css`, `cycle.css`, `theme.css`
- a pasta `fonts/` (a fonte D-DIN, guardada no próprio site)
- `orbe-mark.svg`, `favicon.ico`, `favicon-32.png`, `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png`
- a pasta `tests/` (opcional)

`firestore.rules` não precisa ir para o site: é o texto das regras que se cola no console do Firebase (veja abaixo). Desde a v39, **`middleware.js` saiu**: apague-o da hospedagem, senão a Vercel continua pedindo o login antigo.

A fonte é a **D-DIN**, a mesma da spacex.com, em regular e negrito (`fonts/d-din-regular.woff` e `fonts/d-din-bold.woff`, cerca de 27 KB cada, no formato compactado WOFF). Ela é da Datto, com licença SIL Open Font License 1.1, que permite usar e distribuir; o aviso da licença vem dentro dos próprios arquivos da fonte. Fica no site, então o visual é o mesmo com ou sem internet.

## Login e nuvem (Firebase)

Desde a v39, quem entra e onde ficam os dados é o **Firebase** (projeto `orbe-e513f`), no plano gratuito Spark:
- **Authentication:** cada pessoa entra com **e-mail e senha**, e o e-mail precisa ser confirmado por um link.
- **Firestore:** cada pessoa tem o próprio espaço na nuvem. Os registros vão para lá 2 segundos depois de feitos e chegam aos outros aparelhos na hora.
- **Só convidados:** só entra quem tem o e-mail na lista de **Convidados** (Central de comando, só para quem administra) ou é administrador. A regra fica no próprio Firestore (`firestore.rules`), não só na tela.

**Tela de entrada (`login.html`):**
- **Entrar:** e-mail e senha.
- **Primeiro acesso:** a pessoa convidada cria a senha (pelo menos 8 caracteres, digitada duas vezes) e recebe um link para confirmar o e-mail; depois de tocar no link, **Já confirmei** abre o app. Um e-mail sem convite não cria conta.
- **Esqueci minha senha:** envia um link para criar uma senha nova.
- A sessão fica no aparelho até a pessoa sair (**Salvar → Sua conta → Sair do Orbe**). Os e-mails do Firebase saem em português; peça para olhar também a caixa de spam.

**Configurar no console do Firebase (uma vez):** em https://console.firebase.google.com, projeto **orbe**:
1. **Authentication → Método de login:** ative **E-mail/senha**.
2. **Authentication → Configurações → Domínios autorizados:** adicione o endereço do site na Vercel (por exemplo `seu-site.vercel.app`, e o domínio próprio, se houver). `localhost` já vem autorizado.
3. **Firestore Database:** crie o banco no modo de produção, local `southamerica-east1` (São Paulo).
4. **Firestore → Regras:** apague o texto que estiver lá, cole o conteúdo inteiro de `firestore.rules` e clique em **Publicar**.
5. **Firestore → Dados → Iniciar coleção:** ID da coleção `admins`; ID do documento: o **seu e-mail, em minúsculas**; adicione um campo qualquer (por exemplo `nome`, tipo string, com o seu nome) e salve. É assim que o Firestore sabe quem administra.
6. Publique o site (sem o `middleware.js`), abra-o e use **Primeiro acesso** com o seu e-mail.
7. Convide as outras pessoas em **Central de comando → Convidados**, e avise para usarem **Primeiro acesso**.

**Os dados que já existem:** o login antigo guardava uma cópia local por pessoa em cada navegador. Na primeira entrada com o Firebase, a conta fica com a cópia da pessoa que usava aquele navegador (o app reconhece pelo cookie do login antigo) e tudo sobe para a nuvem. Se uma cópia antiga sobrar sem dono, **Salvar → Backup** mostra **Trazer a cópia N**, que junta os registros dela aos da conta. Por segurança, baixe um backup em cada aparelho antes de publicar a v39.

**Administrador:** quem está em `admins` vê duas coisas a mais:
- **Central de comando → Convidados:** a lista de e-mails que podem entrar. **Convidar** acrescenta (o e-mail fica em minúsculas, com um nome opcional); **Remover** encerra o acesso da pessoa, e os dados dela continuam guardados.
- **Hoje → Acompanhamento:** a semana de cada convidado, lida do resumo que o app da pessoa envia: horas estudadas, aderência até ontem (sem os dias de imprevisto), dias seguidos, revisões atrasadas, horas dos últimos 7 dias e por matéria, último estudo, horas do mês, revisões e avanço do edital. Quem ainda não entrou aparece como “ainda não entrou no Orbe”. **Atualizar** lê de novo.

**Depois da troca:** as variáveis `ORBE_*` da Vercel e os tokens do GitHub não são mais usados; dá para apagá-las e revogar os tokens. Os repositórios de dados do GitHub podem ficar como cópia antiga.

**Sem internet:** o app abre a última versão guardada e a sessão continua; os registros esperam na cópia local (aviso **Pendente**) e sobem quando a conexão voltar.

**Testar no computador:** por um servidor local (`localhost`), o site pede a mesma entrada do Firebase. O modo de teste (`?teste`) não usa a nuvem.

**Ao publicar uma versão nova**, aumente o número `?v=` de todos os arquivos em `index.html` e `login.html`, nos `import` de `cloud.js` e `login.js`, e o valor de `CACHE` em `sw.js`. Arquivos de versões anteriores que não são mais usados e podem ser apagados da hospedagem: `middleware.js`, `eagle.svg`, `eagle.png`, `orbeevo-mark.png` e, desde a fonte D-DIN, `fonts/manrope-latin.woff2`, `fonts/manrope-latin-ext.woff2` e `fonts/OFL.txt` (a licença da Manrope); e, desde a fonte em WOFF, `fonts/d-din-regular.otf` e `fonts/d-din-bold.otf`.

## Marca e visual

**Marca:** o símbolo do Orbe é um “o” feito de quatro traços de comprimentos diferentes, em tons de verde (verde, verde-escuro, verde-limão e verde-claro), como as matérias de um ciclo de estudos, cada uma com suas horas. O verde é a cor da marca e não muda. Ao lado, a escrita **ORBE** em maiúsculas espaçadas (D-DIN negrito). O símbolo e a escrita são maiores que as abas, para a marca se destacar. O centro das letras fica na mesma altura do centro das letras das abas, e uma linha fina separa a marca das abas. A assinatura “Seu universo de estudos” fica no login e no título da página. O símbolo fica em `orbe-mark.svg` e, com `favicon.ico` e `favicon-32.png`, vira o ícone da aba do navegador e dos favoritos, em verde. O ícone do app instalado (`icon-192.png`, `icon-512.png`, `icon-maskable-512.png` e `apple-touch-icon.png`) é o símbolo verde sobre fundo preto.

**Visual:** inspirado na spacex.com: tipografia D-DIN, abas, títulos e botões em maiúsculas espaçadas, cantos quase retos, linhas finas no lugar de sombras e um único verde para foco e progresso. O texto comum fica em regular e só títulos, abas e botões em negrito. Os tamanhos são contidos (texto base de 15 px, títulos e abas pequenos em maiúsculas) para a tela não ficar carregada. O conteúdo fica centralizado, com até 1.120 px de largura.
- **Barra do topo:** da mesma largura do conteúdo, com marca e abas do mesmo tamanho e um traço sob a aba aberta. A barra some para cima ao rolar a página para baixo e volta ao rolar para cima; longe do topo, ela ganha fundo. Em tablets, as abas descem para uma segunda linha.
- **Celular:** as abas vão para uma **barra fixa no rodapé**: Hoje, Método, Histórico, Revisões e Menu. Método abre Ciclo e Cronograma, e Menu abre a Central de comando, Notas e Salvar e backup, numa lista que sobe da parte de baixo da tela. O ponto verde das Revisões também aparece nela.
- **Transições:** cada área aparece com um leve fade ao ser aberta (desligado quando o aparelho pede menos movimento).
- As cores ficam em variáveis no início de `index.html` (`:root`), e `theme.css`, carregado por último, desenha a barra, os botões e os cartões.

**Órbita (`space.js`):** uma animação bem fraca, só em dois lugares: atrás do relógio do **modo foco** (veja Modo foco) e na tela **Entrar**. As páginas do app não têm animação de fundo. É um pequeno sistema desenhado com os seus dados:
- **uma espiral das últimas 10 semanas**, ao redor do centro, uma volta por semana e um traço por dia, mais forte quanto mais do estudo previsto no dia foi feito; as semanas antigas vão apagando e hoje fica na ponta de fora, enchendo conforme você estuda;
- **nas órbitas de fora, cada matéria ativa** gira na sua cor, com um rastro do tamanho do progresso da semana; ele fecha o círculo quando a meta da semana daquela matéria é cumprida;
- **no centro, só no modo foco, o anel do Orbe**: a volta atual do ciclo.
A animação é toda em tons de verde (linhas, estrelas e uma tonalidade de verde para cada matéria, a mesma na órbita e no anel), para em outra aba do navegador, fica parada para quem pede menos movimento e, no celular, desenha menos quadros por segundo para poupar bateria.
- **Login:** com dados de exemplo (lá ainda não há dados). No celular, ocupa a tela; no computador, fica pequena, entre o título **Entrar** e o botão **Entrar**, sem cobrir o nome do site.
- **Marcos:** quando uma volta do ciclo termina ou uma matéria cumpre o planejado da semana, aparece um aviso discreto no topo (“Volta 3 do ciclo concluída”, “Meta da semana de Português cumprida”), e, no modo foco, o satélite da matéria solta um anel na animação.

**Tema:** o padrão é o claro. O botão de lua/sol no topo troca para o escuro (fundo preto e texto branco), e a escolha fica salva no aparelho. O tema é aplicado antes da página aparecer, sem piscar.

**Como funciona?:** os textos de ajuda mais longos ficam recolhidos atrás de **Como funciona?**, para as telas ficarem limpas. Os que explicam o que a Orbe faz sozinha (`guide.js`) são escritos com as **suas configurações** (as padrão, até você mudar) e mudam junto com elas, sem entrar na lógica por trás. São eles:
- a Carga disponível (“Sua semana tem 24:00 de estudo: 4:00 por dia, de segunda a sábado…”);
- as Matérias e fases, com quantas estão em cada fase;
- o Modo foco, com o seu Personalizado;
- a legenda do Cronograma, o Planejado x feito e o círculo do Ciclo;
- as Revisões, dizendo se o domingo entra ou não.

**Boas-vindas, Meteoro e Orbonauta (`welcome.js`):** quem ainda não traçou a primeira rota é um **Meteoro**; ao traçá-la, vira **Orbonauta**.
- Um Meteoro recebe, uma vez em cada aparelho, a janela **Boas-vindas à Orbe!**, com o manifesto. **Entrar em órbita** faz a contagem “3, 2, 1” e abre a Central de comando; **Explorar primeiro** fecha a janela.
- No topo da Central de comando, o Meteoro vê a **Primeira configuração**: “Sua rota está prestes a ser traçada e a Central de comando está pronta. Defina seu primeiro objetivo, monte seu cronograma e comece a ver a sua evolução decolar!”, o campo **Seu objetivo** e o botão **Traçar minha rota**.
- Depois disso, a Central de comando mostra sempre: “Aqui é um espaço seguro para testar e recalcular a rota sempre que necessário!”, com “Orbonauta desde…”, o objetivo e a **data da prova** (opcional), que podem ser mudados.
- **Patente e ritmo:** abaixo da rota, a patente atual e quanto falta para a próxima; com data da prova, quantos dias faltam e quantas horas o ritmo atual da semana soma até lá.
- **Patentes:** Cadete, Tripulante (20 pontos), Piloto (60), Navegador (150), Comandante (300), Explorador estelar (600) e Lenda da Orbe (1000). Cada hora estudada vale 1 ponto e cada volta do ciclo concluída vale 3. A patente aparece num selo discreto na saudação do Hoje, e subir de patente mostra um aviso uma vez.
- **Tour:** ao traçar a rota, 4 balões apontam as abas Hoje, Método, Revisões e Histórico (na barra do rodapé, no celular). **Pular tour** fecha; **Rever o tour** fica na Central de comando.
- A rota (objetivo, data da prova e o dia em que foi traçada) fica no planejamento, então vale em todos os aparelhos. Quem já usava a Orbe antes (com estudos de dias anteriores, ou configurações mudadas em outro dia) vira Orbonauta sozinho, desde o primeiro estudo, e não vê as boas-vindas.

**Simulador de rota (Central de comando, `simulator.js`):** “E se você mudasse o ritmo?”. Os controles começam no estudo por dia e no máximo por matéria atuais e mostram, sem salvar nada, as horas da semana (e a diferença para a rota atual), quantas matérias e de que tamanho ficam os blocos de cada dia, as horas por semana de cada matéria (com o valor de antes) e, com data da prova, as horas até lá. **Aplicar à minha rota** grava o estudo por dia e o máximo por matéria (com horas diferentes por dia, todos os dias de estudo passam a ter o mesmo estudo); **Voltar ao atual** desfaz a simulação.

**Tela de login:** o olho no campo **Senha** mostra o que está sendo digitado e esconde de novo no segundo toque (e ao entrar).

**Aparência (Central de comando):** o quadro **Aparência** guarda escolhas só deste aparelho, como o tema:
- **Tamanho:** compacto, padrão ou confortável (texto e espaço dentro dos quadros).
- **Na aba Hoje, mostrar:** Ciclo de hoje, Cronograma de hoje e Revisões. Por padrão, o Cronograma de hoje fica desligado; marque-o para que ele apareça. Os quadros que ficam ocupam o espaço. Cada quadro do Hoje também recolhe ao tocar no título, e o app lembra quais ficaram recolhidos.
- **Celebrar missões cumpridas:** ligado por padrão. Ao cumprir a missão do dia ou da semana, ao traçar a rota e ao subir de patente, anéis verdes se abrem a partir do símbolo da Orbe por 2 segundos. Desligado (ou com “reduzir movimento” no aparelho), fica só o aviso.

## Instalar como aplicativo e usar sem internet

Depois de aberto uma vez com internet, o site funciona sem conexão com a última versão carregada. No celular, use **Adicionar à tela inicial** (ou **Instalar app**); no computador, o ícone de instalação na barra de endereço. Sem internet, os registros ficam na cópia local e são sincronizados quando você conectar de novo. Com internet, a página sempre busca a versão publicada.

## Salvar - Sincronizar (como funciona por trás)

Cada registro fica primeiro na cópia local do aparelho e vai para a nuvem da conta (Firestore) 2 segundos depois. Os outros aparelhos recebem a mudança na hora, e cada aparelho junta o que chega com o que tem, registro por registro (vale a edição mais recente).

- Os dados vão em **partes**: o planejamento e cada lista (sessões, revisões, notas…) dividida em 16 partes. Uma alteração regrava só a parte dela.
- Antes de gravar, o app confere se aquela parte continua como ele a leu. Se outro aparelho mudou a parte nesse meio-tempo, o app lê de novo, junta e só então grava: um aparelho nunca apaga o que o outro salvou.
- **Salvar** mostra a hora da última gravação e o tamanho dos dados (limite de 10 MB). **Sincronizar agora** força uma rodada.
- **Acompanhamento:** depois de cada gravação, o app envia um **resumo** separado para quem administra: horas dos últimos 400 dias, matérias, cronograma da semana, dias de imprevisto e datas de revisão. Notas, assuntos e observações nunca vão no resumo, e as regras não deixam o administrador ler o espaço da pessoa.
- **Cota gratuita:** o plano Spark permite 50 mil leituras e 20 mil gravações por dia; uma pessoa usa poucas centenas. Se a cota acabar, os registros esperam na cópia local e sobem no dia seguinte.

**Sua conta:** em **Salvar**, aparece com qual e-mail você entrou e o botão **Sair do Orbe**. Sair encerra a sessão no aparelho; os registros continuam na cópia local e na nuvem.

## Vários aparelhos e matérias renomeadas

- Cada matéria tem um identificador fixo. Ao renomear, o nome antigo continua ligado a ela, e sessões, revisões, notas e dias do ciclo passam a mostrar o nome novo. Isso vale também para registros feitos em outro aparelho com o nome antigo.
- O planejamento é juntado campo a campo: estudo por dia, máximo por matéria, dias, matérias por dia, domingo, lista de matérias, ciclo e ordem do cronograma. Se um aparelho muda as horas e outro a ordem do ciclo, as duas alterações ficam. Quando os dois mudam o mesmo campo, vale a alteração mais recente.
- Sessões, revisões, notas e dias registrados são combinados pelo identificador; a mesma sessão editada em dois aparelhos fica com a edição mais recente.

O arquivo de dados está no **schema 6**. O planejamento ganhou os campos `mission` (a rota: objetivo, data da prova e data em que foi traçada) e `breaks` (os dias de imprevisto), juntados entre aparelhos como os outros campos. Arquivos e backups antigos continuam sendo aceitos. Uma página aberta com a versão anterior recusa o arquivo novo sem sobrescrevê-lo: recarregue as páginas abertas em outros aparelhos depois de publicar.

## Abas

Hoje · Método (Ciclo ou Cronograma, num menu que abre ao passar o mouse ou tocar) · Histórico · Revisões · Notas · Central de comando. O site abre em **Hoje**. No celular, as abas ficam na barra do rodapé (veja Marca e visual).

**Salvar** não fica entre as abas. Ela abre pelo **ícone de nuvem** no topo, ao lado do botão de tema, e, no celular, também por **Menu → Salvar e backup**.

O aviso **Pendente - Sincronizar** só aparece no topo quando há algo esperando para ser salvo: alterações sem conexão ou uma sincronização que falhou. Clicar nele abre Salvar. Com a sincronização automática, as alterações são gravadas em 2 segundos, sem aviso.

Quando há revisão para hoje ou atrasada, um pequeno **ponto verde** aparece logo depois do nome da aba **Revisões**, em qualquer aba aberta: passando o mouse sobre a aba aparece quantas estão atrasadas e agendadas para hoje. O ponto some quando as revisões são feitas.

## Hoje

**Saudação, mensagem do dia e missões:**
- No topo, o Orbonauta lê “E aí, Orbonauta, pronto para a linha de chegada de hoje?” (num dia livre do plano, um convite para recarregar as energias), com o selo da patente antes e, logo abaixo, o objetivo e a contagem até a prova (“Rumo: … · faltam N dias para a prova”; “a prova é amanhã”; “a prova é hoje!”). O Meteoro lê “Ei, Meteoro! Sua rota ainda não foi traçada.”, com o atalho para a Central de comando.
- Abaixo da barra do dia aparece uma **mensagem do dia**: curta, diferente a cada dia, sobre estudos, metas, vida, dificuldades, superação e fé no caminho, sem cunho religioso. Ela muda com o momento: de manhã (5h às 12h), mensagens para começar; à tarde (12h às 18h), para persistir; à noite, para descansar e reconhecer o dia. Na véspera e no dia da prova, mensagens próprias.
- **De volta à órbita:** quem volta depois de 3 dias ou mais sem estudo é recebido com uma mensagem acolhedora, sem cobrança, e três escolhas: **Começar 25 minutos** (abre o modo foco com a próxima matéria e pausas de 25 + 5), **Marcar esses dias como imprevisto** (os dias de estudo perdidos viram dias de descanso) e **Agora não** (some até o dia seguinte).
- **Dia de imprevisto (modo descanso):** abaixo da barra do dia, “Imprevisto hoje? Marcar como dia de descanso” (enquanto o dia não foi cumprido) e “Ontem foi um imprevisto?” (quando ontem era dia de estudo e ficou sem estudo). Um dia de imprevisto não quebra os dias seguidos, sai da aderência (Planejado x feito), do planejado do Resumo da semana e da missão da semana, e não tem missão do dia. **Desfazer** volta atrás. Os dias ficam no planejamento e valem em todos os aparelhos.
- **Missão do dia:** ao estudar todo o previsto para hoje, a saudação vira “Missão cumprida, Orbonauta do dia!”, e um aviso aparece no topo uma vez.
- **Missão da semana:** ao estudar tudo o que o cronograma da semana prevê, aparece “Missão cumprida, Orbonauta da semana!”, uma vez por semana, e o Resumo da semana começa por “Missão da semana cumprida”.

A primeira aba reúne o dia: a data grande, quanto já foi estudado do estudo previsto para hoje e as revisões pendentes; o **Ciclo de hoje** e, se ligado em Central de comando → Aparência, o **Cronograma de hoje**, lado a lado (✓ nas matérias já estudadas hoje e ▶ para estudar as outras no modo foco); e as **Revisões** de hoje e atrasadas, com atalho para a aba Revisões. A primeira revisão da fila fica em destaque, com o botão **Revisar agora**, que abre a aba Revisões com o cronômetro já iniciado e ligado a ela (salvar a sessão finaliza a revisão). Ao lado das horas do dia, um contador discreto mostra os **dias seguidos** com estudo (a partir de 2) e o **recorde** (a maior sequência que você já fez; “seu recorde” quando a atual é a maior). Os dias livres do plano não interrompem a sequência. Quando a sequência atual passa um recorde anterior de 2 dias ou mais, aparece no topo o aviso “Novo recorde: N dias seguidos”, uma vez por número. O botão principal (“Estudar … no modo foco →”), à direita da data (abaixo dela no celular), abre o modo foco com a próxima matéria do ciclo.

**Resumo da semana** (seção no fim da aba, aberta por padrão; dá para recolher no título): de segunda até hoje, horas estudadas, porcentagem do planejado no cronograma, voltas do ciclo concluídas e revisões feitas; uma barra por dia, de segunda a domingo, com o feito em verde sobre o planejado em verde-claro; horas por matéria (feito / planejado), com a matéria mais atrasada marcada; e uma sugestão:
- 90% ou mais do planejado: semana consistente, com a ideia de subir o estudo por dia em 30 minutos se sobrou fôlego;
- de 70% a 90%: aponta a matéria mais atrasada e sugere colocá-la no começo da semana (Ordem do cronograma) ou mover um bloco dela para um dia mais leve;
- abaixo de 70%: mostra o feito contra o planejado e sugere um estudo por dia perto da média real, com um atalho para a Central de comando.

**Diário de bordo (domingo):** no domingo, o Resumo da semana pergunta “O que funcionou nesta semana?”. A resposta fica nas **Notas**, como “Diário de bordo”, e pode ser atualizada no mesmo dia.

## Modo foco

Abre pelo botão principal da aba Hoje, pelo ▶ de cada matéria ou pelo botão **Modo foco** do cronômetro de estudo. É uma tela inteira em preto e branco com a matéria, o relógio grande no estilo de lançamento (**T+ 00:42:10**, o tempo líquido de estudo), a barra do bloco do ciclo (“faltam 0:45”) e os botões **Começar/Retomar**, **Pausar** e **Encerrar e salvar**. Usa o mesmo cronômetro da aba Histórico, então a sessão é salva do mesmo jeito.

- **Animação:** a órbita aparece bem fraca atrás do relógio e, só aqui, com o **anel do Orbe** no centro: a volta atual do ciclo, um arco por bloco na cor da matéria, aceso quando o bloco está feito (sem ciclo, o anel é o próprio símbolo). O botão **Animação** liga e desliga, e a escolha fica salva no aparelho.

- **Sons de foco (até 2 horas cada):** ruído marrom (grave e constante), ruído rosa (agudos mais suaves, como chuva), ruído branco (todas as frequências, o que mais abafa barulhos) ou batidas binaurais de 15 Hz (use fones). Tocar, pausar, reiniciar e volume; a escolha fica salva no aparelho. O app também lembra o último som tocado com cada matéria e o deixa escolhido ao abrir o modo foco para ela (passando o mouse no seletor, aparece “Último som usado em …”). A evidência científica sobre esses sons é limitada: use o que funcionar para você.
- **Pausas programadas:** sem pausas, 25 + 5, 50 + 10 (padrão) ou 90 + 15 minutos. Ao fim do tempo de foco, o cronômetro pausa sozinho e toca um som; a tela mostra quanto falta da pausa e avisa quando ela termina. A escolha fica salva no aparelho.
- **Tela cheia** e **Sair** (ou Esc). Sair não para o cronômetro.
- Enquanto o modo foco está aberto, a tela não apaga (onde o navegador permite), e o título da aba mostra o tempo.

**Desfazer:** ao excluir uma sessão, uma nota, uma revisão ou um plano de revisões (inclusive a exclusão de todas as revisões de uma matéria), um aviso com **Desfazer** fica na tela por 10 segundos e devolve o que foi excluído.

## Central de comando

A aba **Central de comando** (no celular, **Menu → Central de comando**) guarda tudo o que o Cronograma e o Ciclo usam: estudo por dia e máximo por matéria no dia (até 12h cada), dias de segunda a sábado, matérias por dia (Automático deixa as horas do dia decidirem), domingo, bloco mínimo e a lista de matérias. **Incluir domingo** acrescenta o domingo à semana e ao ciclo. Cada matéria tem fase e peso de 0,1 a 5:

**Horas diferentes por dia:** marque **Horas diferentes por dia da semana** para escolher as horas de cada dia, de segunda a domingo, em passos de 30 minutos até 12h; **Livre** tira o dia da semana de estudo. O ciclo segue as horas de cada dia. Desmarcar volta para estudo por dia, dias de estudo e domingo.

**Dias com números diferentes de matérias:** com horas diferentes por dia, um dia pode ter mais matérias que outro (por exemplo, segunda a sexta com 5h e máximo de 1:30 têm 4 matérias; um sábado de 7h tem 5). Um aviso no Ciclo e na Central de comando mostra como ficou a semana e oferece: **mesmo número de matérias todos os dias** (com a duração que os blocos teriam, avisando se passarem do máximo), **escolher outro dia da semana** para o dia com mais ou menos matérias (as horas dos dois dias são trocadas) ou **ajustar** estudo por dia e máximo por matéria. O × esconde o aviso até a Central de comando mudar.

- **Aguardando**: a fila de entrada. A ordem da lista é a ordem de entrada.
- **Construção**: a primeira passada por todos os tópicos do edital.
- **Consolidação**: todos os tópicos já vistos; a matéria aparece menos vezes na volta. (A fase Manutenção não é mais oferecida; uma matéria que já estava nela conta como Consolidação.)

**Esquema de prioridades.** A semana de estudo (estudo por dia × dias de estudo) é dividida assim:

- Cada matéria recebe uma parte da semana pelo peso; em **Consolidação**, ela conta metade de uma em Construção. O peso ajusta a matéria dentro da própria fase.
- A Construção é protegida sozinha: com todas as vagas ocupadas, a Consolidação fica com até 35% da semana; cada vaga vazia passa o tempo dela para a Consolidação. Sem matérias em Construção, todas dividem a semana igualmente.
- **Cada dia tem o menor número de matérias possível.** O estudo do dia é dividido em blocos iguais de até o **máximo por matéria no dia**, nunca menores que o **bloco mínimo por dia** (30 min, 45 min ou 1 hora). Exemplo: 5h por dia com máximo de 1:30 dá 4 matérias de 1:15 em todos os dias; com 4h e máximo de 2h, 2 matérias de 2h. Os blocos andam de 5 em 5 minutos e sempre fecham as horas do dia. Se o mínimo e o máximo não couberem juntos, vale o mínimo (blocos maiores, com aviso na Central de comando).
- Com **Automático** em Matérias por dia essa conta é feita sozinha; um número fixo divide cada dia por esse número.
- A parte de cada matéria na semana vira **quantas vezes ela aparece na volta**, no máximo uma vez por dia: Construção aparece mais vezes, Consolidação menos. Com horas diferentes por dia, a volta conta o bloco médio da semana.
- Se a parte de uma consolidada não chegar a meio bloco por semana, as consolidadas entram em **rodízio**: a volta dura mais e cada uma aparece ao menos uma vez por volta.
- **Vagas de Construção** são automáticas: o app guarda quantas matérias você costuma ter em Construção ao mesmo tempo. Quando uma passa para Consolidação, ele sugere a primeira Aguardando para a vaga; o × da sugestão mantém menos matérias em Construção.
- **Poucas matérias ativas**: se não houver matérias ativas suficientes para o dia caber no máximo por matéria, os blocos crescem sozinhos e a Central de comando avisa até quanto foram.

O avanço do edital (matérias consolidadas de todas) aparece na Central de comando e no Ciclo.

## Cronograma

O Cronograma é **fixo e previsível**, separado do Ciclo: as matérias de cada dia saem da Central de comando (horas, máximo por matéria, bloco mínimo, fases e pesos) e da **Ordem do cronograma**, e o que não for estudado **não passa para o dia seguinte**. Quem se ajusta ao que foi estudado é o Ciclo.

- Cada dia tem o menor número de matérias que o máximo por matéria permite, e a parte de cada matéria na semana vira um número de blocos, no máximo um por dia.
- Quando as partes fecham em blocos inteiros, **toda semana é igual** (por exemplo, com 4h por dia, máximo de 1:30 e 4 matérias em Construção mais 1 consolidada: 4 blocos de cada matéria em Construção e 2 da consolidada, sempre nos mesmos dias). Quando não fecham, a fração **alterna entre as semanas** (2 blocos numa, 1 na seguinte), sempre da mesma forma, contada a partir de uma segunda-feira fixa. Assim qualquer semana pode ser planejada com antecedência.
- **Ordem do cronograma** (seção recolhida da aba): arraste pela alça ou use as setas. A ordem define em que dias cada matéria aparece e a sequência dentro do dia; em empate, a primeira da lista fica com o bloco a mais. Ela vale só para o cronograma; a **Ordem do ciclo** continua na aba Ciclo e só muda o ciclo. **Usar a ordem da lista de matérias** copia a ordem da Central de comando.
- **Próximas semanas:** as setas ‹ › ao lado do título mostram as semanas seguintes (até 26 à frente), já com as matérias de cada dia. **Distribuição da semana** mostra os blocos e as horas de cada matéria na semana que está na tela.
- **Ajustar só uma semana:** arraste uma matéria de hoje ou de um dia seguinte para outro dia (no celular, toque nela e escolha o dia no menu; pelo teclado, Enter na matéria). O bloco vai com a mesma duração, os dois dias ganham a marca “ajustado” e ficam guardados assim, inclusive nas próximas semanas que você ajustar. A Ordem do cronograma e as outras semanas não mudam. Não dá para mover para um dia que já tem a matéria nem para dias passados. **Trocar duas matérias de dia:** arraste uma matéria sobre outra de outro dia (no celular, use “ou trocar de dia com” no mesmo menu). As duas trocam de lugar e cada dia mantém a duração dos seus blocos, então as horas de cada dia não mudam; a troca só aparece quando nenhuma das duas já está no outro dia. **Desfazer ajustes desta semana** devolve os dias ao cronograma.
- Mudar a Central de comando ou a ordem vale a partir de hoje; os dias passados ficam como foram planejados.

Na semana, ✓ indica presença de estudo (qualquer sessão de Estudo da matéria no dia, mesmo abaixo do tempo planejado). O relógio aparece nos blocos de hoje e abre o cronômetro com a matéria escolhida. Em dias passados sem estudo, o ícone de anotação abre Notas com data e matéria preenchidas. Quando a jornada do dia foi cumprida pelo ciclo (todas as matérias da lista do ciclo estudadas) ou o estudo do dia atingiu o estudo por dia, o cronograma mostra “ciclo ✓” (um texto pequeno em verde, sem caixa, como “ajustado” e o “• Hoje” do dia atual, que tem só uma moldura fina verde) e o ícone de setas circulares no lugar da anotação, mesmo que as matérias estudadas sejam outras. Os dias passados ficam preservados quando você muda a Central de comando; os símbolos recomeçam a cada segunda-feira.

**Planejado x feito:** abaixo da semana, cada matéria tem uma barra com as horas do cronograma na semana e as estudadas (sessões de Estudo; revisões ficam de fora), e o resumo mostra o planejado, o feito e a **aderência só dos dias encerrados** (até ontem, por exemplo “aderência até ontem: 80% (9:36 de 12:00)”): o dia de hoje entra na conta amanhã, então a aderência não cai enquanto o dia ainda está em andamento. Na segunda-feira, ela aparece a partir de terça. As setas trocam de semana.

## Ciclo de estudos

A aba **Ciclo** (em Método) funciona como um ciclo de estudos clássico: uma sequência de blocos com horas, que não depende do dia da semana. Cada volta tem os blocos do esquema de prioridades, intercalados: as matérias em Construção aparecem várias vezes, e as consolidadas aparecem entre elas, na ordem do ciclo. Cada dia pega as próximas matérias da sequência, uma vez cada, no menor número que o máximo por matéria permite, e divide as horas do dia entre elas (5h com máximo de 1:30: 4 matérias de 1:15). O ciclo conta horas: um bloco termina quando suas horas são estudadas, e o que passar adianta a próxima volta. Se no dia a matéria foi estudada menos do que o programado, ela conta como feita e o círculo mostra o tanto estudado depois do ✓, por exemplo “✓ 0:45/1 hora”; o restante não volta para o dia seguinte.

- **Ciclo de hoje**: o círculo de setas mostra só as matérias do dia, na ordem de estudo, com as horas de cada uma; o centro mostra a duração do dia e quanto já foi feito. Tocar em uma matéria abre o cronômetro. No celular, as matérias aparecem em lista abaixo do círculo.
- **Adiantar**: se estudar além do ciclo de hoje, o botão traz para o dia a próxima matéria do ciclo que ainda não está no dia, com um bloco do tamanho dos de hoje; uma matéria do dia só volta quando não houver outra. Dá para desfazer.
- **Hoje no ciclo** e **Ordem do ciclo** ficam recolhidos; clique no título para ver a lista do dia (com pendências) ou para mudar a ordem. Avisos do ciclo podem ser fechados no ×.
- **Barra da volta**: abaixo do círculo, a volta inteira aparece bloco a bloco, na ordem de estudo, com os blocos feitos, os de hoje e os que faltam, as horas da volta, as voltas concluídas na semana e a previsão de quando a volta atual fecha.
- **Semana programada**: a seção expansível mostra a semana atual, com o que o ciclo programou e o que foi estudado nos dias passados, e uma projeção para hoje e os próximos dias que supõe cada dia cumprido.
- **Histórico**: cada linha é uma volta, com período, duração, horas por matéria e dias com pendência; o botão da linha mostra os dias. Aparecem as duas voltas mais recentes, e **Ver demais ciclos** traz as outras, com filtros por mês, matéria e situação, ordenação (mais recentes, mais rápidas, mais horas) e um resumo com médias, a volta mais rápida e a matéria mais adiada.

- Uma matéria da lista que não for estudada abre o próximo dia de estudo, com “Pendente desde”.
- Dias fora do Planejamento (os que passam de “Dias de seg. a sáb.” e o domingo, se não estiver incluído) são livres: não geram lista nem pendências, e o que faltou abre o próximo dia de estudo. Estudar num dia livre mesmo assim conta na volta.
- Arraste pela alça, use as setas ou, com a alça em foco, as setas do teclado. A nova ordem vale a partir de hoje.
- **Reiniciar contagem de voltas** recomeça na volta 1 a partir de hoje, sem apagar sessões, notas ou dias anteriores. Na primeira abertura desta versão, a contagem recomeça uma vez, para não misturar voltas das regras anteriores.
- Revisões não contam no ciclo. Editar ou excluir sessões recalcula o ciclo.

## Cronômetro e histórico

Em **Histórico**, use Iniciar, Pausar, Parar e Salvar sessão; as pausas ficam fora das horas líquidas. Uma sessão que passa da meia-noite é dividida entre as datas. No gráfico **Evolução do estudo**, cada barra é uma só, de cantos retos: o estudo em verde embaixo e a revisão em verde-claro em cima. O cronômetro mostra a próxima matéria do ciclo. Com **Avisar quando completar o bloco do ciclo**, um som e uma notificação avisam quando o tempo de hoje atinge o bloco sugerido. A notificação depende da permissão do navegador e da página aberta; em segundo plano, o aviso pode atrasar até um minuto.

Registros manuais aceitam hoje e dias anteriores, no formato `h:mm`. Edite ou exclua pelo histórico; gráficos e totais são recalculados. As semanas começam na segunda-feira.

## Revisões

Registre matéria, assunto, data estudada e observações: o sistema agenda D+1, D+7, D+14, D+30, D+90 e D+120 a partir da data original. Revisões que cairiam no domingo passam para segunda-feira, exceto quando o domingo está incluído no Planejamento. O cronômetro de revisão registra horas como Revisão no mesmo histórico; iniciar um cronômetro pausa o outro.

A agenda mostra as revisões de hoje e atrasadas; as programadas e finalizadas ficam na seção expansível. Ao mudar a fase de uma matéria, desloque ou exclua as revisões pendentes manualmente; nada é excluído automaticamente.

O **ponto verde** depois do nome da aba Revisões indica revisões de hoje ou atrasadas. A seção Lembretes (notificações de revisão e exportação para calendário `.ics`) foi retirada.

## Notas

Registre o motivo de um estudo não realizado e um ajuste para a próxima tentativa, para hoje ou dias passados. Uma nota de hoje troca o relógio pelo ícone de atividade não realizada. Notas não registram horas. O Diário de bordo de domingo também aparece aqui.

## Backup e mudança de endereço

Em **Salvar → Backup e mudança de endereço**, baixe o backup (`orbe-AAAA-MM-DD.json`). No endereço novo, basta entrar com a mesma conta: os dados vêm da nuvem. O backup continua útil como cópia guardada por você. Importar combina os registros pelos identificadores, sem duplicar. Dados no navegador pertencem ao endereço em que foram criados.

## Testes

`tests/core.test.js` cobre as regras de `study-core.js`: migração de arquivos antigos, junção entre aparelhos, nomes antigos, formato do arquivo e as regras do ciclo. Abra `tests/index.html` pelo site (ou por um servidor local) para rodar no navegador, ou use `node --test tests/` se tiver o Node instalado.

`tests/ui.html` roda os testes de tela: abre o app de verdade num quadro, com a data simulada, e confere a semana do ciclo (horas de cada dia e domingo livre), o mesmo número de matérias em todos os dias, o aviso de dias com números diferentes de matérias, pendências, estudo parcial, Adiantar, o ponto verde de revisões, o cronograma fixo, as próximas semanas, mover uma matéria e desfazer, a aderência dos dias encerrados, a ordem do cronograma separada da ordem do ciclo, a aba Hoje com o modo foco salvando a sessão, a troca de duas matérias de dia, o modo escuro e o resumo da semana aberto por padrão, horas por dia, Desfazer, Planejado x feito, Salvar fora das abas (aviso Pendente e nuvem), a barra do celular com Método e Menu, o relógio T+ com a animação que liga e desliga no modo foco, o padrão (tema claro, verde, Cronograma de hoje desligado) e a Aparência (tamanho, quadros do Hoje escondidos e recolhidos) a órbita só no modo foco com o aviso de meta da semana, o Revisar agora, os dias seguidos com a barra de cada dia no resumo, o som lembrado por matéria as explicações longas em “Como funciona?” o recorde de dias seguidos com o aviso de novo recorde, o Meteoro (boas-vindas, primeira configuração e Traçar minha rota), a mensagem do dia com a missão do dia, o “Como funciona?” com as suas configurações, o olho da senha no login, a data da prova (contagem, ritmo e mensagens da véspera e do dia), a mensagem por momento do dia, a patente com o aviso ao subir, o dia de imprevisto (sequência e aderência protegidas), o De volta à órbita, o Diário de bordo de domingo, o tour depois de Traçar minha rota, o Simulador de rota e a celebração que pode ser desligada, a nuvem simulada (partes, outro aparelho, conflito, resumo), Convidados, as cópias de antes das contas e a tela de entrada. `tests/core.test.js` também confere que os dados viram partes estáveis e voltam iguais, que o resumo do Acompanhamento não leva notas nem assuntos, e que a rota (`mission`, com a data da prova) e os dias de imprevisto (`breaks`) são validados e chegam aos outros aparelhos.

**Modo de teste:** abrir o site com `?teste` no fim do endereço (por exemplo `.../index.html?teste`) usa dados separados no navegador, sem tocar nos seus registros e sem instalar o app offline. Os testes de tela usam esse modo e apagam os dados de teste ao terminar.

**Arquivos do ciclo:** `cycle-model.js` calcula o ciclo (dias, voltas, pendências, adiantamentos) e `cycle.js` desenha a aba e trata cliques. Os cálculos ficam guardados até um registro ou a Central de comando mudar, o que mantém o app rápido mesmo com anos de dados (testado com 5 anos: cerca de 6.300 sessões e 2.300 revisões).

**Login e nuvem:** a entrada e a sincronização com o Firebase foram conferidas com uma nuvem simulada nos testes de tela (as partes sobem, a mudança de outro aparelho chega, a gravação que esbarra em outro aparelho relê e junta, o resumo sem assuntos, Convidados, cópias antigas e as três formas de entrar). A entrada de verdade (criar a senha, confirmar o e-mail, entrar) só pode ser conferida no site publicado, com o seu e-mail.
