---
title: Hosting e distribuzione di ASP.NET Core
author: tdykstra
description: Informazioni sulla configurazione degli ambienti host e sulla distribuzione delle app ASP.NET Core.
ms.author: riande
manager: wpickett
ms.custom: mvc
ms.date: 08/07/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: host-and-deploy/index
ms.openlocfilehash: 6ce77922dd8a0fcb81ea6a72f9179c9c81105dda
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="e8b8b-103">Hosting e distribuzione di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e8b8b-103">Host and deploy ASP.NET Core</span></span>

<span data-ttu-id="e8b8b-104">In generale, per distribuire un'app ASP.NET Core in un ambiente host:</span><span class="sxs-lookup"><span data-stu-id="e8b8b-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="e8b8b-105">Pubblicare l'app in una cartella nel server host.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-105">Publish the app to a folder on the hosting server.</span></span>
* <span data-ttu-id="e8b8b-106">Configurare un gestore processi che avvia l'app quando arrivano richieste e la riavvia quando si blocca o quando il server viene riavviato.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="e8b8b-107">Configurare un proxy inverso che inoltra le richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-107">Set up a reverse proxy that forwards requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="e8b8b-108">Pubblicare in una cartella</span><span class="sxs-lookup"><span data-stu-id="e8b8b-108">Publish to a folder</span></span> 

<span data-ttu-id="e8b8b-109">Il comando CLI [dotnet publish](/dotnet/articles/core/tools/dotnet-publish) compila il codice dell'app e copia i file necessari per eseguire l'app in una cartella *publish*.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-109">The [dotnet publish](/dotnet/articles/core/tools/dotnet-publish) CLI command compiles app code and copies the files needed to run the app into a *publish* folder.</span></span> <span data-ttu-id="e8b8b-110">Quando si esegue la distribuzione da Visual Studio, il passaggio `dotnet publish` viene eseguito automaticamente prima della copia dei file nella destinazione di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-110">When deploying from Visual Studio, the `dotnet publish` step happens automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="e8b8b-111">Contenuto della cartella</span><span class="sxs-lookup"><span data-stu-id="e8b8b-111">Folder contents</span></span>

<span data-ttu-id="e8b8b-112">La cartella *publish* contiene i file dell'app con estensione *exe* e *dll*, le relative dipendenze e facoltativamente il runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-112">The *publish* folder contains *.exe* and *.dll* files for the app, its dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="e8b8b-113">È possibile pubblicare un'app .NET Core come app *indipendente* o come app *dipendente dal framework*.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-113">A .NET Core app can be published as *self-contained* or *framework-dependent* app.</span></span> <span data-ttu-id="e8b8b-114">Se l'app è indipendente i file con estensione *DLL* che contengono il runtime .NET sono inclusi nella cartella *publish*.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-114">If the app is self-contained, the *.dll* files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="e8b8b-115">Se l'app è dipendente dal framework i file di runtime .NET non sono inclusi, perché l'app contiene un riferimento a una versione di .NET installata nel server.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-115">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that is installed on the server.</span></span> <span data-ttu-id="e8b8b-116">Il modello di distribuzione predefinito è il modello dipendente dal framework.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-116">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="e8b8b-117">Per altre informazioni, vedere [Distribuzione di applicazioni .NET Core](/dotnet/articles/core/deploying/index).</span><span class="sxs-lookup"><span data-stu-id="e8b8b-117">For more information, see [.NET Core application deployment](/dotnet/articles/core/deploying/index).</span></span>

<span data-ttu-id="e8b8b-118">Oltre ai file con estensione *EXE* e *DLL* la cartella *publish* di un'app ASP.NET Core contiene in genere i file di configurazione, gli asset statici e le visualizzazioni MVC.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-118">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="e8b8b-119">Per altre informazioni, vedere [Directory structure](xref:host-and-deploy/directory-structure) (Struttura della directory).</span><span class="sxs-lookup"><span data-stu-id="e8b8b-119">For more information, see [Directory structure](xref:host-and-deploy/directory-structure).</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="e8b8b-120">Configurare un gestore processi</span><span class="sxs-lookup"><span data-stu-id="e8b8b-120">Set up a process manager</span></span>

