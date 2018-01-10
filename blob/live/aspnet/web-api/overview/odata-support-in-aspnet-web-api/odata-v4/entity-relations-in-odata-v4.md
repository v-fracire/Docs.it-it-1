---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/entity-relations-in-odata-v4
title: Entity Relations in OData v4 con ASP.NET Web API 2.2 | Documenti Microsoft
author: MikeWasson
description: "La maggior parte dei set di dati definiscono le relazioni tra entità: sono presenti ordini; un libro può avere autori; prodotti siano fornitori. Utilizzo di OData, i client è possono navigare nei..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2014
ms.topic: article
ms.assetid: 72657550-ec09-4779-9bfc-2fb15ecd51c7
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/entity-relations-in-odata-v4
msc.type: authoredcontent
ms.openlocfilehash: 4127fb2fa83f513a4faeb222068ca99f234ece22
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
<a name="entity-relations-in-odata-v4-using-aspnet-web-api-22"></a><span data-ttu-id="a159b-104">Entity Relations in OData v4 con ASP.NET Web API 2.2</span><span class="sxs-lookup"><span data-stu-id="a159b-104">Entity Relations in OData v4 Using ASP.NET Web API 2.2</span></span>
====================
<span data-ttu-id="a159b-105">da [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="a159b-105">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

> <span data-ttu-id="a159b-106">La maggior parte dei set di dati definiscono le relazioni tra entità: sono presenti ordini; un libro può avere autori; prodotti siano fornitori.</span><span class="sxs-lookup"><span data-stu-id="a159b-106">Most data sets define relations between entities: Customers have orders; books have authors; products have suppliers.</span></span> <span data-ttu-id="a159b-107">Utilizzando OData, i client possono passare tramite relazioni di entità.</span><span class="sxs-lookup"><span data-stu-id="a159b-107">Using OData, clients can navigate over entity relations.</span></span> <span data-ttu-id="a159b-108">Dato un prodotto, è possibile trovare il fornitore.</span><span class="sxs-lookup"><span data-stu-id="a159b-108">Given a product, you can find the supplier.</span></span> <span data-ttu-id="a159b-109">È anche possibile creare o rimuovere le relazioni.</span><span class="sxs-lookup"><span data-stu-id="a159b-109">You can also create or remove relationships.</span></span> <span data-ttu-id="a159b-110">Ad esempio, è possibile impostare il fornitore per un prodotto.</span><span class="sxs-lookup"><span data-stu-id="a159b-110">For example, you can set the supplier for a product.</span></span>
> 
> <span data-ttu-id="a159b-111">Questa esercitazione viene illustrato come supportare queste operazioni in OData v4 mediante ASP.NET Web API.</span><span class="sxs-lookup"><span data-stu-id="a159b-111">This tutorial shows how to support these operations in OData v4 using ASP.NET Web API.</span></span> <span data-ttu-id="a159b-112">L'esercitazione si basa sull'esercitazione [creare un OData v4 Endpoint Using ASP.NET Web API 2](create-an-odata-v4-endpoint.md).</span><span class="sxs-lookup"><span data-stu-id="a159b-112">The tutorial builds on the tutorial [Create an OData v4 Endpoint Using ASP.NET Web API 2](create-an-odata-v4-endpoint.md).</span></span>
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="a159b-113">Versioni del software utilizzate nell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="a159b-113">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="a159b-114">2.1 API Web</span><span class="sxs-lookup"><span data-stu-id="a159b-114">Web API 2.1</span></span>
> - <span data-ttu-id="a159b-115">OData v4</span><span class="sxs-lookup"><span data-stu-id="a159b-115">OData v4</span></span>
> - [<span data-ttu-id="a159b-116">Visual Studio 2013 Update 2</span><span class="sxs-lookup"><span data-stu-id="a159b-116">Visual Studio 2013 Update 2</span></span>](https://www.visualstudio.com/downloads/download-visual-studio-vs)
> - <span data-ttu-id="a159b-117">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="a159b-117">Entity Framework 6</span></span>
> - <span data-ttu-id="a159b-118">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="a159b-118">.NET 4.5</span></span>
> 
> 
> ## <a name="tutorial-versions"></a><span data-ttu-id="a159b-119">Versioni dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="a159b-119">Tutorial versions</span></span>
> 
> <span data-ttu-id="a159b-120">Per OData versione 3, vedere [supporto Entity Relations in OData v3](https://asp.net/web-api/overview/odata-support-in-aspnet-web-api/odata-v3/working-with-entity-relations).</span><span class="sxs-lookup"><span data-stu-id="a159b-120">For the OData Version 3, see [Supporting Entity Relations in OData v3](https://asp.net/web-api/overview/odata-support-in-aspnet-web-api/odata-v3/working-with-entity-relations).</span></span>


## <a name="add-a-supplier-entity"></a><span data-ttu-id="a159b-121">Aggiungere un'entità Supplier</span><span class="sxs-lookup"><span data-stu-id="a159b-121">Add a Supplier Entity</span></span>

> [!NOTE]
> <span data-ttu-id="a159b-122">L'esercitazione si basa sull'esercitazione [creare un OData v4 Endpoint Using ASP.NET Web API 2](create-an-odata-v4-endpoint.md).</span><span class="sxs-lookup"><span data-stu-id="a159b-122">The tutorial builds on the tutorial [Create an OData v4 Endpoint Using ASP.NET Web API 2](create-an-odata-v4-endpoint.md).</span></span>


<span data-ttu-id="a159b-123">Innanzitutto, è necessaria un'entità correlata.</span><span class="sxs-lookup"><span data-stu-id="a159b-123">First, we need a related entity.</span></span> <span data-ttu-id="a159b-124">Aggiungere una classe denominata `Supplier` nella cartella Models.</span><span class="sxs-lookup"><span data-stu-id="a159b-124">Add a class named `Supplier` in the Models folder.</span></span>

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample1.cs)]

<span data-ttu-id="a159b-125">Aggiungere una proprietà di navigazione per la `Product` classe:</span><span class="sxs-lookup"><span data-stu-id="a159b-125">Add a navigation property to the `Product` class:</span></span>

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample2.cs?highlight=13-15)]

