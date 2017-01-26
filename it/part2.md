# 2. HTTP Oggi

Il protocollo HTTP 1.1 è usato virtualmente per tutto su Internet. Sono stati fatti grandi investimenti su protocolli e infrastrutture che potessero trarre vantaggio da questo, fino al punto che ormai oggi è spesso più facile produrre basandosi su HTTP piuttosto che creare qualcosa partendo da zero.

## 2.1 HTTP 1.1 è enorme

Quando HTTP fu creato fu recepito come un protocollo semplice e lineare, ma il tempo ci ha dimostrato quanto ciò sia falso. La specifica HTTP 1.0 RFC 1945 rilasciata nel 1996 è solamente 60 pagine. La specifica RFC 2616, che descrive HTTP 1.1, fu pubblicata solamente 3 anni più tardi ed era già cresciuta a 176 pagine. In più, quando all' IETF lavorammo sull'aggiornamento dell' RFC 2616, quest'utlimo fu diviso e convertito in 6 documenti, con un totale di pagine molto più alto (RFC 7230 e famiglia fu il risultato di questo aggiornamento). HTTP 1.1 è enorme e contiene una miriade di dettagli, sottigliezze e, non meno importanti, sezioni opzionali.

## 2.2 Un mondo di opzioni

Per sua natura, HTTP 1.1 racchiude piccoli dettagli e opzioni disponibili per espansioni future. Questo ha portato a farlo diventare un ecosistema per cui nessuna implementazione si preoccupa di riprodurre tutto - e non è nemmeno realmente possibile descrivere cosa sia questo 'tutto'. Questo ha portato ad una situazione dove le caratteristiche che erano inizialmente poco usate hanno visto poche implementazioni, e coloro che avevano implementato tali caratteristiche non ne vedano un futuro.

Più tardi questo ha causato dei problemi di interoperabilità quando utenti finali e server hanno cominciato ad aumentare l'utilizzo di tali features.

## 2.3 L'uso inadeguato del TCP

HTTP 1.1 ha difficoltà ad sfruttare a pieno vantaggio tutta la potenza e le performance che il protocollo TCP ha da offrire. I client HTTP e i browser devono impiegare tutta la loro creatività per trovare soluzioni che decrementino i tempi di caricamento delle pagine.

Altri tentativi che sono stati fatti in parallelo negli anni hanno altresì confermato che TCP non è così semplicemente sostituibile e perciò continuiamo a lavorare e migliorare sia TCP che i protocolli che lo sfruttano.

TCP potrebbe essere utilizzato meglio per evitare pause o sprechi di tempo che potrebbero essere stati impiegati per inviare o ricevere più informazioni. La prossima sezione metterà in luce alcune di queste mancanze.

## 2.4 Dimensioni di trasferimento e numero di oggetti

Quando osserviamo la tendenza per alcuni dei siti più popolari del web e quanto impieghiamo per scaricare le loro pagine principali, possiamo notare un chiaro modello che ne emerge. Negli anni la quantità di dati che deve essere recuperata è gradualmente cresciuta fino e oltre 1.9MB. Cosa è più importante in questo contesto è che oltre un centinaio di risorse sono necessare per mostrare ogni pagina.

Come mostrato dal grafico sottostante, questa tendenza continua nella sua crescita e non abbiamo nessuna informazione sugli sviluppi futuri. In verde è mostrata la crescita dei dati da trasferire mentre in rosso la quantità di risorse richieste in media per servire le pagine dei siti web più popolari nel mondo, e come sono cambiate negli ultimi 4 anni.

![transfer size growth](https://raw.githubusercontent.com/bagder/http2-explained/master/images/transfer-size-growth.png)

## 2.5 La latenza uccide

<img style="float: right;" src="https://raw.githubusercontent.com/bagder/http2-explained/master/images/page-load-time-rtt-decreases.png" />

HTTP 1.1 è molto sensibile alla latenza, parzialmente anche perché HTTP Pipeling mostra ancora problematiche a tal punto che risulta disattivato dalla maggior parte degli utilizzatori.

Mentre abiamo visto un gran cambiamento nella banda disponibile agli utenti negli ultimi anni, non è stato possibile osservare lo stesso cambiamento nella riduzione delle latenze. Le latenze alte sono dannose e, come per esempio a molte delle tecnologie mobili correnti, rendono molto difficile usufruire di un'esperienza di navigazione veloce anche in presenza di connessioni a banda larga.

Un altro utilizzo che ha bisogno di basse latenze è un certo tipo di trasmissione video, come quello delle videoconferenze, giochi e simili, dove non è possibile generare in anticipo un flusso di dati da spedire.

## 2.6. Head of line blocking

HTTP Pipelining è un modo di servire un'altra richiesta mentre attendiamo la risposta da una precedente. Può essere assimilabile alle code che si generano allo sportello della banca o in un super market. Non si può sapere quali sono le persone davanti in coda che saranno più celeri o chi sarà quello 'noioso' che impiegherà tanto tempo a farsi servire: questa persona può essere chiamata "head of line blocking".

<img style="float: right;" src="https://raw.githubusercontent.com/bagder/http2-explained/master/images/head-of-line-blocking.jpg" />

Sicuramente si può fare attenzione nello scegliere la coda così da accordasi in quella che crediamo la migliore e a volte è concesso crearne una nuova, ma alla fine non si può evitare di fare una decisione e una volta che è stata fatta non si può più cambiare coda.

Creare una nuova coda è associato a delle penalizzazioni in termini di performance e risorse e quindi non è realmente scalabile oltre un numero esiguo di linee. Semplicemente non c'è una soluzione perfetta a ciò.

Tutt'oggi, 2015, molti browser sono distribuiti con HTTP Pipeling disattivata.

Letture aggiuntive su questo argomento possono essere trovare anche nel [bugzilla entry 264354](https://bugzilla.mozilla.org/show_bug.cgi?id=264354) di Firefox.
