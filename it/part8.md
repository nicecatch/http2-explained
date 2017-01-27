# 8. Un mondo http2

Come saranno le cose quando http2 verrà adottato? Verrà effettivamente adottato?

## 8.1. Come http2 influenzerà le persone ordinarie?

http2 non è nè largamente distribuito nè usato. Non possiamo dire con sicurezza come andranno le cose. Abbiamo visto come SPDY è stato usato e possiamo fare qualche calcolo e previsione basandoci su quello e esperimenti passati e futuri.

http2 riduce il numero di round-trip necessari ed elimina completamente il dilemma dell' "head of line blocking" con il multiplexing e lo scarto istantaneo di flussi non voluti.

Permette un gran numero di flussi paralleli, parallelizzazione che supera anche i siti di oggi che sfruttano maggiormente lo sharding.

Con le priorità usate opportunamente nei flussi, ci sono buone probabilità che i client ottengano prima le informazioni importanti. E contando tutto questo, potrei dire che questo porterà ad un caricamento più veloce delle pagine e siti web più responsivi. In poche parole: un'esperienza di navigazione migliore.

Quanto più veloce o quanti miglioramenti vedremo è difficile da dire. Primo, la tecnologia non è ancora completamente matura e secondo non abbiamo ancora nemmeno visto client e serber con implementazioni che tantno di trarre vantaggio di tutti i punti di forza che questo protocollo ha da offrire.

## 8.2. Come http2 influenzerà lo sviluppo web?

Negli anni gli svilppatori web e gli ambienti di sviluppo orientati al web hanno accumulato trucchi e strumenti per aggirare i problemi indotti da HTTP 1.1, come quelli che ho elencato all'inizio di questo documento.

Tante di queste alternative che strumenti e sviluppatori adesso usano di serie e senza pensarci potrebbe addirittura compromettere le prestazioni di http2 o al massimo non trarre vantaggio dei super poteri di hhtp2. Spriting e inlining non dovrebbero essere fatti con http2. Lo sharding potrebbe essere svantaggioso per http2 che beneficierà di meno connessioni.

Un problema è certamente che i siti e gli sviluppatori web avranno bisogno di realizzare e distribuire per un mondo che, almeno nel breve periodo, sarà comopsto sia da client HTTP 1.1 che http2 rendendo più impegnativo fornire ad entrambi le prestazioni migliori senza dover offire due diverse interfacce.

Per questa ragione sospetto che passerà del tempo prima di poter arrivare a veder sfruttate tutte le potenzialità di http2.

## 8.3. Implementazioni http2

