# 3. Cosa è stato fatto per risolvere i problemi di latenza

Ogni volta che si incontrano dei problemi, le persone si riuniscono per cercare delle soluzioni. Alcune di queste sono intelligenti e utili, altre terribilmente maldestre.

## 3.1 Spriting
<img style="float: right;" src="https://raw.githubusercontent.com/bagder/http2-explained/master/images/spriting.jpg" />

Spriting è il termine usato per indicare un insieme di immagini di piccole dimensioni messe assieme per formare una singola immagine più grande. Poi attraverso l'utilizzo di Javascript o dei CSS esse vengono ritagliate nuovamente per poter essere utilizzate singolarmente.

Un sito può usare questo trucco in favore della velocità. In HTTP 1.1 recuperare una singola immagine è molto più veloce che doverne prelevare 100 più piccole.

Purtroppo però questo approccio ha i propri difetti. Infatti le pagine che hanno bisogno di mostrare solo poche immagini devono comunque scaricarle tutte. Così come la rimozione dalla memoria cache impone la cancellazione di tutte quante, senza poter lasciare le più usate.

## 3.2 Inlining

Inlining (in linea, in italiano) è un altro trucco utile per evitare di inviare singole immagini ed è realizzato usando data: URL incorporati nei CSS. Questo ha benefici e difetti simili allo spriting.

    .icon1 {
        background: url(data:image/png;base64,<data>) no-repeat;
    }

    .icon2 {
        background: url(data:image/png;base64,<data>) no-repeat;
    }


## 3.3 Concatenazione

Un sito di grandi dimensioni può ritrovarsi a dover servire differenti file Javascript. Strumenti appositi possono aiutare lo sviluppatore a unire tutti questi file in uno singolo così da facilitare il lavoro del browser che al caricamento della pagina debba scaricare un unico script piuttosto che dozzine di piccoli file. Così facendo però troppi dati sono scambiai quando è richiesta una minima parte del file intero. E troppi dati devono essere reinviati quando è apportato un minimo cambiamento.

Questa pratica è principalmente un inconveniente per lo sviluppatore coinvolto.

## 3.4 Sharding

L'ultimo trucco di cui parlerò è chiamato "sharding". Praticamente significa fornire il contenuto del tuo servizio su quanti più host possibili. A prima occhiata può sembrare strano, ma c'è una valida ragione dietro a questo.

Inizialmente le specifiche HTTP 1.1 definivano che ad un utente fosse permesso di usare al massimo due connessioni TCP per ogni host. Così, per non violare questa imposizione, i siti più intelligenti hanno semplicemente inventato nuovi hostname e così - voilà - attraverso lo sharding possono essere iniziate più connessioni in favore di una riduzione dei tempi di caricamento.

Con il passare del tempo questa limitazione è stata rimossa e gli utenti oggi raggiungono facilmente 6-8 connessioni per hostname; nonostante tutto continua ad essere presente un limite, rendendo l'utilizzo di questa tecnica necessario. Così come il numero di oggetti sta sempre più aumentando - come ho già mostrato - questo ampio numero di connessioni serve soprattutto a essere sicuri che HTTP operi bene e renda il sito veloce. Non è inusuale per i siti di usare 50 o addirittura fino a 100 connessioni per attraverso l'utilizzo di questa tecnica. Recentemente alcune statistiche di httparchive.org hanno mostrato come i top 300k URL nel mondo hanno bisogno in media di 40(!) connessioni TCP per mostrare il sito e questa tendenza sta aumentando pian piano con il passare del tempo.

Un'altra ragione è anche mettere imamgini o risorse simili su un host separato che non usa cookie poichè la dimensione di questi ultimi in questi giorni può essere piuttosto significativa. Mettere a disposizioni immagini che non necessitano di cookie e che quindi permettono richieste HTTP più leggere può spesso aumentare le prestazioni.

L'immagine sottostante mostra il packet trace di una navigazione in uno dei più importanti siti svedesi e come le richieste sono distribuite su diversi hostname.

![image sharding at expressen.se](https://raw.githubusercontent.com/bagder/http2-explained/master/images/expressen-sharding.jpg)
