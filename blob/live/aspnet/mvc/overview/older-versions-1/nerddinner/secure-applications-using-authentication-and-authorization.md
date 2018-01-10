---
uid: mvc/overview/older-versions-1/nerddinner/secure-applications-using-authentication-and-authorization
title: Proteggere le applicazioni tramite l'autenticazione e autorizzazione | Documenti Microsoft
author: microsoft
description: Passaggio 9 viene illustrato come aggiungere l'autenticazione e autorizzazione per proteggere l'applicazione NerdDinner, in modo che gli utenti devono registrare e accedere al sito da creare...
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: 9e4d5cac-b071-440c-b044-20b6d0c964fb
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/secure-applications-using-authentication-and-authorization
msc.type: authoredcontent
ms.openlocfilehash: a23b2cf4d1728624698c0db49c25ea7efd3af67d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
<a name="secure-applications-using-authentication-and-authorization"></a><span data-ttu-id="e36be-103">Proteggere le applicazioni tramite l'autenticazione e autorizzazione</span><span class="sxs-lookup"><span data-stu-id="e36be-103">Secure Applications Using Authentication and Authorization</span></span>
====================
<span data-ttu-id="e36be-104">da [Microsoft](https://github.com/microsoft)</span><span class="sxs-lookup"><span data-stu-id="e36be-104">by [Microsoft](https://github.com/microsoft)</span></span>

[<span data-ttu-id="e36be-105">Scarica il PDF</span><span class="sxs-lookup"><span data-stu-id="e36be-105">Download PDF</span></span>](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> <span data-ttu-id="e36be-106">Si tratta di passaggio 9 di una liberazione [esercitazione applicazione "NerdDinner"](introducing-the-nerddinner-tutorial.md) che si interromperanno-through come compilare un piccolo, ma completa, un'applicazione web con ASP.NET MVC 1.</span><span class="sxs-lookup"><span data-stu-id="e36be-106">This is step 9 of a free ["NerdDinner" application tutorial](introducing-the-nerddinner-tutorial.md) that walks-through how to build a small, but complete, web application using ASP.NET MVC 1.</span></span>
> 
> <span data-ttu-id="e36be-107">Passaggio 9 viene illustrato come aggiungere l'autenticazione e autorizzazione per proteggere l'applicazione NerdDinner, in modo che gli utenti devono registrare e l'accesso al sito per creare nuovi dinners e solo l'utente che ospita una cena possibile modificarlo in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="e36be-107">Step 9 shows how to add authentication and authorization to secure our NerdDinner application, so that users need to register and login to the site to create new dinners, and only the user who is hosting a dinner can edit it later.</span></span>
> 
> <span data-ttu-id="e36be-108">Se si utilizza ASP.NET MVC 3, è consigliabile seguire il [recupero avviato con MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) o [negozio MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) esercitazioni.</span><span class="sxs-lookup"><span data-stu-id="e36be-108">If you are using ASP.NET MVC 3, we recommend you follow the [Getting Started With MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) or [MVC Music Store](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) tutorials.</span></span>


## <a name="nerddinner-step-9-authentication-and-authorization"></a><span data-ttu-id="e36be-109">NerdDinner passaggio 9: Autenticazione e autorizzazione</span><span class="sxs-lookup"><span data-stu-id="e36be-109">NerdDinner Step 9: Authentication and Authorization</span></span>

<span data-ttu-id="e36be-110">Ora il nostro NerdDinner applicazione concede chiunque visitare il sito la possibilità di creare e modificare i dettagli di qualsiasi dinner.</span><span class="sxs-lookup"><span data-stu-id="e36be-110">Right now our NerdDinner application grants anyone visiting the site the ability to create and edit the details of any dinner.</span></span> <span data-ttu-id="e36be-111">È necessario modificare questo, in modo che gli utenti devono registrare e account di accesso al sito per creare nuovi dinners e aggiungere una restrizione in modo che solo l'utente che ospita una cena possibile modificarlo in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="e36be-111">Let's change this so that users need to register and login to the site to create new dinners, and add a restriction so that only the user who is hosting a dinner can edit it later.</span></span>

<span data-ttu-id="e36be-112">A tale scopo si userà l'autenticazione e autorizzazione per proteggere l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="e36be-112">To enable this we'll use authentication and authorization to secure our application.</span></span>

### <a name="understanding-authentication-and-authorization"></a><span data-ttu-id="e36be-113">Autorizzazione e informazioni sull'autenticazione</span><span class="sxs-lookup"><span data-stu-id="e36be-113">Understanding Authentication and Authorization</span></span>

<span data-ttu-id="e36be-114">*Autenticazione* è il processo di identificare e convalidare l'identità di un client a un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="e36be-114">*Authentication* is the process of identifying and validating the identity of a client accessing an application.</span></span> <span data-ttu-id="e36be-115">In termini più semplici, risulta sull'identificazione "che l'utente finale quando visitano un sito Web".</span><span class="sxs-lookup"><span data-stu-id="e36be-115">Put more simply, it is about identifying "who" the end-user is when they visit a website.</span></span> <span data-ttu-id="e36be-116">ASP.NET supporta più modalità per autenticare gli utenti del browser.</span><span class="sxs-lookup"><span data-stu-id="e36be-116">ASP.NET supports multiple ways to authenticate browser users.</span></span> <span data-ttu-id="e36be-117">Per le applicazioni web di Internet, l'approccio più comune di autenticazione utilizzato è denominato "Autenticazione basata su form".</span><span class="sxs-lookup"><span data-stu-id="e36be-117">For Internet web applications, the most common authentication approach used is called "Forms Authentication".</span></span> <span data-ttu-id="e36be-118">Autenticazione basata su form consente agli sviluppatori di creare un form di accesso HTML all'interno di propria applicazione e quindi convalidare il nome utente/password che un utente finale invia rispetto a un database o un altro archivio delle credenziali di password.</span><span class="sxs-lookup"><span data-stu-id="e36be-118">Forms Authentication enables a developer to author an HTML login form within their application, and then validate the username/password an end-user submits against a database or other password credential store.</span></span> <span data-ttu-id="e36be-119">Se la combinazione di nome utente/password è corretta, lo sviluppatore può quindi chiedere ASP.NET per rilasciare un cookie HTTP crittografato per identificare l'utente in tutte le richieste future.</span><span class="sxs-lookup"><span data-stu-id="e36be-119">If the username/password combination is correct, the developer can then ask ASP.NET to issue an encrypted HTTP cookie to identify the user across future requests.</span></span> <span data-ttu-id="e36be-120">Ti invieremo un utilizzando l'autenticazione basata su form con l'applicazione NerdDinner.</span><span class="sxs-lookup"><span data-stu-id="e36be-120">We'll by using forms authentication with our NerdDinner application.</span></span>

<span data-ttu-id="e36be-121">*Autorizzazione* è il processo volto a determinare se un utente autenticato dispone dell'autorizzazione per accedere a un particolare URL/risorsa o di eseguire un'azione.</span><span class="sxs-lookup"><span data-stu-id="e36be-121">*Authorization* is the process of determining whether an authenticated user has permission to access a particular URL/resource or to perform some action.</span></span> <span data-ttu-id="e36be-122">Ad esempio, all'interno dell'applicazione NerdDinner si desidera autorizzare che possono accedere solo gli utenti che sono registrati il *Dinners/crea* URL e creare di nuovo Dinners.</span><span class="sxs-lookup"><span data-stu-id="e36be-122">For example, within our NerdDinner application we'll want to authorize that only users who are logged in can access the */Dinners/Create* URL and create new Dinners.</span></span> <span data-ttu-id="e36be-123">Si consiglia inoltre di aggiungere la logica di autorizzazione in modo che solo l'utente che ospita una cena può modificare, ad esempio e negare l'accesso in modifica a tutti gli altri utenti.</span><span class="sxs-lookup"><span data-stu-id="e36be-123">We'll also want to add authorization logic so that only the user who is hosting a dinner can edit it – and deny edit access to all other users.</span></span>

### <a name="forms-authentication-and-the-accountcontroller"></a><span data-ttu-id="e36be-124">Autenticazione basata su form e il AccountController</span><span class="sxs-lookup"><span data-stu-id="e36be-124">Forms Authentication and the AccountController</span></span>

<span data-ttu-id="e36be-125">Il modello di progetto di Visual Studio predefinito per ASP.NET MVC Abilita autenticazione basata su form automaticamente quando vengono create nuove applicazioni ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="e36be-125">The default Visual Studio project template for ASP.NET MVC automatically enables forms authentication when new ASP.NET MVC applications are created.</span></span> <span data-ttu-id="e36be-126">Aggiunge automaticamente anche un'implementazione di pagina di accesso account preesistente al progetto, che rende molto semplice per l'integrazione di protezione all'interno di un sito.</span><span class="sxs-lookup"><span data-stu-id="e36be-126">It also automatically adds a pre-built account login page implementation to the project – which makes it really easy to integrate security within a site.</span></span>

<span data-ttu-id="e36be-127">Pagina master predefinita Site. master consente di visualizzare un collegamento "Accedi" in alto a destra del sito quando l'utente accede a tale colonna non è autenticata:</span><span class="sxs-lookup"><span data-stu-id="e36be-127">The default Site.master master page displays a "Log On" link at the top-right of the site when the user accessing it is not authenticated:</span></span>

![](secure-applications-using-authentication-and-authorization/_static/image1.png)

<span data-ttu-id="e36be-128">Facendo clic sul collegamento "Accedi" consente a un utente di */Account/accesso* URL:</span><span class="sxs-lookup"><span data-stu-id="e36be-128">Clicking the "Log On" link takes a user to the */Account/LogOn* URL:</span></span>

![](secure-applications-using-authentication-and-authorization/_static/image2.png)

<span data-ttu-id="e36be-129">Possono farlo facendo clic sul collegamento "Register" – giungeranno per visitatori che è ancora stato registrato il */Account/Register* URL e consentire loro di immettere i dettagli dell'account:</span><span class="sxs-lookup"><span data-stu-id="e36be-129">Visitors who haven't registered can do so by clicking the "Register" link – which will take them to the */Account/Register* URL and allow them to enter account details:</span></span>

![](secure-applications-using-authentication-and-authorization/_static/image3.png)

<span data-ttu-id="e36be-130">Fare clic sul pulsante "Register" verrà creare un nuovo utente all'interno del sistema di appartenenze di ASP.NET e autenticare l'utente al sito utilizzando l'autenticazione basata su form.</span><span class="sxs-lookup"><span data-stu-id="e36be-130">Clicking the "Register" button will create a new user within the ASP.NET Membership system, and authenticate the user onto the site using forms authentication.</span></span>

<span data-ttu-id="e36be-131">Quando un utente è connesso, Site cambia alto a destra della pagina per l'output di un "pagina iniziale [username]!"</span><span class="sxs-lookup"><span data-stu-id="e36be-131">When a user is logged-in, the Site.master changes the top-right of the page to output a "Welcome [username]!"</span></span> <span data-ttu-id="e36be-132">messaggio ed esegue il rendering di un "Disconnetti" collegamento anziché un "Accedi" uno.</span><span class="sxs-lookup"><span data-stu-id="e36be-132">message and renders a "Log Off" link instead of a "Log On" one.</span></span> <span data-ttu-id="e36be-133">Facendo clic sul collegamento "Disconnetti" si disconnette l'utente:</span><span class="sxs-lookup"><span data-stu-id="e36be-133">Clicking the "Log Off" link logs out the user:</span></span>

![](secure-applications-using-authentication-and-authorization/_static/image4.png)

<span data-ttu-id="e36be-134">La funzionalità di accesso, disconnessione e la registrazione precedente viene implementata all'interno della classe AccountController che è stato aggiunto al progetto da Visual Studio, durante la creazione del progetto.</span><span class="sxs-lookup"><span data-stu-id="e36be-134">The above login, logout, and registration functionality is implemented within the AccountController class that was added to our project by Visual Studio when it created the project.</span></span> <span data-ttu-id="e36be-135">L'interfaccia utente per il AccountController viene implementata utilizzando i modelli di visualizzazione all'interno della directory \Views\Account:</span><span class="sxs-lookup"><span data-stu-id="e36be-135">The UI for the AccountController is implemented using view templates within the \Views\Account directory:</span></span>

![](secure-applications-using-authentication-and-authorization/_static/image5.png)

<span data-ttu-id="e36be-136">La classe AccountController utilizza il sistema di autenticazione basata su form ASP.NET per rilasciare i cookie di autenticazione crittografata e l'API di appartenenza ASP.NET per archiviare e convalidare i nomi utente e password.</span><span class="sxs-lookup"><span data-stu-id="e36be-136">The AccountController class uses the ASP.NET Forms Authentication system to issue encrypted authentication cookies, and the ASP.NET Membership API to store and validate usernames/passwords.</span></span> <span data-ttu-id="e36be-137">L'API di appartenenza ASP.NET è estendibile e consente a qualsiasi archivio di credenziali di password da utilizzare.</span><span class="sxs-lookup"><span data-stu-id="e36be-137">The ASP.NET Membership API is extensible and enables any password credential store to be used.</span></span> <span data-ttu-id="e36be-138">ASP.NET viene fornito con implementazioni del provider di appartenenze predefinito che archiviano nome utente e password all'interno di un database SQL o all'interno di Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e36be-138">ASP.NET ships with built-in membership provider implementations that store username/passwords within a SQL database, or within Active Directory.</span></span>

<span data-ttu-id="e36be-139">È possibile configurare il provider di appartenenze deve utilizzare l'applicazione NerdDinner aprendo il file "Web. config" nella directory principale del progetto e cercare il &lt;appartenenza&gt; sezione all'interno di esso.</span><span class="sxs-lookup"><span data-stu-id="e36be-139">We can configure which membership provider our NerdDinner application should use by opening the "web.config" file at the root of the project and looking for the &lt;membership&gt; section within it.</span></span> <span data-ttu-id="e36be-140">Registra il provider di appartenenze SQL, aggiunti quando è stato creato il progetto Web. config predefinito e lo configura per utilizzare una stringa di connessione denominata "ApplicationServices" per specificare il percorso del database.</span><span class="sxs-lookup"><span data-stu-id="e36be-140">The default web.config added when the project was created registers the SQL membership provider, and configures it to use a connection-string named "ApplicationServices" to specify the database location.</span></span>

<span data-ttu-id="e36be-141">La stringa di connessione predefinita "ApplicationServices" (specificato all'interno di &lt;connectionStrings&gt; sezione del file Web. config) è configurato per utilizzare SQL Express.</span><span class="sxs-lookup"><span data-stu-id="e36be-141">The default "ApplicationServices" connection string (which is specified within the &lt;connectionStrings&gt; section of the web.config file) is configured to use SQL Express.</span></span> <span data-ttu-id="e36be-142">Punta a un database di SQL Express denominato "ASPNETDB. MDF"dell'applicazione" App\_dati "directory.</span><span class="sxs-lookup"><span data-stu-id="e36be-142">It points to a SQL Express database named "ASPNETDB.MDF" under the application's "App\_Data" directory.</span></span> <span data-ttu-id="e36be-143">Se questo database non esiste la prima volta che viene utilizzata l'API di appartenenza all'interno dell'applicazione, ASP.NET verrà automaticamente creato il database e il provisioning lo schema del database di appartenenza appropriata all'interno di essa:</span><span class="sxs-lookup"><span data-stu-id="e36be-143">If this database doesn't exist the first time the Membership API is used within the application, ASP.NET will automatically create the database and provision the appropriate membership database schema within it:</span></span>

![](secure-applications-using-authentication-and-authorization/_static/image6.png)

<span data-ttu-id="e36be-144">Se anziché utilizzare SQL Express Desideravamo per utilizzare un'istanza completa di SQL Server (o per connettersi a un database remoto), tutti sarà necessaria attività da eseguire consiste nell'aggiornare la stringa di connessione "ApplicationServices" all'interno del file Web. config e verificare che lo schema appropriato appartenenza è stato aggiunto al database di che cui punta.</span><span class="sxs-lookup"><span data-stu-id="e36be-144">If instead of using SQL Express we wanted to use a full SQL Server instance (or connect to a remote database), all we'd need to-do is to update the "ApplicationServices" connection string within the web.config file and make sure that the appropriate membership schema has been added to the database it points at.</span></span> <span data-ttu-id="e36be-145">È possibile eseguire il "aspnet\_regsql.exe" utilità all'interno della directory \Windows\Microsoft.NET\Framework\v2.0.50727\ per aggiungere lo schema appropriato per l'appartenenza e di altri servizi delle applicazioni ASP.NET a un database.</span><span class="sxs-lookup"><span data-stu-id="e36be-145">You can run the "aspnet\_regsql.exe" utility within the \Windows\Microsoft.NET\Framework\v2.0.50727\ directory to add the appropriate schema for membership and the other ASP.NET application services to a database.</span></span>

### <a name="authorizing-the-dinnerscreate-url-using-the-authorize-filter"></a><span data-ttu-id="e36be-146">Autorizzazione URL Dinners/Create utilizzando il filtro [Authorize]</span><span class="sxs-lookup"><span data-stu-id="e36be-146">Authorizing the /Dinners/Create URL using the [Authorize] filter</span></span>

<span data-ttu-id="e36be-147">Non è necessario scrivere codice per abilitare un'implementazione di gestione di account per l'applicazione NerdDinner e l'autenticazione protetta.</span><span class="sxs-lookup"><span data-stu-id="e36be-147">We didn't have to write any code to enable a secure authentication and account management implementation for the NerdDinner application.</span></span> <span data-ttu-id="e36be-148">Gli utenti possono registrare i nuovi account con l'applicazione e account di accesso/disconnessione del sito.</span><span class="sxs-lookup"><span data-stu-id="e36be-148">Users can register new accounts with our application, and login/logout of the site.</span></span>

<span data-ttu-id="e36be-149">È ora possibile aggiungere logica di autorizzazione all'applicazione e utilizzare lo stato di autenticazione nome utente di visitatori per controllare cosa possono e non all'interno del sito.</span><span class="sxs-lookup"><span data-stu-id="e36be-149">Now we can add authorization logic to the application, and use the authentication status and username of visitors to control what they can and can't do within the site.</span></span> <span data-ttu-id="e36be-150">Si inizierà dall'aggiunta di logica di autorizzazione per i metodi di azione della classe organizzazione DinnersController "Crea".</span><span class="sxs-lookup"><span data-stu-id="e36be-150">Let's begin by adding authorization logic to the "Create" action methods of our DinnersController class.</span></span> <span data-ttu-id="e36be-151">In particolare, si richiederà che gli utenti che accedono il *Dinners/crea* URL deve essere registrato.</span><span class="sxs-lookup"><span data-stu-id="e36be-151">Specifically, we will require that users accessing the */Dinners/Create* URL must be logged in.</span></span> <span data-ttu-id="e36be-152">Se non sono connessi è sarà reindirizza alla pagina di accesso in modo che essi possono Accedi.</span><span class="sxs-lookup"><span data-stu-id="e36be-152">If they aren't logged in we'll redirect them to the login page so that they can sign-in.</span></span>

<span data-ttu-id="e36be-153">Implementazione di questa logica è piuttosto semplice.</span><span class="sxs-lookup"><span data-stu-id="e36be-153">Implementing this logic is pretty easy.</span></span> <span data-ttu-id="e36be-154">Ecco cosa da fare è aggiungere un attributo di filtro [Authorize] per i metodi di azione Create in questo modo:</span><span class="sxs-lookup"><span data-stu-id="e36be-154">All we need to-do is to add an [Authorize] filter attribute to our Create action methods like so:</span></span>

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample1.cs)]

<span data-ttu-id="e36be-155">ASP.NET MVC supporta la possibilità di creare "filtri azione" che possono essere utilizzati per implementare la logica riutilizzabile che può essere applicata in modo dichiarativo a metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="e36be-155">ASP.NET MVC supports the ability to create "action filters" that can be used to implement re-usable logic that can be declaratively applied to action methods.</span></span> <span data-ttu-id="e36be-156">Il filtro [Authorize] è uno dei filtri azione predefiniti forniti da ASP.NET MVC e consente agli sviluppatori di regole di autorizzazione in modo dichiarativo applicate ai metodi di azione e alle classi controller.</span><span class="sxs-lookup"><span data-stu-id="e36be-156">The [Authorize] filter is one of the built-in action filters provided by ASP.NET MVC, and it enables a developer to declaratively apply authorization rules to action methods and controller classes.</span></span>

<span data-ttu-id="e36be-157">Quando applicato senza parametri (come sopra) il filtro [Authorize] impone che l'utente che effettua la richiesta del metodo di azione deve essere effettuato l'accesso, e verrà reindirizzati automaticamente il browser all'URL di accesso se non sono.</span><span class="sxs-lookup"><span data-stu-id="e36be-157">When applied without any parameters (like above) the [Authorize] filter enforces that the user making the action method request must be logged in – and it will automatically redirect the browser to the login URL if they aren't.</span></span> <span data-ttu-id="e36be-158">Quando esegue il reindirizzamento all'URL richiesto originariamente viene passato come argomento di stringa di query (ad esempio: / Account/accesso? ReturnUrl = % 2fDinners % 2fCreate).</span><span class="sxs-lookup"><span data-stu-id="e36be-158">When doing this redirect the originally requested URL is passed as a querystring argument (for example: /Account/LogOn?ReturnUrl=%2fDinners%2fCreate).</span></span> <span data-ttu-id="e36be-159">Il AccountController quindi reindirizzerà l'utente all'URL richiesto originariamente una volta all'accesso.</span><span class="sxs-lookup"><span data-stu-id="e36be-159">The AccountController will then redirect the user back to the originally requested URL once they login.</span></span>

<span data-ttu-id="e36be-160">Il filtro [Authorize] supporta facoltativamente la possibilità di specificare una proprietà "Users" o "Ruoli" che può essere utilizzata per richiedere che l'utente sia nel registro e all'interno di un elenco di utenti consentiti o un membro del ruolo di sicurezza consentiti.</span><span class="sxs-lookup"><span data-stu-id="e36be-160">The [Authorize] filter optionally supports the ability to specify a "Users" or "Roles" property that can be used to require that the user is both logged in and within a list of allowed users or a member of an allowed security role.</span></span> <span data-ttu-id="e36be-161">Ad esempio, il codice riportato di seguito consente solo due utenti specifici, "scottgu" e "billg" accedere all'URL Dinners/Create:</span><span class="sxs-lookup"><span data-stu-id="e36be-161">For example, the code below only allows two specific users, "scottgu" and "billg", to access the /Dinners/Create URL:</span></span>

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample2.cs)]

