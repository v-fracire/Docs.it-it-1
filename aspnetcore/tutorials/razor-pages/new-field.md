---
title: Aggiungere un nuovo campo a una pagina Razor in ASP.NET Core
author: rick-anderson
description: Illustra come aggiungere un nuovo campo a una pagina Razor con Entity Framework Core
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: e280bc9553113982a1f1a77eabab32575c905237
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862291"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>Aggiungere un nuovo campo a una pagina Razor in ASP.NET Core

Di [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/rp/download.md)]

In questa sezione viene usato Migrazioni Code First di [Entity Framework](/ef/core/get-started/aspnetcore/new-db) per:

* Aggiungere un nuovo campo al modello.
* Eseguire la migrazione nel database della modifica al nuovo schema del campo.

Quando si usa Code First di Entity Framework per creare automaticamente un database, Code First:

* Aggiunge una tabella al database per rilevare se lo schema del database è sincronizzato con le classi di modelli da cui è stato generato.
* Se le classi di modelli non sono sincronizzate con il database, Entity Framework genera un'eccezione.

La verifica automatica del modello o schema sincronizzato rende più semplice individuare i problemi di codice o database incoerente.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Aggiunta di una proprietà Rating al modello Movie

Aprire il file *Models/Movie.cs* e aggiungere una proprietà `Rating`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Compilare l'app.

Modificare *Pages/Movies/Index.cshtml* e aggiungere un campo `Rating`:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml.?highlight=40-42,61-63)]

Aggiornare le pagine seguenti:

* Aggiungere il campo `Rating` alle pagine Delete (Elimina) e Details (Dettagli).
* Aggiornare [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) con un campo `Rating`.
* Aggiungere il campo `Rating` alla pagina Edit (Modifica).

L'app non funzionerà finché non si aggiorna il database in modo da includere il nuovo campo. Se si esegue l'app ora, verrà visualizzato un errore `SqlException`:

`SqlException: Invalid column name 'Rating'.`

Questo errore viene visualizzato perché la classe del modello Movie aggiornata è diversa rispetto allo schema della tabella Movie nel database. Nella tabella del database non è presente una colonna `Rating`.

Per correggere questo errore, esistono alcuni approcci:

1. Fare in modo che Entity Framework elimini e crei di nuovo automaticamente il database usando il nuovo schema di classi del modello. Questo approccio è utile nelle prime fasi del ciclo di sviluppo e consente di migliorare rapidamente lo schema del modello e il database insieme. Lo svantaggio è che si perdono i dati esistenti nel database. Non usare questo approccio in un database di produzione. L'eliminazione del database per la modifica dello schema e l'uso di un inizializzatore per inizializzare automaticamente un database con i dati di test è spesso un modo produttivo per sviluppare un'app.

2. Modificare esplicitamente lo schema del database esistente in modo che corrisponda alle classi del modello. Il vantaggio di questo approccio è che i dati vengono mantenuti. È possibile apportare questa modifica manualmente o creando uno script di modifica del database.

3. Usare Migrazioni Code First per aggiornare lo schema del database.

Per questa esercitazione usare Migrazioni Code First.

Aggiornare la classe `SeedData` in modo che fornisca un valore per la nuova colonna. Di seguito viene illustrata una modifica di esempio, ma si apporterà questa modifica per ogni blocco `new Movie`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Vedere il [file SeedData.cs completato](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).

Compilare la soluzione.

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Aggiungere una migrazione per il campo Rating

Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Console di Gestione pacchetti**.
Nella Console di Gestione pacchetti immettere i comandi seguenti:

```powershell
Add-Migration Rating
Update-Database
```

Il comando `Add-Migration` indica al framework di:

* Confrontare il modello `Movie` con lo schema di database `Movie`.
* Creare codice per migrare lo schema del database nel nuovo modello.

Il nome "Rating" è arbitrario e viene usato per denominare il file di migrazione. È consigliabile usare un nome significativo per il file di migrazione.

<a name="ssox"></a>

Se si eliminano tutti i record nel database, il database viene inizializzato e viene incluso il campo `Rating`. È possibile eseguire questa operazione con i collegamenti di eliminazione nel browser o da [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX). Per eliminare il database da SSOX:

* Selezionare il database in SSOX.
* Fare clic con il pulsante destro del mouse sul database e selezionare *Elimina*.
* Selezionare **Chiudi connessioni esistenti**.
* Scegliere **OK**.
* Nella [Console di Gestione pacchetti](xref:tutorials/razor-pages/new-field#pmc) aggiornare il database:

  ```powershell
  Update-Database
  ```

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- copy/paste this tab to the next. Not worth an include  --> SQLite non supporta le migrazioni.

* Eliminare il database o modificarne il nome nel file *appsettings.json*.
* Eliminare la cartella *Migrations* e tutti i file al suo interno.

Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:

```console
dotnet ef migrations add Rating
dotnet ef database update
```

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

SQLite non supporta le migrazioni.

* Eliminare il database o modificarne il nome nel file *appsettings.json*.
* Eliminare la cartella *Migrations* e tutti i file al suo interno.

Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:

```console
dotnet ef migrations add Rating
dotnet ef database update
```

---  
<!-- End of VS tabs -->

Eseguire l'app e verificare che sia possibile creare/modificare/visualizzare i film con un campo `Rating`. Se il database non è inizializzato, impostare un punto di interruzione nel metodo `SeedData.Initialize`.

> [!div class="step-by-step"]
> [Precedente: Aggiunta della funzionalità di ricerca](xref:tutorials/razor-pages/search)
> [Successivo: Aggiunta della funzionalità di convalida](xref:tutorials/razor-pages/validation)
