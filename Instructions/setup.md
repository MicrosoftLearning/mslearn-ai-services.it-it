---
lab:
  title: Configurazione dell'ambiente lab
  module: Setup
---

# Configurazione dell'ambiente lab

Gli esercizi sono pensati per essere completati in un ambiente lab ospitato. Se si intende completarli nel proprio computer, è possibile eseguire questa operazione installando il software seguente. Quando si usa un ambiente personale, si potrebbero notare dialoghi e comportamenti imprevisti. Considerata l'ampia gamma di possibili configurazioni locali, il team del corso non può fornire assistenza per tutti i problemi che possono verificarsi in un ambiente personale.

> **Nota**: Le istruzioni seguenti sono relative a un computer Windows 11. È anche possibile usare Linux o MacOS. Potrebbe essere necessario adattare le istruzioni del lab per il sistema operativo scelto.

### Sistema operativo di base (Windows 11)

#### Windows 11

Installare Windows 11 e applicare tutti gli aggiornamenti.

#### Edge

Installare [Edge (Chromium)](https://microsoft.com/edge)

### ASP.NET Core SDK

1. Scaricarlo e installarlo da https://dotnet.microsoft.com/download (scaricare .NET Core SDK, non solo il runtime). Se si eseguono i lab in questo corso nel proprio computer, è necessario avere .NET 7.0. I lab sono stati testati su .NET 7.0, ma la versione 7.0 non è attualmente supportata. È possibile usare la versione 8.0, ma potrebbero verificarsi alcuni problemi secondari. Si consiglia vivamente di usare l'ambiente ospitato.

### C++ Redistributable

1. Scaricare e installare il pacchetto Visual C++ Redistributable (x64) da https://aka.ms/vs/16/release/vc_redist.x64.exe.

### Node.JS

1. Scaricare la versione LTS più recente da https://nodejs.org/en/download/ 
2. Eseguire l'installazione con le opzioni predefinite

### Python (e pacchetti obbligatori)

1. Scaricare la versione 3.11 da https://docs.conda.io/en/latest/miniconda.html 
2. Eseguire il programma di installazione per installare. **Importante**: selezionare le opzioni per aggiungere Miniconda alla variabile PATH e registrare Miniconda come ambiente Python predefinito.
3. Dopo l'installazione, aprire il prompt di Anaconda e immettere i comandi seguenti per installare i pacchetti: 

```
pip install flask requests python-dotenv pylint matplotlib pillow
pip install --upgrade numpy
```

### Interfaccia della riga di comando di Azure

1. Scaricarlo da https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest 
2. Eseguire l'installazione con le opzioni predefinite

### Git

1. Scaricarlo e installarlo da https://git-scm.com/download.html usando le opzioni predefinite


### Visual Studio Code (ed estensioni)

1. Scaricarlo da https://code.visualstudio.com/Download 
2. Eseguire l'installazione con le opzioni predefinite 
3. Dopo l'installazione, avviare Visual Studio Code e nella scheda **Estensioni** (CTRL+MAIUSC+X), cercare e installare le estensioni seguenti di Microsoft:
    - Python
    - C#
    - Funzioni di Azure
    - PowerShell