<span data-ttu-id="e36be-162">I nomi utente specifico all'interno di codice di incorporamento tende a essere tuttavia abbastanza non gestibile.</span><span class="sxs-lookup"><span data-stu-id="e36be-162">Embedding specific user names within code tends to be pretty un-maintainable though.</span></span> <span data-ttu-id="e36be-163">Un approccio migliore consiste nel definire "ruoli" di livello superiore che controlla il codice, quindi eseguire il mapping di utenti al ruolo di utilizzo di un database o un sistema di active directory (l'elenco di mapping utente effettivo da archiviare esternamente dal codice di attivazione).</span><span class="sxs-lookup"><span data-stu-id="e36be-163">A better approach is to define higher-level "roles" that the code checks against, and then to map users into the role using either a database or active directory system (enabling the actual user mapping list to be stored externally from the code).</span></span> <span data-ttu-id="e36be-164">ASP.NET include un'API di gestione di ruoli predefiniti, nonché un set predefinito di provider di ruoli (incluse quelle per SQL e Active Directory) che consentono di eseguire il mapping di utenti e ruoli.</span><span class="sxs-lookup"><span data-stu-id="e36be-164">ASP.NET includes a built-in role management API as well as a built-in set of role providers (including ones for SQL and Active Directory) that can help perform this user/role mapping.</span></span> <span data-ttu-id="e36be-165">È quindi possibile aggiornare il codice per consentire agli utenti all'interno di un ruolo specifico "admin" accedere all'URL Dinners/Create:</span><span class="sxs-lookup"><span data-stu-id="e36be-165">We could then update the code to only allow users within a specific "admin" role to access the /Dinners/Create URL:</span></span>

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample3.cs)]

