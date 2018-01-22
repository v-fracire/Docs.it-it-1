---
title: La migrazione da ASP.NET Web API
author: ardalis
description: 
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/webapi
ms.openlocfilehash: aef00b63c9889100116facc610bec99f889e4c46
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="migrating-from-aspnet-web-api"></a><span data-ttu-id="44582-102">La migrazione da ASP.NET Web API</span><span class="sxs-lookup"><span data-stu-id="44582-102">Migrating from ASP.NET Web API</span></span>

<span data-ttu-id="44582-103">Da [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="44582-103">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="44582-104">API Web sono servizi HTTP in grado di raggiungono una vasta gamma di client, inclusi browser e dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="44582-104">Web APIs are HTTP services that reach a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="44582-105">ASP.NET MVC di base include il supporto per la compilazione di API Web che fornisce un singolo modo coerente, di compilazione di applicazioni web.</span><span class="sxs-lookup"><span data-stu-id="44582-105">ASP.NET Core MVC includes support for building Web APIs providing a single, consistent way of building web applications.</span></span> <span data-ttu-id="44582-106">In questo articolo viene descritto come i passaggi necessari per eseguire la migrazione di un'implementazione di API Web di API Web ASP.NET ad ASP.NET MVC di base.</span><span class="sxs-lookup"><span data-stu-id="44582-106">In this article, we demonstrate the steps required to migrate a Web API implementation from ASP.NET Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="44582-107">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/webapi/sample) ([procedura per il download](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="44582-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="review-aspnet-web-api-project"></a><span data-ttu-id="44582-108">Progetto API Web ASP.NET di revisione</span><span class="sxs-lookup"><span data-stu-id="44582-108">Review ASP.NET Web API Project</span></span>

<span data-ttu-id="44582-109">Questo articolo viene utilizzato il progetto di esempio, *ProductsApp*, creato nell'articolo [Introduzione a ASP.NET Web API](https://docs.microsoft.com/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) come punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="44582-109">This article uses the sample project, *ProductsApp*, created in the article [Getting Started with ASP.NET Web API](https://docs.microsoft.com/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) as its starting point.</span></span> <span data-ttu-id="44582-110">In tale progetto, un progetto di API Web ASP.NET semplice viene configurato come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="44582-110">In that project, a simple ASP.NET Web API  project is configured as follows.</span></span>

<span data-ttu-id="44582-111">In *Global.asax.cs*, viene effettuata una chiamata a `WebApiConfig.Register`:</span><span class="sxs-lookup"><span data-stu-id="44582-111">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[Main](../migration/webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="44582-112">`WebApiConfig`è definito in *App_Start*, e ha solo una riga statica `Register` metodo:</span><span class="sxs-lookup"><span data-stu-id="44582-112">`WebApiConfig` is defined in *App_Start*, and has just one static `Register` method:</span></span>

[!code-csharp[Main](../migration/webapi/sample/ProductsApp/App_Start/WebApiConfig.cs?highlight=15,16,17,18,19,20)]


<span data-ttu-id="44582-113">Questa classe consente di configurare [routing degli attributi](https://docs.microsoft.com/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), anche se non è effettivamente utilizzato nel progetto.</span><span class="sxs-lookup"><span data-stu-id="44582-113">This class configures [attribute routing](https://docs.microsoft.com/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="44582-114">Configura inoltre la tabella di routing viene utilizzato da ASP.NET Web API.</span><span class="sxs-lookup"><span data-stu-id="44582-114">It also configures the routing table which is used by ASP.NET Web API.</span></span> <span data-ttu-id="44582-115">In questo caso, si otterranno gli URL in base al formato ASP.NET Web API */api/ {controller} / {id}*, con *{id}* sia facoltativo.</span><span class="sxs-lookup"><span data-stu-id="44582-115">In this case, ASP.NET Web API will expect URLs to match the format */api/{controller}/{id}*, with *{id}* being optional.</span></span>

<span data-ttu-id="44582-116">Il *ProductsApp* progetto include un solo controller semplice, che eredita da `ApiController` ed espone due metodi:</span><span class="sxs-lookup"><span data-stu-id="44582-116">The *ProductsApp* project includes just one simple controller, which inherits from `ApiController` and exposes two methods:</span></span>

[!code-csharp[Main](../migration/webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=19,24)]

<span data-ttu-id="44582-117">Infine, il modello di *prodotto*, utilizzato per il *ProductsApp*, una classe semplice:</span><span class="sxs-lookup"><span data-stu-id="44582-117">Finally, the model, *Product*, used by the *ProductsApp*, is a simple class:</span></span>

[!code-csharp[Main](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="44582-118">Dopo aver creato un progetto semplice da cui iniziare, è possibile illustrare come eseguire la migrazione di questo progetto API Web per ASP.NET MVC di base.</span><span class="sxs-lookup"><span data-stu-id="44582-118">Now that we have a simple project from which to start, we can demonstrate how to migrate this Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="44582-119">Creare il progetto di destinazione</span><span class="sxs-lookup"><span data-stu-id="44582-119">Create the Destination Project</span></span>

<span data-ttu-id="44582-120">Utilizzando Visual Studio, creare una nuova soluzione vuota e denominarla *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="44582-120">Using Visual Studio, create a new, empty solution, and name it *WebAPIMigration*.</span></span> <span data-ttu-id="44582-121">Aggiungere esistente *ProductsApp* progetto a esso, quindi, aggiungere un nuovo progetto applicazione ASP.NET Core Web alla soluzione.</span><span class="sxs-lookup"><span data-stu-id="44582-121">Add the existing *ProductsApp* project to it, then, add a new ASP.NET Core Web Application Project to the solution.</span></span> <span data-ttu-id="44582-122">Denominare il nuovo progetto *ProductsCore*.</span><span class="sxs-lookup"><span data-stu-id="44582-122">Name the new project *ProductsCore*.</span></span>

![Finestra di dialogo Nuovo progetto aprire modelli Web](webapi/_static/add-web-project.png)

<span data-ttu-id="44582-124">Successivamente, scegliere il modello di progetto API Web.</span><span class="sxs-lookup"><span data-stu-id="44582-124">Next, choose the Web API project template.</span></span> <span data-ttu-id="44582-125">Verrà eseguita la migrazione è il *ProductsApp* contenuto al nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="44582-125">We will migrate the *ProductsApp* contents to this new project.</span></span>

![Finestra di dialogo Nuovo applicazione Web con il modello di progetto API Web selezionato nell'elenco di modelli ASP.NET Core](webapi/_static/aspnet-5-webapi.png)

<span data-ttu-id="44582-127">Eliminare il `Project_Readme.html` file dal nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="44582-127">Delete the `Project_Readme.html` file from the new project.</span></span> <span data-ttu-id="44582-128">La soluzione dovrebbe risultare simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="44582-128">Your solution should now look like this:</span></span>

![Apri in Esplora soluzioni con file e cartelle dei progetti ProductsApp e ProductsCore soluzione dell'applicazione](webapi/_static/webapimigration-solution.png)

## <a name="migrate-configuration"></a><span data-ttu-id="44582-130">La migrazione della configurazione</span><span class="sxs-lookup"><span data-stu-id="44582-130">Migrate Configuration</span></span>

<span data-ttu-id="44582-131">ASP.NET di base non vengono più utilizzati *Global. asax*, *Web. config*, o *App_Start* cartelle.</span><span class="sxs-lookup"><span data-stu-id="44582-131">ASP.NET Core no longer uses *Global.asax*, *web.config*, or *App_Start* folders.</span></span> <span data-ttu-id="44582-132">Al contrario, tutte le attività di avvio vengono eseguite in *Startup.cs* nella radice del progetto (vedere [avvio dell'applicazione](../fundamentals/startup.md)).</span><span class="sxs-lookup"><span data-stu-id="44582-132">Instead, all startup tasks are done in *Startup.cs* in the root of the project (see [Application Startup](../fundamentals/startup.md)).</span></span> <span data-ttu-id="44582-133">In ASP.NET MVC di base, il routing basato su attributi è ora inclusa per impostazione predefinita quando `UseMvc()` viene chiamato e questo è l'approccio consigliato per la configurazione delle route di API Web (e il modo in cui il progetto iniziale di API Web gestisce routing).</span><span class="sxs-lookup"><span data-stu-id="44582-133">In ASP.NET Core MVC, attribute-based routing is now included by default when `UseMvc()` is called; and, this is the recommended approach for configuring Web API routes (and is how the Web API starter project handles routing).</span></span>

[!code-none[Main](../migration/webapi/sample/ProductsCore/Startup.cs?highlight=40)]

<span data-ttu-id="44582-134">Supponendo che si desidera utilizzare il routing degli attributi nel progetto in futuro, è necessaria alcuna configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="44582-134">Assuming you want to use attribute routing in your project going forward, no additional configuration is needed.</span></span> <span data-ttu-id="44582-135">È sufficiente applicare gli attributi in base alle esigenze per i controller e azioni, come avviene nell'esempio `ValuesController` classe inclusa nel progetto di avvio di Web API:</span><span class="sxs-lookup"><span data-stu-id="44582-135">Simply apply the attributes as needed to your controllers and actions, as is done in the sample `ValuesController` class that is included in the Web API starter project:</span></span>

[!code-csharp[Main](../migration/webapi/sample/ProductsCore/Controllers/ValuesController.cs?highlight=9,13,20,27,33,39)]

<span data-ttu-id="44582-136">Si noti la presenza di *[controller]* sulla riga 8.</span><span class="sxs-lookup"><span data-stu-id="44582-136">Note the presence of *[controller]* on line 8.</span></span> <span data-ttu-id="44582-137">Routing basato su attributo ora supporta determinati token, ad esempio *[controller]* e *[azione]*.</span><span class="sxs-lookup"><span data-stu-id="44582-137">Attribute-based routing now supports certain tokens, such as *[controller]* and *[action]*.</span></span> <span data-ttu-id="44582-138">Questi token vengono sostituiti in fase di esecuzione con il nome del controller o azione, rispettivamente, a cui è stato applicato l'attributo.</span><span class="sxs-lookup"><span data-stu-id="44582-138">These tokens are replaced at runtime with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="44582-139">Questa opzione serve a ridurre il numero di stringhe chiave nel progetto e garantisce che le route rimarrà sincronizzate con i corrispondenti controller e le azioni quando viene applicato il refactoring di ridenominazione automatica.</span><span class="sxs-lookup"><span data-stu-id="44582-139">This serves to reduce the number of magic strings in the project, and it ensures the routes will be kept synchronized with their corresponding controllers and actions when automatic rename refactorings are applied.</span></span>

<span data-ttu-id="44582-140">Per eseguire la migrazione il controller API di prodotti, è necessario copiare *ProductsController* al nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="44582-140">To migrate the Products API controller, we must first copy *ProductsController* to the new project.</span></span> <span data-ttu-id="44582-141">Quindi includere semplicemente l'attributo della route sul controller:</span><span class="sxs-lookup"><span data-stu-id="44582-141">Then simply include the route attribute on the controller:</span></span>

```csharp
[Route("api/[controller]")]
```

<span data-ttu-id="44582-142">È inoltre necessario aggiungere il `[HttpGet]` attributo per i due metodi, poiché entrambi deve essere chiamati tramite HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="44582-142">You also need to add the `[HttpGet]` attribute to the two methods, since they both should be called via HTTP Get.</span></span> <span data-ttu-id="44582-143">Includere la prospettiva di un parametro "id" nell'attributo `GetProduct()`:</span><span class="sxs-lookup"><span data-stu-id="44582-143">Include the expectation of an "id" parameter in the attribute for `GetProduct()`:</span></span>

```csharp
// /api/products
[HttpGet]
...

// /api/products/1
[HttpGet("{id}")]
```

<span data-ttu-id="44582-144">A questo punto, il routing è configurato correttamente. Tuttavia, è Impossibile ancora eseguirne il test.</span><span class="sxs-lookup"><span data-stu-id="44582-144">At this point, routing is configured correctly; however, we can't yet test it.</span></span> <span data-ttu-id="44582-145">Altre modifiche devono essere eseguite prima *ProductsController* verrà compilato.</span><span class="sxs-lookup"><span data-stu-id="44582-145">Additional changes must be made before *ProductsController* will compile.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="44582-146">Eseguire la migrazione di modelli e controller</span><span class="sxs-lookup"><span data-stu-id="44582-146">Migrate Models and Controllers</span></span>

<span data-ttu-id="44582-147">L'ultimo passaggio del processo di migrazione per questo progetto API Web semplice consiste nel copiare i controller e tutti i modelli utilizzano.</span><span class="sxs-lookup"><span data-stu-id="44582-147">The last step in the migration process for this simple Web API project is to copy over the Controllers and any Models they use.</span></span> <span data-ttu-id="44582-148">In questo caso, è sufficiente copiare *Controllers/ProductsController.cs* dal progetto originale a quello nuovo.</span><span class="sxs-lookup"><span data-stu-id="44582-148">In this case, simply copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span> <span data-ttu-id="44582-149">Copiare l'intera cartella modelli del progetto originale a quello nuovo.</span><span class="sxs-lookup"><span data-stu-id="44582-149">Then, copy the entire Models folder from the original project to the new one.</span></span> <span data-ttu-id="44582-150">Modificare gli spazi dei nomi per corrispondere al nuovo nome di progetto (*ProductsCore*).</span><span class="sxs-lookup"><span data-stu-id="44582-150">Adjust the namespaces to match the new project name (*ProductsCore*).</span></span>  <span data-ttu-id="44582-151">A questo punto, è possibile compilare l'applicazione e si troverà un numero di errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="44582-151">At this point, you can build the application, and you will find a number of compilation errors.</span></span> <span data-ttu-id="44582-152">In genere, questi devono rientrare nelle categorie seguenti:</span><span class="sxs-lookup"><span data-stu-id="44582-152">These should generally fall into the following categories:</span></span>

* <span data-ttu-id="44582-153">*ApiController* non esiste</span><span class="sxs-lookup"><span data-stu-id="44582-153">*ApiController* does not exist</span></span>

* <span data-ttu-id="44582-154">*System.Web.Http* dello spazio dei nomi non esiste.</span><span class="sxs-lookup"><span data-stu-id="44582-154">*System.Web.Http* namespace does not exist</span></span>

* <span data-ttu-id="44582-155">*IHttpActionResult* non esiste</span><span class="sxs-lookup"><span data-stu-id="44582-155">*IHttpActionResult* does not exist</span></span>

<span data-ttu-id="44582-156">Fortunatamente, questi sono molto semplici correggere:</span><span class="sxs-lookup"><span data-stu-id="44582-156">Fortunately, these are all very easy to correct:</span></span>

* <span data-ttu-id="44582-157">Modifica *ApiController* a *Controller* (potrebbe essere necessario aggiungere *utilizzando Microsoft.AspNetCore.Mvc*)</span><span class="sxs-lookup"><span data-stu-id="44582-157">Change *ApiController* to *Controller* (you may need to add *using Microsoft.AspNetCore.Mvc*)</span></span>

* <span data-ttu-id="44582-158">Eliminare qualsiasi istruzione che fa riferimento a *System.Web.Http*</span><span class="sxs-lookup"><span data-stu-id="44582-158">Delete any using statement referring to *System.Web.Http*</span></span>

* <span data-ttu-id="44582-159">Modificare qualsiasi metodo restituzione *IHttpActionResult* per restituire un *IActionResult*</span><span class="sxs-lookup"><span data-stu-id="44582-159">Change any method returning *IHttpActionResult* to return a *IActionResult*</span></span>

<span data-ttu-id="44582-160">Una volta queste modifiche sono state effettuate e non utilizzati tramite istruzioni rimossi, dopo la migrazione *ProductsController* classe è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="44582-160">Once these changes have been made and unused using statements removed, the migrated *ProductsController* class looks like this:</span></span>

[!code-csharp[Main](../migration/webapi/sample/ProductsCore/Controllers/ProductsController.cs?highlight=1,2,6,8,9,27)]

<span data-ttu-id="44582-161">A questo punto debba essere in grado di eseguire il progetto di migrazione e passare a */api/prodotti*; e, è necessario visualizzare l'elenco completo dei 3 prodotti.</span><span class="sxs-lookup"><span data-stu-id="44582-161">You should now be able to run the migrated project and browse to */api/products*; and, you should see the full list of 3 products.</span></span> <span data-ttu-id="44582-162">Passare a */api/products/1* dovrebbe essere visualizzata prima del prodotto.</span><span class="sxs-lookup"><span data-stu-id="44582-162">Browse to */api/products/1* and you should see the first product.</span></span>

## <a name="summary"></a><span data-ttu-id="44582-163">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="44582-163">Summary</span></span>

<span data-ttu-id="44582-164">La migrazione di un progetto ASP.NET Web API semplice per ASP.NET MVC di base è piuttosto semplice, grazie al supporto incorporato per l'API Web di ASP.NET MVC di base.</span><span class="sxs-lookup"><span data-stu-id="44582-164">Migrating a simple ASP.NET Web API project to ASP.NET Core MVC is fairly straightforward, thanks to the built-in support for Web APIs in ASP.NET Core MVC.</span></span> <span data-ttu-id="44582-165">Le parti principali, che saranno necessario eseguire la migrazione di tutti i progetti ASP.NET Web API sono route, controller e i modelli, gli aggiornamenti per i tipi utilizzati dai controller e azioni.</span><span class="sxs-lookup"><span data-stu-id="44582-165">The main pieces every ASP.NET Web API project will need to migrate are routes, controllers, and models, along with updates to the types used by  controllers and actions.</span></span>