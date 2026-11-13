<img width="1597" height="860" alt="Schermata_20261113_150758" src="https://github.com/user-attachments/assets/9976cc31-8c81-4354-b9af-d06ed84eb332" />


# MKCaveau

**MKCaveau** è un archivio personale offline, generato come singolo file HTML, pensato per raccogliere informazioni private, documenti, codici, contatti, scadenze e procedure da consultare quando necessario. Il progetto comprende due componenti che lavorano insieme:

- **MKCaveau Maker**: l'interfaccia di preparazione e modifica dei dati. Consente di compilare il modello, importare un archivio esistente e generare un nuovo file protetto.
- **MKCaveau**: l'archivio HTML prodotto dal Maker. Include una schermata di accesso (Gate), la decifratura locale e l'interfaccia di consultazione del Caveau.

Il progetto è realizzato con HTML, CSS e JavaScript lato client. Non richiede un server applicativo, un database o un account. L'archivio generato è autonomo e può essere conservato come file HTML.

> **Avvertenza importante:** MKCaveau è progettato per trattare dati sensibili, ma un'applicazione HTML/JavaScript eseguita nel browser non equivale a un gestore di password sottoposto ad audit. Le funzioni di cifratura proteggono il contenuto del file a riposo; non rendono sicuro un dispositivo compromesso, non impediscono la copia dei dati dopo lo sblocco e non sostituiscono backup, aggiornamenti e buone pratiche operative. Leggere attentamente la sezione [Sicurezza: cosa protegge e cosa no](#sicurezza-cosa-protegge-e-cosa-no).

---

## Indice