### <a name="using-the-useridentityname-property-when-creating-dinners"></a><span data-ttu-id="e36be-166">Utilizzando la proprietà User.Identity.Name durante la creazione di Dinners</span><span class="sxs-lookup"><span data-stu-id="e36be-166">Using the User.Identity.Name property when Creating Dinners</span></span>

<span data-ttu-id="e36be-167">È possibile recuperare il nome utente dell'utente connesso di una richiesta utilizzando la proprietà User.Identity.Name esposta nella classe di base Controller.</span><span class="sxs-lookup"><span data-stu-id="e36be-167">We can retrieve the username of the currently logged-in user of a request using the User.Identity.Name property exposed on the Controller base class.</span></span>

<span data-ttu-id="e36be-168">Precedente quando è implementata la versione HTTP-POST, il metodo di creazione del metodo di azione abbiamo hardcoded la proprietà "HostedBy" della cena in una stringa statica.</span><span class="sxs-lookup"><span data-stu-id="e36be-168">Earlier when we implemented the HTTP-POST version of our Create() action method we had hardcoded the "HostedBy" property of the Dinner to a static string.</span></span> <span data-ttu-id="e36be-169">È possibile aggiornare questo codice per utilizzare invece la proprietà User.Identity.Name, nonché aggiungere automaticamente una RSVP per la creazione di Dinner dell'host:</span><span class="sxs-lookup"><span data-stu-id="e36be-169">We can now update this code to instead use the User.Identity.Name property, as well as automatically add an RSVP for the host creating the Dinner:</span></span>

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample4.cs)]

