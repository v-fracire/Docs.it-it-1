---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-deployment-properties-for-a-target-environment
title: "Configurazione delle proprietà di distribuzione di un ambiente di destinazione | Documenti Microsoft"
author: jrjlee
description: "In questo argomento viene descritto come configurare le proprietà specifiche dell'ambiente per distribuire la soluzione di contatto Manager di esempio in un ambiente di destinazione specifico..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: b5b86e03-b8ed-46e6-90fa-e1da88ef34e9
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-deployment-properties-for-a-target-environment
msc.type: authoredcontent
ms.openlocfilehash: f27b8376b332ff21185be0fd5c00ced7d40a20bd
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
<a name="configuring-deployment-properties-for-a-target-environment"></a>Configurazione delle proprietà di distribuzione di un ambiente di destinazione
====================
da [Jason Lee](https://github.com/jrjlee)

[Scarica il PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> In questo argomento viene descritto come configurare le proprietà specifiche dell'ambiente per distribuire la soluzione di contatto Manager di esempio in un ambiente di destinazione specifico.


In questo argomento fa parte di una serie di esercitazioni basate su requisiti di distribuzione dell'organizzazione di una società fittizia denominata Fabrikam, Inc. Questa serie di esercitazioni utilizza una soluzione di esempio & #x 2014; il [Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md) #x 2014; & soluzione per rappresentare un'applicazione web con un livello di complessità, tra cui un'applicazione ASP.NET MVC 3, Windows realistico Servizio di Communication Foundation (WCF) e un progetto di database.

Il metodo di distribuzione il fulcro di queste esercitazioni si basa sul progetto file split approccio descritto in [comprendere il processo di compilazione](../web-deployment-in-the-enterprise/understanding-the-build-process.md), in cui il processo di compilazione è controllato da due progetti #x 2014; & file contenente una compilare le istruzioni che si applicano a ogni ambiente di destinazione e quella contenente impostazioni specifiche dell'ambiente di compilazione e distribuzione. In fase di compilazione, il file di progetto specifici dell'ambiente viene unito nel file di progetto indipendenti dall'ambiente in modo da formare un set completo di istruzioni di compilazione.

## <a name="process-overview"></a>Panoramica del processo

Il file di progetto che si userà per compilare e distribuire la soluzione di gestione di contatto viene suddivisa in due file fisici:

- Impostazioni e le istruzioni di compilazione contenente universal (il *Publish.proj* file).
- Le impostazioni di compilazione contenente specifiche dell'ambiente (*Env Dev.proj*, *Env Stage.proj*e così via).

In fase di compilazione, file di progetto specifici dell'ambiente viene unito di universal *Publish.proj* file in modo da formare un set completo di istruzioni di compilazione. È possibile configurare la distribuzione in ambienti di destinazione specifico mediante la creazione o personalizzazione dei file di progetto specifici dell'ambiente con impostazioni che indicano il proprio scenario di distribuzione.

Molti di questi valori vengono determinati da parte dell'ambiente di destinazione è configurato & #x 2014; in particolare, se il server web di destinazione è configurato per utilizzare il servizio agente di distribuzione Web (l'agente remoto) oppure il gestore di distribuzione Web. Per ulteriori informazioni su questi approcci e per indicazioni sulla scelta dell'approccio corretto per il proprio ambiente, vedere [scelta dell'approccio di destra per distribuzione Web](choosing-the-right-approach-to-web-deployment.md).

Il [uno scenario di gestione di contatto](../deploying-web-applications-in-enterprise-scenarios/enterprise-web-deployment-scenario-overview.md) richiede due file di progetto specifici dell'ambiente:

- Distribuzione in un ambiente di test per sviluppatori (*Env Dev.proj*). L'ambiente di test per sviluppatori è configurato per accettare le distribuzioni remote con l'agente remoto, come descritto in [Scenario: configurazione di un ambiente di Test per la distribuzione Web](scenario-configuring-a-test-environment-for-web-deployment.md). Questo file deve fornire l'agente remoto indirizzo dell'endpoint, nonché le impostazioni di specifici della posizione come stringhe di connessione e gli endpoint del servizio.
- Distribuzione in un ambiente di gestione temporanea (*Env Stage.proj*). Ambiente di gestione temporanea è configurato per accettare le distribuzioni remote utilizzando il gestore di distribuzione Web, come descritto in [Scenario: configurazione di un ambiente di gestione temporanea per la distribuzione Web](scenario-configuring-a-staging-environment-for-web-deployment.md). Questo file deve fornire l'indirizzo dell'endpoint gestore distribuzione Web, nonché impostazioni specifici della posizione come stringhe di connessione e gli endpoint del servizio.

È importante notare che le impostazioni configurate nel file di progetto specifici dell'ambiente non influiscono sul contenuto del web pacchetto stesso & #x 2014; invece controllano come il pacchetto viene distribuito e vengono forniti i valori di parametro quando il pacchetto è estratto. Si sta importando il pacchetto web nell'ambiente di produzione manualmente, come descritto in [Scenario: configurazione di un ambiente di produzione per la distribuzione Web](scenario-configuring-a-production-environment-for-web-deployment.md) e [manualmente l'installazione di pacchetti di Web](../web-deployment-in-the-enterprise/manually-installing-web-packages.md), in modo non è importante specificare le impostazioni è stato utilizzato nel file di progetto specifici dell'ambiente al momento della generazione del pacchetto. Internet Information Services (IIS) Manager richiederà i valori con parametri, ad esempio le stringhe di connessione e gli endpoint del servizio, quando si importa il pacchetto.

Per distribuire la soluzione di gestione di contatto per l'ambiente di destinazione, è possibile personalizzare questo file o utilizzarlo come modello e creare un file.

**Per configurare le impostazioni di distribuzione specifico dell'ambiente per la soluzione di gestione di contatto**

1. Aprire la soluzione ContactManager WCF in Visual Studio 2010.
2. Nel **Esplora** finestra, espandere il **pubblica** cartella, espandere il **EnvConfig** cartella, quindi fare doppio clic **Env-Dev.proj**.

    ![](configuring-deployment-properties-for-a-target-environment/_static/image1.png)
3. Sostituire i valori delle proprietà di *Env Dev.proj* file con i valori corretti per l'ambiente di test.

    > [!NOTE]
    > Nella tabella che segue questa procedura vengono fornite ulteriori informazioni su ognuna di queste proprietà.
4. Salvare il lavoro e quindi chiudere il *Env Dev.proj* file.

## <a name="choosing-the-right-deployment-properties"></a>Scegliere le proprietà di distribuzione appropriata

La tabella seguente descrive lo scopo di ogni proprietà nel file di progetto specifici dell'ambiente di esempio, *Env Dev.proj*e vengono fornite informazioni sui valori è necessario fornire.

| Nome proprietà | Dettagli |
| --- | --- |
| **MSDeployComputerName** il nome dell'endpoint server o un servizio web di destinazione. | Se si distribuisce il servizio agente remoto nel server web di destinazione, è possibile specificare il nome del computer di destinazione (ad esempio, **TESTWEB1** o **TESTWEB1.fabrikam.net**), oppure è possibile specificare il computer remoto endpoint dell'agente (ad esempio, `http://TESTWEB1/MSDEPLOYAGENTSERVICE`). La distribuzione funziona allo stesso modo in ogni caso. Se si distribuisce al gestore di distribuzione Web nel server web di destinazione, è necessario specificare l'endpoint del servizio e includere il nome del sito Web IIS come parametro di stringa di query (ad esempio, `https://STAGEWEB1:8172/MSDeploy.axd?site=DemoSite`). |
| **MSDeployAuth** il metodo che distribuzione Web deve utilizzare per l'autenticazione al computer remoto. | Questo deve essere impostato su **NTLM** o **base**. In genere, si userà **NTLM** se si distribuisce il servizio agente remoto e **base** se si distribuisce al gestore distribuzione Web. Se si utilizza l'autenticazione di base, è inoltre necessario specificare il nome utente e la password che deve rappresentare lo strumento di distribuzione Web di IIS (distribuzione Web) per eseguire la distribuzione. In questo esempio, questi valori vengono forniti tramite il **MSDeployUsername** e **MSDeployPassword** proprietà. Se si utilizza l'autenticazione NTLM, è possibile omettere queste proprietà o lasciare il campo vuoto. |
| **MSDeployUsername** se si utilizza l'autenticazione di base, distribuzione Web utilizzerà questo account del computer remoto. | Questo deve essere nel formato *dominio*\*username * (ad esempio, **FABRIKAM\matt**). Questo valore viene utilizzato solo se si specifica l'autenticazione di base. Se si utilizza l'autenticazione NTLM, la proprietà può essere omessa. Se viene fornito un valore, verrà ignorato. |
| **MSDeployPassword** se si utilizza l'autenticazione di base, distribuzione Web utilizzerà la password nel computer remoto. | Si tratta della password per l'account utente specificato nella **MSDeployUsername** proprietà. Questo valore viene utilizzato solo se si specifica l'autenticazione di base. Se si utilizza l'autenticazione NTLM, la proprietà può essere omessa. Se viene fornito un valore, verrà ignorato. |
| **ContactManagerIisPath** il percorso di IIS in cui si desidera distribuire l'applicazione MVC Contact Manager. | Il percorso deve essere visualizzato in Gestione IIS, nel formato [*nome del sito Web IIS*] / [*web**nome applicazione*]. Tenere presente che il sito Web IIS deve essere presente prima di distribuire l'applicazione. Ad esempio, se si crea un sito Web IIS denominato DemoSite, è possibile specificare il percorso di IIS per l'applicazione MVC come DemoSite/ContactManager. |
| **ContactManagerServiceIisPath** il percorso di IIS in cui si desidera distribuire il servizio WCF di gestione di contatto. | Ad esempio, se si crea un sito Web IIS denominato DemoSite, è possibile specificare il percorso di IIS per il servizio WCF come **DemoSite/ContactManagerService**. |
| **ContactManagerTargetUrl** l'URL in cui il servizio WCF può essere raggiunto. | L'operazione potrebbe richiedere il modulo [*URL radice del sito Web IIS*] / [*nome applicazione di servizio*] / [*endpoint del servizio*]. Ad esempio, se si crea un sito Web IIS sulla porta 85, l'URL potrebbe assumere la forma `http://localhost:85/ContactManagerService/ContactService.svc`. Tenere presente che l'applicazione MVC e il servizio WCF vengono distribuiti nello stesso server. Di conseguenza, questo URL avviene solo dal computer in cui è installato. Per questo motivo, è preferibile utilizzare localhost o l'indirizzo IP, anziché il nome del computer o un'intestazione host, nell'URL. Se si utilizza il nome del computer o un'intestazione host, il [controllo di loopback](https://go.microsoft.com/?linkid=9805131) funzionalità di sicurezza di IIS può bloccare l'URL e restituire un **HTTP 401.1 - autorizzazione negata** errore. |
| **CmDatabaseConnectionString** la stringa di connessione per il server di database. | La stringa di connessione determina le credenziali VSDBCMD utilizzerà per contattare il server di database e creare il database e le credenziali che il pool di applicazioni server web verrà utilizzato per contattare il server di database e interagire con il database. Essenzialmente sono disponibili due opzioni qui. È possibile specificare **la sicurezza integrata di = true**, in tal caso viene utilizzata l'autenticazione integrata di Windows: **origine dati = TESTDB1; Integrated Security = true** In questo caso, il database verrà creato utilizzando le credenziali dell'utente che esegue lo strumento VSDBCMD eseguibile e l'applicazione accederà al database utilizzando l'identità dell'account computer server web. In alternativa, è possibile specificare il nome utente e password di un account di SQL Server. In questo caso, le credenziali di SQL Server vengono utilizzate da VSDBCMD per creare il database sia dal pool di applicazioni per interagire con il database: **origine dati = TESTDB1; Id utente = ASqlUser; Password = Pa$ $w0rd** le procedure dettagliate in questo argomento si presuppongono che si userà l'autenticazione integrata di Windows. |
| **CmTargetDatabase** il nome che si desidera assegnare il database creato nel server di database. | Il valore fornito di seguito viene aggiunto al comando VSDBCMD come parametro. Consente inoltre di compilare una stringa di connessione completa che il pool di applicazioni nel server web è possibile utilizzare per interagire con il database. |
  

Questi esempi viene illustrato come configurare queste proprietà per scenari di distribuzione specifico.

### <a name="example-1x2014deployment-to-the-remote-agent-service"></a>Esempio 1 & #x 2014; la distribuzione per il servizio agente remoto

In questo esempio:

- Esegue la distribuzione del servizio agente remoto su TESTWEB1.
- Si sta per indicare la distribuzione Web per utilizzare l'autenticazione NTLM. Distribuzione Web viene eseguito con le credenziali usate per richiamare Microsoft Build Engine (MSBuild).
- Si utilizza l'autenticazione integrata per distribuire il **ContactManager** database TESTDB1. Il database verrà distribuito utilizzando le credenziali usate per richiamare MSBuild.


[!code-xml[Main](configuring-deployment-properties-for-a-target-environment/samples/sample1.xml)]


### <a name="example-2x2014deployment-to-the-web-deploy-handler-endpoint"></a>Esempio 2 & #x 2014; la distribuzione di Web distribuire Endpoint gestore

In questo esempio:

- Si sta distribuendo all'endpoint del servizio gestore distribuzione Web su STAGEWEB1.
- Si sta facendo che distribuzione Web per utilizzare l'autenticazione di base.
- Si specifica che distribuzione Web deve rappresentare l'account FABRIKAM\stagingdeployer nel computer remoto.
- Si usa l'autenticazione di SQL Server per distribuire il **ContactManager** database STAGEDB1.


[!code-xml[Main](configuring-deployment-properties-for-a-target-environment/samples/sample2.xml)]


## <a name="conclusion"></a>Conclusione

A questo punto, i file di progetto sono completamente configurati per compilare e distribuire la soluzione di gestione di contatto per uno o più ambienti di destinazione.

Per utilizzare questi file di progetto come parte di un processo di distribuzione passo a passo, ripetibili, è necessario eseguire il *Publish.proj* file utilizzando MSBuild e passare il percorso del file di progetto specifici dell'ambiente come parametro. È possibile farlo in diversi modi:

- Per una panoramica di MSBuild e un'introduzione ai file di progetto personalizzati, vedere [informazioni sui File di progetto](../web-deployment-in-the-enterprise/understanding-the-project-file.md).
- Per informazioni su come formulare un comando di MSBuild che esegue i file di progetto personalizzati, vedere [distribuzione di pacchetti Web](../web-deployment-in-the-enterprise/deploying-web-packages.md).
- Per informazioni su come incorporare i comandi di MSBuild in un file di comando per le distribuzioni di passo a passo, ripetibili, vedere [creazione ed esecuzione di un File di comando di distribuzione](../web-deployment-in-the-enterprise/creating-and-running-a-deployment-command-file.md).
- Per informazioni su come eseguire i file di progetto personalizzati da Team Build, vedere [la creazione di una definizione di compilazione che la distribuzione supporta](../configuring-team-foundation-server-for-web-deployment/creating-a-build-definition-that-supports-deployment.md).

>[!div class="step-by-step"]
[Precedente](creating-a-server-farm-with-the-web-farm-framework.md)