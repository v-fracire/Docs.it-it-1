---
title: Configurare l'autenticazione di Windows in ASP.NET Core
author: scottaddie
description: Informazioni su come configurare l'autenticazione di Windows in ASP.NET Core, usando HTTP. sys, IIS e IIS Express.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 12/18/2018
uid: security/authentication/windowsauth
ms.openlocfilehash: 94dff2f47b2b076cb15f8d385239179b52786678
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637820"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Configurare l'autenticazione di Windows in ASP.NET Core

[Steve Smith](https://ardalis.com) e [Scott Addie](https://twitter.com/Scott_Addie)

L'autenticazione di Windows può essere configurato per le app ASP.NET Core ospitate in IIS o [HTTP. sys](xref:fundamentals/servers/httpsys).

## <a name="windows-authentication"></a>Autenticazione di Windows

L'autenticazione di Windows si basa sul sistema operativo per autenticare gli utenti delle App ASP.NET Core. È possibile usare l'autenticazione di Windows quando il server in esecuzione in una rete aziendale usando le identità di dominio Active Directory o altri account di Windows per identificare gli utenti. L'autenticazione di Windows è più adatta agli ambienti intranet in cui gli utenti, le applicazioni client e server web appartengono allo stesso dominio di Windows.

[Altre informazioni sull'autenticazione di Windows e di installarlo per IIS](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a>Abilitare l'autenticazione di Windows in un'app ASP.NET Core

Il modello di applicazione Web di Visual Studio può essere configurato per supportare l'autenticazione di Windows.

### <a name="use-the-windows-authentication-app-template"></a>Usare il modello di app di autenticazione di Windows

In Visual Studio:

1. Creare una nuova applicazione Web ASP.NET Core.
1. Selezionare l'applicazione Web dall'elenco dei modelli.
1. Selezionare il **Modifica autenticazione** e selezionare **l'autenticazione di Windows**.

Eseguire l'app. Il nome utente viene visualizzato nell'angolo superiore destro dell'app.

![Schermata di Browser l'autenticazione di Windows](windowsauth/_static/browser-screenshot.png)

Per progetti di sviluppo tramite IIS Express, il modello fornisce tutta la configurazione necessaria per usare l'autenticazione di Windows. La sezione seguente illustra come configurare manualmente un'app ASP.NET Core per l'autenticazione di Windows.

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a>Impostazioni di Visual Studio per Windows e l'autenticazione anonima

Il progetto di Visual Studio **delle proprietà** della pagina **Debug** scheda fornisce caselle di controllo per l'autenticazione di Windows e l'autenticazione anonima.

![Schermata del Browser l'autenticazione di Windows con le opzioni di autenticazione evidenziate](windowsauth/_static/vs-auth-property-menu.png)

In alternativa, è possibile configurare queste due proprietà nel *launchsettings. JSON* file:

[!code-json[](windowsauth/sample/launchSettings.json?highlight=3-4)]

## <a name="enable-windows-authentication-with-iis"></a>Abilitare l'autenticazione di Windows con IIS

IIS Usa il [modulo di ASP.NET Core](xref:host-and-deploy/aspnet-core-module) per ospitare App ASP.NET Core. L'autenticazione di Windows è configurata in IIS, non l'app. Le sezioni seguenti illustrano come usare Gestione IIS per configurare un'app ASP.NET Core per usare l'autenticazione di Windows.

### <a name="iis-configuration"></a>Configurazione di IIS

Abilitare il servizio ruolo IIS per l'autenticazione di Windows. Per altre informazioni, vedere [abilitare l'autenticazione di Windows nei servizi di ruolo IIS (vedere il passaggio 2)](xref:host-and-deploy/iis/index#iis-configuration).

Per impostazione predefinita, il Middleware di integrazione IIS è configurato per autenticare automaticamente le richieste. Per altre informazioni, vedere [Host ASP.NET Core in Windows con IIS: Le opzioni di IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

Per impostazione predefinita, il modulo ASP.NET Core è configurato per inoltrare il token di autenticazione di Windows per l'app. Per altre informazioni, vedere [riferimento configurazione modulo ASP.NET Core: Attributi dell'elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

### <a name="create-a-new-iis-site"></a>Creare un nuovo sito IIS

Specificare un nome e una cartella e consentirgli di creare un nuovo pool di applicazioni.

### <a name="customize-authentication"></a>Personalizzare l'autenticazione

Aprire le funzionalità di autenticazione per il sito.

![Menu di scelta l'autenticazione IIS](windowsauth/_static/iis-authentication-menu.png)

Disabilitare l'autenticazione anonima e abilitare l'autenticazione di Windows.

![Impostazioni di autenticazione IIS](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a>Pubblicare il progetto alla cartella del sito IIS

Usa Visual Studio o .NET Core CLI, pubblicare l'app per la cartella di destinazione.

![Finestra di dialogo di pubblicazione di Visual Studio](windowsauth/_static/vs-publish-app.png)

Altre informazioni sulle [pubblicazione in IIS](xref:host-and-deploy/iis/index).

Avviare l'app per verificare l'autenticazione di Windows.

## <a name="enable-windows-authentication-with-httpsys"></a>Abilitare l'autenticazione di Windows con HTTP. sys

Anche se Kestrel non supporta l'autenticazione di Windows, è possibile usare [HTTP. sys](xref:fundamentals/servers/httpsys) per supportare gli scenari self-hosted in Windows. L'esempio seguente configura l'host web dell'app per usare HTTP. sys con l'autenticazione di Windows:

[!code-csharp[](windowsauth/sample/Program2x.cs?highlight=9-14)]

> [!NOTE]
> Per la delega all'autenticazione in modalità kernel, HTTP.sys usa il protocollo di autenticazione Kerberos. L'autenticazione in modalità utente non è supportata con Kerberos e HTTP.sys. È necessario usare l'account del computer per decrittografare il token/ticket Kerberos ottenuto da Active Directory e inoltrato dal client al server per autenticare l'utente. Registrare il nome dell'entità servizio per l'host, non l'utente dell'app.

> [!NOTE]
> Http. sys non è supportata in Nano Server versione 1709 o successiva. Per usare l'autenticazione di Windows e HTTP. sys con Nano Server, usare una [contenitore di Server Core (microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/). Per altre informazioni su Server Core, vedere [qual è l'opzione di installazione Server Core in Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="work-with-windows-authentication"></a>Utilizzo con l'autenticazione di Windows

Lo stato di configurazione dell'accesso anonimo determina il modo in cui il `[Authorize]` e `[AllowAnonymous]` attributi vengono usati nell'app. Le due sezioni seguenti illustrano come gestire gli Stati non consentiti e consentito la configurazione dell'accesso anonimo.

### <a name="disallow-anonymous-access"></a>Non consentire l'accesso anonimo

Quando è abilitata l'autenticazione di Windows e accesso anonimo è disabilitato, il `[Authorize]` e `[AllowAnonymous]` attributi non hanno alcun effetto. Se il sito IIS (o HTTP. sys) è configurato per non consentire l'accesso anonimo, la richiesta raggiunga mai l'app. Per questo motivo, il `[AllowAnonymous]` attributo non è applicabile.

### <a name="allow-anonymous-access"></a>Consenti accesso anonimo

Quando sono abilitati sia l'autenticazione di Windows e l'accesso anonimo, usare il `[Authorize]` e `[AllowAnonymous]` attributi. Il `[Authorize]` attributo consente di proteggere parti dell'app che realmente richiedono l'autenticazione di Windows. Il `[AllowAnonymous]` esegue l'override dell'attributo `[Authorize]` utilizzo all'interno delle App che consentono l'accesso anonimo degli attributi. Visualizzare [autorizzazione semplice](xref:security/authorization/simple) per informazioni dettagliate sull'utilizzo di attributi.

In ASP.NET Core 2.x, il `[Authorize]` attributo richiede una configurazione aggiuntiva nel *Startup.cs* per stimolare le richieste anonime per l'autenticazione di Windows. La configurazione consigliata varia leggermente in base sul server web in uso.

> [!NOTE]
> Per impostazione predefinita, gli utenti che non dispongono di autorizzazione per accedere a una pagina vengono visualizzati una risposta HTTP 403 vuota. Il [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) può essere configurato per fornire agli utenti una migliore esperienza di "Accesso negato".

#### <a name="iis"></a>IIS

Se si usa IIS, aggiungere il codice seguente il `ConfigureServices` metodo:

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a>HTTP.sys

Se si Usa HTTP. sys, aggiungere il codice seguente il `ConfigureServices` metodo:

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a>Rappresentazione

ASP.NET Core non implementa la rappresentazione. Le app vengono eseguite con l'identità dell'applicazione per tutte le richieste, usando l'identità del pool o un processo dell'app. Se è necessario eseguire in modo esplicito un'azione per conto di un utente, usare `WindowsIdentity.RunImpersonated`. Eseguire una singola azione in questo contesto e quindi chiudere il contesto.

[!code-csharp[](windowsauth/sample/Startup.cs?name=snippet_Impersonate&highlight=10-18)]

Si noti che `RunImpersonated` non supporta operazioni asincrone e non deve essere usata per scenari complessi. Ad esempio, di wrapping delle richieste intere o catene di middleware, non è supportato o consigliato.