<span data-ttu-id="e8b8b-121">Un'app ASP.NET Core è un'app console che deve essere avviata all'avvio di un server e riavviata in caso di arresto anomalo del server stesso.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-121">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="e8b8b-122">Per automatizzare le operazioni di avvio e riavvio, deve essere presente un gestore processi.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-122">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="e8b8b-123">I gestori processi più comuni per ASP.NET Core sono:</span><span class="sxs-lookup"><span data-stu-id="e8b8b-123">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="e8b8b-124">Linux</span><span class="sxs-lookup"><span data-stu-id="e8b8b-124">Linux</span></span>
  * [<span data-ttu-id="e8b8b-125">Nginx</span><span class="sxs-lookup"><span data-stu-id="e8b8b-125">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="e8b8b-126">Apache</span><span class="sxs-lookup"><span data-stu-id="e8b8b-126">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="e8b8b-127">WINDOWS</span><span class="sxs-lookup"><span data-stu-id="e8b8b-127">Windows</span></span>
  * [<span data-ttu-id="e8b8b-128">IIS</span><span class="sxs-lookup"><span data-stu-id="e8b8b-128">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="e8b8b-129">Servizio Windows</span><span class="sxs-lookup"><span data-stu-id="e8b8b-129">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="e8b8b-130">Configurare un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="e8b8b-130">Set up a reverse proxy</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8b8b-131">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8b8b-131">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e8b8b-132">Se l'app usa il servizio Web [Kestrel](xref:fundamentals/servers/kestrel) è possibile usare come server proxy inverso [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="e8b8b-132">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) web server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="e8b8b-133">Un server proxy inverso riceve le richieste HTTP da Internet e le inoltra a Kestrel dopo alcune operazioni di gestione preliminari.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-133">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span> <span data-ttu-id="e8b8b-134">Per altre informazioni, vedere [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#when-to-use-kestrel-with-a-reverse-proxy) (Quando usare Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="e8b8b-134">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8b8b-135">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8b8b-135">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e8b8b-136">Se l'app usa il server Web [Kestrel](xref:fundamentals/servers/kestrel) ed è esposta a Internet, usare [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index) come server proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-136">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) web server and will be exposed to the Internet, use [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) as a reverse proxy server.</span></span> <span data-ttu-id="e8b8b-137">Un server proxy inverso riceve le richieste HTTP da Internet e le inoltra a Kestrel dopo alcune operazioni di gestione preliminari.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-137">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span> <span data-ttu-id="e8b8b-138">Il motivo principale per l'uso di un proxy inverso è la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-138">The main reason for using a reverse proxy is security.</span></span> <span data-ttu-id="e8b8b-139">Per altre informazioni, vedere [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel?tabs=aspnetcore1x#when-to-use-kestrel-with-a-reverse-proxy) (Quando usare Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="e8b8b-139">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel?tabs=aspnetcore1x#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

---

## <a name="using-visual-studio-and-msbuild-to-automate-deployment"></a><span data-ttu-id="e8b8b-140">Uso di Visual Studio e MSBuild per automatizzare la distribuzione</span><span class="sxs-lookup"><span data-stu-id="e8b8b-140">Using Visual Studio and MSBuild to automate deployment</span></span>

<span data-ttu-id="e8b8b-141">In molti casi la distribuzione richiede attività aggiuntive oltre alla copia dell'output da `dotnet publish` a un server.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-141">Deployment often requires additional tasks besides copying the output from `dotnet publish` to a server.</span></span> <span data-ttu-id="e8b8b-142">Ad esempio è possibile che nella cartella *publish* siano necessari file aggiuntivi o vengano esclusi uno o più file.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-142">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="e8b8b-143">Per la distribuzione Web, Visual Studio usa MSBuild, che può essere personalizzato per eseguire molte altre attività durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-143">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="e8b8b-144">Per altre informazioni, vedere [Publish profiles in Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles) (Pubblicare profili in Visual Studio) e il libro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Uso di MSBuild e Team Foundation Build).</span><span class="sxs-lookup"><span data-stu-id="e8b8b-144">For more information, see [Publish profiles in Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles) and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="e8b8b-145">Mediante la [funzionalità Pubblica sito Web](xref:tutorials/publish-to-azure-webapp-using-vs) o il [supporto di Git incorporato](xref:host-and-deploy/azure-apps/azure-continuous-deployment) è possibile eseguire direttamente la distribuzione di app da Visual Studio al Servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="e8b8b-145">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="e8b8b-146">Visual Studio Team Services supporta la [distribuzione continua al Servizio app di Azure](/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?tabs=vsts).</span><span class="sxs-lookup"><span data-stu-id="e8b8b-146">Visual Studio Team Services supports [continuous deployment to Azure App Service](/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?tabs=vsts).</span></span>

## <a name="publishing-to-azure"></a><span data-ttu-id="e8b8b-147">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="e8b8b-147">Publishing to Azure</span></span>

<span data-ttu-id="e8b8b-148">Per istruzioni su come pubblicare l'app in Azure usando Visual Studio, vedere [Pubblicare un'app Web ASP.NET Core in Servizio app di Azure con Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs).</span><span class="sxs-lookup"><span data-stu-id="e8b8b-148">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="e8b8b-149">L'app può essere pubblicata in Azure anche dalla [riga di comando](xref:tutorials/publish-to-azure-webapp-using-cli).</span><span class="sxs-lookup"><span data-stu-id="e8b8b-149">The app can also be published to Azure from the [command line](xref:tutorials/publish-to-azure-webapp-using-cli).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8b8b-150">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e8b8b-150">Additional resources</span></span>

<span data-ttu-id="e8b8b-151">Per informazioni sull'uso di Docker come ambiente host, vedere [Host ASP.NET Core apps in Docker](xref:host-and-deploy/docker/index) (Ospitare app ASP.NET Core in Docker).</span><span class="sxs-lookup"><span data-stu-id="e8b8b-151">For information on using Docker as a hosting environment, see [Host ASP.NET Core apps in Docker](xref:host-and-deploy/docker/index).</span></span>