<span data-ttu-id="e36be-170">Poiché è stato aggiunto un attributo [Authorize] per il metodo di metodo di creazione, ASP.NET MVC assicura che il metodo di azione viene eseguita solo se è connesso l'utente visita l'URL Dinners/Create nel sito.</span><span class="sxs-lookup"><span data-stu-id="e36be-170">Because we have added an [Authorize] attribute to the Create() method, ASP.NET MVC ensures that the action method only executes if the user visiting the /Dinners/Create URL is logged in on the site.</span></span> <span data-ttu-id="e36be-171">Di conseguenza, il valore della proprietà User.Identity.Name conterrà sempre un nome utente valido.</span><span class="sxs-lookup"><span data-stu-id="e36be-171">As such, the User.Identity.Name property value will always contain a valid username.</span></span>

### <a name="using-the-useridentityname-property-when-editing-dinners"></a><span data-ttu-id="e36be-172">Utilizzando la proprietà User.Identity.Name durante la modifica di Dinners</span><span class="sxs-lookup"><span data-stu-id="e36be-172">Using the User.Identity.Name property when Editing Dinners</span></span>

<span data-ttu-id="e36be-173">Ora aggiungere una logica di autorizzazione che limita gli utenti in modo che possano modificare solo le proprietà di dinners che vengono anch'essi ospitano.</span><span class="sxs-lookup"><span data-stu-id="e36be-173">Let's now add some authorization logic that restricts users so that they can only edit the properties of dinners they themselves are hosting.</span></span>

