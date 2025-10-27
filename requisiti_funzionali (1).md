# Requisiti Funzionali Dettagliati

**Data di Generazione:** 27/10/2025
**Autore:** CodeVolver AI

## 1. Introduzione e Contesto
- **Scopo del Documento:** Questo documento ha lo scopo di delineare in maniera esaustiva i requisiti funzionali di un sistema CRM avanzato, come derivato dall'analisi del codice sorgente fornito. L'obiettivo è fornire una base chiara e dettagliata per lo sviluppo, il testing e la gestione del progetto, garantendo che tutte le parti interessate abbiano una comprensione uniforme delle funzionalità del sistema.
- **Contesto di Business:** Il sistema analizzato risponde all'esigenza aziendale di gestire in modo integrato le interazioni con i clienti e le transazioni di vendita attraverso **canali multipli**, specificamente `retail` (vendita al dettaglio fisica) e `online` (e-commerce). Questa integrazione è cruciale per offrire una visione unificata del cliente, ottimizzare i processi di fatturazione e monitorare le performance di vendita su diverse piattaforme, contribuendo a migliorare l'esperienza del cliente e l'efficienza operativa.
- **Panoramica della Funzionalità:** Il codice implementa le funzionalità fondamentali di un Customer Relationship Management (CRM) con capacità di gestione clienti, generazione di fatture e riepilogo delle vendite. La sua caratteristica distintiva è la capacità di differenziare e gestire le transazioni basate sul canale di vendita (`retail` o `online`), applicando regole di business specifiche, come la tassazione IVA differenziata per il canale `online`. Permette di aggiungere clienti, generare fatture per importi specifici, calcolare il totale dovuto (inclusi gli oneri fiscali) e fornire un resoconto aggregato delle vendite per canale.

## 2. Diagramma di Flusso (Mermaid)
```mermaid
graph TD;
    A[Inizio] --> B{Sistema CRM Avviato};
    B --> C{Aggiungi Cliente?};
    C -- Si --> D[Registra Dati Cliente];
    D --> E{Genera Fattura?};
    C -- No --> E;
    E -- Si --> F{Seleziona Cliente Esistente?};
    F -- No --> G[Errore: Cliente Non Trovato];
    F -- Si --> H[Determina Canale di Vendita (Retail/Online)];
    H -- Online --> I[Applica Tasse (es. IVA 22%)];
    H -- Retail --> J[Nessuna Tassa Specifica Canale (0%)];
    I --> K[Genera ID Fattura Unico];
    J --> K;
    K --> L[Crea e Registra Fattura];
    L --> M[Stampa Dettaglio Fattura (Log)];
    L --> N{Riepilogo Vendite?};
    M --> N;
    N -- Si --> O[Aggrega Vendite per Canale];
    O --> P[Visualizza Riepilogo];
    P --> Q[Fine];
    N -- No --> Q;
    G --> Q;
```

## 3. Requisiti Funzionali Specifici

- **ID Requisito:** RF-001
  **Nome:** Gestione Clienti
  **Descrizione:** Il sistema deve permettere la creazione e la registrazione di nuovi clienti nel CRM, associando a ciascuno un identificativo univoco, un nome, un indirizzo email e, opzionalmente, un indirizzo fisico.
  **Attori Coinvolti:** Utente (operatore CRM), Sistema CRM.
  **Precondizioni:** Nessuna precondizione specifica per l'aggiunta del primo cliente. Per i successivi, il sistema deve essere operativo.
  **Postcondizioni:** Un nuovo oggetto `Cliente` è stato creato e archiviato nel database clienti del CRM. Il cliente è ora disponibile per le operazioni future (es. generazione fatture).

- **ID Requisito:** RF-002
  **Nome:** Generazione Fatture Multicanale
  **Descrizione:** Il sistema deve consentire la generazione di fatture per i clienti esistenti, specificando l'importo della transazione e il canale di vendita (`retail` o `online`). Il sistema deve applicare logiche di tassazione differenziate in base al canale.
  **Attori Coinvolti:** Utente (operatore CRM), Sistema CRM.
  **Precondizioni:** Il cliente per il quale si intende generare la fattura deve essere precedentemente registrato nel sistema (RF-001).
  **Postcondizioni:** Viene generato un nuovo oggetto `Fattura` con un ID univoco. La fattura include i dettagli del cliente, l'importo netto, le tasse calcolate e la data di emissione. La fattura viene aggiunta all'elenco delle fatture registrate e viene generato un log dell'operazione.

