---
lab:
  title: Gestire la sicurezza dei Servizi di Azure AI
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Gestire la sicurezza dei Servizi di Azure AI

La sicurezza è una considerazione fondamentale per qualsiasi applicazione e, in quanto sviluppatore, è necessario assicurarsi che l'accesso a risorse come i Servizi di Azure AI sia limitato solo a coloro che lo richiedono.

L'accesso ai Servizi di Azure AI viene in genere controllato tramite chiavi di autenticazione, che vengono generate quando si crea inizialmente una risorsa di Servizi di Azure AI.

## Clonare il repository in Visual Studio Code

Il codice verrà sviluppato usando Visual Studio Code. I file di codice per l'app sono stati forniti in un repository GitHub.

> **Suggerimento**: Se il repository **mslearn-ai-services** è già stato clonato di recente, aprirlo in Visual Studio Code. In caso contrario, eseguire i passaggi seguenti per clonarlo nell'ambiente di sviluppo.

1. Avviare Visual Studio Code.
2. Aprire il riquadro comandi (MAIUSC+CTRL+P) ed eseguire un comando **Git: Clone** per clonare il repository `https://github.com/MicrosoftLearning/mslearn-ai-services` in una cartella locale. Non è importante usare una cartella specifica.
3. Dopo la clonazione del repository, aprire la cartella in Visual Studio Code.
4. Attendere il completamento dell'installazione di file aggiuntivi per supportare i progetti in codice C# nel repository, se necessario

    > **Nota**: se viene richiesto di aggiungere gli asset necessari per la compilazione e il debug, selezionare **Non adesso**.

5. Espandere la cartella `Labfiles/02-ai-services-security`.

È stato fornito il codice sia per C# che per Python. Espandere la cartella del linguaggio preferito.

## Effettuare il provisioning di una risorsa di Servizi di Azure AI

Se non è già disponibile nella sottoscrizione, sarà necessario effettuare il provisioning di una risorsa di **Servizi di Azure AI**.

1. Aprire il portale di Azure all'indirizzo `https://portal.azure.com` ed eseguire l'accesso usando l'account Microsoft associato alla sottoscrizione di Azure.
2. Nella barra di ricerca superiore cercare *Servizi di Azure AI*, selezionare **Servizi di Azure AI** e creare una risorsa account multiservizio di Servizi di Azure AI con le impostazioni seguenti:
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *scegliere o creare un gruppo di risorse. Se si usa una sottoscrizione con restrizioni, si potrebbe non essere autorizzati a creare un nuovo gruppo di risorse. Usare quello fornito*
    - **Area**: *scegliere una qualsiasi area disponibile*
    - **Nome**: *immettere un nome univoco*
    - **Piano tariffario**: Standard S0.
3. Selezionare le caselle di controllo necessarie e creare la risorsa.
4. Attendere il completamento della distribuzione e quindi visualizzare i relativi dettagli.

## Gestire le chiavi di autenticazione

Quando è stata creata la risorsa di Servizi di Azure AI, sono state generate due chiavi di autenticazione. È possibile gestirle nel portale di Azure o tramite l'interfaccia della riga di comando di Azure.

1. Nel portale di Azure passare alla risorsa di Servizi di Azure AI e visualizzare la relativa pagina **Chiavi ed endpoint**. Questa pagina contiene le informazioni necessarie per connettersi alla risorsa e usarla dalle applicazioni sviluppate. In particolare:
    - Un *endpoint* HTTP a cui le applicazioni client possono inviare richieste.
    - Due *chiavi* che possono essere usate per l'autenticazione (le applicazioni client possono usare entrambe le chiavi. Una pratica comune consiste nell'usarne una per lo sviluppo e un'altra per la produzione. È possibile rigenerare facilmente la chiave di sviluppo dopo che gli sviluppatori hanno terminato il lavoro per impedire l'accesso continuo).
    - La *località* in cui è ospitata la risorsa. Questa informazione è necessaria per le richieste ad alcune API, ma non a tutte.
