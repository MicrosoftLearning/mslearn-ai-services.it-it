---
lab:
  title: Implementazione della Sicurezza dei contenuti di Azure AI
---

# Implementazione della Sicurezza dei contenuti di Azure AI

In questo esercizio, verrà effettuato il provisioning di una risorsa di sicurezza dei contenuti, verrà testata la risorsa in Studio AI della piattaforma Azure e la risorsa nel codice.

## Effettuare il provisioning di una risorsa di *Sicurezza dei contenuti*

Se non è già disponibile, sarà necessario effettuare il provisioning di una risorsa di **Sicurezza dei contenuti** nella sottoscrizione di Azure.

1. Aprire il portale di Azure all'indirizzo `https://portal.azure.com` ed eseguire l'accesso usando l'account Microsoft associato alla sottoscrizione di Azure.
1. Selezionare **Crea una risorsa**.
1. Nel campo di ricerca, cercare **Sicurezza dei contenuti**. Quindi, nei risultati selezionare **Crea** in **Sicurezza dei contenuti di Azure AI**.
1. Effettuare il provisioning della risorsa usando le impostazioni seguenti:
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*.
    - **Gruppo di risorse**: *Selezionare o creare un gruppo di risorse*.
    - **Area**: selezionare **Stati Uniti orientali**
    - **Nome**: *immettere un nome univoco*.
    - **Piano tariffario**: selezionare **F0** (*gratuito*) o **S** (*standard*) se F0 non è disponibile.
1. Selezionare **Rivedi e crea**, quindi **Crea** per effettuare il provisioning della risorsa.
1. Attendere il completamento della distribuzione e quindi passare alla risorsa.
1. Seleziona **Controllo di accesso** nella barra di spostamento sinistra, quindi selezionare **+ Aggiungi** e **Aggiungi assegnazione di ruolo**.
1. Scorrere verso il basso per scegliere il ruolo **Utente Servizi cognitivi** e selezionare **Avanti**.
1. Aggiungere l'account a questo ruolo e quindi selezionare **Rivedi e assegna**.
1. Nella barra di spostamento sinistra, selezionare **Gestione risorse** e **Chiavi ed endpoint**. Lasciare aperta questa pagina per poter copiare le chiavi in un secondo momento.

## Usare le protezioni delle richieste di Sicurezza dei contenuti di Azure AI

In questo esercizio si userà Studio AI della piattaforma Azure per testare le protezioni delle richieste di sicurezza dei contenuti con due input di esempio. Uno simula una richiesta dell'utente e l'altro simula un documento con testo potenzialmente non sicuro incorporato.

1. In un'altra scheda del browser, aprire la pagina Sicurezza dei contenuti di [Studio AI della piattaforma Azure](https://ai.azure.com/explore/contentsafety) e accedere.
1. Selezionare **Prova** in **Moderare il contenuto di testo**.
1. Nella pagina **Modera il contenuto di testo**, in **Servizi Azure AI** selezionare la risorsa Sicurezza dei contenuti creata in precedenza.
1. Selezionare **Più categorie di rischio in una frase**. Esaminare il testo del documento per individuare potenziali problemi.
1. Selezionare **Esegui test** ed esaminare i risultati.
1. Facoltativamente, modificare i livelli di soglia e selezionare di nuovo **Esegui test**.
1. Sulla barra di spostamento a sinistra selezionare **Rilevamento materiale protetto per il testo**.
1. Selezionare **Testi protetti** e notare che questi sono i testi di una canzone pubblicata.
1. Selezionare **Esegui test** ed esaminare i risultati.
1. Sulla barra di spostamento sinistra selezionare **Moderare il contenuto dell'immagine**.
1. Selezionare **Contenuto di autolesionismo**.
1. Si noti che tutte le immagini sono sfocate per impostazione predefinita in Studio AI. Si dovrebbe anche sapere che il contenuto sessuale negli esempi è molto lieve.
1. Selezionare **Esegui test** ed esaminare i risultati.
1. Selezionare **Protezioni delle richieste** nella barra di spostamento sinistra.
1. Nella **pagina Protezioni delle richieste**, in **Servizi di Azure AI** selezionare la risorsa Sicurezza dei contenuti creata in precedenza.
1. Selezionare **Richiedi e documentare il contenuto dell'attacco**. Esaminare la richiesta dell'utente e il testo del documento per individuare potenziali problemi.
1. Selezionare **Esegui test**.
1. In **Visualizza risultati**, verificare che gli attacchi jailbreak siano stati rilevati sia nella richiesta dell'utente sia nel documento.

    > [!TIP]
    > Il codice è disponibile per tutti gli esempi in Studio AI.