- **ID Requisito:** RF-003
  **Nome:** Calcolo Tasse e Totale Fattura
  **Descrizione:** Il sistema deve calcolare automaticamente le tasse applicabili all'importo della fattura in base al canale di vendita. Specificamente, per le fatture generate tramite il canale `online`, deve essere applicata un'IVA del 22%. Il sistema deve quindi calcolare il totale complessivo della fattura sommando l'importo netto e le tasse.
  **Attori Coinvolti:** Sistema CRM.
  **Precondizioni:** Una fattura è in fase di creazione con un importo e un canale di vendita specificati.
  **Postcondizioni:** Il campo `tasse` della fattura è popolato con il valore corretto. Il metodo `totale_con_tasse()` restituisce l'importo complessivo (importo netto + tasse).

- **ID Requisito:** RF-004
  **Nome:** Stampa Dettaglio Fattura
  **Descrizione:** Il sistema deve essere in grado di generare una rappresentazione testuale formattata dei dettagli di una fattura, includendo informazioni come il nome e l'email del cliente, l'importo netto, le tasse, il totale complessivo e la data di emissione.
  **Attori Coinvolti:** Sistema CRM.
  **Precondizioni:** Una fattura valida è stata generata e registrata (RF-002, RF-003).
  **Postcondizioni:** Viene prodotta una stringa di testo contenente i dettagli formattati della fattura, pronta per la visualizzazione o l'invio.

- **ID Requisito:** RF-005
  **Nome:** Riepilogo Vendite per Canale
  **Descrizione:** Il sistema deve fornire un riepilogo aggregato delle vendite totali, suddiviso per canale (`retail` e `online`), mostrando il totale complessivo degli importi (incluse le tasse) per ciascun canale.
  **Attori Coinvolti:** Utente (operatore CRM), Sistema CRM.
  **Precondizioni:** Almeno una fattura deve essere stata generata e registrata nel sistema (RF-002).
  **Postcondizioni:** Viene restituito un dizionario o una struttura dati equivalente che indica il totale delle vendite per ciascun canale (`retail` e `online`).

## 4. Comportamento Atteso e Regole di Business

Il sistema CRM è progettato per gestire le interazioni con i clienti e le transazioni di vendita seguendo specifiche regole di business e comportamenti attesi:

*   **Gestione Clienti (RF-001):**
    *   Un cliente viene identificato in modo univoco tramite il suo `id_cliente`.
    *   È possibile registrare un cliente fornendo `id_cliente`, `nome` ed `email`. L'indirizzo è un campo opzionale.
    *   Il sistema deve mantenere un elenco di clienti registrati, accessibile tramite il loro `id_cliente`.

*   **Generazione Fatture (RF-002, RF-003):**
    *   Per generare una fattura, è obbligatorio che il `id_cliente` fornito corrisponda a un cliente esistente nel sistema. In caso contrario, il sistema deve sollevare un'eccezione (`ValueError: "Cliente non trovato."`).
    *   Ogni fattura generata riceve un `id_fattura` univoco, generato automaticamente utilizzando un UUID (Universal Unique Identifier).
    *   La `data_emissione` della fattura viene impostata automaticamente alla data e ora corrente al momento della creazione, se non specificata diversamente.
    *   **Regola di Business - Tassazione:**
        *   Se il `canale` di vendita è `'online'`, viene applicata una tassa pari al 22% dell'`importo` netto della fattura. Questo rappresenta l'IVA standard per l'e-commerce.
        *   Se il `canale` di vendita è `'retail'`, non viene applicata alcuna tassa addizionale specifica al canale nel calcolo automatico (il valore `tasse` è 0.0 per default, a meno di specificazioni esterne non considerate in questo contesto).
    *   Il sistema deve registrare la fattura generata nell'elenco delle fatture.
    *   Il sistema deve produrre un output di log (`print`) che indichi il canale, il nome del cliente e il totale della fattura generata.

*   **Calcolo Totale Fattura (RF-003):**
    *   Il `totale_con_tasse()` di una fattura è calcolato come la somma dell'`importo` netto e delle `tasse` applicate.

*   **Stampa Dettaglio Fattura (RF-004):**
    *   La rappresentazione testuale della fattura include: `canale` (in maiuscolo), `Cliente.nome`, `Cliente.email`, `importo` netto formattato a due cifre decimali, `tasse` formattate a due cifre decimali, `totale_con_tasse` formattato a due cifre decimali e `data_emissione` formattata come `gg/mm/aaaa`.

*   **Riepilogo Vendite (RF-005):**
    *   Il sistema deve aggregare il `totale_con_tasse()` di tutte le fatture registrate, suddividendole per il loro `canale` di appartenenza (`retail` o `online`).
    *   Il riepilogo restituito è un dizionario con i nomi dei canali come chiavi e la somma totale delle vendite per ciascun canale come valori.

## 5. Casi di Test e Criteri di Accettazione