<span data-ttu-id="e36be-174">A tale scopo, innanzitutto verrà aggiunto un metodo di supporto "IsHostedBy(username)" all'oggetto Dinner (all'interno della classe parziale di Dinner.cs che è stato creato in precedenza).</span><span class="sxs-lookup"><span data-stu-id="e36be-174">To help with this, we'll first add an "IsHostedBy(username)" helper method to our Dinner object (within the Dinner.cs partial class we built earlier).</span></span> <span data-ttu-id="e36be-175">Questo metodo di supporto restituisce true o false a seconda se un nome utente fornito corrisponde alla proprietà Dinner HostedBy e incapsula la logica necessaria per eseguire un confronto di stringhe tra maiuscole e minuscole di essi:</span><span class="sxs-lookup"><span data-stu-id="e36be-175">This helper method returns true or false depending on whether a supplied username matches the Dinner HostedBy property, and encapsulates the logic necessary to perform a case-insensitive string comparison of them:</span></span>

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample5.cs)]

<span data-ttu-id="e36be-176">Si aggiungerà quindi un attributo [Authorize] ai metodi di azione Edit() all'interno della classe DinnersController.</span><span class="sxs-lookup"><span data-stu-id="e36be-176">We'll then add an [Authorize] attribute to the Edit() action methods within our DinnersController class.</span></span> <span data-ttu-id="e36be-177">Ciò garantisce che gli utenti necessario eseguire l'accesso alla richiesta di un */Dinners/modifica / [id]* URL.</span><span class="sxs-lookup"><span data-stu-id="e36be-177">This will ensure that users must be logged in to request a */Dinners/Edit/[id]* URL.</span></span>