Provare a documentare le specifiche implementazioni in un testo come questo è completamente futile e condananto a peccare di vecchiaia in tempi relativamente brevi. Piuttosto spiegherò la situazione in termini più ampi e suggerisco la lettura della [lista delle implementazioni](https://github.com/http2/http2-spec/wiki/Implementations) sul sito web di http2.

Era già presente un cospicuo numero di implementazioni già dalle fasi iniziali, numero che è cresciuto nel tempo durante il lavoro su http2. Al momento ci sono più di 40 implementazioni, e molte di esse realizzano la versione finale.

### 8.3.1 Browsers

Firefox è stato il brower in cima alla lista dell'avanzamento tecnologico., Twitter ha mantenuto e offerto i suoi servizi tramite http2. Google ha cominciato dall'Aprile del 2014 ad offrire supporto ad http2 su qualche server di prova e a cominciare da Maggio 2014 hanno offerto supporto ad http2 sulle versioni di sviluppo di Chrome. Microsoft ha mostrato una preview tecnica con supporto a http2 per la prossima versione di Internet Explorer. Safari (con iOS 9 e Mac Os El Capitan) e Opera hanno affermato che supporteranno http2.

### 8.3.2 I server

Esistono già diverse implementazioni di http2.

Nginx offre suporto a http2 dalla versione
[1.9.5](https://www.nginx.com/blog/nginx-1-9-5/) rilasciata il 22 Settembre 2015 (dove sostituisce il modulo SPDY, così che non possono essere eseguiti nella stessa istanza).

Il server httpd di Apache ha supporto ad http2 tramite [mod_http2](https://httpd.apache.org/docs/2.4/mod/mod_http2.html) a partire dalla versione 2.4.17 rialasciata il 9 Ottobre 2015.

[H2O](https://h2o.examp1e.net/), [Apache Traffic
Server](http://trafficserver.apache.org/), [nghttp2](https://nghttp2.org/),
[Caddy](http://caddyserver.com/) e
[LiteSpeed](https://www.litespeedtech.com/products/litespeed-web-server/overview)
hanno rilasciato tutti quanti server con supporto http2.

### 8.3.3 Altri

curl e libcurl supportano sia http2 insicuro che attraverso TLS tramite diverse librerie TLS.

Wireshark supporta http2. Lo strumento perfetto per analizzare il traffico http2.

## 8.4. Critiche comuni a http2

Durante lo sviluppo di questo protocollo il dibattito si è susseguito e molte persone sostengono che http2 è nato completamente sbagliato. Vorrei menzionare qualcuna delle più comuni critiche che vengono sollevate contro esso:

### 8.4.1. “Il protocollo è stato pensato e/o fatto da Google”

Ci sono anche variazioni che sostengono che il mondo sarà sempre più controllato da Google. Questo non è vero. Il protocollo è stato sviluppato all'interno di IETF nella stessa maniera con cui sono stati sviluppati tutti gli altri protocolli negli ultimi 30 anni. Comunque, tutti riconosciamo e ammettiamo l'enorme lavoro svolto da Google con SPDY che non solo ha dimostrato la possibilità di distribuire un nuovo protocollo ma ha anche mostrato i guadagnani che sarebbero pervenuti.

Google ha pubblicamente [annunciato](https://blog.chromium.org/2015/02/hello-http2-goodbye-spdy.html) che toglieranno il supporto a SPDY e NPN da Chrome nel 2016 e che si impegneranno a migrare i server a http2.

### 8.4.2. “Il protocollo è utile solo per i browser”

Questo è parzialmente vero. Uno dei conduttori principali dietro lo sviluppo di http2 è la correzione di HTTP pipeling. Se per il tuo caso d'uso originalmente non c'era nessun bisogno di essa, probabilmente l'utilizzo http2 non apporterà grande migliorie. Non è stato l'unico progresso nel protocollo ma sicuramente è uno tra i maggiori.

Appena i servizi realizzeranno quali sono le vere potenzialità dei flussi 'multiplexati' su singola connessione, suppongo che vedremo molte più applicazioni usare http2.

Le API REST più piccole e utilizzi più programmatici di HTTP 1.x non vedranno i grandi benefici apportati da http2. Ma comunque ci saranno pochi aspetti negativi con http2 per la maggior parte degli utenti.

### 8.4.3. “Il protocollo è utile unicamente per i grandi siti”

Niente affatto. Le capacità di multiplexing aiuteranno l'esperienza per connessioni ad alta latenza che solitamente i piccoli siti senza ridondanza geografica offrono. I grandi siti sono spesso meglio distribuiti e offrono round-trip più corti e veloci agli utenti.

### 8.4.4. “L'uso di TLS lo rende più lento”

Questo può essere vero fino ad un certo punto. L'handshake TLS aggiunge sicuramente qualcosa, ma ci sono sforzi continui nel ridurre il numero di round-trip necessai anche per TLS.  See for example [istlsfastyet.com](https://istlsfastyet.com/)
for one source of info.

Telecom and other network operators, for example in the ATIS Open Web
Alliance, say that they [need unencrypted
traffic](http://www.atis.org/openweballiance/docs/OWAKickoffSlides051414.pdf)
to offer caching, compression and other techniques necessary to provide a fast
web experience over satellite, in airplanes and similar.  http2 does not make
TLS use mandatory so we shouldn't conflate the terms.

Many Internet users have expressed a preference for TLS to be used more widely and we should help to protect users' privacy.

Experiments have also shown that by using TLS, there is a higher degree of success than when implementing new plain-text protocols over port 80 as there are just too many middle boxes out in the world that interfere with what they would think is HTTP 1.1 if it goes over port 80 and might look like HTTP at times.

Finally, thanks to http2's multiplexed streams over a single connection, normal browser use cases still could end up doing substantially fewer TLS handshakes and thus perform faster than HTTPS would when still using HTTP 1.1.

### 8.4.5. “Not being ASCII is a deal-breaker”

Yes, we like being able to see protocols in the clear since it makes debugging and tracing easier. But text based protocols are also more error prone and open up for much more parsing and parsing problems.

If you really can't take a binary protocol, then you couldn't handle TLS and compression in HTTP 1.x either and its been there and used for a very long time.

### 8.4.6. “It isn't any faster than HTTP/1.1”

This is of course subject to debate and discussions on how to measure what faster means, but already in the SPDY days many tests were made that proved faster browser page loads (like ["How Speedy is SPDY?"](https://www.usenix.org/system/files/conference/nsdi14/nsdi14-paper-wang_xiao_sophia.pdf) by people at University of Washington and ["Evaluating the Performance of SPDY-enabled Web Servers"](http://www.neotys.com/blog/performance-of-spdy-enabled-web-servers) by Hervé Servy) and such experiments have been repeated with http2 as well. I'm looking forward to seeing more such tests and experiments getting published. A [basic first test made by httpwatch.com](http://blog.httpwatch.com/2015/01/16/a-simple-performance-comparison-of-https-spdy-and-http2) might imply that HTTP/2 holds its promises.

### 8.4.7. “It has layering violations”

Seriously, that's your argument? Layers are not holy untouchable pillars of a global religion and if we've crossed into a few gray areas when making http2 it has been in the interest of making a good and effective protocol within the given constraints.

### 8.4.8. “It doesn't fix several HTTP/1.1 shortcomings”

That's true. With the specific goal of maintaining HTTP/1.1 paradigms there were several old HTTP features that had to remain. Such as the common headers that also include the often dreaded cookies, authorization headers and more. But by the upside of maintaining these paradigms is that we got a protocol that is possible to deploy without an inconceivable amount of upgrade work that requires fundamental parts to be completely replaced or rewritten. Http2 is basically just a new framing layer.

## 8.5. Will http2 become widely deployed?

It is too early to tell for sure, but I can still guess and estimate and that's what I'll do here.

The naysayers will say “look at how good IPv6 has done” as an example of a new protocol that's taken decades to just start to get widely deployed. http2 is not an IPv6 though. This is a protocol on top of TCP using the ordinary HTTP update mechanisms and port numbers and TLS etc. It will not require most routers or firewalls to change at all.

Google proved to the world with their SPDY work that a new protocol like this can be deployed and used by browsers and services with multiple implementations in a fairly short amount of time. While the amount of servers on the Internet that offer SPDY today is in the 1% range, the amount of data those servers deal with is much larger. Some of the absolutely most popular web sites today offer SPDY.

http2, based on the same basic paradigms as SPDY, I would say is likely to be deployed even more since it is an IETF protocol. SPDY deployment was always held back a bit by the “it is a Google protocol” stigma.

There are several big browsers behind the roll-out. Representatives from Firefox, Chrome, Safari, Internet Explorer and Opera have expressed they will ship http2 capable browsers and they have shown working implementations.

There are several big server operators that are likely to offer http2 soon, including Google, Twitter and Facebook and we hope to see http2 support soon get added to popular server implementations such as the Apache HTTP Server and nginx. H2o is a new blazingly fast HTTP server with http2 support that shows potential.

Some of the biggest proxy vendors, including HAProxy, Squid and Varnish have expressed their intentions to support http2.

All through-out 2015, the amount of traffic that is http2 has been increasing. In early September, Firefox 40 usage was at 13% out of all HTTP traffic and 27% out of all HTTPS traffic, while Google see roughly 18% incoming HTTP/2. It should be noted that Google runs other new protocol experiments as well (see QUIC in 12.1) which makes the http2 usage levels lower than it could otherwise be.
