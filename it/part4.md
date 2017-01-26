# 4. Aggiornando HTTP

E se potessimo creare un protocollo migliore? Innanzitutto dovrebbe...

1. Essere meno sensibile alle latenze
2. Correggere gli errori di pipeling e di "head of the line blocking"
3. Eliminare il bisogno di continuare a incrementare il numero di connessioni per ogni host
4. Mantenere tutte le interfacce esistenti, tutti i contenuti, il formato delle URI e gli schemi
5. Essere realizzato all'interno del gruppo di lavoro di HTTPbis di IETF

## 4.1. IETF e il gruppo di lavoro HTTPbis

Internet Engineering Task Force (IETF) è un'organizzazione che sviluppa e promuove gli standard di internet, principalmente protocolli. Sono maggiormente conosciuti per la serie di specifiche RFC, che documentano tutto, come TCP, DNS, FTP, best practices, HTTP a anche numerose varianti che non hanno mai preso vita.

All'interno di IETF, gruppi di lavoro dedicati sono formati allo scopo di lavorare per un obiettivo ben definito. Viene creato un 'documento costitutivo' come qualche linea guida e limitazione su quello che dovrà essere prodotto.

Il gruppo di lavoro su HTTPbis (a seguire una spiegazione del nome) fu formato durante l'estate del 2007 e gli fu richiesto di create un aggiornamento della specifica HTTP 1.1. Ma una vera discussione riguardo questa nuova versione non cominciò realmente se non verso la fine del 2012. I lavori di aggiornamento verrono poi completati nella prima parte del 2014 e vennero tarascritti nella serie di specifiche [RFC 7230](https://tools.ietf.org/html/rfc7230).

La riunione finale di interoperabilità per il gruppo di lavoro sull'HTTPbis fu tenuta a New York a Giugno 2014. Le discussioni rimanenti e le procedure IETF per ottenere l'RFC finale hanno continuato a svolgersi fino all'anno successivo.

Alcuni dei principali attori nel campo dell' HTTP sono mancati alle discussioni e agli incontri del gruppo di lavoro. Non vorrei nominare nessuna compagnia o prodotto in particolare, ma evidentemente qualcuno è abbastanza confidente che IETF farà un buon lavoro senza il bisogno di coinvolgere queste compagnie direttamente...

### 4.1.1. Il "bis" nel nome

Il gruppo è stato chiamato HTTPbis, dove "bis" si riferisce all' [avverbio latino 'due volte'](https://it.wiktionary.org/wiki/bis#Etimologia_.2F_Derivazione).  Bis è comunemente usato come un suffisso o una parte di nome dall' IETF per un aggiornamento o una rivisitazione fatta su una specifica; in questo caso, l'aggiornamento di HTTP 1.1.

## 4.2. http2 proviene da SPDY

[SPDY](http://en.wikipedia.org/wiki/SPDY) è un protocollo sviluppato apertamente da Google. Hanno chiesto a chiunque di partecipare alla realizzazione di questo progetto, ma era ovvio che avrebbero beneficiato dall'essere in controllo sia di uno dei browser più popolari che un numero di significativo di server molto utilizzati per i loro servizi.

Quando il gruppo HTTPbis decise che era tempo di iniziare a lavorare su http2, SPDY aveva già dimostrato che era un concetto funzionante. Aveva mostrato che era possibile dispiegarlo su Internet e i numeri provavano quanto bene si stesse comportando. Il lavoro su http2 cominciò basandoni sulla bozza di SPDY/3 che fu di fatto incorporata in http2 draft-00 con un po' di 'cerca e sostituisci'.
