---
title: Avvio dell'applicazione in ASP.NET Core
author: ardalis
description: Individuazione come la classe di avvio in ASP.NET Core consente di configurare servizi e delle pipeline delle richieste dell'applicazione.
ms.author: tdykstra
manager: wpickett
ms.custom: mvc
ms.date: 12/08/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/startup
ms.openlocfilehash: 8adb96c7261a2e7b1556f0daddcf6f135862b53a
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="application-startup-in-aspnet-core"></a><span data-ttu-id="d1ef1-103">Avvio dell'applicazione in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1ef1-103">Application startup in ASP.NET Core</span></span>

<span data-ttu-id="d1ef1-104">Da [Steve Smith](https://ardalis.com), [Tom Dykstra](https://github.com/tdykstra), e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d1ef1-104">By [Steve Smith](https://ardalis.com), [Tom Dykstra](https://github.com/tdykstra), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d1ef1-105">La `Startup` classe consente di configurare servizi e delle pipeline delle richieste dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-105">The `Startup` class configures services and the app's request pipeline.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="d1ef1-106">La classe di avvio</span><span class="sxs-lookup"><span data-stu-id="d1ef1-106">The Startup class</span></span>

<span data-ttu-id="d1ef1-107">Utilizzare le applicazioni ASP.NET Core un `Startup` (classe), denominato `Startup` per convenzione.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="d1ef1-108">La `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-108">The `Startup` class:</span></span>

* <span data-ttu-id="d1ef1-109">Può includere facoltativamente un [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) metodo per configurare i servizi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-109">Can optionally include a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) method to configure the app's services.</span></span>
* <span data-ttu-id="d1ef1-110">Deve includere un [configura](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) metodo per creare una pipeline di elaborazione delle richieste dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-110">Must include a [Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="d1ef1-111">`ConfigureServices`e `Configure` vengono chiamati dal runtime all'avvio dell'app:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-111">`ConfigureServices` and `Configure` are called by the runtime when the app starts:</span></span>

[!code-csharp[Main](startup/snapshot_sample/Startup1.cs)]

<span data-ttu-id="d1ef1-112">Specificare il `Startup` classe con il [WebHostBuilderExtensions](/dotnet/api/Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions) [UseStartup&lt;TStartup&gt; ](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup#Microsoft_AspNetCore_Hosting_WebHostBuilderExtensions_UseStartup__1_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) metodo:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-112">Specify the `Startup` class with the [WebHostBuilderExtensions](/dotnet/api/Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions) [UseStartup&lt;TStartup&gt;](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup#Microsoft_AspNetCore_Hosting_WebHostBuilderExtensions_UseStartup__1_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) method:</span></span>

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=10)]

<span data-ttu-id="d1ef1-113">Il `Startup` costruttore della classe accetta le dipendenze definite dall'host.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-113">The `Startup` class constructor accepts dependencies defined by the host.</span></span> <span data-ttu-id="d1ef1-114">Un utilizzo comune di [inserimento di dipendenze](xref:fundamentals/dependency-injection) nel `Startup` classe è per inserire [IHostingEnvironment](/dotnet/api/Microsoft.AspNetCore.Hosting.IHostingEnvironment) per configurare i servizi dall'ambiente:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-114">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject [IHostingEnvironment](/dotnet/api/Microsoft.AspNetCore.Hosting.IHostingEnvironment) to configure services by environment:</span></span>

[!code-csharp[Main](startup/snapshot_sample/Startup2.cs)]

<span data-ttu-id="d1ef1-115">Un'alternativa all'inserimento `IHostingStartup` consiste nell'usare un approccio basato su convenzioni.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-115">An alternative to injecting `IHostingStartup` is to use a conventions-based approach.</span></span> <span data-ttu-id="d1ef1-116">L'app è possibile definire separato `Startup` classi per diversi ambienti (ad esempio, `StartupDevelopment`), e la classe di avvio appropriato viene selezionata in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-116">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`), and the appropriate startup class is selected at runtime.</span></span> <span data-ttu-id="d1ef1-117">La classe il cui suffisso di nome corrispondente all'ambiente corrente ha la priorità.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-117">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="d1ef1-118">Se l'app viene eseguita nell'ambiente di sviluppo e include sia un `Startup` classe e un `StartupDevelopment` (classe), la `StartupDevelopment` classe viene utilizzata.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-118">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="d1ef1-119">Per ulteriori informazioni vedere [utilizzo di più ambienti](xref:fundamentals/environments#startup-conventions).</span><span class="sxs-lookup"><span data-stu-id="d1ef1-119">For more information see [Working with multiple environments](xref:fundamentals/environments#startup-conventions).</span></span>