1. In **Passaggi successivi**, in **Visualizza il codice** selezionare **Visualizza codice**. Viene visualizzata la finestra **Codice di esempio**.
1. Usare la freccia giù per selezionare Python o C# e quindi selezionare **Copia** per copiare il codice di esempio negli Appunti.
1. Chiudere la schermata **Codice di esempio**.

### Configurare l'applicazione

A questo punto si creerà un'applicazione in C# o Python.

#### C#

##### Prerequisiti

* [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
* [.NET 8](https://dotnet.microsoft.com/en-us/download/dotnet/8.0) è il framework di destinazione per questo esercizio.
* [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) per Visual Studio Code.

##### Configurazione

Per preparare Visual Studio Code per l'esercizio, seguire questa procedura.

1. Avviare Visual Studio Code e nella vista Explorer fare clic su **Crea progetto .NET** selezionando **App console**.
1. Selezionare una cartella nel computer e assegnare un nome al progetto. Selezionare **Crea progetto** e accettare il messaggio di avviso.
1. Nel riquadro Explorer, espandere Esplora soluzioni e selezionare **Program.cs**.
1. Compilare ed eseguire il progetto selezionando **Esegui** -> **Esegui senza debug**. 
1. In Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto C# e scegliere **Aggiungi pacchetto NuGet**.
1. Cercare **Azure.AI.TextAnalytics** e selezionare la versione più recente.
1. Cercare un secondo pacchetto NuGet: **Microsoft.Extensions.Configuration.Json 8.0.0**. Il file di progetto dovrebbe ora elencare due pacchetti NuGet.

##### Aggiungi codice

1. Incollare il codice di esempio copiato in precedenza nella sezione **ItemGroup**.
1. Scorrere verso il basso per trovare *Sostituisci con la subscription _key ed endpoint*.
1. Nel portale di Azure, nella pagina Chiavi ed endpoint, copiare una delle chiavi (1 o 2). Sostituire **<your_subscription_key>** con questo valore.
1. Nel portale di Azure, nella pagina Chiavi ed endpoint, copiare l'endpoint. Incollare questo valore nel codice per sostituire **<your_endpoint_value>**.
1. In **Studio AI della piattaforma Azure** copiare il valore della **richiesta dell'utente**. Incollarlo nel codice per sostituire **<test_user_prompt>**.
1. Scorrere verso il basso fino a **<this_is_a_document_source>** ed eliminare questa riga di codice.
1. In **Studio AI della piattaforma Azure** copiare il valore del **Documento**.
1. Scorrere verso il basso fino a **<this_is_another_document_source>** e incollare il valore del documento.
1. Selezionare **Esegui** -> **segui senza debug** e verificare che sia stato rilevato un attacco. 

#### Python

##### Prerequisiti

* [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Viene installata l'[estensione Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) per Visual Studio Code.

* Viene installato il [modulo rchieste](https://pypi.org/project/requests/).

1. Creare un nuovo file Python con un'estensione **.py** e assegnare un nome appropriato.
1. Incollare il codice di esempio copiato in precedenza.
1. Scorrere verso il basso per trovare la sezione intitolata *Sostituisci con la subscription _key ed endpoint*.
1. Nel portale di Azure, nella pagina Chiavi ed endpoint, copiare una delle chiavi (1 o 2). Sostituire **<your_subscription_key>** con questo valore.
1. Nel portale di Azure, nella pagina Chiavi ed endpoint, copiare l'endpoint. Incollare questo valore nel codice per sostituire **<your_endpoint_value>**.
1. In **Studio AI della piattaforma Azure** copiare il valore della **richiesta dell'utente**. Incollarlo nel codice per sostituire **<test_user_prompt>**.
1. Scorrere verso il basso fino a **<this_is_a_document_source>** ed eliminare questa riga di codice.
1. In **Studio AI della piattaforma Azure** copiare il valore del **Documento**.
1. Scorrere verso il basso fino a **<this_is_another_document_source>** e incollare il valore del documento.
1. Dal terminale integrato per il file eseguire il programma, ad esempio:

    - `.\prompt-shield.py`

1. Convalidare che venga rilevato un attacco.
1. Facoltativamente, è possibile sperimentare con diversi valori di contenuto di test e documento.
