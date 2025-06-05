---
lab:
  title: Usare un contenitore di Servizi di Azure AI
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Usare un contenitore di Servizi di Azure AI

L'uso dei Servizi di Azure AI ospitati in Azure consente agli sviluppatori di applicazioni di concentrarsi sull'infrastruttura per il proprio codice, sfruttando al contempo i servizi scalabili gestiti da Microsoft. In molti scenari, però, le organizzazioni richiedono un maggiore controllo sull'infrastruttura del servizio e sui dati passati tra i servizi.

Molte API dei Servizi di Azure AI possono essere in pacchetto e distribuite in un *contenitore*, consentendo alle organizzazioni di ospitare i Servizi di Azure AI nella propria infrastruttura, ad esempio nei server Docker locali, Istanze di Azure Container o cluster di servizi Azure Kubernetes. I Servizi di Azure AI in contenitori devono comunicare con un account di Servizi di Azure AI basato su Azure per supportare la fatturazione, ma i dati applicazione non vengono passati al servizio back-end e le organizzazioni hanno un maggiore controllo sulla configurazione della distribuzione dei contenitori, consentendo soluzioni personalizzate per l'autenticazione, la scalabilità e altre considerazioni.

> **Nota**: Al momento è in fase di analisi un problema riscontrato da alcuni utenti per cui i contenitori non vengono distribuiti correttamente e le chiamate a tali contenitori hanno esito negativo. Gli aggiornamenti di questo lab verranno forniti non appena il problema è stato risolto.

## Clonare il repository in Visual Studio Code

Il codice verrà sviluppato usando Visual Studio Code. I file di codice per l'app sono stati forniti in un repository GitHub.

> **Suggerimento**: Se il repository **mslearn-ai-services** è già stato clonato, aprirlo in Visual Studio Code. In caso contrario, eseguire i passaggi seguenti per clonarlo nell'ambiente di sviluppo.

1. Avviare Visual Studio Code.
2. Aprire il riquadro comandi (MAIUSC+CTRL+P) ed eseguire un comando **Git: Clone** per clonare il repository `https://github.com/MicrosoftLearning/mslearn-ai-services` in una cartella locale. Non è importante usare una cartella specifica.
3. Dopo la clonazione del repository, aprire la cartella in Visual Studio Code.
4. Attendere l'installazione di file aggiuntivi per supportare i progetti di codice C# nel repository, se necessario

    > **Nota**: se viene richiesto di aggiungere gli asset necessari per la compilazione e il debug, selezionare **Non adesso**.

5. Espandere la cartella `Labfiles/04-use-a-container`.

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
5. Al termine della distribuzione, passare alla risorsa e visualizzare la rispettiva pagina **Chiavi ed endpoint**. Nella procedura successiva saranno necessari l'endpoint e una delle chiavi indicate in questa pagina.

## Distribuire ed eseguire un contenitore di analisi del sentiment