2. È ora possibile usare il comando seguente per ottenere l'elenco delle chiavi di Servizi di Azure AI, sostituendo *&lt;resourceName&gt;* con il nome della risorsa di Servizi di Azure AI e *&lt;resourceGroup&gt;* con il nome del gruppo di risorse in cui è stata creata.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

    Il comando restituisce un elenco delle chiavi per la risorsa di Servizi di Azure AI. Sono presenti due chiavi denominate **key1** e **key2**.

    > **Suggerimento**: Se l'interfaccia della riga di comando di Azure non è ancora stata autenticata, eseguire `az login` e accedere all'account.

3. Per testare i Servizi di Azure AI, è possibile usare **curl**, uno strumento da riga di comando per le richieste HTTP. Nella cartella **02-ai-services-security** aprire **rest-test.cmd** e modificare il comando **curl** in esso contenuto (illustrato di seguito), sostituendo *&lt;yourEndpoint&gt;* e *&lt;yourKey&gt;* con l'URI dell'endpoint e la chiave **Key1** per usare l'API Analisi del testo nella risorsa di Servizi di Azure AI.

    ```bash
    curl -X POST "<yourEndpoint>/language/:analyze-text?api-version=2023-04-01" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: 81468b6728294aab99c489664a818197" --data-ascii "{'analysisInput':{'documents':[{'id':1,'text':'hello'}]}, 'kind': 'LanguageDetection'}"
    ```

4. Salvare le modifiche e quindi eseguire il comando seguente:

    ```
    ./rest-test.cmd
    ```