<span data-ttu-id="a159b-126">Aggiungere un nuovo **DbSet** per la `ProductsContext` classe, in modo che Entity Framework includerà la tabella fornitore nel database.</span><span class="sxs-lookup"><span data-stu-id="a159b-126">Add a new **DbSet** to the `ProductsContext` class, so that Entity Framework will include the Supplier table in the database.</span></span>

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample3.cs?highlight=10)]

<span data-ttu-id="a159b-127">In WebApiConfig.cs, aggiungere un &quot;Suppliers&quot; del set di entità per entity data model:</span><span class="sxs-lookup"><span data-stu-id="a159b-127">In WebApiConfig.cs, add a &quot;Suppliers&quot; entity set to the entity data model:</span></span>

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample4.cs?highlight=6)]

## <a name="add-a-suppliers-controller"></a><span data-ttu-id="a159b-128">Aggiungere un Controller di fornitori</span><span class="sxs-lookup"><span data-stu-id="a159b-128">Add a Suppliers Controller</span></span>

<span data-ttu-id="a159b-129">Aggiungere un `SuppliersController` classe nella cartella controller.</span><span class="sxs-lookup"><span data-stu-id="a159b-129">Add a `SuppliersController` class to the Controllers folder.</span></span>

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample5.cs)]

<span data-ttu-id="a159b-130">Non verrà illustrato come aggiungere le operazioni CRUD per questo controller.</span><span class="sxs-lookup"><span data-stu-id="a159b-130">I won't show how to add CRUD operations for this controller.</span></span> <span data-ttu-id="a159b-131">I passaggi sono gli stessi controller Products (vedere [creare un Endpoint di OData v4](create-an-odata-v4-endpoint.md)).</span><span class="sxs-lookup"><span data-stu-id="a159b-131">The steps are the same as for the Products controller (see [Create an OData v4 Endpoint](create-an-odata-v4-endpoint.md)).</span></span>

## <a name="getting-related-entities"></a><span data-ttu-id="a159b-132">Recupero di entità correlate</span><span class="sxs-lookup"><span data-stu-id="a159b-132">Getting Related Entities</span></span>

<span data-ttu-id="a159b-133">Per ottenere il fornitore per un prodotto, il client invia una richiesta GET:</span><span class="sxs-lookup"><span data-stu-id="a159b-133">To get the supplier for a product, the client sends a GET request:</span></span>

[!code-console[Main](entity-relations-in-odata-v4/samples/sample6.cmd)]

<span data-ttu-id="a159b-134">Per supportare questa richiesta, aggiungere il metodo seguente alla `ProductsController` classe:</span><span class="sxs-lookup"><span data-stu-id="a159b-134">To support this request, add the following method to the `ProductsController` class:</span></span>

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample7.cs)]

