# 6. Il protocollo http2

Ora che abbiamo descritto abbastanza cosa c'è dietro ad http2, la storia e le politiche decisionali, tuffiamoci nelle specifiche del protocollo: i bit e i concetti che compongono http2.

## 6.1. Binary

http2 è un protocollo binario.

Soffermiamoci per un minuto. Se sei stato coinvolto in qualche protocollo internet prima d'ora, ci sono buone probabilità che reagirai istintivamente contro questa scelta, elencando le motivazioni di come i protocolli basati su testo/ascii siano superiori in quanto gli esseri umano possono forgiare richieste attraverso telenet e così via...

http2 è binario per rendere la formulazione (framing in inglese) molto più facile. Riconoscere l'inizio e la fine di una trama (frame in inglese) è una delle cose più complicate in HTTP 1.1 e nei protocolli basati su testo in generale. Evitando gli spazi bianchi opzionali e le molteplici maniere per poter scrivere la stessa cosa, le implementazioni si semplificano.

Inoltre viene molto più facile separare alcune sezioni di protocollo dal framing - separazione assente nel protocoll HTTP1.

Inoltre il fatto che il protocllo utilizzi la compressione e verrà spesso utilizzato tramite TLS diminuisce il valore del testo, dato che non sarebbe comunque possibile vedere del testo sul cavo di trasmissione. Dobbiamo semplicemente abituarci all'idea di usare strumenti come Wireshark per ispezionare cosa sta succedendo a livello protocollo.

Il 'debugging' di questo protocollo potrà essere fatto con strumenti come curl, o analizzando il flusso di rete con il dissettore http2 di Wireshark e simili.

## 6.2. Il formato binario

<img style="float: right;" src="https://raw.githubusercontent.com/bagder/http2-explained/master/images/frame-layout.png" />

http2 invia frame binari. Esistono differenti tipi di frame che possono essere inviati e hanno tutti lo stesso formato: Lunghezza, Tipo, Flags, Identificatore Flusso (Stream ID) e contenuto (payload).

Esistono dieci diffenti tipi di frame definiti nella specifica http2 e probabilmente i due più importanti sono i frame che hanno una relazione 1:1 con HTTP 1.1 : DATA (contenuto) e HEADERS (instestazioni). Descrvierò in dettaglio alcui di questi frame più avanti

## 6.3. Flussi mescolati ('multiplexed streams')

L'identificatore di stream sopra descritto associa ogni frame inviato tramite http2 con un "flusso". Per flusso si intende una sequesnza di frame indipendente e bi-direzinale scambiata tra il client e il server tramite una connessione http2.

Una singola connessione http2 può contenere più flussi aperti concorrenti, con ogni estremità che permette l'interlacciamento di flussi diversi. I flussi possono essere stabiliti e usati unilateralmente o condivisi sia dal client che dal server e possono essere chiusi da qualunque degli endpoint. L'ordine con cui questi frame sono presentati all'interno del flusso è importante. Il mittente infatti elabora i frame nell'ordiine con cui li riceve.

Mescolare (multiplexare) il flusso significa che i pacchetti da stream differrenti sono introdotti nella stessa connessione. Due (o più) treni individuali di informazioni sono composti in uno singolo e suddivisi nuovamente una volta raggiunta la destizione. Qui i due treni:

