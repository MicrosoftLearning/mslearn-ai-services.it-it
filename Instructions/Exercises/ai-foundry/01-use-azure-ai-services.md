---
lab:
  title: Introduzione ai Servizi di Azure AI
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Introduzione ai Servizi di Azure AI

In questo esercizio è possibile iniziare a usare i Servizi di Azure AI creando una risorsa di **Servizi di Azure AI** nella sottoscrizione di Azure e usandola da un'applicazione client. L'obiettivo dell'esercizio non è acquisire competenze in un particolare servizio, ma piuttosto acquisire familiarità con un modello generale per il provisioning e l'uso dei Servizi di Azure AI come sviluppatore.

## Clonare il repository in Visual Studio Code

Il codice verrà sviluppato usando Visual Studio Code. I file di codice per l'app sono stati forniti in un repository GitHub.

> **Suggerimento**: Se il repository **mslearn-ai-services** è già stato clonato, aprirlo in Visual Studio Code. In caso contrario, eseguire i passaggi seguenti per clonarlo nell'ambiente di sviluppo.

1. Avviare Visual Studio Code.
2. Aprire il riquadro comandi (MAIUSC+CTRL+P) ed eseguire un comando **Git: Clone** per clonare il repository `https://github.com/MicrosoftLearning/mslearn-ai-services` in una cartella locale. Non è importante usare una cartella specifica.
3. Dopo la clonazione del repository, aprire la cartella in Visual Studio Code.
4. Attendere l'installazione di file aggiuntivi per supportare i progetti di codice C# nel repository, se necessario

    > **Nota**: se viene richiesto di aggiungere gli asset necessari per la compilazione e il debug, selezionare **Non adesso**.

5. Espandere la cartella `Labfiles/01-use-azure-ai-services`.

È stato fornito il codice sia per C# che per Python. Espandere la cartella del linguaggio preferito.

## Effettuare il provisioning di una risorsa di Servizi di Azure AI

I servizi basati sul cloud di Servizi di Azure AI incapsulano le funzionalità di intelligenza artificiale che è possibile incorporare nelle applicazioni. È possibile effettuare il provisioning di singole risorse dei Servizi di Azure AI per API specifiche (ad esempio, **Lingua** o **Visione**) oppure è possibile effettuare il provisioning di una singola risorsa di **Servizi di Azure AI** che fornisce l'accesso a più API di Servizi di Azure AI tramite un singolo endpoint e una singola chiave. In questo caso si userà una singola risorsa di **Servizi di Azure AI**.

1. Aprire il portale di Azure all'indirizzo `https://portal.azure.com` ed eseguire l'accesso usando l'account Microsoft associato alla sottoscrizione di Azure.
2. Nella barra di ricerca superiore cercare *Servizi di Azure AI*, selezionare **Servizi di Azure AI** e creare una risorsa account multiservizio di Servizi di Azure AI con le impostazioni seguenti:
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *scegliere o creare un gruppo di risorse. Se si usa una sottoscrizione con restrizioni, si potrebbe non essere autorizzati a creare un nuovo gruppo di risorse. Usare quello fornito*
    - **Area**: *scegliere una qualsiasi area disponibile*
    - **Nome**: *immettere un nome univoco*
    - **Piano tariffario**: Standard S0.
3. Selezionare le caselle di controllo necessarie e creare la risorsa.
4. Attendere il completamento della distribuzione e quindi visualizzare i relativi dettagli.
5. Passare alla risorsa e visualizzare la relativa pagina **Chiavi ed endpoint**. Questa pagina contiene le informazioni necessarie per connettersi alla risorsa e usarla dalle applicazioni sviluppate. In particolare:
    - Un *endpoint* HTTP a cui le applicazioni client possono inviare richieste.
    - Due *chiavi* che possono essere usate per l'autenticazione (le applicazioni client possono usare entrambe le chiavi per l'autenticazione).
    - La *località* in cui è ospitata la risorsa. Questa informazione è necessaria per le richieste ad alcune API, ma non a tutte.

## Usare un'interfaccia REST

Le API di Servizi di Azure AI sono basate su REST, quindi è possibile usarle inviando richieste JSON su HTTP. In questo esempio si esplorerà un'applicazione console che usa l'API REST **Lingua** per eseguire il rilevamento della lingua; ma il principio di base è lo stesso per tutte le API supportate dalla risorsa servizi di intelligenza artificiale di Azure.

> **Nota**: in questo esercizio è possibile scegliere se usare l'API REST da **C#** o **Python**. Nella procedura seguente eseguire le azioni appropriate per il linguaggio scelto.

1. In Visual Studio Code espandere la cartella **C-Sharp** o **Python** a seconda delle preferenze del linguaggio.
2. Visualizzare il contenuto della cartella **rest-client** e notare che include un file per le impostazioni di configurazione:

    - **C#**: appsettings.json
    - **Python**: .env

    Aprire il file di configurazione e aggiornare i valori in modo che includano l'**endpoint** e una **chiave** di autenticazione per la risorsa di Servizi di Azure AI. Salva le modifiche.