### Casi di Test per RF-001: Gestione Clienti
| ID Test | Descrizione | Dati di Input | Risultato Atteso | Criteri di Successo |
|---|---|---|---|---|
| TC-ADD-001 | Aggiunta cliente con indirizzo | `id_cliente=1`, `nome="Mario Rossi"`, `email="mario.rossi@email.com"`, `indirizzo="Via Roma 10, Milano"` | Cliente creato e registrato | Il dizionario `crm.clienti` contiene un oggetto `Cliente` con `id_cliente=1`, `nome="Mario Rossi"`, `email="mario.rossi@email.com"`, `indirizzo="Via Roma 10, Milano"`. |
| TC-ADD-002 | Aggiunta cliente senza indirizzo | `id_cliente=2`, `nome="Laura Bianchi"`, `email="laura.bianchi@email.com"`, `indirizzo=None` | Cliente creato e registrato | Il dizionario `crm.clienti` contiene un oggetto `Cliente` con `id_cliente=2`, `nome="Laura Bianchi"`, `email="laura.bianchi@email.com"`, `indirizzo=None`. |

### Casi di Test per RF-002 & RF-003: Generazione Fatture Multicanale e Calcolo Tasse/Totale
| ID Test | Descrizione | Dati di Input | Risultato Atteso | Criteri di Successo |
|---|---|---|---|---|
| TC-INV-RET-001 | Generazione fattura Retail | `id_cliente=1`, `importo=100.00`, `canale="retail"` | Fattura generata con tasse 0.00 | Una `Fattura` è aggiunta a `crm.fatture` per `cliente.id_cliente=1`. `fattura.importo` è 100.00, `fattura.tasse` è 0.00, `fattura.totale_con_tasse()` è 100.00. Il log `[RETAIL] Fattura generata per Mario Rossi (€100.00)` è stampato. |
| TC-INV-ONL-002 | Generazione fattura Online | `id_cliente=2`, `importo=100.00`, `canale="online"` | Fattura generata con tasse 22.00 | Una `Fattura` è aggiunta a `crm.fatture` per `cliente.id_cliente=2`. `fattura.importo` è 100.00, `fattura.tasse` è 22.00, `fattura.totale_con_tasse()` è 122.00. Il log `[ONLINE] Fattura generata per Laura Bianchi (€122.00)` è stampato. |
| TC-INV-ERR-003 | Generazione fattura per cliente non esistente | `id_cliente=99`, `importo=50.00`, `canale="retail"` | Eccezione `ValueError` sollevata | Il metodo `genera_fattura` solleva `ValueError("Cliente non trovato.")`. Nessuna fattura è aggiunta a `crm.fatture`. |

### Casi di Test per RF-004: Stampa Dettaglio Fattura
| ID Test | Descrizione | Dati di Input | Risultato Atteso | Criteri di Successo |
|---|---|---|---|---|
| TC-PRN-001 | Stampa fattura Retail | Oggetto `Fattura` generato da TC-INV-RET-001 | Stringa formattata dettagliata | La stringa prodotta da `stampa_fattura()` contiene "--- FATTURA RETAIL ---", "Cliente: Mario Rossi", "Importo netto: €100.00", "Tasse: €0.00", "Totale: €100.00". |
| TC-PRN-002 | Stampa fattura Online | Oggetto `Fattura` generato da TC-INV-ONL-002 | Stringa formattata dettagliata | La stringa prodotta da `stampa_fattura()` contiene "--- FATTURA ONLINE ---", "Cliente: Laura Bianchi", "Importo netto: €100.00", "Tasse: €22.00", "Totale: €122.00". |

### Casi di Test per RF-005: Riepilogo Vendite per Canale
| ID Test | Descrizione | Dati di Input | Risultato Atteso | Criteri di Successo |
|---|---|---|---|---|
| TC-SUM-001 | Riepilogo vendite con 2 fatture | Fatture generate in TC-INV-RET-001 e TC-INV-ONL-002 | Riepilogo totale per canale | Il risultato di `crm.riepilogo_vendite()` è `{"retail": 100.00, "online": 122.00}`. |
| TC-SUM-002 | Riepilogo vendite senza fatture | Nessuna fattura generata | Riepilogo con totali zero | Il risultato di `crm.riepilogo_vendite()` è `{"retail": 0, "online": 0}`. |

## 6. Moduli e Componenti Interessati

