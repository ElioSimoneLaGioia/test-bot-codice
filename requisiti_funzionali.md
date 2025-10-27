### Funzionalità: Aggiunta di un nuovo cliente

**Descrizione:** Il sistema deve permettere l'aggiunta di nuovi clienti al database CRM, associando loro un ID univoco, un nome e un indirizzo email.

**Precondizioni:**
* Nessuna specifica precondizione oltre al fatto che il sistema CRM sia operativo.

**Comportamento atteso:**
* Il sistema crea e memorizza un nuovo oggetto 'Cliente' con i dati forniti ('id_cliente', 'nome', 'email').
* Il cliente viene associato all'ID fornito nel registro dei clienti del CRM.

**Criteri di accettazione:**
* Un cliente con un ID, nome ed email specificati deve essere aggiunto correttamente al sistema.
* Dopo l'aggiunta, il cliente deve essere recuperabile tramite il suo ID.

**Moduli / componenti interessati:** `CRM`, `Cliente`

---

### Funzionalità: Generazione di una nuova fattura

**Descrizione:** Il sistema deve consentire la generazione di una nuova fattura per un cliente esistente, specificando l'importo. La data di emissione sarà impostata automaticamente alla data corrente.

**Precondizioni:**
* Il cliente a cui è destinata la fattura deve esistere nel sistema CRM (aggiunto tramite la funzionalità 'Aggiunta di un nuovo cliente').

**Comportamento atteso:**
* Il sistema recupera il cliente specificato tramite il suo ID.
* Viene creato un nuovo oggetto 'Fattura' con un ID univoco, associato al cliente, all'importo e alla data di emissione corrente.
* La fattura viene aggiunta all'elenco delle fatture del CRM.
* Se il cliente non viene trovato, il sistema genera un errore.

**Criteri di accettazione:**
* Se il cliente esiste, una nuova fattura deve essere generata correttamente con l'importo e la data corrente.
* La fattura generata deve essere associata al cliente corretto.
* La fattura deve essere aggiunta all'elenco delle fatture del sistema e avere un ID univoco.
* Se il cliente non esiste, il sistema deve sollevare un errore indicando "Cliente non trovato."

**Moduli / componenti interessati:** `CRM`, `Fattura`, `Cliente`

---

### Funzionalità: Visualizzazione di una fattura

**Descrizione:** Il sistema deve permettere di visualizzare i dettagli di una fattura specifica in un formato leggibile e strutturato.

**Precondizioni:**
* La fattura da visualizzare deve essere stata precedentemente generata nel sistema.

**Comportamento atteso:**
* Il sistema genera una stringa formattata contenente i dettagli della fattura: il nome del cliente, l'email del cliente, l'importo della fattura e la sua data di emissione.

**Criteri di accettazione:**
* La stringa di output deve presentare chiaramente il nome del cliente, l'email del cliente, l'importo formattato con due cifre decimali e la data di emissione nel formato "DD/MM/YYYY".
* L'output deve includere intestazioni e piè di pagina per la leggibilità, come "--- FATTURA RETAIL ---" e "----------------------".

**Moduli / componenti interessati:** `Fattura`