<span data-ttu-id="d1ef1-120">Per altre informazioni, vedere `WebHostBuilder`, vedere il [Hosting](xref:fundamentals/hosting) argomento.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-120">To learn more about `WebHostBuilder`, see the [Hosting](xref:fundamentals/hosting) topic.</span></span> <span data-ttu-id="d1ef1-121">Per informazioni sulla gestione degli errori durante l'avvio, vedere [la gestione delle eccezioni di avvio](xref:fundamentals/error-handling#startup-exception-handling).</span><span class="sxs-lookup"><span data-stu-id="d1ef1-121">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="d1ef1-122">Il metodo ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="d1ef1-122">The ConfigureServices method</span></span>

<span data-ttu-id="d1ef1-123">Il [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) metodo:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-123">The [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) method is:</span></span>

* <span data-ttu-id="d1ef1-124">Parametro facoltativo.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-124">Optional.</span></span>
* <span data-ttu-id="d1ef1-125">Chiamato dall'host web prima di `Configure` metodo per configurare i servizi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-125">Called by the web host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="d1ef1-126">Dove [opzioni di configurazione](xref:fundamentals/configuration/index) vengono impostate per convenzione.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-126">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="d1ef1-127">Aggiunta dei servizi al contenitore del servizio li rende disponibili all'interno dell'app e il `Configure` metodo.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-127">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="d1ef1-128">I servizi vengono risolti tramite [inserimento di dipendenze](xref:fundamentals/dependency-injection) o da [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices).</span><span class="sxs-lookup"><span data-stu-id="d1ef1-128">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices).</span></span>

<span data-ttu-id="d1ef1-129">L'host web può configurare alcuni servizi prima `Startup` metodi vengono chiamati.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-129">The web host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="d1ef1-130">I dettagli sono disponibili nel [Hosting](xref:fundamentals/hosting) argomento.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-130">Details are available in the [Hosting](xref:fundamentals/hosting) topic.</span></span> 

<span data-ttu-id="d1ef1-131">Per le funzionalità che richiedono l'installazione sostanziale, esistono `Add[Service]` metodi di estensione in [IServiceCollection](/dotnet/api/Microsoft.Extensions.DependencyInjection.IServiceCollection).</span><span class="sxs-lookup"><span data-stu-id="d1ef1-131">For features that require substantial setup, there are `Add[Service]` extension methods on [IServiceCollection](/dotnet/api/Microsoft.Extensions.DependencyInjection.IServiceCollection).</span></span> <span data-ttu-id="d1ef1-132">Un'app web tipico registra services per Entity Framework, l'identità e MVC:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-132">A typical web app registers services for Entity Framework, Identity, and MVC:</span></span>

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=4,7,11&start=40&end=55)]

## <a name="services-available-in-startup"></a><span data-ttu-id="d1ef1-133">Servizi disponibili nella finestra di avvio</span><span class="sxs-lookup"><span data-stu-id="d1ef1-133">Services available in Startup</span></span>