<span data-ttu-id="e36be-178">È quindi possibile aggiungere codice per i metodi di modifica che utilizza il metodo di supporto Dinner.IsHostedBy(username) per verificare che l'utente connesso corrisponda all'host Dinner.</span><span class="sxs-lookup"><span data-stu-id="e36be-178">We can then add code to our Edit methods that uses the Dinner.IsHostedBy(username) helper method to verify that the logged-in user matches the Dinner host.</span></span> <span data-ttu-id="e36be-179">Se l'utente non è l'host, viene visualizza una vista "InvalidOwner" e terminare la richiesta.</span><span class="sxs-lookup"><span data-stu-id="e36be-179">If the user is not the host, we'll display an "InvalidOwner" view and terminate the request.</span></span> <span data-ttu-id="e36be-180">Il codice per eseguire questa operazione è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="e36be-180">The code to do this looks like below:</span></span>

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample6.cs)]

<span data-ttu-id="e36be-181">È quindi possibile fare doppio clic sulla directory \Views\Dinners e scegliere Aggiungi -&gt;consente di visualizzare il comando di menu per creare una nuova visualizzazione "InvalidOwner".</span><span class="sxs-lookup"><span data-stu-id="e36be-181">We can then right-click on the \Views\Dinners directory and choose the Add-&gt;View menu command to create a new "InvalidOwner" view.</span></span> <span data-ttu-id="e36be-182">È possibile popolarla con il messaggio di errore seguente:</span><span class="sxs-lookup"><span data-stu-id="e36be-182">We'll populate it with the below error message:</span></span>

