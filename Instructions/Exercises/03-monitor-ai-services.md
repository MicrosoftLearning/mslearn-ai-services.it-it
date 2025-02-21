---
lab:
  title: Monitorare i Servizi di Azure AI
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Monitorare i Servizi di Azure AI

Servizi di Azure AI può essere una parte essenziale di un'infrastruttura generale delle applicazioni. È importante essere in grado di monitorare l'attività e ricevere avvisi in caso di problemi che potrebbero richiedere attenzione.

## Clonare il repository in Visual Studio Code

Il codice verrà sviluppato usando Visual Studio Code. I file di codice per l'app sono stati forniti in un repository GitHub.

> **Suggerimento**: Se il repository **mslearn-ai-services** è già stato clonato, aprirlo in Visual Studio Code. In caso contrario, eseguire i passaggi seguenti per clonarlo nell'ambiente di sviluppo.

1. Avviare Visual Studio Code.
2. Aprire il riquadro comandi (MAIUSC+CTRL+P) ed eseguire un comando **Git: Clone** per clonare il repository `https://github.com/MicrosoftLearning/mslearn-ai-services` in una cartella locale. Non è importante usare una cartella specifica.
3. Dopo la clonazione del repository, aprire la cartella in Visual Studio Code.
4. Attendere l'installazione di file aggiuntivi per supportare i progetti di codice C# nel repository, se necessario

    > **Nota**: se viene richiesto di aggiungere gli asset necessari per la compilazione e il debug, selezionare **Non adesso**.

5. Espandere la cartella `Labfiles/03-monitor-ai-services`.

## Effettuare il provisioning di una risorsa di Servizi di Azure AI

Se non è già disponibile nella sottoscrizione, sarà necessario effettuare il provisioning di una risorsa di **Servizi di Azure AI**.

1. Aprire il portale di Azure all'indirizzo `https://portal.azure.com` ed eseguire l'accesso usando l'account Microsoft associato alla sottoscrizione di Azure.
2. Nella barra di ricerca superiore, cercare *Servizi di Azure AI*, selezionare **Account multiservizio di Servizi di Azure AI** e creare una risorsa con le impostazioni seguenti:
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *scegliere o creare un gruppo di risorse. Se si usa una sottoscrizione con restrizioni, si potrebbe non essere autorizzati a creare un nuovo gruppo di risorse. Usare quello fornito*
    - **Area**: *scegliere una qualsiasi area disponibile*
    - **Nome**: *immettere un nome univoco*
    - **Piano tariffario**: Standard S0.
3. Selezionare le caselle di controllo necessarie e creare la risorsa.
4. Attendere il completamento della distribuzione e quindi visualizzare i relativi dettagli.
5. Al termine della distribuzione, passare alla risorsa e visualizzare la rispettiva pagina **Chiavi ed endpoint**. Prendere nota dell'URI dell'endpoint, che sarà necessario più avanti.

## Configurare un avviso

Iniziamo il monitoraggio definendo una regola di avviso in modo da poter rilevare l'attività nella risorsa di Servizi di Azure AI.

1. Nel portale di Azure passare alla risorsa di Servizi di Azure AI e visualizzare la relativa pagina **Avvisi** (nella sezione **Monitoraggio**).
2. Selezionare **Crea +** elenco a discesa, quindi selezionare **Regola di avviso**
3. Nella pagina **Creare una regola di avviso**, in **Ambito**verificare che la risorsa di Servizi di Azure AI sia elencata. (Chiudere il riquadro **Seleziona un segnale** se aperto)
4. Selezionare la scheda **Condizione** e selezionare il collegamento **Visualizza tutti i segnali** per visualizzare il riquadro **Seleziona un segnale** visualizzato a destra, in cui è possibile selezionare un tipo di segnale da monitorare.
5. Nell'elenco **tipo di segnale** scorrere verso il basso fino alla sezione **log attività**, quindi selezionare **Chiavi elenco (Account API di Servizi cognitivi)**. Quindi seleziona **Applica**.
6. Esaminare l'attività nelle ultime 6 ore.
7. Selezionare la scheda **Azioni**. Si noti che è possibile specificare un *gruppo di azioni*. In questo modo è possibile configurare azioni automatiche quando viene generato un avviso, ad esempio l'invio di una notifica tramite posta elettronica. Questa operazione non verrà eseguita in questo esercizio, ma può essere utile eseguirla in un ambiente di produzione.
8. Nella scheda **Dettagli** impostare **Nome regola di avviso** su **Key List Alert**.
9. Selezionare **Rivedi e crea**. 
10. Esaminare la configurazione per l'avviso. Selezionare **Crea** e attendere la creazione della regola di avviso.
11. È ora possibile usare il comando seguente per ottenere l'elenco delle chiavi di Servizi di Azure AI, sostituendo *&lt;resourceName&gt;* con il nome della risorsa di Servizi di Azure AI e *&lt;resourceGroup&gt;* con il nome del gruppo di risorse in cui è stata creata.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

    Il comando restituisce un elenco delle chiavi per la risorsa di Servizi di Azure AI.

    > **Nota** Se non è stato eseguito l'accesso all'interfaccia della riga di comando di Azure, potrebbe essere necessario eseguire `az login` prima che il comando Elenca chiavi entri in funzione.