![one train](https://raw.githubusercontent.com/bagder/http2-explained/master/images/train-justin.jpg)
![another train](https://raw.githubusercontent.com/bagder/http2-explained/master/images/train-ikea.jpg)

I due treni mescolati nella stessa connessione:

![multiplexed train](https://raw.githubusercontent.com/bagder/http2-explained/master/images/train-multiplexed.jpg)

## 6.4. Priorità e dipendenze

Ogni flusso ha una priorità (anche chiamata "peso"), che è usata per indicare all'altra estremità quale flusso è da considerare più importante in caso siano presenti limitazioni che inducono il server a selezionare quale flusso inviare per primo.

Utilizzando il frame PRIORITY, un client può informare il server da quale altro flusso dipende il flusso attuale. Permette quindi al client di costruire un "albero delle priorità" dove più flussi "figli" posono dipendere dal completamento di un flusso "padre".

I pesi e le dipendenze possono essere cambiati dinamicamente, così per esempio un browser può decidere quale risorse caricare prima quando un utente scorre una pagina piena di immagini, o dare maggior priorità a nuovi flussi nel momento che l'utente decide di cambiare finestra di navigazione.

## 6.5. Compressione dell'intestazione

HTTP è un protocolo senza stato (stateless). In breve, questo significa che ogni richiesta deve essere accompagnata quanti più dettagli possibili per permettere al server di completare la richiesta, senza dover ripetere troppe informazioni proveniente dai flussi precedenti. Poichè http2 non modifica questo paradigma, funzionerà nello stesso modo.

Tutto questo però rende HTTP ripetitivo. Quando un client domanda troppe risorse dallo stesso server, come le immagini da una pagina web, vengono generate una serie di richieste simili tra di loro. Una serie di cose pressochè identiche demanda compressione.

Mentre il numero di oggetti per pagina web è cresciuto (come già menzionato), anche l'utilizzo dei cookie è cresciuto e la grandezza delle richieste sono andati di pari passo. I cookie inoltre devono essere inclusi in ogni richiesta, e spesso in richieste differenti vengono inclusi gli stessi cookie.

Le richieste HTTP 1.1 sono diventate così larghe che a volte sono più grandi della finestra TCP (TCP window), il che rende la comunicazione assai lenta poichè hanno bisogno di un round-trip completo per ottenere un ACK dal server prima che la richiesta completa sia inviata. Questa è un'altra buona ragione per il tema compressione.

### 6.5.1. La compressione è un argomento delicato

HTTP e SPDY si sono rivelati vulnerabili agli attacchi [BREACH](http://en.wikipedia.org/wiki/BREACH_%28security_exploit%29) e [CRIME](http://en.wikipedia.org/wiki/CRIME). Inserendo un testo determinato nel flusso e notando come l'output cambi in base ad esso, un malintenzionato può capire cosa è stato inviato anche in informazioni criptate.

Comprimere contenuti dinamici - senza diventare vulnerabili a uno di questi attacchi - richiede qualche considerazione in più. E questo è quello che il team HTTPbus ha provato a fare.

Dentro [HPACK](http://www.rfc-editor.org/rfc/rfc7541.txt), è stata specificata la Header Compression per HTTP/2, che – come il nome suggerisci - è un formato di compressione pensato appositamente per le intestazioni http2 ed è stato specificato in una bozza separata. Il nuovo formato, assieme ad altre contromisure (come un bit che chiede agli intermediari di non comprimere specifiche intestazioni e il riempimento - padding in inglese - opzionale dei frame), renderà più difficile per i malintenzionati poter sfruttare la compressione a proprio vantaggio.

Con parole di Roberto Peon (uno dei creatori di HPACK):

> “HPACK è stato pensato per rendere difficile - in caso di implementazione conforme - la perdita di informazioni, velocizzare la codifica e decodifica, fornire al ricevente controllo sulla grandezza del contenuto della compressione, permettere proxy-reindexing (cioè uno stato condiviso tra la parte che si interfaccia con l'utente e quella interna nascosta dietro un proxy), e permettere una rapida comparazione di stringhe di testo codficicate con l'algoritmo di Huffman

## 6.6. Reset - cambia la prospettiva

Uno degli incovenienti di HTTP 1.1 è non poter fermare facilmente un messaggio già iniziato a spedire con un determianto Content-Length. Sicuramente si può chiudere la connessione TCP, ma questo viene a costo di doverne instanziare una nuova.

Una soluzione migliore potrebbe essere quella di fermare un messaggio e cominciarne uno nuovo. Questo può essere fatto con il frame RST_STREAM di http2 che aiuterà a prevenire sprechi di banda e la necessità di interrompere connessioni già avviate.

## 6.7. Server push

QUesta funzionalità è anche chiamata "cache push". L'idea è che se il client chiede la risorsa X, il server potrebbe già sapere che il client probabilmente avrà bisogno anche della risorsa Z, e fornirla al client prima della richiesta. Questo aiuterà il client a metterla in cache, pronta per usarla quando ne avrà bisogno.

Server push è qualcosa che il client deve esplicitatamente permettere al server di fare. Anche a quel punto, il client può tranquillamente terminare uno stream forzato (pushed stream) in qualsiasi momento attraverso il frame RST_STREAM in caso non volesse una particolare risorsa.

## 6.8. Cotrollo del flusso

Ogni singolo flusso http2 ha le sue finestre di flusso (flow window) in cui all'altra estremità è permesso inviare dati. Se conosci SSH, questo concetto è molto simile in spirito e stile.

Per ogni flusso, entrambi i partecipanti devono avvertire di avere abbastanza spazio per ricere i dati in arrivo, mentre all'altra estremità è consentito inviare tanti dati quanto è estesa questa finestra. Solo i frame DATA sono soggetti a questo controllo del flusso.
