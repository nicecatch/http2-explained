# 5. Concetti http2

Quindi, http2 cosa è capace di realizzare? Quali sono i limiti imposti per il gruppo HTTPbis?

I limiti erano in effetti alquanto restrittivi e hanno messo diversi paletti all'abilità di innovazione del team.

- http2 deve mantenere i paradigmi HTTP. Rimane un protocollo dove l'utente manda una richiesta al server tramite TCP.

- Gli URL http:// e https:// non possono essere cambiati. Non ci possono essere nuovi schemi per questo. Il numero di contenuti distribuiti con questi URL è troppo alto per aspettarci di poterli cambiare tutti quanti

- Server e client HTTP1 saranno presenti ancora per decenni, dobbiamo essere in grado di delegarli ('proxarli') dietro a dei server http2

- Di conseguenza i proxy devono essere in grado di associare con relazioni 1:1 le caratteristiche http2 con quelle degli utenti HTTP 1.1

- Rimuovere o ridurre le parti opzionali del protocollo. Questo non era realmente una richiesta ma più un mantra proveninente da SPDY e il team Google. Assicurandosi che tutto fosse obbligatorio non c'è alcuna possibilità che alcune implementazioni possano omettere qualcosa della specifica cadendo poi in trappola in un futuro prossimo

- Mai più versioni minori. I client e server http2 o sono o non sono pienamente compatibili con http2. Se si dovesse presentare un bisogno di estendere il protocollo o modificare qualcosa, allora nascerà http3

## 5.1. http2 per gli schemi URI esistenti

Come già menzionato, gli schemi URI esistenti non possono essere modificati, quindi http2 dovrà adattarsi ad essi. Dato che sono già usati per HTTP 1.x, dobbiamo trovare un modo per aggiornare il protocollo ad http2 o altrimenti chiedere al server di usare http2 invece che protocolli più vecchi.

HTTP 1.1 agisce definita, cioè utilizzando l'intestazione 'Upgrade:' che permette al server di rispondere ad un vecchio protocollo usando il nuovo, al costo però di 'andate-ritorni' (TCP round-trip) addizionali.

Il costo di questi round-trip addizionali non è un qualcosa che il team di SPDY era disposto a sopportare, e siccome SPDY era stato pensato unicamente per essere usato tramite TLS, hanno sviluppato una nuova estensione TLS che alleggerisce sensibilmente le negoziazioni. Con questa estensione, chiamata NPN (Next Protocol Negotiation), il server può informare il client dei protocolli conosciuti cosicché il client possa usare quello che preferisce.

## 5.2. http2 per https://

Un'attenzione particolare riguardo http2 è stata spesa per fare in modo che quest'ultimo funzionasse adeguatamente con TLS. SPDY richiede obbligatoriamente TLS e ci sono state spinte in questa direzione anche per http2, anche se poi è stato deciso di distribuire http2 con TLS opzionale. Comunque, due importanti personalità hanno esplicitamente dichiarato che distribuiranno il loro software http2 solo con TLS mandatorio; queste due figure sono le persone a capo di Mozilla Firefox e di Google Chrome, due dei più importanti browser al giorno d'oggi.

Le ragioni che ruoutano attorno a questa scelta includono il rispetto per la privacy dell'utente finale e la natura di alcune rilevazioni che mostrano come i nuovi protocolli hanno più successo quando distribuiti con TLS. Questo a causa della supposizione che qualsiasi cosa passi attraverso la porta 80 sia HTTP 1.1, rendendo così inutili alcuni intermiediari che distruggono o interferiscono il traffico proveniente da tale porta che sia inviato con protocolli diversi da, appunto, HTTP1.

Questo argomento ha scaturito agitazioni nelle mailing-list e negli incontri - è giusto o sbagliato? La questione è controversa - attento a sollevare l'argomento in presenza di un membro del gruppo HTTPbis!

Similmente c'è stato un lungo e feroce dibattito al riguardo del fatto degli algoritmi di cifratura quando http2 viene utilizzato con TLS. Deve supportarne un insieme ristretto? Vietare l'utilizzo di alcuni esplicitandoli in una lista nera? O lasciare la decisione al gruppo di persone che lavora dietro TLS? Alla fine la specifica sancisce che TLS deve essere almeno alla versione 1.2 e limita l'utilizzo di alcuni algoritmi di cifratura.

## 5.3. Negoziazioni http2 con TLS

Next Protocol Negotiation (NPN) è il protocollo che SPDY usa per negoziare con i server TLS. Siccome non era un protocollo standard, dopo essere stato inglobato dall'IETF ha assunto un altro nome: ALPN (Application Layer Protocol Negotiation). L'utilizzo di ALPN è promosso con http2, mentre client e server SPDY tutt'ora usano NPN.

Il fatto che NPN esistesse già da prima e che ALPN ha impiegato un po' ad essere standardizzato, ha portato molti client e server http2 ad implementare e usare entrambe le estensioni. Inoltre molti server offrono sia SPDY che http2, quindi il fatto che siano supportati NPN e ALPN è perfettamente ragionevole.

ALPN si differenzia da NPN principalmente su chi deve prendere la decisione riguardo al protocollo da usare. Con ALPN il cliente informa il server con una lista di protocolli in ordine di preferenza e il server usa quello che ritiene necessario, mentre con NPN è il client ad avere l'ultima parola.

## 5.4. http2 per http://

Come già menzionato, con le tramissioni in chiaro attraverso HTTP 1.1, http2 presenta al server l'header 'Upgrade:'. Se il server conosce http2 risponderà con uno stato "101 Switching" e da quel momento in poi userà http2 per la trasmissione. Questa procedura di upgrade costa un'intero viaggio round-trip, ma il vantaggio è che è possibile mantenere una connessione http2 aperta molto più a lungo e usarla più di una tipica connessione HTTP1.

Mentre i portavoce di qualche browser hanno già dichiarato che non renderanno disponibile nel proprio software questa implementazione, il team di Internet Explorer lo farà, così come curl già lo supporta.