Molte API dei Servizi di Azure AI di uso comune sono disponibili nelle immagini del contenitore. Per un elenco completo, vedere la [documentazione dei Servizi di Azure AI](https://learn.microsoft.com/en-us/azure/ai-services/cognitive-services-container-support#containers-in-azure-ai-services). In questo esercizio si userà l'immagine del contenitore per l'API di *Analisi del sentiment* di Analisi del testo, ma i principi sono gli stessi per tutte le immagini disponibili.

1. Nella pagina **Home** del portale di Azure selezionare il pulsante **&#65291;Crea una risorsa**, cercare *istanze di contenitore* e creare una risorsa **Istanze di Container** con le impostazioni seguenti:

    - **Nozioni di base**:
        - **Sottoscrizione**: *la sottoscrizione di Azure usata*
        - **Gruppo di risorse**: *Scegliere il gruppo di risorse contenente la risorsa dei Servizi di Azure AI*
        - **Nome contenitore**: *immettere un nome univoco*
        - **Area**: *scegliere una qualsiasi area disponibile*
        - **Zone di disponibilità**: Nessuna
        - **SKU**: Standard
        - **Origine immagine**: Altro registro
        - **Tipo di immagine**: Pubblico
        - **Immagine**: `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:latest`
        - **Tipo di sistema operativo**: Linux
        - **Dimensioni**: 1 vCPU, 8 GB di memoria
    - **Rete**:
        - **Tipo di rete**: Pubblico
        - **Etichetta del nome DNS**: *immettere un nome univoco per l'endpoint contenitore*
        - **Porte**: *cambiare la porta TCP da 80 a **5000***
    - **Avanzate**:
        - **Criteri di riavvio**: In caso di esito negativo
        - **Variabili di ambiente**:

            | Contrassegna come sicura | Chiave | Valore |
            | -------------- | --- | ----- |
            | Sì | `ApiKey` | *Entrambe le chiavi per la risorsa dei Servizi di Azure AI* |
            | Sì | `Billing` | *L'URI dell'endpoint per la risorsa dei Servizi di Azure AI* |
            | No | `Eula` | `accept` |

        - **Override comando**: [ ]
        - **Gestione delle chiavi**: Chiavi gestite da Microsoft (MMK)
    - **Tag:**
        - *Non aggiungere tag*

2. Selezionare **Rivedi e crea** e quindi **Crea**. Attendere il completamento della distribuzione e quindi passare alla risorsa distribuita.
    > **Nota** Si noti che la distribuzione di un contenitore di Azure per intelligenza artificiale in Istanze di Azure Container richiede in genere 5-10 minuti (provisioning) prima che siano pronti per l'uso.
3. Osservare le proprietà seguenti della risorsa dell'istanza di contenitore nella pagina **Panoramica**:
    - **Stato**: deve essere *In esecuzione*.
    - **Indirizzo IP**: indirizzo IP pubblico che è possibile usare per accedere alle istanze di contenitore.
    - **FQDN**: *nome di dominio completo* della risorsa delle istanze di contenitore. È possibile usarlo in alternativa all'indirizzo IP per accedere alle istanze di contenitore.

    > **Nota**: in questo esercizio è stata distribuita l'immagine del contenitore di Servizi di Azure AI per l'analisi del sentiment in una risorsa di Istanze di Azure Container. È possibile usare un approccio simile per distribuirlo in un host *[Docker](https://www.docker.com/products/docker-desktop)* nel computer o nella rete eseguendo il comando seguente (su una singola riga) per distribuire il contenitore di analisi del sentiment nell'istanza di Docker locale, sostituendo *&lt;yourEndpoint&gt;* e *&lt;yourKey&gt;* con l'URI dell'endpoint e una delle chiavi per la risorsa di Servizi di Azure AI.
    > Il comando cerca l'immagine nel computer locale e, se non la trova, la estrae dal registro immagini *mcr.microsoft.com* e la distribuisce nell'istanza di Docker. Al termine della distribuzione, il contenitore verrà avviato e sarà in ascolto delle richieste in ingresso sulla porta 5000.

    ```
    docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:latest Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
    ```

## Usare il contenitore

1. Nel proprio editor, aprire **rest-test.cmd** e modificare il comando **curl** contenuto (mostrato di seguito), sostituendo *&lt;your_ACI_IP_address_or_FQDN&gt;* con l'indirizzo IP o il nome di dominio completo (FQDN) del contenitore.

    ```
    curl -X POST "http://<your_ACI_IP_address_or_FQDN>:5000/text/analytics/v3.1/sentiment" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'The performance was amazing! The sound could have been clearer.'},{'id':2,'text':'The food and service were unacceptable. While the host was nice, the waiter was rude and food was cold.'}]}"
    ```

2. Salvare le modifiche apportate allo script premendo **CTRL+S**. Tenere presente che non è necessario specificare l'endpoint o la chiave di Servizi di Azure AI. La richiesta viene elaborata dal servizio in contenitori. A sua volta, il contenitore comunica periodicamente con il servizio in Azure per segnalare l'utilizzo ai fini della fatturazione, ma non invia i dati della richiesta.
3. Immettere il comando seguente per eseguire lo script:

    ```
    ./rest-test.cmd
    ```

4. Verificare che il comando restituisca un documento JSON contenente informazioni sul sentiment rilevato nei due documenti di input (che devono essere positivo e negativo, in quest'ordine).

## Risoluzione dei problemi

> **Nota:**  
> La visualizzazione del contenitore come "In corso di esecuzione" nel portale di Azure **non** significa sempre che sia pronta per elaborare le richieste. Se si tenta di usare `curl` e si ricevono errori come:
>
> ```text
> curl: (52) Empty reply from server
> curl: (56) Recv failure: Connection reset by peer
> ```
>
> Ciò può significare che il contenitore non è stato avviato con i parametri o l'ambiente corretti oppure mancano i file di modello o l'accesso a Internet per il download del modello.

### Alternativa: distribuzione del contenitore usando l'interfaccia della riga di comando di Azure

È possibile provare il comando seguente (sostituire l'endpoint `rg-name`, `container-name``ApiKey` e `Billing` con i valori):

```bash
az container create \
  --resource-group <rg-name> \
  --name <container-name> \
  --image mcr.microsoft.com/azure-cognitive-services/sentiment \
  --ports 5000 \
  --dns-name-label ai102sentimentdemo \
  --environment-variables Eula=accept \
  --secure-environment-variables ApiKey=<your_api_key> Billing=<your_billing_endpoint> \
  --cpu 2 --memory 4 \
  --os-type Linux
```
Dopo la distribuzione, è possibile testare l'endpoint (sostituire <ACI_IP> in base alle proprie esigenze):
```bash
curl -X POST "http://<ACI_IP>:5000/text/analytics/v3.0/sentiment?model-version=latest" \
-H "Content-Type: application/json" \
-d '{
  "documents": [
    {
      "id": "1-en",
      "language": "en",
      "text": "The performance was amazing! The sound could have been clearer."
    },
    {
      "id": "2-en",
      "language": "en",
      "text": "The food and service were unacceptable. While the host was nice, the waiter was rude and food was cold."
    }
  ]
}'
```
**Prova**:

    If v3.1 endpoints do not work, try using v3.0 or v3.1-preview.1 with above cmd.

    Always check the /status endpoint and container logs for model loading messages (look for: Model loaded from /input/TextAnalytics/v3.x/Sentiment).

    If you still encounter issues, running the container locally with Docker may help to diagnose local vs. cloud issues.

## Eseguire la pulizia

Una volta completati gli esperimenti con l'istanza di contenitore, è consigliabile eliminarla.

1. Nel portale di Azure aprire il gruppo di risorse in cui sono state create le risorse per questo esercizio.
2. Selezionare la risorsa dell'istanza di contenitore ed eliminarla.

## Pulire le risorse

Se non si usano le risorse di Azure create in questo lab per altri moduli di training, è possibile eliminarle per evitare addebiti aggiuntivi.

1. Aprire il portale di Azure in `https://portal.azure.com` e nella barra di ricerca superiore cercare le risorse create in questo lab.

2. Nella pagina della risorsa selezionare **Elimina** e seguire le istruzioni per eliminare la risorsa. In alternativa, è possibile eliminare l'intero gruppo di risorse per pulire tutte le risorse contemporaneamente.

## Ulteriori informazioni

Per altre informazioni sulla containerizzazione di Servizi di Azure AI, vedere la [documentazione sui contenitori dei Servizi di Azure AI](https://learn.microsoft.com/azure/ai-services/cognitive-services-container-support).