Il comando restituisce un documento JSON contenente informazioni sulla lingua rilevata nei dati di input (che sarà l'inglese).

5. Se una chiave viene compromessa o gli sviluppatori che la possiedono non richiedono più l'accesso, è possibile rigenerarla nel portale o tramite l'interfaccia della riga di comando di Azure. Eseguire il comando seguente per rigenerare la **chiave key1** (sostituendo *&lt;resourceName&gt;* e *&lt;resourceGroup&gt;* per la risorsa).

    ```
    az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1
    ```

Viene restituito l'elenco delle chiavi per la risorsa di Servizi di Azure AI. Si noti che **key1** è cambiato dall'ultimo recupero.

6. Eseguire nuovamente il comando **rest-test** con la vecchia chiave (è possibile usare la freccia **^** per scorrere i comandi precedenti) e verificare che ora abbia esito negativo.
7. Modificare il comando *curl* in **rest-test.cmd** sostituendo la chiave con il nuovo valore **key1** e salvare le modifiche. Eseguire quindi di nuovo il comando **rest-test** e verificare che abbia esito positivo.

> **Suggerimento:** in questo esercizio sono stati usati i nomi completi dei parametri dell'interfaccia della riga di comando di Azure, ad esempio **--resource-group**.  È anche possibile usare alternative più brevi, ad esempio **-g**, per rendere i comandi meno dettagliati (ma un po' più difficili da comprendere).  Il [Riferimento ai comandi dell'interfaccia della riga di comando di Servizi di Azure AI](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest) elenca le opzioni dei parametri per ogni comando dell'interfaccia della riga di comando di Servizi di Azure AI.

## Proteggere l'accesso alla chiave con Azure Key Vault

È possibile sviluppare applicazioni che utilizzano Servizi di Azure AI usando una chiave per l'autenticazione. Ciò significa tuttavia che il codice dell'applicazione deve essere in grado di ottenere la chiave. Un'opzione consiste nell'archiviare la chiave in una variabile di ambiente o in un file di configurazione in cui viene distribuita l'applicazione, ma questo approccio lascia la chiave vulnerabile agli accessi non autorizzati. Un approccio migliore quando si sviluppano applicazioni in Azure consiste nell'archiviare la chiave in modo sicuro in Azure Key Vault e fornire l'accesso alla chiave tramite un'*identità gestita* (in altre parole, un account utente usato dall'applicazione stessa).

### Creare un insieme di credenziali delle chiavi e aggiungere un segreto

Prima di tutto, è necessario creare un insieme di credenziali delle chiavi e aggiungere un *segreto* per la chiave di Servizi di Azure AI.

1. Prendere nota del valore **key1** per la risorsa di Servizi di Azure AI o copiarlo negli Appunti.
2. Nella pagina **Home** del portale di Azure selezionare il pulsante **&#65291;Crea una risorsa**, cercare *Key Vault* e creare una risorsa **Key Vault** con le impostazioni seguenti:

    - **Scheda Informazioni di base**
        - **Sottoscrizione**: *la sottoscrizione di Azure usata*
        - **Gruppo di risorse**: *lo stesso gruppo di risorse della risorsa di Servizi di Azure AI*
        - **Nome dell'insieme di credenziali delle chiavi**: *immettere un nome univoco*
        - **Area**: *La stessa area della risorsa di Servizi di Azure AI*
        - **Piano tariffario**: Standard
    
    - Scheda **Configurazione di accesso**
        -  **Modello di autorizzazione**: Criterio di accesso all'insieme di credenziali
        - Scorrere verso il basso fino alla sezione **Criteri di accesso** e selezionare l'utente usando la casella a sinistra. Selezionare quindi **Rivedi e crea** e selezionare **Crea** per creare la risorsa.
     
3. Attendere il completamento della distribuzione e quindi passare alla risorsa dell'insieme di credenziali delle chiavi.
4. Nel pannello di navigazione a sinistra selezionare **Segreti** (nella sezione Oggetti).
5. Selezionare **+ Genera/Importa** e aggiungere un nuovo segreto con le impostazioni seguenti:
    - **Opzioni di caricamento**: Manuale
    - **Nome**: AI-Services-Key * (è importante che corrisponda esattamente, perché in seguito si eseguirà il codice che recupera il segreto in base a questo nome)*
    - **Valore**: *Key1** dei Servizi di Azure AI **dell'utente*
6. Seleziona **Crea**.

### Creare un'entità servizio

Per accedere al segreto nell'insieme di credenziali delle chiavi, l'applicazione deve usare un'entità servizio che abbia accesso al segreto. Si userà l'interfaccia della riga di comando di Azure per creare l'entità servizio, individuare il relativo ID oggetto e concedere l'accesso al segreto in Azure Vault.

1. Eseguire il comando dell'interfaccia della riga di comando di Azure seguente, sostituendo *&lt;spName&gt;* con un nome appropriato univoco per un'identità dell'applicazione, ad esempio *ai-app* con le iniziali aggiunte alla fine. Il nome deve essere univoco all'interno del tenant. Sostituire anche *&lt;subscriptionId&gt;* e *&lt;resourceGroup&gt;* con i valori corretti per l'ID sottoscrizione e il gruppo di risorse contenente le risorse di Servizi di Azure AI e dell'insieme di credenziali delle chiavi:

    > **Suggerimento**: se non si è certi dell'ID sottoscrizione, usare il comando **az account show** per recuperare le informazioni sulla sottoscrizione. L'ID sottoscrizione è l'attributo **id** nell'output. Se viene visualizzato un errore sull'oggetto già esistente, scegliere un nome univoco diverso.

    ```
    az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    ```

L'output di questo comando include informazioni sulla nuova entità servizio. L'aspetto sarà simile al seguente:

    ```
    {
        "appId": "abcd12345efghi67890jklmn",
        "displayName": "api://ai-app-",
        "password": "1a2b3c4d5e6f7g8h9i0j",
        "tenant": "1234abcd5678fghi90jklm"
    }
    ```

Prendere nota dei valori **appId**, **password** e **tenant**, in quanto saranno necessari in un secondo momento (se si chiude questo terminale, non sarà possibile recuperare la password. È quindi importante prendere nota dei valori. È possibile incollare l'output in un nuovo file di testo sul proprio computer locale per assicurarsi di poter trovare i valori necessari in un secondo momento).

2. Per ottenere l'**ID oggetto** dell'entità servizio, eseguire il comando seguente dell'interfaccia della riga di comando di Azure, sostituendo *&lt;appId&gt;* con il valore dell'ID app dell'entità servizio.

    ```
    az ad sp show --id <appId>
    ```

3. Copiare il valore `id` nel codice JSON restituito in risposta. 
3. Per assegnare alla nuova entità servizio l'autorizzazione per accedere ai segreti in Key Vault, eseguire il comando seguente dell'interfaccia della riga di comando di Azure, sostituendo *&lt;keyVaultName&gt;* con il nome della risorsa di Azure Key Vault e *&lt;objectId&gt;* con il valore dell'ID appena copiato.

    ```
    az keyvault set-policy -n <keyVaultName> --object-id <objectId> --secret-permissions get list
    ```

### Usare l'entità servizio in un'applicazione

A questo punto è possibile usare l'identità dell'entità servizio in un'applicazione, in modo che possa accedere alla chiave segreta di Servizi cognitivi nell'insieme di credenziali delle chiavi e usarla per connettersi alla risorsa di Servizi cognitivi.

> **Nota**: in questo esercizio le credenziali dell'entità servizio verranno archiviate nella configurazione dell'applicazione e verranno usate per autenticare un'identità **ClientSecretCredential** nel codice dell'applicazione. Questa operazione è valida per lo sviluppo e il test, ma in un'applicazione di produzione reale un amministratore assegna un'*identità gestita* all'applicazione in modo che usi l'identità dell'entità servizio per accedere alle risorse, senza memorizzare nella cache o archiviare la password.

1. Nel terminale passare alla cartella **C-Sharp** o **Python** a seconda delle preferenze del linguaggio eseguendo `cd C-Sharp` o `cd Python`. Eseguire quindi `cd keyvault_client` per passare alla cartella dell'app.
2. Installare i pacchetti necessari per usare Azure Key Vault e l'API Analisi del testo nella risorsa di Servizi di Azure AI eseguendo il comando appropriato per il linguaggio scelto:

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    dotnet add package Azure.Identity --version 1.5.0
    dotnet add package Azure.Security.KeyVault.Secrets --version 4.2.0-beta.3
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.3.0
    pip install azure-identity==1.5.0
    pip install azure-keyvault-secrets==4.2.0
    ```

3. Visualizzare il contenuto della cartella **keyvault-client** e notare che include un file per le impostazioni di configurazione:
    - **C#**: appsettings.json
    - **Python**: .env

    Aprire il file di configurazione e aggiornare i valori di configurazione contenuti in base alle impostazioni seguenti:
    
    - **Endpoint** per la risorsa di Servizi di Azure AI
    - Nome della risorsa di **Azure Key Vault**
    - **Tenant** dell'entità servizio
    - **ID applicazione** dell'entità servizio
    - **Password** dell'entità servizio

     Salvare le modifiche premendo **CTRL+S**.
4. Si noti che la cartella **keyvault-client** contiene un file di codice per l'applicazione client:

    - **C#**: Program.cs
    - **Python**: keyvault-client.py

    Aprire il file di codice ed esaminare il codice in esso contenuto, notando i dettagli seguenti:
    - Lo spazio dei nomi per l'SDK installato viene importato
    - Il codice nella funzione **Main** recupera le impostazioni di configurazione dell'applicazione e quindi usa le credenziali dell'entità servizio per ottenere la chiave di Servizi di Azure AI dall'insieme di credenziali delle chiavi.
    - La funzione **GetLanguage** usa l'SDK per creare un client per il servizio e quindi usa il client per rilevare la lingua del testo immesso.
5. Immettere il comando seguente per eseguire il programma:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python keyvault-client.py
    ```

6. Quando richiesto, immettere del testo ed esaminare la lingua rilevata dal servizio. Ad esempio, provare a immettere "Hello", "Bonjour" e "Gracias".
7. Al termine del test dell'applicazione, immettere "quit" per arrestare il programma.

## Pulire le risorse

Se non si usano le risorse di Azure create in questo lab per altri moduli di training, è possibile eliminarle per evitare addebiti aggiuntivi.

1. Aprire il portale di Azure in `https://portal.azure.com` e nella barra di ricerca superiore cercare le risorse create in questo lab.

2. Nella pagina della risorsa selezionare **Elimina** e seguire le istruzioni per eliminare la risorsa. In alternativa, è possibile eliminare l'intero gruppo di risorse per pulire tutte le risorse contemporaneamente.

## Ulteriori informazioni

Per altre informazioni sulla protezione dei Servizi di Azure AI, vedere la documentazione [Sicurezza dei Servizi di Azure AI](https://docs.microsoft.com/azure/ai-services/security-features).
