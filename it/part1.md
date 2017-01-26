# 1. Background

Questo documento descrive http2 sia a livello di protocollo che tecnico. È cominciato come una presentazione che Daniel ha fatto a Stoccolma nell'Aprile del 2014, presentazione che poi è stata convertita ed estesa in un document completo co tutti i dettagli e le spiegazioni.

RFC 7540 è il nome ufficiale delle specifiche finali di http2 ed è stato pubblicato il 15 Maggio 2015: http://www.rfc-editor.org/rfc/rfc7540.txt

Tutti gli errori in questo documento sono miei e sono il risultato di mancanze. Grazie per ogni segnalazione affinchè possa correggere il documento nell'aggiornamento successivo.

In questo documento ho provato ad usare costantemente la parola "http2" per descrivere il nuovo protocollo mentre in termini tecnici il nome esatto è HTTP/2. Ho fatto questa decisione per una più facile leggibilità e fluidità dei discorsi.

## 1.1 Autore

Il mio nome è Daniel Stenberg e lavoro per Mozilla. Ho lavorato per l'open source e  il networking per oltre 20 anni in numerosi progetti. Probabilmente sono meglio conosciuto per essere lo sviluppatore in capo ai progetti vurl e libcurl. Ho fatto parte del gruppo di lavoro di IETF HTTPbis pe diversi anni grazie al quale ho tenuto aggiornato il protocllo HTTP 1.1 oltre ad aver partecipato alla standardizzazione di http2.

  Email: daniel@haxx.se

  Twitter: [@bagder](https://twitter.com/bagder)

  Web: [daniel.haxx.se](http://daniel.haxx.se/)

  Blog: [daniel.haxx.se/blog](http://daniel.haxx.se/blog/)

## 1.2 Aiuto!

Se dovessi incorrere in errori, omisioni, disguidi o bugie eclatanti in questo documento, per favore fammi pervenire una copia aggiornata del paragrafo in oggetto e io provvederò a correggerlo. Darò credito a tutte le persone che aiuteranno! Spero di rendere questo documento migliore con il passare del tempo.  

QUesto documento è disponibile qui. [http://daniel.haxx.se/http2](http://daniel.haxx.se/http2)

## 1.3 License

<img style="float: right;" src="https://raw.githubusercontent.com/bagder/http2-explained/master/images/creative-commons.png" />

Questo documento è coperto dalla licensa Creative Commons Attribution 4.0: http://creativecommons.org/licenses/by/4.0/

## 1.4 Storia del documento

La prima versione del documento è stata pubblicata il 25 Aprile 2014. Qui sotto sono elencati i cambiamenti dell versioni più recenti.

### Versione 1.13

- Conversione della versione principale del documento al formato Markdown
- 13: Più risorse menzionate, collegamenti e descrizioni aggiornati
- 12: Aggiornata la descrizione del QUIC con riferimenti alla bozza
- 8.5: Aggiornamento con numeri correnti
- 3.4: La media adesso è 40 connessioni TCP
- 6.4: Aggiornato per allineare con le specifiche

### Version 1.12

- 1.1: HTTP/2 è adesso un RFC ufficiale
- 6.5.1: Collegamento al HPACK RFC
- 9.1: Menzione al cambiamento di configurazione per Firefox 36+
- 12.1: Aggiunta la sezione rigurdante il QUIC

### Version 1.11

- Numerosi miglioramenti di stile linguistico grazie ai contributori
- 8.3.1: Menzione agli sviluppi specifici di httpd per Apache e nginx

### Version 1.10

- 1: Il protocollo è in stato “sembra ok"
- 4.1: Aggiornamenti di carattere linguistico
- Facciata: Aggiunta immagine e chiamata “http2 explained”, collegamento corretto
- 1.4: Aggiunta la sezione sulla storia del documento
- Correzione errori ortografici
- 14: Aggiunto ringraziamento ai segnalatori di bug
- 2.4: Migliori etichette per il grafico sulla crescita di HTTP
- 6.3: Corretto l'ordine dei vaogni nel treno 'multiplexato'
- 6.5.1: HPACK draft-12

### Version 1.9

- Aggiornamento a HTTP/2 draft-17 e HPACK draft-11  
- Aggiunta della sezione "10. http2 in Chromium"
- Correzioni errori ortografici  
- Arrivati a 300 implementazioni
- 8.5: Aggiunte le cifre d'utilizzo
- 8.3: Riferimenti anche a Internet Explorer  
- 8.3.1 Aggiunto "implementazioni mancanti"  
- 8.4.3: RIferimento al fatto che TLS incrementa le percentuali di successo