[!code-aspx[Main](secure-applications-using-authentication-and-authorization/samples/sample7.aspx)]

<span data-ttu-id="e36be-183">E, quando un utente tenta di modificare una cena che non possiedono, verrà visualizzato un messaggio di errore:</span><span class="sxs-lookup"><span data-stu-id="e36be-183">And now when a user attempts to edit a dinner they don't own, they'll get an error message:</span></span>

![](secure-applications-using-authentication-and-authorization/_static/image7.png)

<span data-ttu-id="e36be-184">È possibile ripetere gli stessi passaggi per i metodi di azione Delete () all'interno di questo controller per bloccare l'autorizzazione per eliminare anche Dinners e assicurarsi che solo l'host di una cena possibile eliminarlo.</span><span class="sxs-lookup"><span data-stu-id="e36be-184">We can repeat the same steps for the Delete() action methods within our controller to lock down permission to delete Dinners as well, and ensure that only the host of a Dinner can delete it.</span></span>

### <a name="showinghiding-edit-and-delete-links"></a><span data-ttu-id="e36be-185">Modifica di mostrare o nascondere e collegamenti Elimina</span><span class="sxs-lookup"><span data-stu-id="e36be-185">Showing/Hiding Edit and Delete Links</span></span>

<span data-ttu-id="e36be-186">Il metodo di azione di modifica e l'eliminazione della classe organizzazione DinnersController è stiamo collegamento da questo URL dettagli:</span><span class="sxs-lookup"><span data-stu-id="e36be-186">We are linking to the Edit and Delete action method of our DinnersController class from our Details URL:</span></span>