<span data-ttu-id="a159b-135">Questo metodo utilizza una convenzione di denominazione predefinito</span><span class="sxs-lookup"><span data-stu-id="a159b-135">This method uses a default naming convention</span></span>

- <span data-ttu-id="a159b-136">Nome del metodo: GetX, dove X è la proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="a159b-136">Method name: GetX, where X is the navigation property.</span></span>
- <span data-ttu-id="a159b-137">Nome del parametro: *chiave*</span><span class="sxs-lookup"><span data-stu-id="a159b-137">Parameter name: *key*</span></span>

<span data-ttu-id="a159b-138">Se si segue questa convenzione di denominazione, API Web esegue automaticamente il mapping della richiesta HTTP al metodo del controller.</span><span class="sxs-lookup"><span data-stu-id="a159b-138">If you follow this naming convention, Web API automatically maps the HTTP request to the controller method.</span></span>

<span data-ttu-id="a159b-139">Richiesta di esempio, HTTP:</span><span class="sxs-lookup"><span data-stu-id="a159b-139">Example HTTP request:</span></span>

[!code-console[Main](entity-relations-in-odata-v4/samples/sample8.cmd)]

<span data-ttu-id="a159b-140">Risposta di esempio, HTTP:</span><span class="sxs-lookup"><span data-stu-id="a159b-140">Example HTTP response:</span></span>

[!code-console[Main](entity-relations-in-odata-v4/samples/sample9.cmd)]

### <a name="getting-a-related-collection"></a><span data-ttu-id="a159b-141">Recupero di una raccolta correlata</span><span class="sxs-lookup"><span data-stu-id="a159b-141">Getting a related collection</span></span>

<span data-ttu-id="a159b-142">Nell'esempio precedente, un prodotto è un fornitore.</span><span class="sxs-lookup"><span data-stu-id="a159b-142">In the previous example, a product has one supplier.</span></span> <span data-ttu-id="a159b-143">Una proprietà di navigazione possa inoltre restituire una raccolta.</span><span class="sxs-lookup"><span data-stu-id="a159b-143">A navigation property can also return a collection.</span></span> <span data-ttu-id="a159b-144">Il codice seguente ottiene i prodotti per un fornitore:</span><span class="sxs-lookup"><span data-stu-id="a159b-144">The following code gets the products for a supplier:</span></span>

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample10.cs)]

<span data-ttu-id="a159b-145">In questo caso, il metodo restituisce un **IQueryable** anziché un **SingleResult&lt;T&gt;**</span><span class="sxs-lookup"><span data-stu-id="a159b-145">In this case, the method returns an **IQueryable** instead of a **SingleResult&lt;T&gt;**</span></span>

<span data-ttu-id="a159b-146">Richiesta di esempio, HTTP:</span><span class="sxs-lookup"><span data-stu-id="a159b-146">Example HTTP request:</span></span>

[!code-console[Main](entity-relations-in-odata-v4/samples/sample11.cmd)]

<span data-ttu-id="a159b-147">Risposta di esempio, HTTP:</span><span class="sxs-lookup"><span data-stu-id="a159b-147">Example HTTP response:</span></span>

[!code-console[Main](entity-relations-in-odata-v4/samples/sample12.cmd)]

## <a name="creating-a-relationship-between-entities"></a><span data-ttu-id="a159b-148">Creazione di una relazione tra entità</span><span class="sxs-lookup"><span data-stu-id="a159b-148">Creating a Relationship Between Entities</span></span>

