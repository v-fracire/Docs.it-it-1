---
title: Controlli di integrità in ASP.NET Core
author: guardrex
description: Informazioni su come configurare i controlli di integrità per l'infrastruttura ASP.NET Core, ad esempio database e app.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2018
uid: host-and-deploy/health-checks
ms.openlocfilehash: d8fd43d9d689396cf30ca371763cdf7ac9423c77
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862634"
---
# <a name="health-checks-in-aspnet-core"></a>Controlli di integrità in ASP.NET Core

Di [Luke Latham](https://github.com/guardrex) e [Glenn Condron](https://github.com/glennc)

ASP.NET Core offre il middleware per i controlli di integrità e librerie per la creazione di report sull'integrità dei componenti dell'infrastruttura di app.

I controlli di integrità vengono esposti da un'app come endpoint HTTP. È possibile configurare endpoint dei controlli di integrità per svariati scenari di monitoraggio in tempo reale:

* I probe di integrità possono essere usati dagli agenti di orchestrazione e dai servizi di bilanciamento del carico per controllare lo stato di un'app. Ad esempio, un agente di orchestrazione potrebbe rispondere a controllo di integrità non superato arrestando una distribuzione in sequenza o riavviando un contenitore. Un servizio di bilanciamento del carico potrebbe reagire a un'app non integra trasferendo il traffico dall'istanza con errori a un'istanza integra.
* È possibile monitorare lo stato di integrità di memoria, disco e altre risorse fisiche del server.
* I controlli di integrità possono testare le dipendenze di un'app, ad esempio i database e gli endpoint di servizio esterni, per verificare la disponibilità e il normale funzionamento.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

L'app di esempio include esempi degli scenari descritti in questo argomento. Per eseguire l'app di esempio per un determinato scenario, usare il comando [dotnet run](/dotnet/core/tools/dotnet-run) dalla cartella del progetto in una shell dei comandi. Per informazioni dettagliate su come usare l'app di esempio, vedere il file *README.md* dell'app di esempio e le descrizioni degli scenari in questo argomento.

## <a name="prerequisites"></a>Prerequisiti

I controlli di integrità vengono in genere usati con un servizio di monitoraggio esterno o un agente di orchestrazione per controllare lo stato di un'app. Prima di aggiungere i controlli di integrità a un'app, decidere quale sistema di monitoraggio usare. Il sistema di monitoraggio determina quali tipi di controlli di integrità creare e come configurare i relativi endpoint.

Fare riferimento al [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) oppure aggiungere un riferimento al pacchetto [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks).

L'app di esempio fornisce il codice di avvio per illustrare i controlli di integrità per diversi scenari. Lo scenario [Probe del database](#database-probe) esamina l'integrità di una connessione di database usando [BeatPulse](https://github.com/Xabaril/BeatPulse). Lo scenario [Probe DbContext](#entity-framework-core-dbcontext-probe) esamina un database usando un elemento `DbContext` di EF Core. Per esplorare gli scenari relativi ai database usando l'app di esempio:

* Creare un database e fornire la stringa di connessione nel file *appsettings.json* dell'app.
* Aggiungere un riferimento al pacchetto [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

> [!NOTE]
> [BeatPulse](https://github.com/Xabaril/BeatPulse) non viene gestito né supportato da Microsoft.

Un altro scenario relativo ai controlli di integrità illustra come filtrare i controlli di integrità per una porta di gestione. L'app di esempio richiede di creare un file *Properties/launchSettings.json* che include l'URL di gestione e la porta di gestione. Per altre informazioni, vedere la sezione [Filtrare in base alla porta](#filter-by-port).

## <a name="basic-health-probe"></a>Probe di integrità di base

Per molte app, una configurazione del probe di integrità di base, che segnala la disponibilità dell'app per elaborare le richieste (*attività*), è sufficiente per individuare lo stato dell'app.

La configurazione di base registra i servizi dei controlli di integrità e chiama il middleware per i controlli di integrità per rispondere a un endpoint di URL con una risposta di integrità. Per impostazione predefinita, non vengono registrati controlli di integrità specifici per testare particolari dipendenze o sottosistemi. L'app viene considerata integra se riesce a rispondere all'URL dell'endpoint di integrità. Il writer di risposta predefinito scrive lo stato (`HealthCheckStatus`) come risposta di testo non crittografato per il client, indicando uno stato `HealthCheckResult.Healthy` o `HealthCheckResult.Unhealthy`.

Registrare i servizi dei controlli di integrità con `AddHealthChecks` in `Startup.ConfigureServices`. Aggiungere il middleware per i controlli di integrità con `UseHealthChecks` nella pipeline di elaborazione della richiesta di `Startup.Configure`.

Nell'app di esempio l'endpoint di controllo di integrità viene creato in `/health` (*BasicStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/BasicStartup.cs?name=snippet1&highlight=5,10)]

Per eseguire lo scenario di configurazione di base usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:

```console
dotnet run --scenario basic
```

### <a name="docker-example"></a>Esempio Docker

[Docker](xref:host-and-deploy/docker/index) offre una direttiva `HEALTHCHECK` predefinita che può essere usata per controllare lo stato di un'app che usa la configurazione dei controlli di integrità di base:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Creare controlli di integrità

I controlli di integrità vengono creati implementando l'interfaccia `IHealthCheck`. Il metodo `IHealthCheck.CheckHealthAsync` restituisce `Task<HealthCheckResult>` che indica l'integrità come `Healthy`, `Degraded` o `Unhealthy`. Il risultato viene scritto come risposta di testo non crittografato con un codice di stato configurabile. La configurazione viene descritta nella sezione [Opzioni dei controlli di integrità](#health-check-options). `HealthCheckResult` può anche restituire coppie chiave-valore facoltative.

### <a name="example-health-check"></a>Controllo di integrità di esempio

La classe `ExampleHealthCheck` seguente illustra il layout di un controllo di integrità:

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public ExampleHealthCheck()
    {
        // Use dependency injection (DI) to supply any required services to the
        // health check.
    }

    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context, 
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Execute health check logic here. This example sets a dummy
        // variable to true.
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a>Registrare i servizi di controllo dell'integrità

Il tipo `ExampleHealthCheck` viene aggiunto ai servizi di controllo dell'integrità con `AddCheck`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck<ExampleHealthCheck>("example_health_check");
}
```

L'overload `AddCheck` illustrato nell'esempio seguente imposta lo stato di errore (`HealthStatus`) per segnalare quando il controllo di integrità indica un errore. Se lo stato di errore è impostato su `null` (impostazione predefinita), viene segnalato `HealthStatus.Unhealthy`. Questo overload è uno scenario utile per gli autori di librerie, in cui lo stato di errore indicato dalla libreria viene applicato dall'app quando si verifica un errore di controllo dell'integrità se l'implementazione del controllo dell'integrità rispetta l'impostazione.

Si possono usare *tag* per filtrare i controlli di integrità, come illustrato più avanti nella sezione [Filtrare i controlli di integrità](#filter-health-checks).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" });
```

`AddCheck` può anche eseguire una funzione lambda. Nell'esempio seguente il nome del controllo di integrità viene specificato come `Example` e il controllo restituisce sempre uno stato integro:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Example", () => 
            HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" })
}
```

### <a name="use-health-checks-middleware"></a>Usare il middleware per i controlli di integrità

In `Startup.Configure` chiamare `UseHealthChecks` nella pipeline di elaborazione con l'URL dell'endpoint o il percorso relativo:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health");
}
```

Se i controlli di integrità devono essere in ascolto su una porta specifica, usare un overload di `UseHealthChecks` per impostare la porta, come illustrato più avanti nella sezione [Filtrare in base alla porta](#filter-by-port):

```csharp
app.UseHealthChecks("/health", port: 8000);
```

Il middleware per i controlli di integrità è un *middleware terminale* nella pipeline di elaborazione della richiesta dell'app. Il primo endpoint di controllo dell'integrità ha rilevato che viene eseguita una corrispondenza esatta con l'URL della richiesta, che blocca il resto della pipeline del middleware. In caso di corto circuito, non viene eseguito alcun middleware che segue il controllo di integrità corrispondente.

## <a name="health-check-options"></a>Opzioni dei controlli di integrità

`HealthCheckOptions` consente di personalizzare il comportamento dei controlli di integrità:

* [Filtrare i controlli di integrità](#filter-health-checks)
* [Personalizzare il codice di stato HTTP](#customize-the-http-status-code)
* [Eliminare le intestazioni della cache](#suppress-cache-headers)
* [Personalizzare l'output](#customize-output)

### <a name="filter-health-checks"></a>Filtrare i controlli di integrità

Per impostazione predefinita, il middleware per i controlli di integrità esegue tutti i controlli di integrità registrati. Per eseguire un subset dei controlli di integrità, specificare una funzione che restituisce un valore booleano all'opzione `Predicate`. Nell'esempio seguente il controllo di integrità `Bar` viene filtrato in base al tag (`bar_tag`) nell'istruzione condizionale della funzione, dove `true` viene restituito solo se la proprietà `Tag` del controllo di integrità corrisponde a `foo_tag` o a `baz_tag`:

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () => 
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () => 
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
        .AddCheck("Baz", () => 
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // Filter out the 'Bar' health check. Only Foo and Baz execute.
        Predicate = (check) => check.Tags.Contains("foo_tag") || 
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a>Personalizzare il codice di stato HTTP

Usare `ResultStatusCodes` per personalizzare il mapping dello stato di integrità dei codici di stato HTTP. Le assegnazioni `StatusCode` seguenti sono i valori predefiniti usati dal middleware. Modificare i valori dei codici di stato in base ai requisiti.

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // The following StatusCodes are the default assignments for
        // the HealthCheckStatus properties.
        ResultStatusCodes =
        {
            [HealthCheckStatus.Healthy] = StatusCodes.Status200OK,
            [HealthCheckStatus.Degraded] = StatusCodes.Status200OK,
            [HealthCheckStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
}
```

### <a name="suppress-cache-headers"></a>Eliminare le intestazioni della cache

`AllowCachingResponses` controlla se il middleware per i controlli di integrità aggiunge intestazioni HTTP a una risposta di probe per impedire la memorizzazione della risposta nella cache. Se il valore è `false` (impostazione predefinita), il middleware imposta le intestazioni `Cache-Control`, `Expires` e `Pragma` o ne esegue l'override per impedire la memorizzazione della risposta nella cache. Se il valore è `true`, il middleware non modifica le intestazioni della risposta nella cache.

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // The default value is false.
        AllowCachingResponses = false
    });
}
```

### <a name="customize-output"></a>Personalizzare l'output

L'opzione `ResponseWriter` ottiene o imposta un delegato usato per scrivere la risposta. Il delegato predefinito scrive una risposta in testo non crittografato minima con il valore di stringa `HealthReport.Status`.

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // WriteResponse is a delegate used to write the response.
        ResponseWriter = WriteResponse
    });
}

private static Task WriteResponse(HttpContext httpContext, 
    HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

## <a name="database-probe"></a>Probe del database

Un controllo di integrità può specificare una query di database da eseguire come test booleano per indicare se il database sta rispondendo normalmente.

L'app di esempio usa [BeatPulse](https://github.com/Xabaril/BeatPulse), una libreria di controlli di integrità per le app ASP.NET Core, per eseguire un controllo di integrità su un database di SQL Server. BeatPulse esegue una query `SELECT 1` sul database per verificare che la connessione al database sia integra.

> [!WARNING]
> Quando si controlla una connessione di database con una query, scegliere una query che viene restituita rapidamente. Dall'approccio alla query possono derivare il sovraccarico del database e il degrado delle prestazioni. Nella maggior parte dei casi, l'esecuzione di una query di test non è necessaria. È sufficiente stabilire una connessione al database. Se invece è necessario eseguire una query, scegliere una semplice query SELECT, ad esempio `SELECT 1`.

Per usare la libreria BeatPulse, includere un riferimento al pacchetto [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Fornire una stringa di connessione di database valida nel file *appsettings.json* dell'app di esempio. L'app usa una database di SQL Server denominato `HealthCheckSample`:

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Registrare i servizi dei controlli di integrità con `AddHealthChecks` in `Startup.ConfigureServices`. L'app di esempio chiama il metodo `AddSqlServer` di BeatPulse con la stringa di connessione del database (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Chiamare il middleware per i controlli di integrità nella pipeline di elaborazione dell'app in `Startup.Configure`:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_Configure)]

Per eseguire lo scenario di probe del database di base usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:

```console
dotnet run --scenario db
```

> [!NOTE]
> [BeatPulse](https://github.com/Xabaril/BeatPulse) non viene gestito né supportato da Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Probe DbContext di Entity Framework Core

Il controllo di `DbContext` è supportato nelle app che usano [Entity Framework (EF) Core](/ef/core/). Questo controllo verifica che l'app possa comunicare con il database configurato per un elemento `DbContext` di EF Core. Per impostazione predefinita, `DbContextHealthCheck` chiama il metodo `CanConnectAsync` di EF Core. È possibile determinare l'operazione da eseguire quando si controlla l'integrità usando gli overload del metodo `AddDbContextCheck`.

`AddDbContextCheck<TContext>` registra un controllo di integrità per `DbContext` (`TContext`). Per impostazione predefinita, il nome del controllo di integrità è il nome del tipo `TContext`. È disponibile un overload per configurare lo stato di errore, i tag e una query di test personalizzata.

Nell'app di esempio `AppDbContext` viene fornito a `AddDbContextCheck` e registrato come servizio in `Startup.ConfigureServices`.

*DbContextHealthStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Nell'app di esempio `UseHealthChecks` aggiunge il middleware per i controlli di integrità in `Startup.Configure`.

*DbContextHealthStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_Configure)]

Per eseguire lo scenario di probe `DbContext` usando l'app di esempio, verificare che il database specificato dalla stringa di connessione non esista nell'istanza di SQL Server. Se il database esiste, eliminarlo.

Eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:

```console
dotnet run --scenario dbcontext
```

Dopo che l'app è in esecuzione, controllare lo stato di integrità effettuando una richiesta all'endpoint `/health` in un browser. Il database e `AppDbContext` non esistono, quindi l'app fornisce la risposta seguente:

```
Unhealthy
```

Attivare l'app di esempio per creare il database. Effettuare una richiesta a `/createdatabase`. L'app risponde:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Effettuare una richiesta all'endpoint `/health`. Il database e il contesto esistono, quindi l'app risponde:

```
Healthy
```

Attivare l'app di esempio per eliminare il database. Effettuare una richiesta a `/deletedatabase`. L'app risponde:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Effettuare una richiesta all'endpoint `/health`. L'app fornisce una risposta non integra:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Separare i probe di idoneità e di attività

In alcuni scenari di hosting vengono usati due controlli di integrità che distinguono due stati dell'app:

* L'app è funzionante, ma non ancora pronta a ricevere le richieste. Questo stato è l'*idoneità* dell'app.
* L'app è funzionante e risponde alle richieste. Questo stato è l'*attività* dell'app.

Il controllo di idoneità in genere esegue un set di controlli più completo e dispendioso in termini di tempo per determinare se tutti i sottosistemi e le risorse dell'app sono disponibili. Un controllo di attività esegue semplicemente un rapido controllo per determinare se l'app è disponibile per elaborare le richieste. Dopo che l'app ha superato il controllo di idoneità, non è necessario caricare ulteriormente l'app con il dispendioso set di controlli di idoneità. Sarà sufficiente controllare solo l'attività.

L'app di esempio contiene un controllo di integrità per segnalare il completamento dell'attività di avvio con esecuzione prolungata in un [servizio ospitato](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` espone una proprietà, `StartupTaskCompleted`, che il servizio ospitato può impostare su `true` al termine dell'attività con esecuzione prolungata (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=5)]

L'attività in background con esecuzione prolungata viene avviata da un [servizio ospitato](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). Al termine dell'attività, `StartupHostedServiceHealthCheck.StartupTaskCompleted` viene impostata su `true`:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Il controllo di integrità viene registrato con `AddCheck` in `Startup.ConfigureServices` insieme al servizio ospitato. Poiché il servizio ospitato deve impostare la proprietà sul controllo di integrità, il controllo di integrità viene anche registrato nel contenitore del servizio (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Chiamare il middleware per i controlli di integrità nella pipeline di elaborazione dell'app in `Startup.Configure`. Nell'app di esempio gli endpoint dei controlli di integrità vengono creati in `/health/ready` per il controllo di idoneità e in `/health/live` per il controllo di attività. Il controllo di idoneità filtra i controlli di integrità per il controllo di integrità con il tag `ready`. Il controllo di attività filtra `StartupHostedServiceHealthCheck` restituendo `false` in `HealthCheckOptions.Predicate` (per altre informazioni, vedere [Filtrare i controlli di integrità](#filter-health-checks)):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_Configure)]

Per eseguire lo scenario di configurazione di idoneità/attività usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:

```console
dotnet run --scenario liveness
```

In a browser visitare più volte `/health/ready` per 15 secondi. Il controllo di integrità segnala `Unhealthy` per i primi 15 secondi. Dopo 15 secondi, l'endpoint segnala `Healthy`, che indica il completamento dell'attività con esecuzione prolungata da parte del servizio ospitato.

### <a name="kubernetes-example"></a>Esempio di Kubernetes

L'uso di controlli di idoneità e attività separati è utile in un ambiente come [Kubernetes](https://kubernetes.io/). In Kubernetes un'app potrebbe dover eseguire un'operazione di avvio con esecuzione prolungata prima di accettare le richieste, ad esempio un test della disponibilità del database sottostante. L'uso di controlli separati consente all'agente di orchestrazione di distinguere se l'app è funzionante, ma non è ancora pronta o se l'app non è stata avviata. Per altre informazioni sui probe di idoneità e di attività in Kubernetes, vedere [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (Configurare i probe di attività e di idoneità in Kubernetes) nella documentazione di Kubernetes.

L'esempio seguente illustra la configurazione di un probe di idoneità di Kubernetes:

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Probe basato sulle metriche con un writer di risposta personalizzata

L'app di esempio illustra un controllo di integrità della memoria con un writer di risposta personalizzata.

`MemoryHealthCheck` segnala uno stato danneggiato se l'app usa più di una determinata soglia di memoria (1 GB nell'app di esempio). `HealthCheckResult` include informazioni sul Garbage Collector (GC) per l'app (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Registrare i servizi dei controlli di integrità con `AddHealthChecks` in `Startup.ConfigureServices`. Invece di abilitare il controllo di integrità passandolo ad `AddCheck`, `MemoryHealthCheck` viene registrato come servizio. Tutti i servizi registrati da `IHealthCheck` sono disponibili per il middleware e i servizi di controllo dell'integrità. È consigliabile registrare i servizi di controllo dell'integrità come servizi Singleton.

*CustomWriterStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Chiamare il middleware per i controlli di integrità nella pipeline di elaborazione dell'app in `Startup.Configure`. Viene fornito un delegato `WriteResponse` alla proprietà `ResponseWriter` per visualizzare una risposta JSON personalizzata quando il controllo di integrità viene eseguito:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_Configure&highlight=6)]

Il metodo `WriteResponse` formatta `CompositeHealthCheckResult` in un oggetto JSON e genera l'output JSON per la risposta del controllo di integrità:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Per eseguire lo scenario di probe basato sulle metriche con il writer di risposta personalizzata usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:

```console
dotnet run --scenario writer
```

> [!NOTE]
> [BeatPulse](https://github.com/Xabaril/BeatPulse) include gli scenari di controllo dell'integrità basati sulle metriche, inclusi i controlli di attività del valore massimo e di archiviazione su disco.
>
> [BeatPulse](https://github.com/Xabaril/BeatPulse) non viene gestito né supportato da Microsoft.

## <a name="filter-by-port"></a>Filtrare in base alla porta

La chiamata a `UseHealthChecks` con una porta limita le richieste di controllo dell'integrità alla porta specificata. Questa chiamata viene in genere usata nell'ambiente di un contenitore per esporre una porta per il monitoraggio dei servizi.

L'app di esempio configura la porta usando il [provider di configurazione della variabile di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider). La porta viene impostata nel file *launchSettings.json* e passata al provider di configurazione tramite una variabile di ambiente. È anche necessario configurare il server per l'ascolto di richieste sulla porta di gestione.

Per usare l'app di esempio per illustrare la configurazione della porta di gestione, creare il file *launchSettings.json* in una cartella *Properties*.

Il file *launchSettings.json* di esempio non è incluso nei file di progetto dell'app di esempio e deve essere creato manualmente.

*Properties/launchSettings.json*:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Registrare i servizi dei controlli di integrità con `AddHealthChecks` in `Startup.ConfigureServices`. La chiamata a `UseHealthChecks` specifica la porta di gestione (*ManagementPortStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=12,18)]

> [!NOTE]
> È possibile evitare di creare il file *launchSettings.json* nell'app di esempio impostando gli URL e la porta di gestione in modo esplicito nel codice. In *Program.cs*, dove viene creato `WebHostBuilder`, aggiungere una chiamata a `UseUrls` e fornire l'endpoint della porta di gestione e l'endpoint di risposta normale dell'app. In *ManagementPortStartup.cs*, dove viene chiamato `UseHealthChecks`, specificare la porta di gestione in modo esplicito.
>
> *Program.cs*:
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

Per eseguire lo scenario di configurazione della porta di gestione usando l'app di esempio, eseguire il comando seguente dalla cartella del progetto in una shell dei comandi:

```console
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuire una libreria di controllo di integrità

Per distribuire un controllo di integrità come libreria:

1. Scrivere un controllo di integrità che implementa l'interfaccia `IHealthCheck` come classe autonoma. La classe può basarsi su [inserimento delle dipendenze](xref:fundamentals/dependency-injection), attivazione del tipo e [opzioni denominate](xref:fundamentals/configuration/options) per accedere ai dati di configurazione.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   // Health check logic
                   //
                   // data1 and data2 are used in the method to
                   // run the probe's health check logic.

                   // Assume that it's possible for this health check
                   // to throw an AccessViolationException.

                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. Scrivere un metodo di estensione con i parametri che l'app chiama nel metodo `Startup.Configure`. Nell'esempio seguente si presuma la firma del metodo di controllo dell'integrità seguente:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   La firma precedente indica che `ExampleHealthCheck` richiede dati aggiuntivi per elaborare la logica del probe di controllo dell'integrità. I dati vengono forniti al delegato usato per creare l'istanza del controllo di integrità quando il controllo di integrità viene registrato con un metodo di estensione. Nell'esempio seguente il chiamante specifica facoltativamente:

   * nome del controllo integrità (`name`). Se `null`, viene usato `example_health_check`.
   * punto dati stringa per il controllo di integrità (`data1`).
   * punto dati Integer per il controllo di integrità (`data2`). Se `null`, viene usato `1`.
   * stato dell'errore (`HealthStatus`). Il valore predefinito è `null`. Se `null`, viene segnalato `HealthStatus.Unhealthy` come stato di errore.
   * tag (`IEnumerable<string>`).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string NAME = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder, 
           string name = default, 
           string data1, 
           int data2 = 1, 
           HealthStatus? failureStatus = default, 
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? NAME,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Server di pubblicazione dei controlli di integrità

Quando `IHealthCheckPublisher` viene aggiunto al contenitore del servizio, il sistema di controllo dell'integrità esegue periodicamente i controlli di integrità e chiama `PublishAsync` con il risultato. Ciò è utile in uno scenario relativo a un sistema di monitoraggio dell'integrità basato su push, che prevede che ogni processo chiami periodicamente il sistema di monitoraggio per determinare l'integrità.

L'interfaccia `IHealthCheckPublisher` ha un solo metodo:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

> [!NOTE]
> [BeatPulse](https://github.com/Xabaril/BeatPulse) include i server di pubblicazione per diversi sistemi, incluso [Application Insights](/azure/application-insights/app-insights-overview).
>
> [BeatPulse](https://github.com/Xabaril/BeatPulse) non viene gestito né supportato da Microsoft.