![](secure-applications-using-authentication-and-authorization/_static/image8.png)

<span data-ttu-id="e36be-187">Attualmente viene mostrato il collegamenti ad azioni di modifica ed eliminazione indipendentemente dal fatto se il visitatore per l'URL di dettagli è l'host di dinner.</span><span class="sxs-lookup"><span data-stu-id="e36be-187">Currently we are showing the Edit and Delete action links regardless of whether the visitor to the details URL is the host of the dinner.</span></span> <span data-ttu-id="e36be-188">È necessario modificare questo in modo che i collegamenti vengono visualizzati solo se l'utente visita è il proprietario della cena.</span><span class="sxs-lookup"><span data-stu-id="e36be-188">Let's change this so that the links are only displayed if the visiting user is the owner of the dinner.</span></span>

<span data-ttu-id="e36be-189">Il metodo di azione all'interno di questo DinnersController Details() recupera un oggetto Dinner e quindi passa come modello di oggetto per il modello di visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="e36be-189">The Details() action method within our DinnersController retrieves a Dinner object and then passes it as the model object to our view template:</span></span>

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample8.cs)]

<span data-ttu-id="e36be-190">È possibile aggiornare il modello di visualizzazione per in modo condizionale mostrare/nascondere i collegamenti di modifica ed eliminazione utilizzando la Dinner.IsHostedBy() come il seguente metodo di supporto:</span><span class="sxs-lookup"><span data-stu-id="e36be-190">We can update our view template to conditionally show/hide the Edit and Delete links by using the Dinner.IsHostedBy() helper method like below:</span></span>

[!code-aspx[Main](secure-applications-using-authentication-and-authorization/samples/sample9.aspx)]

#### <a name="next-steps"></a><span data-ttu-id="e36be-191">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="e36be-191">Next Steps</span></span>

<span data-ttu-id="e36be-192">Ora esaminiamo come è possibile abilitare gli utenti autenticati possono RSVP per l'utilizzo di AJAX dinners.</span><span class="sxs-lookup"><span data-stu-id="e36be-192">Let's now look at how we can enable authenticated users to RSVP for dinners using AJAX.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="e36be-193">[Precedente](implement-efficient-data-paging.md)
[Successivo](use-ajax-to-deliver-dynamic-updates.md)</span><span class="sxs-lookup"><span data-stu-id="e36be-193">[Previous](implement-efficient-data-paging.md)
[Next](use-ajax-to-deliver-dynamic-updates.md)</span></span>