3. Si noti che la cartella **rest-client** contiene un file di codice per l'applicazione client:

    - **C#**: Program.cs
    - **Python**: rest-client.py

    Aprire il file di codice ed esaminare il codice in esso contenuto, notando i dettagli seguenti:
    - Vengono importati vari spazi dei nomi per abilitare la comunicazione HTTP
    - Il codice nella funzione**Main** recupera l'endpoint e la chiave per la risorsa di Servizi di Azure AI, che verranno usati per inviare richieste REST al servizio Analisi del testo.
    - Il programma accetta l'input dell'utente e usa la funzione **GetLanguage** per chiamare l'API REST di rilevamento della lingua di Analisi del testo per l'endpoint di Servizi di Azure AI per rilevare la lingua del testo immesso.
    - La richiesta inviata all'API è costituita da un oggetto JSON contenente i dati di input, in questo caso una raccolta di oggetti **documento**, ognuno dei quali con un **ID** e un **testo**.
    - La chiave per il servizio è inclusa nell'intestazione della richiesta per autenticare l'applicazione client.
    - La risposta del servizio è un oggetto JSON che l'applicazione client può analizzare.

4. Fare clic con il pulsante destro del mouse sulla cartella **rest-client**, selezionare *Apri nel terminale integrato* ed eseguire il comando seguente:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    pip install python-dotenv
    python rest-client.py
    ```

5. Quando richiesto, immettere un testo ed esaminare la lingua rilevata dal servizio, che viene restituita nella risposta JSON. Ad esempio, provare a immettere "Hello", "Bonjour" e "Gracias".
6. Al termine del test dell'applicazione, immettere "quit" per arrestare il programma.

## Usare un SDK

È possibile scrivere codice che usa direttamente le API REST dei Servizi di Azure AI, ma sono disponibili SDK (Software Development Kit) per molti linguaggi di programmazione comuni, tra cui Microsoft C#, Python, Java e Node.js. L'uso di un SDK può semplificare notevolmente lo sviluppo di applicazioni che usano Servizi di Azure AI.

1. In Visual Studio Code espandere la cartella **sdk-client** nella cartella **C-Sharp** o **Python**, in base alle preferenze del linguaggio. Eseguire quindi `cd ../sdk-client` per passare alla cartella **sdk-client** pertinente.

2. Installare il pacchetto di Analisi del testo SDK eseguendo il comando appropriato in base alle preferenze del linguaggio:

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.3.0
    ```

3. Visualizzare il contenuto della cartella **sdk-client** e notare che include un file per le impostazioni di configurazione:

    - **C#**: appsettings.json
    - **Python**: .env

    Aprire il file di configurazione e aggiornare i valori in modo che includano l'**endpoint** e una **chiave** di autenticazione per la risorsa di Servizi di Azure AI. Salva le modifiche.
    
4. Si noti che la cartella **sdk-client** contiene un file di codice per l'applicazione client:

    - **C#**: Program.cs
    - **Python**: sdk-client.py

    Aprire il file di codice ed esaminare il codice in esso contenuto, notando i dettagli seguenti:
    - Lo spazio dei nomi per l'SDK installato viene importato
    - Il codice nella funzione **Main** recupera l'endpoint e la chiave per la risorsa di Servizi di Azure AI, che verranno usati con l'SDK per creare un client per il servizio Analisi del testo.
    - La funzione **GetLanguage** usa l'SDK per creare un client per il servizio e quindi usa il client per rilevare la lingua del testo immesso.

5. Tornare al terminale, assicurarsi di entrare nella cartella **sdk-client** e immettere il comando seguente per eseguire il programma:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python sdk-client.py
    ```

6. Quando richiesto, immettere del testo ed esaminare la lingua rilevata dal servizio. Ad esempio, provare a immettere "Goodbye", "Au revoir" e "Hasta la vista".
7. Al termine del test dell'applicazione, immettere "quit" per arrestare il programma.

> **Nota**: alcune lingue che richiedono set di caratteri Unicode potrebbero non essere riconosciute in questa semplice applicazione console.

## Pulire le risorse

Se non si usano le risorse di Azure create in questo lab per altri moduli di training, è possibile eliminarle per evitare addebiti aggiuntivi.

1. Aprire il portale di Azure in `https://portal.azure.com` e nella barra di ricerca superiore cercare le risorse create in questo lab.

2. Nella pagina della risorsa selezionare **Elimina** e seguire le istruzioni per eliminare la risorsa. In alternativa, è possibile eliminare l'intero gruppo di risorse per pulire tutte le risorse contemporaneamente.

## Ulteriori informazioni

Per altre informazioni sui Servizi di Azure AI, vedere la pagina Web [Servizi di Azure AI](https://docs.microsoft.com/azure/ai-services/what-are-ai-services).