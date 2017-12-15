---
uid: web-api/overview/error-handling/web-api-global-error-handling
title: Globale gestione degli errori in ASP.NET Web API 2 | Documenti Microsoft
author: davidmatson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/03/2014
ms.topic: article
ms.assetid: bffd7863-f63b-4b23-a13c-372b5492e9fb
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/error-handling/web-api-global-error-handling
msc.type: authoredcontent
ms.openlocfilehash: d2bdf04b4da2a099f3a2af100b16682c68f946f2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
<a name="global-error-handling-in-aspnet-web-api-2"></a><span data-ttu-id="fd19b-102">Globale gestione degli errori in ASP.NET Web API 2</span><span class="sxs-lookup"><span data-stu-id="fd19b-102">Global Error Handling in ASP.NET Web API 2</span></span>
====================
<span data-ttu-id="fd19b-103">da [David Matson](https://github.com/davidmatson), [Rick Anderson](https://github.com/Rick-Anderson)</span><span class="sxs-lookup"><span data-stu-id="fd19b-103">by [David Matson](https://github.com/davidmatson), [Rick Anderson](https://github.com/Rick-Anderson)</span></span>

<span data-ttu-id="fd19b-104">Oggi non sarà più facile nell'API Web per accedere o gestire gli errori a livello globale.</span><span class="sxs-lookup"><span data-stu-id="fd19b-104">Today there's no easy way in Web API to log or handle errors globally.</span></span> <span data-ttu-id="fd19b-105">Alcune eccezioni non gestite possono essere elaborati tramite [filtri eccezioni](exception-handling.md), ma esistono un numero di case che non possono gestire i filtri eccezioni.</span><span class="sxs-lookup"><span data-stu-id="fd19b-105">Some unhandled exceptions can be processed via [exception filters](exception-handling.md), but there are a number of cases that exception filters can't handle.</span></span> <span data-ttu-id="fd19b-106">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="fd19b-106">For example:</span></span>

1. <span data-ttu-id="fd19b-107">Eccezioni generate dai costruttori di controller.</span><span class="sxs-lookup"><span data-stu-id="fd19b-107">Exceptions thrown from controller constructors.</span></span>
2. <span data-ttu-id="fd19b-108">Eccezioni generate da gestori di messaggi.</span><span class="sxs-lookup"><span data-stu-id="fd19b-108">Exceptions thrown from message handlers.</span></span>
3. <span data-ttu-id="fd19b-109">Eccezioni generate durante il routing.</span><span class="sxs-lookup"><span data-stu-id="fd19b-109">Exceptions thrown during routing.</span></span>
4. <span data-ttu-id="fd19b-110">Eccezioni generate durante la serializzazione del contenuto di risposta.</span><span class="sxs-lookup"><span data-stu-id="fd19b-110">Exceptions thrown during response content serialization .</span></span>

<span data-ttu-id="fd19b-111">Microsoft desidera fornire un modo semplice e coerenza per accedere e gestire (dove possibile) queste eccezioni.</span><span class="sxs-lookup"><span data-stu-id="fd19b-111">We want to provide a simple, consistent way to log and handle (where possible) these exceptions.</span></span> 

<span data-ttu-id="fd19b-112">Esistono due casi principali per la gestione delle eccezioni, nel caso in cui è sono in grado di inviare una risposta di errore e nel caso in cui è possibile eseguire questa operazione è registro l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="fd19b-112">There are two major cases for handling exceptions, the case where we are able to send an error response and the case where all we can do is log the exception.</span></span> <span data-ttu-id="fd19b-113">Un esempio per quest'ultimo caso è quando viene generata un'eccezione all'interno di streaming del contenuto della risposta; In questo caso è troppo tardi per inviare un nuovo messaggio di risposta, poiché il codice di stato, intestazioni e contenuto parziale passate in rete, pertanto è semplicemente di interrompere la connessione.</span><span class="sxs-lookup"><span data-stu-id="fd19b-113">An example for the latter case is when an exception is thrown in the middle of streaming response content; in that case it is too late to send a new response message since the status code, headers, and partial content have already gone across the wire, so we simply abort the connection.</span></span> <span data-ttu-id="fd19b-114">Anche se l'eccezione non gestita per produrre un nuovo messaggio di risposta, è ancora supportato registrazione dell'eccezione.</span><span class="sxs-lookup"><span data-stu-id="fd19b-114">Even though the exception can't be handled to produce a new response message, we still support logging the exception.</span></span> <span data-ttu-id="fd19b-115">Nei casi in cui è possibile rilevare un errore, è possibile restituire una risposta di errore appropriato come illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="fd19b-115">In cases where we can detect an error, we can return an appropriate error response as shown in the following:</span></span>

[!code-csharp[Main](web-api-global-error-handling/samples/sample1.cs?highlight=6)]

### <a name="existing-options"></a><span data-ttu-id="fd19b-116">Opzioni esistenti</span><span class="sxs-lookup"><span data-stu-id="fd19b-116">Existing Options</span></span>

<span data-ttu-id="fd19b-117">Oltre a [filtri eccezioni](exception-handling.md), [gestori di messaggi](../advanced/http-message-handlers.md) attualmente utilizzati in modo da osservare tutte le risposte di livello 500, ma dispongono di un contesto relative all'errore originale è difficile agire su tali risposte.</span><span class="sxs-lookup"><span data-stu-id="fd19b-117">In addition to [exception filters](exception-handling.md), [message handlers](../advanced/http-message-handlers.md) can be used today to observe all 500-level responses, but acting on those responses is difficult, as they lack context about the original error.</span></span> <span data-ttu-id="fd19b-118">Gestori di messaggi sono anche alcune delle stesse limitazioni di filtri di eccezioni per i casi che possono gestire. Mentre API Web dell'infrastruttura di traccia che acquisisce le condizioni di errore dell'infrastruttura di analisi per scopi di diagnostica e non è progettato per adatto per l'esecuzione in ambienti di produzione.</span><span class="sxs-lookup"><span data-stu-id="fd19b-118">Message handlers also have some of the same limitations as exception filters regarding the cases they can handle.While Web API does have tracing infrastructure that captures error conditions the tracing infrastructure is for diagnostics purposes and is not designed or suited for running in production environments.</span></span> <span data-ttu-id="fd19b-119">Globale di registrazione e gestione delle eccezioni deve essere servizi che possono eseguire durante la produzione e di essere inseriti in soluzioni di monitoraggio esistente (ad esempio, [ELMAH](https://code.google.com/p/elmah/) ).</span><span class="sxs-lookup"><span data-stu-id="fd19b-119">Global exception handling and logging should be services that can run during production and be plugged into existing monitoring solutions (for example, [ELMAH](https://code.google.com/p/elmah/) ).</span></span>

### <a name="solution-overview"></a><span data-ttu-id="fd19b-120">Panoramica della soluzione</span><span class="sxs-lookup"><span data-stu-id="fd19b-120">Solution Overview</span></span>

 <span data-ttu-id="fd19b-121">Offriamo due nuovi servizi sostituibile, [IExceptionLogger](../releases/whats-new-in-aspnet-web-api-21.md) e IExceptionHandler, per accedere e gestire le eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="fd19b-121">We provide two new user-replaceable services, [IExceptionLogger](../releases/whats-new-in-aspnet-web-api-21.md) and IExceptionHandler, to log and handle unhandled exceptions.</span></span> <span data-ttu-id="fd19b-122">I servizi sono molto simili, con due importanti differenze:</span><span class="sxs-lookup"><span data-stu-id="fd19b-122">The services are very similar, with two main differences:</span></span>

1. <span data-ttu-id="fd19b-123">Sono supportati la registrazione, ma solo un solo gestore di eccezioni più logger di eccezioni.</span><span class="sxs-lookup"><span data-stu-id="fd19b-123">We support registering multiple exception loggers but only a single exception handler.</span></span>
2. <span data-ttu-id="fd19b-124">Logger di eccezioni sempre chiamato, anche se si sta per interrompere la connessione.</span><span class="sxs-lookup"><span data-stu-id="fd19b-124">Exception loggers always get called, even if we're about to abort the connection.</span></span> <span data-ttu-id="fd19b-125">Gestori di eccezioni ottengano chiamati solo quando si è ancora in grado di scegliere quale messaggio di risposta da inviare.</span><span class="sxs-lookup"><span data-stu-id="fd19b-125">Exception handlers only get called when we're still able to choose which response message to send.</span></span>

<span data-ttu-id="fd19b-126">Entrambi i servizi forniscono l'accesso a un contesto di eccezione che contiene informazioni rilevanti dal punto in cui è stata rilevata l'eccezione, in particolare il [HttpRequestMessage](https://msdn.microsoft.com/en-us/library/system.net.http.httprequestmessage(v=vs.110).aspx), [HttpRequestContext](https://msdn.microsoft.com/en-us/library/system.web.http.controllers.httprequestcontext(v=vs.118).aspx), generata un'eccezione e l'origine dell'eccezione (dettagli riportati di seguito).</span><span class="sxs-lookup"><span data-stu-id="fd19b-126">Both services provide access to an exception context containing relevant information from the point where the exception was detected, particularly the [HttpRequestMessage](https://msdn.microsoft.com/en-us/library/system.net.http.httprequestmessage(v=vs.110).aspx), the [HttpRequestContext](https://msdn.microsoft.com/en-us/library/system.web.http.controllers.httprequestcontext(v=vs.118).aspx), the thrown exception and the exception source (details below).</span></span>

### <a name="design-principles"></a><span data-ttu-id="fd19b-127">Principi di progettazione</span><span class="sxs-lookup"><span data-stu-id="fd19b-127">Design Principles</span></span>

1. <span data-ttu-id="fd19b-128">**Nessuna modifica di rilievo** perché questa funzionalità viene aggiunto in una versione secondaria, tale vincolo importante conseguenze per la soluzione è non esserci Nessuna modifica di rilievo al tipo di contratto o comportamento.</span><span class="sxs-lookup"><span data-stu-id="fd19b-128">**No breaking changes** Because this functionality is being added in a minor release, one important constraint impacting the solution is that there be no breaking changes, either to type contracts or to behavior.</span></span> <span data-ttu-id="fd19b-129">Questo vincolo è esclusa da una certa pulitura, desideriamo eseguita in termini di blocchi catch esistenti attivare eccezioni in 500 risposte.</span><span class="sxs-lookup"><span data-stu-id="fd19b-129">This constraint ruled out some cleanup we would like to have done in terms of existing catch blocks turning exceptions into 500 responses.</span></span> <span data-ttu-id="fd19b-130">Le operazioni di pulizia è un elemento che è possibile prendere in considerazione per una successiva versione principale.</span><span class="sxs-lookup"><span data-stu-id="fd19b-130">This additional cleanup is something we might consider for a subsequent major release.</span></span> <span data-ttu-id="fd19b-131">Se questo è importante, votare in [voce utente ASP.NET Web API](http://aspnet.uservoice.com/forums/147201-asp-net-web-api/suggestions/5451321-add-flag-to-enable-iexceptionlogger-and-iexception).</span><span class="sxs-lookup"><span data-stu-id="fd19b-131">If this is important to you please vote on it at [ASP.NET Web API user voice](http://aspnet.uservoice.com/forums/147201-asp-net-web-api/suggestions/5451321-add-flag-to-enable-iexceptionlogger-and-iexception).</span></span>
2. <span data-ttu-id="fd19b-132">**Mantenere la coerenza con l'API Web costruisce** pipeline filtro dell'API Web è un ottimo modo per gestire i problemi di montaggio incrociato con la flessibilità di applicare la logica di un ambito di azione specifici, specifici dei controller o globale.</span><span class="sxs-lookup"><span data-stu-id="fd19b-132">**Maintaining consistency with Web API constructs** Web API's filter pipeline is a great way to handle cross-cutting concerns with the flexibility of applying the logic at an action-specific, controller-specific or global scope.</span></span> <span data-ttu-id="fd19b-133">I filtri, inclusi i filtri di eccezione, sono sempre contesti di azione e il controller, anche quando registrato nell'ambito globale.</span><span class="sxs-lookup"><span data-stu-id="fd19b-133">Filters, including exception filters, always have action and controller contexts, even when registered at the global scope.</span></span> <span data-ttu-id="fd19b-134">Esiste che contratto più utile per i filtri, ma significa che i filtri eccezioni, di conseguenza anche a livello globale con ambito non sono più adatta per alcune eccezioni casi, ad esempio eccezioni da gestori di messaggi, in cui nessun contesto di azione o controller.</span><span class="sxs-lookup"><span data-stu-id="fd19b-134">That contract makes sense for filters, but it means that exception filters, even globally scoped ones, aren't a good fit for some exception handling cases, such as exceptions from message handlers, where no action or controller context exists.</span></span> <span data-ttu-id="fd19b-135">Se si desidera utilizzare l'ambito flessibile offerto dai filtri per la gestione delle eccezioni, è necessario comunque i filtri eccezioni.</span><span class="sxs-lookup"><span data-stu-id="fd19b-135">If we want to use the flexible scoping afforded by filters for exception handling, we still need exception filters.</span></span> <span data-ttu-id="fd19b-136">Ma se è necessario gestire l'eccezione all'esterno di un contesto del controller, è anche necessario distinto per la gestione degli errori globale completo (qualcosa senza i vincoli di contesto azione e contesto del controller).</span><span class="sxs-lookup"><span data-stu-id="fd19b-136">But if we need to handle exception outside of a controller context, we also need a separate construct for full global error handling (something without the controller context and action context constraints).</span></span>

### <a name="when-to-use"></a><span data-ttu-id="fd19b-137">Quando utilizzare</span><span class="sxs-lookup"><span data-stu-id="fd19b-137">When to Use</span></span>

- <span data-ttu-id="fd19b-138">Logger di eccezioni sono la soluzione per visualizzare tutti eccezione non gestita rilevata dall'API Web.</span><span class="sxs-lookup"><span data-stu-id="fd19b-138">Exception loggers are the solution to seeing all unhandled exception caught by Web API.</span></span>
- <span data-ttu-id="fd19b-139">Gestori di eccezioni sono la soluzione per la personalizzazione di tutte le risposte possibili alle eccezioni non gestite rilevate dall'API Web.</span><span class="sxs-lookup"><span data-stu-id="fd19b-139">Exception handlers are the solution for customizing all possible responses to unhandled exceptions caught by Web API.</span></span>
- <span data-ttu-id="fd19b-140">I filtri eccezioni sono la soluzione più semplice per elaborare le eccezioni di subset non gestita correlate a una determinata azione o controller.</span><span class="sxs-lookup"><span data-stu-id="fd19b-140">Exception filters are the easiest solution for processing the subset unhandled exceptions related to a specific action or controller.</span></span>

### <a name="service-details"></a><span data-ttu-id="fd19b-141">Dettagli servizio</span><span class="sxs-lookup"><span data-stu-id="fd19b-141">Service Details</span></span>

 <span data-ttu-id="fd19b-142">Le interfacce del servizio logger e il gestore di eccezioni sono i metodi async semplice che i rispettivi contesti:</span><span class="sxs-lookup"><span data-stu-id="fd19b-142">The exception logger and handler service interfaces are simple async methods taking the respective contexts:</span></span> 

[!code-csharp[Main](web-api-global-error-handling/samples/sample2.cs)]

 <span data-ttu-id="fd19b-143">È possibile utilizzare le classi di base per entrambe le interfacce.</span><span class="sxs-lookup"><span data-stu-id="fd19b-143">We also provide base classes for both of these interfaces.</span></span> <span data-ttu-id="fd19b-144">L'override dei metodi di base (sincronizzazione o asincrono) è tutto ciò che è necessario registrare o gestire consigliata volte.</span><span class="sxs-lookup"><span data-stu-id="fd19b-144">Overriding the core (sync or async) methods is all that is required to log or handle at the recommended times.</span></span> <span data-ttu-id="fd19b-145">Per la registrazione di `ExceptionLogger` classe di base garantisce che il metodo di registrazione di componenti di base chiamato solo una volta per ogni eccezione (anche se in un secondo momento propaga ulteriormente nello stack e viene intercettata nuovamente).</span><span class="sxs-lookup"><span data-stu-id="fd19b-145">For logging, the `ExceptionLogger` base class will ensure that the core logging method is only called once for each exception (even if it later propagates further up the call stack and is caught again).</span></span> <span data-ttu-id="fd19b-146">La `ExceptionHandler` classe base chiamerà il principale metodo di gestione solo per i blocchi catch eccezioni nella parte superiore dello stack di chiamate, ignorando legacy annidati.</span><span class="sxs-lookup"><span data-stu-id="fd19b-146">The `ExceptionHandler` base class will call the core handling method only for exceptions at the top of the call stack, ignoring legacy nested catch blocks.</span></span> <span data-ttu-id="fd19b-147">(Versioni semplificate di queste classi di base sono nell'Appendice riportata di seguito). Entrambi `IExceptionLogger` e `IExceptionHandler` ricevere informazioni sull'eccezione tramite un `ExceptionContext`.</span><span class="sxs-lookup"><span data-stu-id="fd19b-147">(Simplified versions of these base classes are in the appendix below.) Both `IExceptionLogger` and `IExceptionHandler` receive information about the exception via an `ExceptionContext`.</span></span>

[!code-csharp[Main](web-api-global-error-handling/samples/sample3.cs)]

<span data-ttu-id="fd19b-148">Quando il framework chiama un logger di eccezioni o un gestore di eccezioni, sempre fornirà un `Exception` e `Request`.</span><span class="sxs-lookup"><span data-stu-id="fd19b-148">When the framework calls an exception logger or an exception handler, it will always provide an `Exception` and a `Request`.</span></span> <span data-ttu-id="fd19b-149">Ad eccezione di unit test, verrà sempre anche fornire un `RequestContext`.</span><span class="sxs-lookup"><span data-stu-id="fd19b-149">Except for unit testing, it will also always provide a `RequestContext`.</span></span> <span data-ttu-id="fd19b-150">Raramente fornirà un `ControllerContext` e `ActionContext` (solo quando si chiama il blocco catch per i filtri eccezioni).</span><span class="sxs-lookup"><span data-stu-id="fd19b-150">It will rarely provide a `ControllerContext` and `ActionContext` (only when calling from the catch block for exception filters).</span></span> <span data-ttu-id="fd19b-151">Raramente fornirà un `Response`(solo in determinati casi IIS quando all'interno di un tentativo di scrivere la risposta).</span><span class="sxs-lookup"><span data-stu-id="fd19b-151">It will very rarely provide a `Response`(only in certain IIS cases when in the middle of trying to write the response).</span></span> <span data-ttu-id="fd19b-152">Si noti che, poiché alcune di queste proprietà possono essere `null` spetta al consumer di verificare la presenza di `null` prima di accedere ai membri della classe di eccezione.`CatchBlock`</span><span class="sxs-lookup"><span data-stu-id="fd19b-152">Note that because some of these properties may be `null` it is up to the consumer to check for `null` before accessing members of the exception class.`CatchBlock`</span></span> <span data-ttu-id="fd19b-153">una stringa che indica il blocco catch visto l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="fd19b-153">is a string indicating which catch block saw the exception.</span></span> <span data-ttu-id="fd19b-154">Di seguito sono riportate le stringhe del blocco catch:</span><span class="sxs-lookup"><span data-stu-id="fd19b-154">The catch block strings are as follows:</span></span>

- <span data-ttu-id="fd19b-155">Server HTTP (SendAsync metodo)</span><span class="sxs-lookup"><span data-stu-id="fd19b-155">HttpServer (SendAsync method)</span></span>
- <span data-ttu-id="fd19b-156">HttpControllerDispatcher. (SendAsync metodo di)</span><span class="sxs-lookup"><span data-stu-id="fd19b-156">HttpControllerDispatcher (SendAsync method)</span></span>
- <span data-ttu-id="fd19b-157">HttpBatchHandler. (SendAsync metodo di)</span><span class="sxs-lookup"><span data-stu-id="fd19b-157">HttpBatchHandler (SendAsync method)</span></span>
- <span data-ttu-id="fd19b-158">IExceptionFilter (elaborazione dell'ApiController della pipeline filtro di eccezione in ExecuteAsync)</span><span class="sxs-lookup"><span data-stu-id="fd19b-158">IExceptionFilter (ApiController's processing of the exception filter pipeline in ExecuteAsync)</span></span>
- <span data-ttu-id="fd19b-159">Host OWIN:</span><span class="sxs-lookup"><span data-stu-id="fd19b-159">OWIN host:</span></span>

    - <span data-ttu-id="fd19b-160">HttpMessageHandlerAdapter.BufferResponseContentAsync (per la memorizzazione nel buffer di output)</span><span class="sxs-lookup"><span data-stu-id="fd19b-160">HttpMessageHandlerAdapter.BufferResponseContentAsync (for buffering output)</span></span>
    - <span data-ttu-id="fd19b-161">HttpMessageHandlerAdapter.CopyResponseContentAsync (per i flussi di output)</span><span class="sxs-lookup"><span data-stu-id="fd19b-161">HttpMessageHandlerAdapter.CopyResponseContentAsync (for streaming output)</span></span>
- <span data-ttu-id="fd19b-162">Host Web:</span><span class="sxs-lookup"><span data-stu-id="fd19b-162">Web host:</span></span>

    - <span data-ttu-id="fd19b-163">HttpControllerHandler.WriteBufferedResponseContentAsync (per la memorizzazione nel buffer di output)</span><span class="sxs-lookup"><span data-stu-id="fd19b-163">HttpControllerHandler.WriteBufferedResponseContentAsync (for buffering output)</span></span>
    - <span data-ttu-id="fd19b-164">HttpControllerHandler.WriteStreamedResponseContentAsync (per i flussi di output)</span><span class="sxs-lookup"><span data-stu-id="fd19b-164">HttpControllerHandler.WriteStreamedResponseContentAsync (for streaming output)</span></span>
    - <span data-ttu-id="fd19b-165">HttpControllerHandler.WriteErrorResponseContentAsync (per gli errori di recupero da errori in modalità di output memorizzato nel buffer)</span><span class="sxs-lookup"><span data-stu-id="fd19b-165">HttpControllerHandler.WriteErrorResponseContentAsync (for failures in error recovery under buffered output mode)</span></span>

<span data-ttu-id="fd19b-166">L'elenco di stringhe di blocco catch è anche disponibile tramite la proprietà statico di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="fd19b-166">The list of catch block strings is also available via static readonly properties.</span></span> <span data-ttu-id="fd19b-167">(La stringa del blocco catch core presenti il ExceptionCatchBlocks statico, la parte rimanente vengono visualizzati in una classe statica ogni per OWIN e web host).`IsTopLevelCatchBlock`</span><span class="sxs-lookup"><span data-stu-id="fd19b-167">(The core catch block string are on the static ExceptionCatchBlocks; the remainder appear on one static class each for OWIN and web host).`IsTopLevelCatchBlock`</span></span> <span data-ttu-id="fd19b-168">è utile per il modello consigliato di gestione delle eccezioni solo nella parte superiore dello stack di chiamate seguente.</span><span class="sxs-lookup"><span data-stu-id="fd19b-168">is helpful for following the recommended pattern of handling exceptions only at the top of the call stack.</span></span> <span data-ttu-id="fd19b-169">Anziché attivare eccezioni in 500 risposte ovunque che si verifica un blocco catch nidificato, un gestore di eccezioni può consentire eccezioni propagazione fino a quando non sono in corso di essere visualizzato dall'host.</span><span class="sxs-lookup"><span data-stu-id="fd19b-169">Rather than turning exceptions into 500 responses anywhere a nested catch block occurs, an exception handler can let exceptions propagate until they are about to be seen by the host.</span></span>

<span data-ttu-id="fd19b-170">Oltre al `ExceptionContext`, una parte di più di informazioni tramite la versione completa di Ottiene un logger `ExceptionLoggerContext`:</span><span class="sxs-lookup"><span data-stu-id="fd19b-170">In addition to the `ExceptionContext`, a logger gets one more piece of information via the full `ExceptionLoggerContext`:</span></span>

[!code-csharp[Main](web-api-global-error-handling/samples/sample4.cs)]

<span data-ttu-id="fd19b-171">La seconda proprietà, `CanBeHandled`, consente a un logger identificare un'eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="fd19b-171">The second property, `CanBeHandled`, allows a logger to identify an exception that cannot be handled.</span></span> <span data-ttu-id="fd19b-172">Quando la connessione sta per essere interrotta e nessun nuovo messaggio di risposta può essere inviato, verrà chiamato il logger ma il gestore verrà ***non*** chiamato, e i logger possono identificare questo scenario da questa proprietà.</span><span class="sxs-lookup"><span data-stu-id="fd19b-172">When the connection is about to be aborted and no new response message can be sent, the loggers will be called but the handler will ***not*** be called, and the loggers can identify this scenario from this property.</span></span>

<span data-ttu-id="fd19b-173">In aggiuntive per il `ExceptionContext`, un gestore ottiene una proprietà di altre può essere impostata in completa `ExceptionHandlerContext` per gestire l'eccezione:</span><span class="sxs-lookup"><span data-stu-id="fd19b-173">In additional to the `ExceptionContext`, a handler gets one more property it can set on the full `ExceptionHandlerContext` to handle the exception:</span></span>

[!code-csharp[Main](web-api-global-error-handling/samples/sample5.cs)]

<span data-ttu-id="fd19b-174">Un gestore di eccezioni indica che è gestita un'eccezione impostando il `Result` proprietà al risultato di un'azione (ad esempio, un [ExceptionResult](https://msdn.microsoft.com/en-us/library/system.web.http.results.exceptionresult(v=vs.118).aspx), [InternalServerErrorResult](https://msdn.microsoft.com/en-us/library/system.web.http.results.internalservererrorresult(v=vs.118).aspx), [ StatusCodeResult](https://msdn.microsoft.com/en-us/library/system.web.http.results.statuscoderesult(v=vs.118).aspx), o un risultato personalizzato).</span><span class="sxs-lookup"><span data-stu-id="fd19b-174">An exception handler indicates that it has handled an exception by setting the `Result` property to an action result (for example, an [ExceptionResult](https://msdn.microsoft.com/en-us/library/system.web.http.results.exceptionresult(v=vs.118).aspx), [InternalServerErrorResult](https://msdn.microsoft.com/en-us/library/system.web.http.results.internalservererrorresult(v=vs.118).aspx), [StatusCodeResult](https://msdn.microsoft.com/en-us/library/system.web.http.results.statuscoderesult(v=vs.118).aspx), or a custom result).</span></span> <span data-ttu-id="fd19b-175">Se il `Result` proprietà è null, viene gestita l'eccezione e verrà generata l'eccezione originale.</span><span class="sxs-lookup"><span data-stu-id="fd19b-175">If the `Result` property is null, the exception is unhandled and the original exception will be re-thrown.</span></span>

<span data-ttu-id="fd19b-176">Per le eccezioni nella parte superiore dello stack di chiamate, è richiesto un passaggio aggiuntivo per assicurare che la risposta è appropriata per i chiamanti di API.</span><span class="sxs-lookup"><span data-stu-id="fd19b-176">For exceptions at the top of the call stack, we took an extra step to ensure the response is appropriate for API callers.</span></span> <span data-ttu-id="fd19b-177">Se l'eccezione si propaga fino a host, il chiamante verrà visualizzata la schermata gialla di morte o un altro host fornito in risposta a cui è in genere in formato HTML e non viene in genere una risposta di errore API appropriata.</span><span class="sxs-lookup"><span data-stu-id="fd19b-177">If the exception propagates up to the host, the caller would see the yellow screen of death or some other host provided response which is typically HTML and not usually an appropriate API error response.</span></span> <span data-ttu-id="fd19b-178">In questi casi, eseguire il backup viene avviato il risultato non null e solo se un gestore di eccezioni personalizzato imposta in modo esplicito per `null` (non gestite) l'eccezione propagherà all'host.</span><span class="sxs-lookup"><span data-stu-id="fd19b-178">In these cases, the Result starts out non-null, and only if a custom exception handler explicitly sets it back to `null` (unhandled) will the exception propagate to the host.</span></span> <span data-ttu-id="fd19b-179">Impostazione `Result` a `null` in tali casi, può essere utile per due scenari:</span><span class="sxs-lookup"><span data-stu-id="fd19b-179">Setting `Result` to `null` in such cases can be useful for two scenarios:</span></span>

1. <span data-ttu-id="fd19b-180">OWIN ospitato API Web con middleware registrato prima/esterno API Web di gestione delle eccezioni personalizzata.</span><span class="sxs-lookup"><span data-stu-id="fd19b-180">OWIN hosted Web API with custom exception handling middleware registered before/outside Web API.</span></span>
2. <span data-ttu-id="fd19b-181">Il debug locale tramite un browser, dove il gialla schermata è effettivamente una risposta utile per un'eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="fd19b-181">Local debugging via a browser, where the yellow screen of death is actually a helpful response for an unhandled exception.</span></span>

<span data-ttu-id="fd19b-182">Per logger di eccezioni e gestori di eccezioni, non eseguire alcuna operazione per il ripristino se il logger o stesso gestore genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="fd19b-182">For both exception loggers and exception handlers, we don't do anything to recover if the logger or handler itself throws an exception.</span></span> <span data-ttu-id="fd19b-183">(Diverso da lasciare che l'eccezione di propagazione, lasciare commenti e suggerimenti nella parte inferiore della pagina, se si dispone di un approccio migliore.) Il contratto per gestori e i logger di eccezioni è che essi deve non consentire eccezioni propagazione fino a relativi chiamanti; in caso contrario, l'eccezione solo propagherà, spesso a tutti gli altri host generando un errore HTML (ad esempio, ASP. Schermata di giallo di NET) inviati al client (che in genere non rappresenta l'opzione preferita per i chiamanti di API che prevedono JSON o XML).</span><span class="sxs-lookup"><span data-stu-id="fd19b-183">(Other than letting the exception propagate, leave feedback at the bottom of this page if you have a better approach.) The contract for exception loggers and handlers is that they should not let exceptions propagate up to their callers; otherwise, the exception will just propagate, often all the way to the host resulting in an HTML error (like the ASP.NET's yellow screen) being sent back to the client (which usually isn't the preferred option for API callers that expect JSON or XML).</span></span>

## <a name="examples"></a><span data-ttu-id="fd19b-184">Esempi</span><span class="sxs-lookup"><span data-stu-id="fd19b-184">Examples</span></span>

### <a name="tracing-exception-logger"></a><span data-ttu-id="fd19b-185">Logger di eccezioni di traccia</span><span class="sxs-lookup"><span data-stu-id="fd19b-185">Tracing Exception Logger</span></span>

<span data-ttu-id="fd19b-186">Logger di eccezioni di sotto di inviare i dati di eccezione per le origini di traccia configurate (inclusa la finestra di output di Debug in Visual Studio).</span><span class="sxs-lookup"><span data-stu-id="fd19b-186">The exception logger below send exception data to configured Trace sources (including the Debug output window in Visual Studio).</span></span>

[!code-csharp[Main](web-api-global-error-handling/samples/sample6.cs)]

### <a name="custom-error-message-exception-handler"></a><span data-ttu-id="fd19b-187">Gestore di eccezioni di messaggio di errore personalizzato</span><span class="sxs-lookup"><span data-stu-id="fd19b-187">Custom Error Message Exception Handler</span></span>

<span data-ttu-id="fd19b-188">Nell'esempio seguente produce una risposta di errore personalizzati ai client, tra cui un indirizzo di posta elettronica per contattare il supporto tecnico.</span><span class="sxs-lookup"><span data-stu-id="fd19b-188">The following below produces a custom error response to clients, including an email address for contacting support.</span></span>

[!code-csharp[Main](web-api-global-error-handling/samples/sample7.cs)]

## <a name="registering-exception-filters"></a><span data-ttu-id="fd19b-189">Registrazione di filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="fd19b-189">Registering Exception Filters</span></span>

<span data-ttu-id="fd19b-190">Se si utilizza il modello di progetto "Applicazione Web di ASP.NET MVC 4" per creare il progetto, inserire il codice di configurazione Web API all'interno di `WebApiConfig` nella classe di *avvia* cartella:</span><span class="sxs-lookup"><span data-stu-id="fd19b-190">If you use the "ASP.NET MVC 4 Web Application" project template to create your project, put your Web API configuration code inside the `WebApiConfig` class, in the *App/_Start* folder:</span></span>

[!code-csharp[Main](exception-handling/samples/sample7.cs?highlight=5)]

## <a name="appendix-base-class-details"></a><span data-ttu-id="fd19b-191">Appendice: Classe di Base dettagli</span><span class="sxs-lookup"><span data-stu-id="fd19b-191">Appendix: Base Class Details</span></span>

[!code-csharp[Main](web-api-global-error-handling/samples/sample8.cs)]