<span data-ttu-id="d1ef1-134">L'host web fornisce alcuni servizi che sono disponibili per il `Startup` costruttore della classe.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-134">The web host provides some services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="d1ef1-135">L'applicazione aggiunge servizi aggiuntivi tramite `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-135">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="d1ef1-136">Servizi host e app sono quindi disponibili in `Configure` e in tutta l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-136">Both the host and app services are then available in `Configure` and throughout the application.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="d1ef1-137">Metodo Configure</span><span class="sxs-lookup"><span data-stu-id="d1ef1-137">The Configure method</span></span>

<span data-ttu-id="d1ef1-138">Il [configura](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) consente di specificare la modalità con cui l'app risponde alle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-138">The [Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="d1ef1-139">La pipeline delle richieste è configurata tramite l'aggiunta di [middleware](xref:fundamentals/middleware) componenti da un [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) istanza.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-139">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware) components to an [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) instance.</span></span> <span data-ttu-id="d1ef1-140">`IApplicationBuilder`è disponibile per il `Configure` (metodo), ma non è registrato nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-140">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="d1ef1-141">Crea un `IApplicationBuilder` e lo passa direttamente al `Configure` ([origine riferimento](https://github.com/aspnet/Hosting/blob/release/2.0.0/src/Microsoft.AspNetCore.Hosting/Internal/WebHost.cs#L179-L192)).</span><span class="sxs-lookup"><span data-stu-id="d1ef1-141">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure` ([reference source](https://github.com/aspnet/Hosting/blob/release/2.0.0/src/Microsoft.AspNetCore.Hosting/Internal/WebHost.cs#L179-L192)).</span></span>

<span data-ttu-id="d1ef1-142">Il [modelli ASP.NET Core](/dotnet/core/tools/dotnet-new) configurare la pipeline con il supporto per una pagina di eccezione developer [BrowserLink](http://vswebessentials.com/features/browserlink), ASP.NET MVC, file statici e pagine di errore:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-142">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for a developer exception page, [BrowserLink](http://vswebessentials.com/features/browserlink), error pages, static files, and ASP.NET MVC:</span></span>

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Startup.cs?range=28-48&highlight=5,6,10,13,15)]

<span data-ttu-id="d1ef1-143">Ogni `Use` metodo di estensione aggiunge un componente del middleware alla pipeline delle richieste.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-143">Each `Use` extension method adds a middleware component to the request pipeline.</span></span> <span data-ttu-id="d1ef1-144">Ad esempio, il `UseMvc` metodo di estensione aggiunge la [middleware routing](xref:fundamentals/routing) alla pipeline delle richieste e configura [MVC](xref:mvc/overview) come gestore predefinito.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-144">For instance, the `UseMvc` extension method adds the [routing middleware](xref:fundamentals/routing) to the request pipeline and configures [MVC](xref:mvc/overview) as the default handler.</span></span>

<span data-ttu-id="d1ef1-145">Servizi aggiuntivi, ad esempio `IHostingEnvironment` e `ILoggerFactory`, può anche essere specificato nella firma del metodo.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-145">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, may also be specified in the method signature.</span></span> <span data-ttu-id="d1ef1-146">Quando specificato, vengono inseriti servizi aggiuntivi se sono disponibili.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-146">When specified, additional services are injected if they're available.</span></span>

<span data-ttu-id="d1ef1-147">Per ulteriori informazioni su come usare `IApplicationBuilder`, vedere [Middleware](xref:fundamentals/middleware).</span><span class="sxs-lookup"><span data-stu-id="d1ef1-147">For more information on how to use `IApplicationBuilder`, see [Middleware](xref:fundamentals/middleware).</span></span>

## <a name="convenience-methods"></a><span data-ttu-id="d1ef1-148">Metodi pratici</span><span class="sxs-lookup"><span data-stu-id="d1ef1-148">Convenience methods</span></span>

<span data-ttu-id="d1ef1-149">[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder.configureservices) e [configura](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) metodi pratici consente invece di specificare un `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-149">[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder.configureservices) and [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) convenience methods can be used instead of specifying a `Startup` class.</span></span> <span data-ttu-id="d1ef1-150">Più chiamate a `ConfigureServices` aggiungere uno a altro.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-150">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="d1ef1-151">Più chiamate al metodo `Configure` usare l'ultima chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-151">Multiple calls to `Configure` use the last method call.</span></span>

[!code-csharp[Main](startup/snapshot_sample/Program.cs?highlight=16,20)]

## <a name="startup-filters"></a><span data-ttu-id="d1ef1-152">Filtri di avvio</span><span class="sxs-lookup"><span data-stu-id="d1ef1-152">Startup filters</span></span>

<span data-ttu-id="d1ef1-153">Utilizzare [IStartupFilter](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) per configurare middleware all'inizio o alla fine di un'app [configura](#the-configure-method) pipeline middleware.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-153">Use [IStartupFilter](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) to configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline.</span></span> <span data-ttu-id="d1ef1-154">`IStartupFilter`è utile per garantire l'esecuzione di un tipo di middleware prima o dopo l'aggiunta da parte di librerie all'inizio o alla fine della pipeline di elaborazione di richieste dell'applicazione middleware.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-154">`IStartupFilter` is useful to ensure that a middleware runs before or after middleware added by libraries at the start or end of the app's request processing pipeline.</span></span>

<span data-ttu-id="d1ef1-155">`IStartupFilter`implementa un singolo metodo, [configura](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter.configure), che riceve e restituisce un `Action<IApplicationBuilder>`.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-155">`IStartupFilter` implements a single method, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter.configure), which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="d1ef1-156">Un [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) definisce una classe per configurare la pipeline di richieste di un'app.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-156">An [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="d1ef1-157">Per ulteriori informazioni, vedere [creazione di una pipeline middleware con IApplicationBuilder](xref:fundamentals/middleware#creating-a-middleware-pipeline-with-iapplicationbuilder).</span><span class="sxs-lookup"><span data-stu-id="d1ef1-157">For more information, see [Creating a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware#creating-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="d1ef1-158">Ogni `IStartupFilter` implementa uno o più middlewares nella pipeline delle richieste.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-158">Each `IStartupFilter` implements one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="d1ef1-159">I filtri vengono richiamati nell'ordine in che cui sono stati aggiunti al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-159">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="d1ef1-160">I filtri possono aggiungere middleware prima o dopo il passaggio di controllo per il filtro successivo, in questo modo viene aggiunta all'inizio o alla fine della pipeline dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-160">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="d1ef1-161">Il [app di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/startup/sample/) ([come scaricare](xref:tutorials/index#how-to-download-a-sample)) viene illustrato come registrare un tipo di middleware con `IStartupFilter`.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-161">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/startup/sample/) ([how to download](xref:tutorials/index#how-to-download-a-sample)) demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="d1ef1-162">L'app di esempio include un tipo di middleware che imposta un valore per le opzioni da un parametro di stringa di query:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-162">The sample app includes a middleware that sets an options value from a query string parameter:</span></span>

[!code-csharp[Main](startup/sample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="d1ef1-163">Il `RequestSetOptionsMiddleware` è configurato nel `RequestSetOptionsStartupFilter` classe:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-163">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[Main](startup/sample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="d1ef1-164">Il `IStartupFilter` è registrato nel contenitore dei servizi in `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-164">The `IStartupFilter` is registered in the service container in `ConfigureServices`:</span></span>

[!code-csharp[Main](startup/sample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="d1ef1-165">Quando un parametro di stringa di query per `option` viene fornito, il middleware elabora il valore assegnato alla prima il middleware MVC esegue il rendering nella risposta:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-165">When a query string parameter for `option` is provided, the middleware processes the value assignment before the MVC middleware renders the response:</span></span>

![Finestra del browser che illustra la pagina di indice viene eseguito il rendering.](startup/_static/index.png)

<span data-ttu-id="d1ef1-168">Ordine di esecuzione di middleware è impostata dall'ordine delle `IStartupFilter` registrazioni:</span><span class="sxs-lookup"><span data-stu-id="d1ef1-168">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="d1ef1-169">Più `IStartupFilter` implementazioni possono interagire con gli stessi oggetti.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-169">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="d1ef1-170">Se l'ordine è importante, ordinare i `IStartupFilter` registrazioni per corrispondere all'ordine che deve essere eseguita loro middlewares del servizio.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-170">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="d1ef1-171">Librerie possono aggiungere middleware con uno o più `IStartupFilter` implementazioni da eseguire prima o dopo l'altro middleware app registrato con `IStartupFilter`.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-171">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="d1ef1-172">Per richiamare un `IStartupFilter` middleware prima di un tipo di middleware aggiunto da una libreria `IStartupFilter`, posizionare la registrazione del servizio prima che la libreria viene aggiunta al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-172">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`, position the service registration before the library is added to the service container.</span></span> <span data-ttu-id="d1ef1-173">Per richiamare in seguito, posizionare la registrazione del servizio dopo aver aggiunto la libreria.</span><span class="sxs-lookup"><span data-stu-id="d1ef1-173">To invoke it afterward, position the service registration after the library is added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d1ef1-174">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d1ef1-174">Additional resources</span></span>

* [<span data-ttu-id="d1ef1-175">Hosting</span><span class="sxs-lookup"><span data-stu-id="d1ef1-175">Hosting</span></span>](xref:fundamentals/hosting)
* [<span data-ttu-id="d1ef1-176">Uso di più ambienti</span><span class="sxs-lookup"><span data-stu-id="d1ef1-176">Working with Multiple Environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="d1ef1-177">Middleware</span><span class="sxs-lookup"><span data-stu-id="d1ef1-177">Middleware</span></span>](xref:fundamentals/middleware)
* [<span data-ttu-id="d1ef1-178">Registrazione</span><span class="sxs-lookup"><span data-stu-id="d1ef1-178">Logging</span></span>](xref:fundamentals/logging/index)
* [<span data-ttu-id="d1ef1-179">Configurazione</span><span class="sxs-lookup"><span data-stu-id="d1ef1-179">Configuration</span></span>](xref:fundamentals/configuration/index)
* <span data-ttu-id="d1ef1-180">[Classe StartupLoader: metodo FindStartupType (origine di riferimento)](https://github.com/aspnet/Hosting/blob/rel/2.0.0/src/Microsoft.AspNetCore.Hosting/Internal/StartupLoader.cs#L66-L116))</span><span class="sxs-lookup"><span data-stu-id="d1ef1-180">[StartupLoader class: FindStartupType method (reference source)](https://github.com/aspnet/Hosting/blob/rel/2.0.0/src/Microsoft.AspNetCore.Hosting/Internal/StartupLoader.cs#L66-L116))</span></span>