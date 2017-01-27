# 7. Estensioni

Il protocollo http2 impone che un ricevente legga ed ignori qualsiasi frame che non conosce (cioè quelli con un tipo frame sconosciuto). Le due parti possono negozionare un nuovo tipo di frame in maniera unitaria, ma ad essi non sarà permesso cambiare stato nè potranno subire il controllo del flusso (flow control).

Questo tema - cioè se http2 dovesse o meno supportare estioni - è stato oggetto di dibattito a lungo durante lo sviluppo del protocollo con opinioni che tendevano sia a favore che contro. Dopo il draft-12 alla fine è stato deciso di permetterle.

Le estensioni non fanno parte del protocollo attuale ma saranno documentate all'esterno delle specifiche base dello stesso. Ci sono già due tipi di frame la cui inclusione è stata discussa e che probabilmente saranno i primi frame ad essere inviati come estensioni. Li descriverò qua sia per la loro popolarità sia per il loro precedente stato di frame "nativi":

## 7.1. Servizi Alternativi

Con l'adozione di http2, c'erano ragioni di sospettare che le connessioni TCP sarebbero diventate più leggere e tenute aperte per molto più a lungo di quelle HTPP 1.x. Un client dovrebbe quindi poter essere capace di 'fare' il più possibile con una singola connessione per host/sito, e questa connessione potrebbe rimanere aperta per un bel po' di tempo.

Questo influenzerà il lavoro dei bilanciatori di carico (load balancer) e potrebbe sorgere la situazione in cui il sito possa suggerire al client di connettersi ad un altro host. Potrebbe essere per ragioni di prestazioni, perchè un sito deve essere spento per manutenzione, etc.

Il server in questo caso potrà mandare l'[Interstazione Alt-Svc:](http://tools.ietf.org/html/draft-ietf-httpbis-alt-svc-10) (o ALTSVC
frame con http2) per informare il client di un servizio alternativo: un altro percorso per lo stesso contenuto usando un nuovo servizio, host e porta.

Il client a questo punto può tentare di connettersi a quel servizio in maniera asincrona e usare l'alternativa solo in caso di successo nello stabilire la nuova connessione.

### 7.1.1. TLS Opportunistico

L'intestazione Alt-Svc permette a un server che fornisce contenuti tramite http:// di informare il client che lo stesso contenuto è disponibile anche tramite una connessione TLS.

Questa è una caratteristica discutibile. Questa connessione potrebbe realizzare TLS non autenticato a non sarebbe pubblicizzata come "sicura" da nessuna parte, non mostrerebbe nessun lucchetto nell'interfaccia grafica (UI), ed infatti non esiste modo per informare l'utente che non è più semplice e vecchio HTTP, ma è TLS opportunistico e molte persone sono contro contrarie a concetto.

## 7.2. Bloccato

Un frame di questo tipo è pnsato per essere spedito esattamente una volta da uno dei due attori coinvolti nel protocollo http2, e cioè quando sono presenti dati da spedire ma il controllo del flusso impedisce di inviare qualsiasi informazione. L'idea è che se la tua implementazione riceve questo frame potrai accorgerti di aver sbagliato qualcosa e/o non stai raggiungendo la velocità di trasmissione migliore.

Una citazione da draft-12, prima che questo frame fosse ritirato per diventare un'estensione:

> "Il frame BLOCKED è incluso in questo draft per facilitare le sperimentazioni. Se il risultato di questi esperimenti non dovessero fornire i risultati sperati, potrebbe essere rimosso"
