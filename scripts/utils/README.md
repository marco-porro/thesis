# 🛠️ Data Preparation: `k_dataset.ipynb`

Questo notebook è il cuore della preparazione dei dati. Si occupa di scaricare le traiettorie di base, generare le dimostrazioni visive tramite motion planning e convertire il tutto nel formato richiesto dal framework LeRobot.

Di seguito viene spiegato il flusso di lavoro passo dopo passo:

### 1 e 2. Installazione Librerie e Autenticazione
Le prime celle del notebook sono dedicate alla configurazione dell'ambiente.
* Vengono installate tutte le librerie e le dipendenze necessarie per far girare ManiSkill, LeRobot e gli script di motion planning.
* Viene effettuato il login su **Hugging Face (HF)** (necessario per caricare i dataset) o su Google Drive.
* **Attenzione:** Dopo aver eseguito le celle di installazione, è generalmente necessario **riavviare la sessione** (Restart Session) di Colab o del notebook, come specificato all'interno del codice stesso, per far sì che le nuove librerie vengano caricate correttamente.

### 2.1 - 2.5 Override degli Ambienti (Environments)
In questa sezione vengono applicati degli *override* agli ambienti di simulazione standard. Questo passaggio è fondamentale per personalizzare alcune caratteristiche degli ambienti originali, come ad esempio:
* La modifica della risoluzione delle telecamere (camera resolution).
* La variazione delle dimensioni o delle caratteristiche fisiche degli oggetti manipolati dal robot.

### 2.6 - 2.71 Script di Motion Planning e Conversione
Qui vengono create o modificate le funzioni core necessarie per la manipolazione dei dati:
* Funzioni per gli script di **motion planning** e per la generazione effettiva delle dimostrazioni (passaggio da stati fisici a vere e proprie sequenze di azioni).
* Funzioni e script necessari per la conversione del dataset generato nel formato specifico **LeRobot Format v3.0**.

### 3 - 7. Elaborazione dei Singoli Task
Questa è la fase operativa principale. Per ognuno dei task scelti, procedendo uno alla volta, il notebook esegue in sequenza queste operazioni:
1. **Download:** Scarica le traiettorie (demos) ufficiali fornite da ManiSkill.
2. **Replay e Generazione Video:** Utilizzando la funzione `replay_force_panda`, vengono eseguiti gli script di motion planning. Le traiettorie scaricate vengono "riprodotte" nella simulazione, trasformandole in dimostrazioni complete di video (osservazioni visive).
3. **Conversione:** Tramite lo script `convert_maniskill_v3_native.py`, i dati grezzi vengono convertiti nel dataset finale. Durante questa fase si scelgono parametri chiave come gli FPS, la risoluzione delle immagini, il tipo di robot, e viene associata un'istruzione in linguaggio naturale (*natural language instruction*).
4. **Upload:** Il dataset specifico appena convertito per il singolo task viene caricato (upload) sul tuo hub di Hugging Face.

### Unione dei Dataset (Merge)
Dopo aver elaborato e caricato su Hugging Face i dataset per ogni singolo task, il notebook procede a unificarli. Viene utilizzato il comando `lerobot-edit-dataset` per fare il **merge** di tutti i dataset individuali in un unico grande dataset comprensivo di tutte le dimostrazioni.

### Check Finale (Facoltativo)
Alla fine del notebook sono presenti delle celle facoltative di **CHECK**. È altamente consigliato eseguirle per ispezionare il dataset ottenuto e assicurarsi che:
* Contenga tutte le informazioni corrette (immagini, stati, azioni).
* Sia effettivamente e pienamente compatibile con il framework LeRobot prima di passare alla fase di addestramento dei modelli.