| Componente | Responsabilità | Dipendenze |
|---|---|---|
| `Cliente` (Classe) | Modella i dati di un cliente (ID, nome, email, indirizzo). | Nessuna (classe base per i dati) |
| `Fattura` (Classe) | Modella i dati di una fattura (ID, cliente, importo, canale, tasse, data emissione). Calcola il totale e formatta la stampa. | `Cliente`, `datetime` |
| `CRM` (Classe) | Gestisce la collezione di clienti e fatture. Orchesta le operazioni di aggiunta cliente, generazione fattura e riepilogo vendite. | `Cliente`, `Fattura`, `uuid`, `datetime` |
| `CRM.aggiungi_cliente()` | Crea e registra un nuovo oggetto `Cliente`. | `Cliente` |
| `CRM.genera_fattura()` | Ricerca cliente, calcola tasse basate sul canale, crea e registra `Fattura`. | `Cliente`, `Fattura`, `uuid` |
| `Fattura.totale_con_tasse()` | Calcola l'importo totale di una fattura (netto + tasse). | Nessuna (metodo interno alla classe) |
| `Fattura.stampa_fattura()` | Genera una stringa formattata con i dettagli della fattura. | `Cliente` (per dati del cliente) |
| `CRM.riepilogo_vendite()` | Aggrega e restituisce il totale delle vendite per ciascun canale. | `Fattura` |

## 7. Rischi e Impatti Potenziali

*   **Rischi Funzionali:**
    *   > ⚠️ **Attenzione:** **Inconsistenza Dati Cliente:** Se l'ID cliente fornito a `genera_fattura` non esiste, il sistema solleva un errore. Tuttavia, non ci sono meccanismi per prevenire la creazione di clienti con ID duplicati o per gestire aggiornamenti ai dati cliente. Questo potrebbe portare a dati obsoleti o incoerenti se non gestito a livello applicativo superiore.
    *   > ⚠️ **Attenzione:** **Errori di Tassazione:** Il calcolo delle tasse (`0.22` per online) è hardcoded. Qualsiasi modifica normativa all'IVA richiederebbe una modifica del codice sorgente, con rischio di errori e tempi di aggiornamento lunghi. Un sistema più robusto dovrebbe gestire i tassi di IVA tramite configurazione esterna.
    *   > ⚠️ **Attenzione:** **Gestione Canali:** I canali sono `'retail'` e `'online'`. L'introduzione di nuovi canali richiederebbe modifiche al codice, in particolare nella logica di tassazione di `genera_fattura` e nel `riepilogo_vendite`.
    *   > ⚠️ **Attenzione:** **Persistenza Dati:** Il sistema corrente non prevede la persistenza dei dati (clienti e fatture vengono mantenuti solo in memoria). Al riavvio dell'applicazione, tutti i dati andrebbero persi. Questo è un rischio significativo per un sistema CRM enterprise e richiede l'integrazione con un database.
    *   > ⚠️ **Attenzione:** **Sicurezza:** Non ci sono meccanismi di autenticazione o autorizzazione. Qualsiasi utente con accesso all'applicazione può eseguire tutte le operazioni, il che è inaccettabile per un sistema aziendale.

*   **Impatti su Altri Sistemi:**
    *   **Sistema Contabile/ERP:** L'assenza di persistenza dei dati e di API di integrazione implica che i dati di fatturazione e riepilogo vendite non possono essere facilmente sincronizzati con sistemi contabili esterni. Sarebbe necessario un processo manuale o uno sviluppo ad-hoc per esportare/importare dati, aumentando il rischio di errori e la complessità operativa.
    *   **Sistema di Gestione Magazzino/Inventario:** Le fatture generate non hanno un impatto diretto sull'inventario, poiché non ci sono dettagli sui prodotti venduti. Per un'azienda che gestisce scorte, sarebbe necessario integrare i dati di vendita con il sistema di magazzino per un accurato controllo dell'inventario.
    *   **Sistemi di Marketing/Email Automation:** Le informazioni sui clienti e le transazioni non sono facilmente esportabili per campagne di marketing mirate. L'integrazione con piattaforme di email marketing o CRM di livello superiore richiederebbe API o esportazioni dedicate.

## 8. Conclusioni e Note

Il sistema CRM analizzato fornisce una solida base per la gestione di clienti e fatture con un'importante differenziazione per canale di vendita. La sua semplicità lo rende un ottimo punto di partenza per dimostrare le funzionalità principali.

Per una soluzione di livello enterprise, sarà fondamentale affrontare i seguenti punti:
*   **Persistenza dei Dati:** Implementare un database (es. SQL, NoSQL) per garantire che i dati di clienti e fatture siano salvati in modo permanente.
*   **Interfaccia Utente:** Sviluppare un'interfaccia grafica (web o desktop) per interagire con il CRM in modo più intuitivo e user-friendly rispetto all'esecuzione diretta del codice.
*   **Configurabilità:** Esternalizzare i tassi di tassazione e le definizioni dei canali in file di configurazione o un'interfaccia di amministrazione.
*   **Sicurezza:** Aggiungere meccanismi di autenticazione, autorizzazione e gestione degli accessi.
*   **Logging Avanzato:** Implementare un sistema di logging più robusto che scriva su file o sistemi di log centralizzati, piuttosto che solo su console.
*   **Estendibilità:** Prevedere la possibilità di aggiungere dettagli ai prodotti nelle fatture e integrare con moduli di inventario o gestione ordini.