<span data-ttu-id="a159b-149">OData supporta la creazione o la rimozione di relazioni tra due entità esistente.</span><span class="sxs-lookup"><span data-stu-id="a159b-149">OData supports creating or removing relationships between two existing entities.</span></span> <span data-ttu-id="a159b-150">Nella terminologia di OData v4, la relazione è un &quot;riferimento&quot;.</span><span class="sxs-lookup"><span data-stu-id="a159b-150">In OData v4 terminology, the relationship is a &quot;reference&quot;.</span></span> <span data-ttu-id="a159b-151">(OData v3, la relazione è stata chiamata una *collegamento*.</span><span class="sxs-lookup"><span data-stu-id="a159b-151">(In OData v3, the relationship was called a *link*.</span></span> <span data-ttu-id="a159b-152">Le differenze di protocollo non è rilevante per questa esercitazione.)</span><span class="sxs-lookup"><span data-stu-id="a159b-152">The protocol differences don't matter for this tutorial.)</span></span>

<span data-ttu-id="a159b-153">Un riferimento con il proprio URI, con il modulo `/Entity/NavigationProperty/$ref`.</span><span class="sxs-lookup"><span data-stu-id="a159b-153">A reference has its own URI, with the form `/Entity/NavigationProperty/$ref`.</span></span> <span data-ttu-id="a159b-154">Di seguito è ad esempio, l'URI per risolvere il riferimento tra un prodotto e il fornitore:</span><span class="sxs-lookup"><span data-stu-id="a159b-154">For example, here is the URI to address the reference between a product and its supplier:</span></span>

[!code-console[Main](entity-relations-in-odata-v4/samples/sample13.cmd)]

<span data-ttu-id="a159b-155">Per aggiungere una relazione, il client invia una richiesta POST o PUT a questo indirizzo.</span><span class="sxs-lookup"><span data-stu-id="a159b-155">To add a relationship, the client sends a POST or PUT request to this address.</span></span>

- <span data-ttu-id="a159b-156">Se la proprietà di navigazione è una singola entità, ad esempio `Product.Supplier`.</span><span class="sxs-lookup"><span data-stu-id="a159b-156">PUT if the navigation property is a single entity, such as `Product.Supplier`.</span></span>
- <span data-ttu-id="a159b-157">REGISTRA se la proprietà di navigazione è una raccolta, ad esempio `Supplier.Products`.</span><span class="sxs-lookup"><span data-stu-id="a159b-157">POST if the navigation property is a collection, such as `Supplier.Products`.</span></span>

<span data-ttu-id="a159b-158">Il corpo della richiesta contiene l'URI di altra entità nella relazione.</span><span class="sxs-lookup"><span data-stu-id="a159b-158">The body of the request contains the URI of the other entity in the relation.</span></span> <span data-ttu-id="a159b-159">Di seguito è riportato un esempio di richiesta:</span><span class="sxs-lookup"><span data-stu-id="a159b-159">Here is an example request:</span></span>

[!code-console[Main](entity-relations-in-odata-v4/samples/sample14.cmd)]

<span data-ttu-id="a159b-160">In questo esempio, il client invia una richiesta PUT per `/Products(6)/Supplier/$ref`, che corrisponde all'URI $ref per il `Supplier` del prodotto con ID = 6.</span><span class="sxs-lookup"><span data-stu-id="a159b-160">In this example, the client sends a PUT request to `/Products(6)/Supplier/$ref`, which is the $ref URI for the `Supplier` of the product with ID = 6.</span></span> <span data-ttu-id="a159b-161">Se la richiesta ha esito positivo, il server invia una risposta 204 (nessun contenuto):</span><span class="sxs-lookup"><span data-stu-id="a159b-161">If the request succeeds, the server sends a 204 (No Content) response:</span></span>

[!code-console[Main](entity-relations-in-odata-v4/samples/sample15.cmd)]

<span data-ttu-id="a159b-162">Ecco il metodo del controller per aggiungere una relazione a un `Product`:</span><span class="sxs-lookup"><span data-stu-id="a159b-162">Here is the controller method to add a relationship to a `Product`:</span></span>

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample16.cs)]

<span data-ttu-id="a159b-163">Il *navigationProperty* parametro specifica la relazione da impostare.</span><span class="sxs-lookup"><span data-stu-id="a159b-163">The *navigationProperty* parameter specifies which relationship to set.</span></span> <span data-ttu-id="a159b-164">(Se è presente più di una proprietà di navigazione sull'entità, è possibile aggiungere più `case` istruzioni.)</span><span class="sxs-lookup"><span data-stu-id="a159b-164">(If there is more than one navigation property on the entity, you can add more `case` statements.)</span></span>

<span data-ttu-id="a159b-165">Il *collegamento* parametro contiene l'URI del fornitore.</span><span class="sxs-lookup"><span data-stu-id="a159b-165">The *link* parameter contains the URI of the supplier.</span></span> <span data-ttu-id="a159b-166">API Web analizza automaticamente il corpo della richiesta per ottenere il valore per questo parametro.</span><span class="sxs-lookup"><span data-stu-id="a159b-166">Web API automatically parses the request body to get the value for this parameter.</span></span>

<span data-ttu-id="a159b-167">Per cercare il fornitore, è necessario l'ID (o chiave), che fa parte di *collegamento* parametro.</span><span class="sxs-lookup"><span data-stu-id="a159b-167">To look up the supplier, we need the ID (or key), which is part of the *link* parameter.</span></span> <span data-ttu-id="a159b-168">A tale scopo, utilizzare il metodo di supporto seguenti:</span><span class="sxs-lookup"><span data-stu-id="a159b-168">To do this, use the following helper method:</span></span>

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample17.cs)]