12. Tornare al browser contenente il portale di Azure e aggiornare la **Pagina Avvisi**. Nella tabella verrà visualizzato un avviso di **gravità 4** (in caso contrario, attendere fino a cinque minuti e aggiornare di nuovo).
13. Selezionare l'avviso per visualizzarne i dettagli.

## Visualizzare una metrica

Oltre a definire gli avvisi, è possibile visualizzare le metriche per la risorsa di Servizi di Azure AI per monitorarne l'utilizzo.

1. Nel portale di Azure, nella pagina relativa alla risorsa Servizi di Azure AI, selezionare **Metriche** (nella sezione **Monitoraggio**).
2. Se non è presente alcun grafico esistente, selezionare **+ Nuovo grafico**. Quindi, nell'elenco **Metrica** esaminare le metriche che è possibile visualizzare e selezionare **Chiamate totali**.
3. Nell'elenco **Aggregazione**, selezionare **Conteggio**.  In questo modo sarà possibile monitorare le chiamate totali alla risorsa del servizio di Azure per intelligenza artificiale; un'opzione utile per determinare il livello d'uso del servizio in un periodo di tempo.
4. Per generare alcune richieste al servizio di Azure per intelligenza artificiale, si userà **curl**, uno strumento a riga di comando per le richieste HTTP. Nell'editor aprire **rest-test.cmd** e modificare il comando **curl** che contiene (illustrato di seguito), sostituendo *&lt;yourEndpoint&gt;* e *&lt;yourKey&gt;* con l'URI dell'endpoint e la chiave **Key1** per usare l'API Analisi del testo nella risorsa Servizi di Azure AI.

    ```
    curl -X POST "<your-endpoint>/language/:analyze-text?api-version=2023-04-01" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <your-key>" --data-ascii "{'analysisInput':{'documents':[{'id':1,'text':'hello'}]}, 'kind': 'LanguageDetection'}"
    ```

5. Salvare le modifiche e quindi eseguire il comando seguente:

    ```
    ./rest-test.cmd
    ```

    Il comando restituisce un documento JSON contenente informazioni sulla lingua rilevata nei dati di input (che sarà l'inglese).

6. Eseguire nuovamente il comando **rest-test** più volte per generare alcune attività di chiamata (è possibile usare il tasto **^** per scorrere i comandi precedenti).
7. Tornare alla pagina **Metriche** nel portale di Azure e aggiornare il grafico del conteggio **Chiamate totali**. Potrebbero essere necessari alcuni minuti prima che le chiamate effettuate usando *curl* si riflettano nel grafico. Continuare ad aggiornare il grafico fino a quando non viene aggiornato includendole.

## Pulire le risorse

Se non si usano le risorse di Azure create in questo lab per altri moduli di training, è possibile eliminarle per evitare addebiti aggiuntivi.

1. Aprire il portale di Azure in `https://portal.azure.com` e nella barra di ricerca superiore cercare le risorse create in questo lab.

2. Nella pagina della risorsa selezionare **Elimina** e seguire le istruzioni per eliminare la risorsa. In alternativa, è possibile eliminare l'intero gruppo di risorse per pulire tutte le risorse contemporaneamente.

## Ulteriori informazioni

Una delle opzioni per il monitoraggio di Servizi di Azure AI consiste nell'usare la*registrazione diagnostica*. Dopo l'abilitazione, la registrazione diagnostica acquisisce informazioni dettagliate sull'utilizzo delle risorse di Servizi di Azure AI e può essere un utile strumento di monitoraggio e debug. L'operazione può richiedere più di un'ora dopo aver configurato la registrazione diagnostica per generare informazioni, motivo per cui non è stata esaminata in questo esercizio; ma è possibile trovare altre informazioni nella [documentazione di Servizi di Azure AI](https://docs.microsoft.com/azure/ai-services/diagnostic-logging).