- [Caratteristiche principali](#caratteristiche-principali)
- [Architettura e flusso di utilizzo](#architettura-e-flusso-di-utilizzo)
- [1. MKCaveau Maker](#1-mkcaveau-maker)
  - [Avvio e consenso alla licenza](#avvio-e-consenso-alla-licenza)
  - [Navigazione e gestione delle sezioni](#navigazione-e-gestione-delle-sezioni)
  - [Gestione degli oggetti](#gestione-degli-oggetti)
  - [Campi e livelli di riservatezza](#campi-e-livelli-di-riservatezza)
  - [Carta di pagamento](#carta-di-pagamento)
  - [Contatti e numeri multipli](#contatti-e-numeri-multipli)
  - [Scadenze](#scadenze)
  - [Immagini e documenti](#immagini-e-documenti)
  - [Checklist e procedure](#checklist-e-procedure)
  - [Note libere](#note-libere)
  - [Importazione](#importazione)
  - [Esportazione e cifratura](#esportazione-e-cifratura)
- [2. Gate: accesso al file protetto](#2-gate-accesso-al-file-protetto)
- [3. Caveau: consultazione dell'archivio](#3-caveau-consultazione-dellarchivio)
  - [Panoramica delle scadenze](#panoramica-delle-scadenze)
  - [Visualizzazione dei campi](#visualizzazione-dei-campi)
  - [Carte 3D](#carte-3d)
  - [Contatti e chiamate](#contatti-e-chiamate)
  - [Galleria e visualizzatore immagini](#galleria-e-visualizzatore-immagini)
  - [Checklist operative](#checklist-operative)
  - [Note nascoste](#note-nascoste)
  - [Ricerca](#ricerca)
  - [Blocco e timeout](#blocco-e-timeout)
  - [Modalità di emergenza](#modalità-di-emergenza)
- [4. Struttura dei dati](#4-struttura-dei-dati)
- [5. Sezioni incluse nel modello iniziale](#5-sezioni-incluse-nel-modello-iniziale)
- [6. Aspetto grafico e adattabilità](#6-aspetto-grafico-e-adattabilità)
- [7. Tecnologie e compatibilità](#7-tecnologie-e-compatibilità)
- [8. Sicurezza: cosa protegge e cosa no](#sicurezza-cosa-protegge-e-cosa-no)
- [9. Guida operativa consigliata](#9-guida-operativa-consigliata)
- [10. Limiti e comportamenti da conoscere](#10-limiti-e-comportamenti-da-conoscere)
- [Licenza](#licenza)

---

## Caratteristiche principali

- Archivio personale distribuito come **file HTML autonomo**.
- Preparazione e modifica dei contenuti tramite Maker.
- Modello iniziale organizzato in sezioni tematiche.
- Creazione di oggetti personalizzati all'interno delle sezioni.
- Campi personalizzati con valore e livello di riservatezza.
- Possibilità di contrassegnare i campi come segreti o segreti senza copia.
- Scheda grafica per i dati delle carte di pagamento.
- Rubrica con più numeri telefonici per contatto.
- Collegamenti `tel:` per avviare una chiamata dai dispositivi compatibili.
- Scadenze aggregate automaticamente nella Panoramica.
- Immagini incorporate nel file come Data URL/Base64.
- Galleria filtrabile per categoria.
- Visualizzatore immagini con navigazione, zoom, rotazione e trascinamento.
- Checklist operative interattive.
- Note libere, anche inizialmente nascoste.
- Ricerca testuale nel Caveau.
- Cifratura del contenuto con **AES-256-GCM** e derivazione della chiave tramite **PBKDF2-HMAC-SHA-256**.
- Salt e nonce generati casualmente tramite Web Crypto.
- Compressione GZIP facoltativa prima della cifratura, quando disponibile e conveniente.
- Gate con tentativi limitati e cooldown progressivo a intervalli.
- Modalità di emergenza per mostrare esclusivamente i contatti telefonici selezionati dal flusso di emergenza.
- Blocco automatico dopo inattività e comando di blocco manuale.
- Interfaccia responsive per desktop, tablet e smartphone.
- Nessuna richiesta di rete prevista dal funzionamento ordinario del Maker e del Caveau.

---

## Architettura e flusso di utilizzo

Il flusso previsto è il seguente:

1. Si apre la pagina che contiene MKCaveau Maker.
2. Si accetta la licenza e il disclaimer mostrati al primo avvio del Maker.
3. Si compilano le sezioni e gli oggetti con i propri dati.
4. Se si dispone già di un archivio esportato, lo si può importare nel Maker.
5. Si seleziona **Genera & Cripta**.
6. Si sceglie una password, la si conferma e si avvia la cifratura.
7. Il Maker genera e scarica un file HTML con nome simile a `gate_mkcaveau_YYYY-MM-DD.html`.
8. Per consultare l'archivio si apre il file generato in un browser compatibile.
9. Nel Gate si inserisce la password. È possibile scegliere l'accesso di emergenza prima di sbloccare.
10. Dopo la decifratura, il browser carica l'interfaccia del Caveau.

### I tre livelli

| Livello | Scopo | Contenuto |
|---|---|---|
| Maker | Creazione e manutenzione | Editor, modello dati, importazione ed esportazione |
| Gate | Protezione dell'accesso | Password, derivazione chiave, decifratura e modalità di emergenza |
| Caveau | Consultazione | Sezioni, campi, contatti, scadenze, immagini, checklist e note |

Il Maker mantiene i dati in memoria JavaScript durante la sessione di modifica. Il codice non implementa un salvataggio automatico su database o su `localStorage`: per conservare le modifiche occorre esportare un nuovo archivio cifrato.

---

# 1. MKCaveau Maker

MKCaveau Maker è l'editor che consente di creare il contenuto del Caveau senza dover modificare manualmente il codice HTML generato.

## Avvio e consenso alla licenza

Il Maker viene associato all'elemento della pagina con identificativo HTML `MKCaveauMaker`. Quando l'utente attiva quell'elemento, viene aperto il Maker.

Al primo accesso viene mostrata una schermata di licenza e disclaimer. Il flusso di consenso prevede:

- lettura del testo fino in fondo;
- attesa del tempo minimo previsto dal componente;
- selezione della casella che conferma la lettura e l'accettazione della licenza MIT e delle condizioni d'uso;
- selezione della seconda casella, relativa all'accettazione specifica delle clausole di esclusione delle garanzie, perdita dei dati, limitazione di responsabilità, obblighi dell'utente e limiti di sicurezza;
- attivazione del pulsante di prosecuzione solo quando le condizioni richieste sono soddisfatte.

Se si seleziona **Non accetto**, la finestra viene chiusa e il Maker non viene avviato. Il consenso viene mantenuto nella variabile di stato della pagina per la sessione corrente; il codice non implementa un registro remoto delle accettazioni.

## Navigazione e gestione delle sezioni

Il Maker presenta:

- una barra superiore con il marchio MKCAVEAU MAKER;
- un indicatore grafico di stato operativo/offline;
- una navigazione laterale con l'elenco delle sezioni;
- un'area centrale dedicata alla modifica della sezione selezionata;
- una barra inferiore con le azioni di importazione ed esportazione.

Per ogni sezione la navigazione mostra il titolo e, quando presente, il numero degli oggetti contenuti.

La sezione **Panoramica** è speciale: viene generata automaticamente a partire dalle scadenze presenti negli altri oggetti e non richiede una configurazione autonoma. Nel Maker viene mostrato un avviso che spiega questa natura automatica.

Su schermi più piccoli la navigazione diventa orizzontale e scorrevole, così da non occupare una colonna laterale permanente.

## Gestione degli oggetti

Le sezioni che contengono dati permettono di gestire uno o più oggetti.

Funzioni disponibili:

- selezionare un oggetto esistente;
- creare un nuovo oggetto con **+ Oggetto**;
- creare il primo oggetto in una sezione vuota con **+ Aggiungi oggetto**;
- modificare il nome;
- modificare il tag descrittivo;
- modificare la nota associata;
- attivare o disattivare il contrassegno **Emergenza**;
- modificare il gruppo sanguigno quando il dato è previsto dall'oggetto;
- eliminare l'oggetto corrente dalla zona pericolo, previa conferma.

Un nuovo oggetto viene inizializzato con un nome predefinito e un campo di esempio, che possono essere modificati.

L'eliminazione di un oggetto rimuove l'elemento dall'array della sezione. Non è previsto un cestino o un comando di annullamento/ripristino.

## Campi e livelli di riservatezza

Ogni oggetto può contenere una lista di campi personalizzati. Ogni campo comprende:

- **Etichetta**: il nome del dato, per esempio “Numero documento” o “PIN”.
- **Valore**: il contenuto da conservare.
- **Livello**: la modalità con cui il valore sarà presentato nel Caveau.

I livelli disponibili nel Maker sono:

| Livello | Comportamento nel Caveau |
|---|---|
| Normale | Il valore è mostrato direttamente; il pulsante consente di copiarlo |
| Segreto | Il valore è inizialmente mascherato; può essere rivelato temporaneamente e dispone dell'azione di copia |
| Segreto no-copia | Il valore è inizialmente mascherato e può essere rivelato temporaneamente; non viene mostrato il pulsante di copia dedicato |

Per i campi segreti la rivelazione si richiude automaticamente dopo circa **8 secondi**. Il livello “no-copia” rimuove il controllo di copia dell'interfaccia, ma non può impedire tecnicamente a un utente che ha sbloccato il file di leggere o acquisire il valore attraverso altri strumenti del browser, screenshot o ispezione della pagina.

Il Maker consente di aggiungere e rimuovere campi. I campi sono definiti dall'utente: non esiste un catalogo rigido di etichette obbligatorie.

## Carta di pagamento

Quando un oggetto contiene la proprietà `card`, il Maker mostra un editor dedicato con quattro valori:

- numero della carta;
- titolare;
- scadenza in formato mese/anno;
- CVV.

Nel Caveau questi valori alimentano una rappresentazione grafica tridimensionale della carta, con fronte e retro. Il numero e il CVV sono mascherati nella visualizzazione iniziale e possono essere rivelati tramite un comando dedicato.

La struttura è pensata per i dati di una carta, ma non esegue verifiche bancarie, autorizzazioni, pagamenti o validazioni presso un istituto finanziario.

## Contatti e numeri multipli

Gli oggetti possono contenere una rubrica di contatti. Per ogni contatto il Maker permette di modificare:

- nome;
- ruolo o descrizione;
- uno o più numeri telefonici;
- nota facoltativa.

I numeri multipli possono essere separati usando `|`, `;`, un a capo oppure un trattino circondato da spazi. Nel Caveau ogni numero riconosciuto viene visualizzato separatamente.

Il Maker consente di aggiungere un contatto e di rimuovere un contatto esistente.

## Scadenze

Un oggetto può contenere una lista di scadenze. Ogni scadenza è composta da:

- un'etichetta, ad esempio “assicurazione”, “bollo” o “revisione”;
- una data nel formato `AAAA-MM-GG`.

Nel Maker è possibile aggiungere e rimuovere righe di scadenza e modificarne sia l'etichetta sia la data.

Le scadenze vengono utilizzate dalla sezione automatica **Panoramica** del Caveau. Per essere interpretate correttamente, le date devono essere inserite nel formato ISO indicato.

## Immagini e documenti

Gli oggetti possono contenere una lista di immagini. Per ogni immagine il Maker mette a disposizione:

- un titolo;
- una categoria, quando il modello dell'immagine prevede tale proprietà;
- un selettore di file che accetta immagini (`image/*`);
- una miniatura, quando è già presente un'immagine;
- un comando per eliminare l'immagine.

Il file selezionato viene letto tramite `FileReader.readAsDataURL()` e salvato nel modello dati come Data URL, quindi incorporato nel successivo HTML esportato. Non viene caricato su un servizio remoto.

È possibile aggiungere nuove immagini con **+ Immagine**. Nella sezione Galleria le nuove immagini ricevono anche una categoria predefinita “Altro”.

Le immagini sono incorporate nel file finale: molte immagini ad alta risoluzione possono aumentare sensibilmente la dimensione dell'HTML e il tempo necessario per cifrarlo, scaricarlo e aprirlo.

## Checklist e procedure

Gli oggetti con proprietà `ck` possono contenere una checklist. Ogni passaggio include:

- titolo dello step;
- descrizione;
- numero telefonico facoltativo.

Il Maker permette di aggiungere step, modificarne i contenuti e rimuoverli. Se è presente un numero, il Caveau lo trasforma in un collegamento telefonico.

La checklist nel Caveau è interattiva, ma lo stato spuntato non viene salvato nel file e non viene sincronizzato con il Maker: è uno stato temporaneo della sessione di consultazione.

## Note libere

Gli oggetti con proprietà `tx` possono contenere testo libero. Il Maker offre un'area di testo multilinea e un'opzione **Nascosta (tocca per rivelare)**.

Quando l'opzione è attiva, nel Caveau il contenuto viene sostituito inizialmente da un invito a toccare per rivelare la nota. La nota viene nuovamente nascosta dopo circa **15 secondi**.

La proprietà `hide` controlla la presentazione iniziale: non è una cifratura separata del singolo appunto. L'intero archivio viene protetto dal meccanismo di cifratura dell'esportazione.

## Importazione

Il pulsante **Importa HTML** apre un selettore di file limitato a file HTML.

Il Maker prova a riconoscere due formati:

1. **Caveau in chiaro**: cerca nel sorgente HTML la dichiarazione JavaScript contenente l'array `V` e importa i dati individuati.
2. **Gate cifrato generato dal progetto**: riconosce il rivestimento mimetizzato, estrae i parametri crittografici e chiede la password per decifrare il contenuto prima di importarlo.

Durante l'importazione di un Gate cifrato viene mostrato un campo password con i comandi **Sblocca e importa** e **Annulla**. Se la password è errata o il file è danneggiato, viene mostrato un messaggio di errore e l'importazione non viene completata.

Dopo un'importazione riuscita, i dati sostituiscono il modello attualmente presente nel Maker e vengono marcati come modificati. È possibile intervenire sui contenuti e generare un nuovo file.

**Compatibilità dell'importazione:** il riconoscimento si basa su specifici schemi testuali e nomi di variabili presenti nei file generati dal progetto. Non è un importatore universale di HTML, JSON, CSV o di file prodotti da gestori di password di terze parti. Un file modificato manualmente o generato da una versione con struttura diversa potrebbe non essere riconosciuto.

## Esportazione e cifratura

Il pulsante **Genera & Cripta** apre il modulo di sigillatura.

### Password

Il modulo richiede:

- una password;
- la conferma della stessa password.

La password deve rispettare i controlli implementati:

- almeno **16 caratteri**;
- almeno **8 caratteri distinti**;
- corrispondenza esatta tra password e conferma.

È presente un indicatore grafico della lunghezza e della varietà dei caratteri. L'indicatore è una valutazione euristica dell'interfaccia, non un test completo della robustezza della password e non un misuratore crittografico dell'entropia.

Il modulo avvisa esplicitamente che la password non viene memorizzata e che non esiste una funzione di recupero. Se viene dimenticata, i dati cifrati non possono essere recuperati tramite il Maker.

### Algoritmi e parametri implementati

- **Derivazione della chiave:** PBKDF2 con HMAC-SHA-256.
- **Iterazioni PBKDF2:** 1.000.000.
- **Salt:** 16 byte generati con `crypto.getRandomValues()`.
- **Cifratura:** AES-GCM con chiave da 256 bit.
- **Nonce/IV:** 12 byte generati casualmente.
- **Tag di autenticazione:** tag GCM da 128 bit, conservato separatamente nel contenitore generato e riunito durante la decifratura.
- **Codifica dei parametri:** Base64.
- **Compressione:** GZIP prima della cifratura, se `CompressionStream` è disponibile e il risultato compresso è più piccolo dell'originale.

La cifratura viene eseguita tramite la Web Crypto API. Il Maker verifica che il contesto sia sicuro (`HTTPS` o `localhost`) e che `crypto.subtle` sia disponibile. Se il requisito non è soddisfatto, l'esportazione genera un errore.

### File generato

Il Maker costruisce il Caveau HTML, cifra il contenuto, crea il Gate e applica un ulteriore rivestimento di mimetizzazione basato su XOR e Base64. Il file viene scaricato con un nome simile a:

```text
gate_mkcaveau_2026-11-13.html
```

La data effettiva nel nome viene generata al momento dell'esportazione.

La mimetizzazione XOR/Base64 è un offuscamento del contenitore, **non un algoritmo di cifratura** e non deve essere considerata una protezione aggiuntiva equivalente alla crittografia AES-GCM.

Dopo aver generato il file, il Maker revoca l'URL temporaneo creato per il download. Le caselle password vengono svuotate dopo l'operazione di cifratura riuscita.

---

# 2. Gate: accesso al file protetto

Il Gate è la schermata iniziale del file HTML esportato. Presenta il marchio MKCaveau, un indicatore grafico animato, un campo password e il pulsante **Apri il caveau**.

Piccola nota: è stato di recente aggiunto anche un sistema per impedire di copiare facilmente e selezionare elementi.

## Accesso standard

1. Aprire il file HTML in un browser compatibile.
2. Inserire la password utilizzata durante l'esportazione.
3. Lasciare deselezionata la modalità di emergenza se si desidera aprire l'intero archivio.
4. Premere **Apri il caveau** oppure il tasto Invio nel campo password.
5. Il browser deriva la chiave e tenta la decifratura AES-GCM.
6. Se la verifica di autenticità ha esito positivo, il contenuto viene decifrato e caricato nel documento.

Se la password non è corretta, o se i dati cifrati risultano alterati, la decifratura fallisce e viene mostrato **Accesso negato**.

## Tentativi e cooldown

Il Gate implementa questi limiti nel contesto della pagina aperta:

- massimo **7 tentativi** falliti;
- dopo ogni terzo tentativo fallito viene attivato un cooldown di **12 secondi**;
- dopo il settimo tentativo fallito il Gate mostra **Caveau permanentemente sigillato** e disabilita il pulsante di accesso.

Il contatore è gestito in memoria JavaScript nella pagina corrente. Non è un blocco persistente sul file: ricaricare o riaprire il documento può azzerare lo stato dei tentativi. Non va quindi interpretato come un sistema anti-brute-force equivalente a un servizio autenticato lato server.

## Mimetizzazione del Gate

Il file esportato include un livello esterno che decodifica una stringa Base64 e applica un'operazione XOR con chiave costante `0x13` per ricostruire il Gate.

Questo livello serve a rendere meno immediata la lettura del contenuto del file tramite una semplice apertura del sorgente. Non sostituisce la cifratura AES-GCM: chi dispone del file può analizzarne il JavaScript e ricostruire il rivestimento.

Il Gate contiene anche controlli anti-debugging e controlli sull'ambiente JavaScript. Sono misure di deterrenza lato client, non una barriera di sicurezza garantita: possono essere aggirate, possono essere influenzate dal browser e non proteggono da un sistema operativo compromesso.

---

# 3. Caveau: consultazione dell'archivio

Dopo lo sblocco, il Caveau mostra un'interfaccia dedicata alla consultazione. Il layout comprende:

- intestazione con marchio e stato;
- campo di ricerca;
- comando **Blocca**;
- navigazione tra le sezioni;
- area centrale con le schede degli oggetti;
- visualizzatore immagini a schermo intero;
- notifiche temporanee (toast).

Il contenuto viene caricato localmente dal file. L'interfaccia dichiara e implementa un funzionamento senza connessione di rete per le sue funzioni ordinarie.

## Panoramica delle scadenze

La sezione Panoramica raccoglie le scadenze `sc` definite negli oggetti delle altre sezioni.

Per ciascuna scadenza il sistema calcola la differenza in giorni rispetto alla data corrente e ordina i risultati dalla scadenza più vicina a quella più lontana, includendo quelle già superate.

La Panoramica mostra tre contatori:

- **Scaduti**: date precedenti al giorno corrente;
- **Entro 90 giorni**: date da oggi fino ai successivi 90 giorni inclusi;
- **Regolari**: date oltre i 90 giorni.

Ogni riga riporta il nome dell'oggetto, l'etichetta della scadenza, la sezione di provenienza, la data e una descrizione relativa, per esempio “scade oggi”, “tra 12 giorni” o “scaduto da 3 giorni”.

Se si seleziona una scadenza, il Caveau passa alla sezione che contiene l'oggetto corrispondente.

Le date non valide o non interpretabili vengono ignorate. La Panoramica non invia notifiche, non crea promemoria di sistema e non aggiorna automaticamente calendari esterni.

## Visualizzazione dei campi

I campi vengono mostrati in riquadri con etichetta e valore.

- I campi normali sono leggibili direttamente.
- I campi segreti sono mascherati e possono essere rivelati temporaneamente.
- I campi segreti no-copia non mostrano il pulsante di copia.
- I valori copiabili possono essere copiati tramite il pulsante dedicato.

Dopo una copia riuscita viene mostrata una notifica. Il codice tenta di svuotare la clipboard dopo **20 secondi**, scrivendo uno spazio al suo interno. Questo comportamento dipende dal supporto e dai permessi del browser e non garantisce la cancellazione della cronologia degli appunti o di eventuali clipboard manager.

## Carte 3D

Gli oggetti con dati `card` vengono visualizzati come carte grafiche con:

- fronte con nome, chip, numero, titolare e scadenza;
- retro con banda magnetica stilizzata e CVV;
- rotazione 3D al clic/tocco;
- comando **Rivela** per mostrare o mascherare numero e CVV;
- comando **Numero** per copiare il numero della carta senza spazi;
- comando **Gira** per alternare fronte e retro.

Numero e CVV sono mascherati all'apertura. Quando vengono rivelati, tornano mascherati automaticamente dopo circa **8 secondi**.

La grafica è una rappresentazione informativa: non è una carta digitale utilizzabile per pagamenti e non interagisce con circuiti bancari.

## Contatti e chiamate

I contatti vengono visualizzati in schede con nome, ruolo, eventuale nota e numeri telefonici.

Per i contatti ordinari viene generato un avatar testuale con le iniziali. Per gli oggetti contrassegnati come emergenza viene utilizzata un'icona telefonica.

Ogni numero riconosciuto dispone di:

- collegamento telefonico `tel:`;
- comando di chiamata;
- comando di copia.

La disponibilità effettiva della chiamata dipende dal dispositivo e dal browser. Su un computer privo di applicazione telefonica configurata, il collegamento potrebbe non produrre un'azione utile.

## Galleria e visualizzatore immagini

Gli oggetti che contengono immagini vengono visualizzati in una griglia di miniature. La sezione Galleria (oggetti con proprietà `g`) aggiunge filtri per categoria e un controllo per mostrare temporaneamente anteprime più nitide.

### Filtri

- **Tutte** mostra tutte le immagini.
- Ogni categoria presente nei dati genera un filtro dedicato.
- Il contatore indica il numero di immagini attualmente visualizzate.

### Anteprime

Le miniature della galleria sono inizialmente ridotte e sfocate per limitare l'esposizione immediata dei dettagli. Il comando **Mostra anteprime** le rende più leggibili; dopo circa **20 secondi** il sistema ripristina automaticamente la visualizzazione sfocata.

Le immagini vengono preparate in modo differito tramite `IntersectionObserver`: il rendering delle miniature viene avviato quando si avvicinano all'area visibile.

### Visualizzatore

Selezionando una miniatura si apre il visualizzatore a schermo intero, che comprende:

- immagine selezionata;
- didascalia con titolo e posizione nella raccolta;
- pulsanti precedente e successiva;
- pulsante di chiusura;
- zoom avanti e indietro;
- rotazione di 90 gradi a sinistra o a destra;
- ripristino della visualizzazione 1:1;
- trascinamento dell'immagine quando ingrandita;
- zoom tramite rotellina del mouse;
- zoom tramite gesto a due dita sui dispositivi touch;
- navigazione tramite swipe orizzontale quando l'immagine non è ingrandita.

La navigazione da tastiera nel visualizzatore supporta:

| Tasto | Azione |
|---|---|
| `Esc` | Chiude il visualizzatore |
| `Freccia destra` | Immagine successiva |
| `Freccia sinistra` | Immagine precedente |
| `+` o `=` | Ingrandisce |
| `-` | Riduce |
| `R` | Ruota di 90 gradi |
| `0` | Ripristina zoom e rotazione |

Quando non ci sono più immagini da navigare, i pulsanti precedente/successiva vengono nascosti.

## Checklist operative

Le checklist mostrano gli step in ordine numerato. Ogni step può includere titolo, descrizione e un eventuale numero da chiamare.

Funzioni disponibili:

- spuntare uno step;
- visualizzare il conteggio degli step completati sul totale;
- visualizzare graficamente gli step completati;
- chiamare il numero associato a uno step, se presente;
- usare **Azzera** per rimuovere tutte le spunte e ripartire.

Lo stato delle spunte è temporaneo: il codice non lo salva nell'HTML e non lo sincronizza tra dispositivi o sessioni.

## Note nascoste

Una nota contrassegnata con `hide: true` viene presentata come contenuto nascosto. Toccando o facendo clic sul testo si rivela il contenuto; dopo circa **15 secondi** la nota torna nascosta.

È disponibile anche il comando **Copia testo**. La funzione di occultamento è una protezione visiva contro la lettura accidentale, non una cifratura indipendente.

## Ricerca

Il campo **Cerca…** consente di cercare nel contenuto dell'archivio.

La ricerca:

- non richiede una selezione preventiva della sezione;
- esamina gli oggetti delle varie sezioni;
- mostra i risultati in un elenco unico;
- riporta il numero di risultati trovati;
- visualizza un messaggio quando non ci sono corrispondenze.

Quando la ricerca è attiva, la navigazione delle sezioni non viene evidenziata come selezione corrente. Se si seleziona una sezione, il campo di ricerca viene svuotato.

La ricerca è una ricerca testuale locale sui dati presenti nel modello. Non consulta Internet e non interroga servizi esterni.

## Blocco e timeout

Il Caveau offre più modalità di blocco:

- **Blocco manuale** tramite il pulsante Blocca;
- doppio clic sul marchio nell'intestazione;
- doppia pressione di `Esc` entro un intervallo breve (la prima pressione mostra un suggerimento, la seconda blocca);
- blocco automatico dopo **5 minuti** di inattività;
- blocco quando la pagina perde visibilità, ad esempio passando a un'altra scheda o minimizzando il browser.

Quando il Caveau viene bloccato, il codice:

- svuota l'area principale e la navigazione;
- azzera il campo di ricerca;
- chiude il visualizzatore immagini;
- rimuove il riferimento all'immagine visualizzata;
- disconnette l'osservatore delle miniature;
- azzera lo stato di zoom e rotazione;
- nasconde l'interfaccia del Caveau e mostra nuovamente la schermata di blocco;
- tenta di svuotare la clipboard scrivendo uno spazio;
- ricarica la pagina, in quanto è attiva la modalità `HARD_LOCK`.

La ricarica è un tentativo di eliminare lo stato applicativo dalla pagina. Non equivale a una cancellazione certificata della memoria del processo, delle copie del browser, della memoria del sistema operativo o di eventuali screenshot.

Durante la perdita del focus della finestra, l'interfaccia applica inoltre un effetto di sfocatura al contenuto; al ritorno del focus l'effetto viene rimosso.

## Modalità di emergenza

Nel Gate è presente l'opzione **Accesso di emergenza — solo numeri**, selezionata per impostazione predefinita.

Se viene lasciata attiva e la password è corretta, il flusso tenta di caricare una versione ridotta dell'archivio contenente la sezione **Numeri utili** e i relativi contatti. Il Caveau viene aperto direttamente in tale modalità.

Caratteristiche del comportamento:

- mostra solo le informazioni di contatto estratte dalla sezione Numeri utili (con fallback alla prima sezione che contiene contatti);
- elimina dal modello ridotto gli altri dati prima di renderizzare la vista;
- avvia un timer di emergenza di **30 secondi**;
- al termine del timer ricarica la pagina;
- se la pagina diventa non visibile, viene ricaricata immediatamente.

**Nota tecnica importante:** per effettuare la decifratura il browser deve comunque elaborare il payload cifrato completo. Il codice tenta di sostituire la struttura completa con una versione ridotta prima di scrivere il documento decifrato nel DOM, ma la modalità di emergenza non è una dimostrazione formale che i dati completi non possano transitare nella memoria del processo o essere recuperati da un ambiente di esecuzione compromesso. È una modalità di visualizzazione ridotta, non un secondo archivio cifrato separatamente.

---

# 4. Struttura dei dati

Il modello principale è un array JavaScript denominato `V`. Ogni elemento rappresenta una sezione. Una sezione può avere metadati e un array `items` di oggetti.

## Sezione

| Proprietà | Tipo | Descrizione |
|---|---|---|
| `title` | stringa | Titolo mostrato nella navigazione |
| `icon` | stringa | Nome dell'icona interna associata alla sezione |
| `sub` | stringa | Sottotitolo/descrizione |
| `ov` | booleano | Indica la sezione speciale Panoramica |
| `items` | array | Oggetti contenuti nella sezione |

## Oggetto

| Proprietà | Tipo | Descrizione |
|---|---|---|
| `name` | stringa | Nome dell'oggetto |
| `tag` | stringa | Etichetta sintetica opzionale |
| `note` | stringa | Nota mostrata sotto i contenuti |
| `em` | booleano | Contrassegno di emergenza e stile dedicato |
| `blood` | stringa | Gruppo sanguigno, se previsto |
| `f` | array | Campi personalizzati |
| `card` | oggetto | Dati della carta di pagamento |
| `ct` | array | Contatti |
| `sc` | array | Scadenze |
| `img` | array | Immagini incorporate |
| `g` | booleano | Attiva la presentazione come galleria filtrabile |
| `ck` | array | Step di checklist |
| `tx` | stringa | Nota libera |
| `hide` | booleano | Nasconde inizialmente la nota libera |

Le proprietà sono opzionali: un oggetto può contenere solo i blocchi di dati di cui ha bisogno.

## Formato dei campi `f`

Ogni campo è rappresentato da un array:

```js
["Etichetta", "Valore"]
```

Il terzo elemento è facoltativo:

```js
["PIN", "1234", 1] // segreto
["Codice riservato", "ABCD", 2] // segreto senza pulsante di copia
```

## Formato della carta `card`

```js
{
  n: "0000 0000 0000 0000", // numero
  h: "Nome Cognome",        // titolare
  e: "00/00",               // scadenza
  c: "000"                  // CVV
}
```

## Formato dei contatti `ct`

```js
{
  n: "Nome del contatto",
  t: "Ruolo o descrizione",
  p: "112 | 113",
  note: "Nota facoltativa"
}
```

## Formato delle scadenze `sc`

```js
[
  ["assicurazione", "2026-12-31"],
  ["revisione", "2027-05-15"]
]
```

## Formato delle immagini `img`

```js
{
  t: "Fronte documento",       // titolo
  c: "Identità",               // categoria facoltativa
  s: "data:image/jpeg;base64,…" // Data URL
}
```

## Formato checklist `ck`

```js
{
  t: "Titolo dello step",
  d: "Descrizione dello step",
  p: "112" // numero facoltativo
}
```

## Formato note `tx`

```js
{
  tx: "Testo della nota",
  hide: true
}
```

L'esempio sopra rappresenta le proprietà di un oggetto: `tx` e `hide` vanno inserite direttamente nell'oggetto stesso.

---

# 5. Sezioni incluse nel modello iniziale

Il Maker parte da un modello precompilato con **13 sezioni**. I valori numerici e identificativi presenti nel modello sono segnaposto dimostrativi da sostituire con dati reali.

| Sezione | Contenuto previsto |
|---|---|
| Panoramica | Riepilogo automatico delle scadenze |
| Carte e banca | Carte di pagamento, IBAN, PIN, password di home banking e numeri di blocco |
| Carta d'identità (CIE) | Numero documento, date, PIN, PUK e immagini dei documenti/fogli codici |
| SIM | Numero telefonico, ICCID, PIN, PUK, PIN2 e PUK2 |
| Fisco | Codice fiscale, partita IVA, credenziali Fisconline e riferimenti di lettere/pratiche |
| Documenti | Patente e tessera sanitaria, numeri, scadenze e immagini fronte/retro |
| Veicoli e polizze | Dati dell'auto, targa, VIN, assicurazione, bollo, revisione, contatti e documenti; altre polizze |
| Salute | Scheda sanitaria di emergenza con allergie, farmaci, patologie, intolleranze, donazione organi, medico, contatto e tessera sanitaria; gruppo sanguigno |
| Numeri utili | Numeri di emergenza, persone di fiducia e assistenza/blocco |
| Se perdo tutto | Procedure guidate per telefono smarrito e portafoglio smarrito |
| Galleria | Raccolta di immagini/documenti filtrabile per categorie |
| Note libere | Appunti riservati e promemoria |
| Altro | Codici di recupero e informazioni personalizzate |

### Dettaglio dei contenuti iniziali

**Carte e banca** include un esempio di carta prepagata, i relativi dati grafici, una scadenza, IBAN, PIN, password di home banking, numero verde di blocco e immagini fronte/retro/altro.

**Carta d'identità (CIE)** include numero, data di rilascio, scadenza, le due parti del PIN, PUK e quattro slot immagine per documento e fogli PIN/PUK.

**SIM** include numero, ICCID, PIN, PUK, PIN2, PUK2 e uno slot per il cartoncino SIM.

**Fisco** include un oggetto per i dati fiscali e un oggetto per lettere dell'Agenzia delle Entrate, con protocollo, data e slot per immagini.

**Documenti** include patente e tessera sanitaria, ciascuna con scadenza, identificativo e immagini fronte/retro.

**Veicoli e polizze** include un oggetto Auto con scadenze assicurazione/bollo/revisione, dati del veicolo, contatti di assistenza e denuncia sinistri, immagini di libretto/revisione/polizza/estratto conto. Include anche un oggetto Altre polizze con campi relativi a casa, vita e infortuni.

**Salute** contiene una scheda contrassegnata come emergenza, un campo gruppo sanguigno e campi per allergie, farmaci, patologie, intolleranze, donazione organi, medico di base, contatto di emergenza e tessera sanitaria.

**Numeri utili** è suddivisa in Emergenze, Persone di fiducia e Assistenza e blocchi. Nel modello sono presenti numeri dimostrativi/precompilati: verificare sempre che siano corretti e aggiornati prima di farvi affidamento.

**Se perdo tutto** include checklist per telefono smarrito e portafoglio smarrito. Gli step sono promemoria generici modificabili dall'utente, non una procedura ufficiale fornita da enti o istituzioni.

**Galleria** include categorie iniziali come Identità, Auto, Banca, Titoli, Esami e Salute.

**Note libere** include un esempio di appunto inizialmente nascosto e un promemoria.

**Altro** include campi di esempio per codici di recupero e una nota che suggerisce possibili usi, come Wi-Fi e appunti riservati.

---

# 6. Aspetto grafico e adattabilità

L'interfaccia usa un'estetica scura ispirata a un caveau: fondi profondi, dettagli color oro, pannelli traslucidi, bordi sottili, indicatori luminosi e animazioni discrete.

## Tema

Il CSS include variabili per colori di sfondo, testo, testo secondario, accento, stato positivo e stato di errore. Sono presenti regole per `prefers-color-scheme`, quindi il progetto adatta diversi colori in base alla preferenza chiara/scura del sistema.

## Desktop

Su schermi ampi il Caveau utilizza una colonna laterale per la navigazione e un'area principale per i contenuti. Il Maker presenta una struttura analoga, con navigazione e pannello editor.

## Smartphone e tablet

Su schermi più piccoli:

- la navigazione laterale diventa una barra orizzontale scorrevole;
- le schede si dispongono in una colonna;
- i campi diventano più larghi e leggibili;
- i controlli touch aumentano la propria area interattiva;
- vengono rispettate le safe area dei dispositivi compatibili;
- il visualizzatore immagini supporta gesti touch.

Sono presenti regole specifiche per orientamento orizzontale, viewport dinamica e schermi molto piccoli.

## Accessibilità e input

Nel codice sono presenti etichette accessibili per diversi pulsanti, attributi `aria-label`, stato `aria-checked` per le checklist e indicazioni di focus visibile. Le animazioni del Maker e del Gate tengono conto di `prefers-reduced-motion` in alcune aree dell'interfaccia.

---

# 7. Tecnologie e compatibilità

## Tecnologie utilizzate

- **HTML5** per la struttura dei documenti.
- **CSS3** per layout, temi, animazioni, effetti, responsive design e trasformazioni 3D.
- **JavaScript moderno** per editor, modello dati, rendering e interazioni.
- **Web Crypto API** (`crypto.subtle`) per PBKDF2 e AES-GCM.
- **Compression Streams API** (`CompressionStream` / `DecompressionStream`) per GZIP, quando supportata.
- **File API** e `FileReader` per importare immagini e file HTML.
- **Blob** e URL temporanei per generare il download dell'archivio.
- **Clipboard API** per copiare valori e tentare di svuotare la clipboard.
- **IntersectionObserver** per il caricamento differito delle miniature.
- **ResizeObserver** per adattare alcuni elementi alle dimensioni disponibili.
- **Pointer Events** per gestire trascinamento, swipe e zoom touch.

Non è presente un framework JavaScript esterno necessario al funzionamento del file generato: il Caveau è costruito con JavaScript e CSS incorporati.

## Contesto sicuro

La Web Crypto API richiede un contesto sicuro. Per l'esportazione dal Maker è necessario utilizzare un contesto HTTPS oppure `localhost` e un browser che esponga `crypto.subtle`.

Aprire un file HTML locale (`file://`) per consultare un archivio già generato può avere comportamenti differenti tra browser e piattaforme. La compatibilità dipende dal supporto del browser alle API utilizzate.

## Browser

È consigliabile utilizzare una versione aggiornata di un browser moderno basato su Chromium, Firefox o Safari. Le funzionalità dipendenti da API specifiche possono non essere disponibili in versioni meno recenti.

Non viene dichiarata una matrice di compatibilità certificata né è presente, nel file esaminato, una suite automatizzata di test cross-browser.

---

# 8. Sicurezza: cosa protegge e cosa no

Questa sezione è essenziale perché MKCaveau può contenere dati personali e credenziali.

## Protezioni effettivamente implementate

- Il payload esportato viene cifrato con AES-256-GCM.
- La chiave viene derivata dalla password tramite PBKDF2-HMAC-SHA-256 con 1.000.000 di iterazioni.
- Salt e nonce vengono generati casualmente tramite Web Crypto.
- AES-GCM fornisce cifratura autenticata: una password errata o un payload alterato causa il fallimento della decifratura.
- La compressione, se usata, avviene prima della cifratura.
- Il Gate non contiene direttamente il modello dati in chiaro nel suo normale payload esterno: contiene il contenuto cifrato e il codice necessario alla decifratura.
- Il Caveau include metadati HTML come `noindex`, `nofollow`, `noarchive` e una Content Security Policy restrittiva nel documento generato.
- L'interfaccia maschera inizialmente alcuni valori e offre un blocco automatico.

## Limiti importanti

### La password non è recuperabile

Non esiste un reset, un account amministratore o una procedura di recupero. Se la password viene persa, il file non può essere decifrato dal Maker.

### La cifratura non protegge un dispositivo compromesso

Dopo lo sblocco, i dati devono essere disponibili al browser per essere mostrati. Malware, estensioni malevole, strumenti di debugging, keylogger, accesso fisico al dispositivo sbloccato o compromissioni del browser possono esporre le informazioni.

### La mimetizzazione non è cifratura

XOR e Base64 rendono meno immediata la lettura del Gate, ma non costituiscono una protezione crittografica. La protezione del contenuto a riposo è affidata ad AES-GCM.

### I controlli anti-debugging non sono una garanzia

I controlli presenti nel Gate sono eseguiti nel browser dell'utente. Non costituiscono un confine di sicurezza affidabile e possono essere aggirati o causare falsi positivi.

### I limiti dei tentativi non sono persistenti

Il contatore dei tentativi è in memoria nella pagina. Ricaricare il documento può azzerarlo. Non è presente un server che registra e limita globalmente i tentativi.

### La modalità di emergenza non è una cifratura separata

Il payload completo viene decifrato per poter estrarre i contatti. Il flusso riduce il documento che viene caricato, ma non offre una garanzia formale di isolamento dei dati non emergenziali dalla memoria del processo.

### Nascondere o mascherare non significa cifrare

I campi segreti, le note nascoste, le anteprime sfocate e l'opzione “no-copia” sono funzioni di presentazione. Non creano una seconda barriera crittografica dopo che l'archivio è stato sbloccato.

### Clipboard

Il tentativo di sovrascrivere la clipboard dopo 20 secondi è best-effort. Il browser può negare l'accesso e altri programmi possono aver già letto il contenuto copiato.

### Metadati e distribuzione

I metadati `noindex`/`noarchive` sono indicazioni ai crawler conformi, non impediscono a chiunque di scaricare, copiare o archiviare un file pubblicato su Internet. Un archivio con dati personali non dovrebbe essere pubblicato in un repository pubblico, in una pagina pubblica o in un servizio di hosting non adeguatamente protetto.

### Backup

La cifratura non crea automaticamente copie di sicurezza. Conservare più copie del file cifrato in luoghi sicuri e verificare periodicamente che possano essere aperte con la password corretta.

## Raccomandazioni operative

1. Usare una passphrase lunga, unica e non riutilizzata altrove.
2. Conservare la password in un gestore di password affidabile o in un luogo fisico sicuro.
3. Non inserire la password in un dispositivo che non si controlla.
4. Evitare computer pubblici, browser condivisi o profili con estensioni non fidate.
5. Non caricare il file cifrato in repository pubblici se non si è consapevoli dei rischi di distribuzione e conservazione.
6. Conservare copie di backup cifrate in più posizioni protette.
7. Verificare le copie di backup dopo ogni nuova esportazione.
8. Sostituire tutti i valori dimostrativi del modello iniziale e controllare i numeri di emergenza prima di affidarsi all'archivio.
9. Aggiornare il file ogni volta che cambiano credenziali, documenti, numeri o scadenze.
10. Considerare la cifratura come protezione del file a riposo, non come protezione assoluta durante l'uso.

---

# 9. Guida operativa consigliata

## Creare il primo Caveau

1. Aprire la pagina che ospita il Maker in un browser aggiornato.
2. Attivare il pulsante con ID `MKCaveauMaker`.
3. Leggere e accettare la licenza e il disclaimer.
4. Scorrere le 13 sezioni del modello iniziale.
5. Sostituire i segnaposto con dati corretti.
6. Rimuovere gli oggetti di esempio non necessari.
7. Aggiungere campi, contatti, immagini, scadenze e checklist dove servono.
8. Verificare in particolare la sezione Numeri utili e le procedure di emergenza.
9. Premere **Genera & Cripta**.
10. Inserire una password lunga e confermarla.
11. Scaricare il file HTML.
12. Conservare il file e la password in luoghi sicuri e separati, secondo il proprio modello di rischio.
13. Aprire una copia di prova del file e verificare che password, dati, immagini e scadenze siano corretti.

## Aggiornare un Caveau esistente

1. Aprire il Maker.
2. Selezionare **Importa HTML**.
3. Scegliere il file `gate_mkcaveau_*.html` oppure un Caveau in chiaro compatibile.
4. Se il file è cifrato, inserire la password corrispondente.
5. Modificare i dati necessari.
6. Generare un nuovo file con **Genera & Cripta**.
7. Verificare il nuovo file prima di sostituire o archiviare la versione precedente.

È consigliabile mantenere una copia cifrata della versione precedente fino a quando la nuova esportazione non è stata verificata.

## Utilizzare la modalità di emergenza

1. Aprire il file Gate.
2. Lasciare selezionata l'opzione **Accesso di emergenza — solo numeri**.
3. Inserire la password.
4. Premere **Apri il caveau**.
5. Consultare i contatti disponibili entro il tempo previsto.

La modalità è progettata per una consultazione breve, ad esempio quando si deve mostrare un numero a un soccorritore o a una persona che sta aiutando. Non va considerata una modalità di condivisione sicura su dispositivi di terzi.

---

# 10. Limiti e comportamenti da conoscere

Per evitare aspettative non corrispondenti al codice attuale, è utile riepilogare ciò che il progetto **non** implementa:

- non esiste un account utente o un sistema di registrazione;
- non esiste un backend o un database remoto;
- non esiste sincronizzazione tra dispositivi;
- non esiste salvataggio automatico persistente del Maker;
- non esiste recupero password;
- non esiste condivisione controllata con utenti o ruoli;
- non esiste un registro degli accessi o delle modifiche;
- non esiste una cronologia versioni integrata;
- non esiste un cestino o un annullamento delle eliminazioni;
- non esiste una notifica push o un promemoria di sistema per le scadenze;
- non esiste sincronizzazione con calendari;
- non esiste un servizio di chiamata integrato: vengono creati collegamenti `tel:`;
- non esiste un servizio di scansione OCR dei documenti;
- non esiste un motore di validazione ufficiale di documenti, IBAN, codici fiscali o carte;
- non esiste una cifratura separata per singola sezione o singolo campo;
- non esiste una modalità di emergenza con un payload crittografico indipendente;
- non esiste una protezione garantita contro screenshot, registrazione schermo o acquisizione tramite strumenti del browser;
- non esiste una garanzia di compatibilità per ogni browser o dispositivo;
- non è presente nel file esaminato una suite automatizzata di test o un audit di sicurezza indipendente.

### Dati temporanei

Alcune interazioni sono volutamente temporanee e non vengono salvate nell'archivio:

- stato delle checkbox delle checklist;
- filtro attivo nella galleria;
- zoom e rotazione correnti del visualizzatore;
- campo di ricerca;
- stato di rivelazione temporanea dei campi segreti;
- stato di rivelazione delle note nascoste.

Per rendere permanenti modifiche ai dati, occorre intervenire nel Maker e generare una nuova esportazione.

---

## Licenza

Il codice incorpora un avviso di **licenza MIT**, con copyright indicato nel progetto come:

**MKCaveau — Copyright © 2026 zMykerd**

La licenza MIT consente uso, copia, modifica, fusione, pubblicazione, distribuzione, sublicenza e vendita del software, a condizione di includere l'avviso di copyright e il testo della licenza nelle copie o porzioni sostanziali del software.

Il software viene fornito “AS IS”, senza garanzie espresse o implicite, nei limiti previsti dal testo della licenza.

---

## Nota finale

MKCaveau mette a disposizione un flusso autonomo per creare un archivio personale, proteggerlo con cifratura lato client e consultarlo offline. Prima di inserirvi informazioni reali, è opportuno provare l'intero ciclo di creazione, esportazione, apertura, importazione e aggiornamento con dati fittizi. Solo dopo aver verificato il funzionamento sul proprio browser e sui dispositivi che si intendono utilizzare è consigliabile trasferire nel Caveau i dati effettivi.