<span data-ttu-id="a159b-169">In pratica, questo metodo utilizza la libreria OData per suddividere il percorso dell'URI in segmenti, individuare il segmento che contiene la chiave e convertire la chiave nel tipo corretto.</span><span class="sxs-lookup"><span data-stu-id="a159b-169">Basically, this method uses the OData library to split the URI path into segments, find the segment that contains the key, and convert the key into the correct type.</span></span>

## <a name="deleting-a-relationship-between-entities"></a><span data-ttu-id="a159b-170">Eliminazione di una relazione tra entità</span><span class="sxs-lookup"><span data-stu-id="a159b-170">Deleting a Relationship Between Entities</span></span>

<span data-ttu-id="a159b-171">Per eliminare una relazione, il client invia una richiesta HTTP DELETE all'URI $ref:</span><span class="sxs-lookup"><span data-stu-id="a159b-171">To delete a relationship, the client sends an HTTP DELETE request to the $ref URI:</span></span>

[!code-console[Main](entity-relations-in-odata-v4/samples/sample18.cmd)]

<span data-ttu-id="a159b-172">Ecco il metodo del controller per eliminare la relazione tra un prodotto e un fornitore:</span><span class="sxs-lookup"><span data-stu-id="a159b-172">Here is the controller method to delete the relationship between a Product and a Supplier:</span></span>

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample19.cs)]

<span data-ttu-id="a159b-173">In questo caso, `Product.Supplier` è il &quot;1&quot; fine di una relazione 1-a-molti, pertanto è possibile eliminare la relazione solo impostando `Product.Supplier` a `null`.</span><span class="sxs-lookup"><span data-stu-id="a159b-173">In this case, `Product.Supplier` is the &quot;1&quot; end of a 1-to-many relation, so you can remove the relationship just by setting `Product.Supplier` to `null`.</span></span>

<span data-ttu-id="a159b-174">Nel &quot;molti&quot; fine di una relazione, il client è necessario specificare quali correlati entità da rimuovere.</span><span class="sxs-lookup"><span data-stu-id="a159b-174">In the &quot;many&quot; end of a relationship, the client must specify which related entity to remove.</span></span> <span data-ttu-id="a159b-175">A tale scopo, il client invia l'URI dell'entità correlata nella stringa di query della richiesta.</span><span class="sxs-lookup"><span data-stu-id="a159b-175">To do so, the client sends the URI of the related entity in the query string of the request.</span></span> <span data-ttu-id="a159b-176">Ad esempio, per rimuovere "Prodotto 1" da "1" fornitore:</span><span class="sxs-lookup"><span data-stu-id="a159b-176">For example, to remove "Product 1" from "Supplier 1":</span></span>

[!code-console[Main](entity-relations-in-odata-v4/samples/sample20.cmd?highlight=1)]

<span data-ttu-id="a159b-177">A tale scopo nell'API Web, è necessario includere un parametro supplementare nel `DeleteRef` metodo.</span><span class="sxs-lookup"><span data-stu-id="a159b-177">To support this in Web API, we need to include an extra parameter in the `DeleteRef` method.</span></span> <span data-ttu-id="a159b-178">Ecco il metodo del controller per un prodotto da rimuovere il `Supplier.Products` relazione.</span><span class="sxs-lookup"><span data-stu-id="a159b-178">Here is the controller method to remove a product from the `Supplier.Products` relation.</span></span>

[!code-csharp[Main](entity-relations-in-odata-v4/samples/sample21.cs)]

<span data-ttu-id="a159b-179">Il *chiave* parametro è la chiave per il fornitore e il *relatedKey* parametro è la chiave per il prodotto rimuovere il `Products` relazione.</span><span class="sxs-lookup"><span data-stu-id="a159b-179">The *key* parameter is the key for the supplier, and the *relatedKey* parameter is the key for the product to remove from the `Products` relationship.</span></span> <span data-ttu-id="a159b-180">Si noti che API Web ottiene automaticamente la chiave dalla stringa di query.</span><span class="sxs-lookup"><span data-stu-id="a159b-180">Note that Web API automatically gets the key from the query string.</span></span>