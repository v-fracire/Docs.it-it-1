---
title: ASP.NET Core MVC con EF Core - Aggiornare dati correlati - 7 di 10
author: rick-anderson
description: In questa esercitazione verrà effettuato l'aggiornamento di dati correlati tramite l'aggiornamento di campi di chiave esterna e proprietà di navigazione.
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 37985c945f2e4b15cfcefb0c126c3209e0bdeac4
ms.sourcegitcommit: 4d74644f11e0dac52b4510048490ae731c691496
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50090732"
---
# <a name="aspnet-core-mvc-with-ef-core---update-related-data---7-of-10"></a>ASP.NET Core MVC con EF Core - Aggiornare dati correlati - 7 di 10

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc-21.md)]

::: moniker range="= aspnetcore-2.0"

Di [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)

L'applicazione Web di esempio Contoso University illustra come creare applicazioni Web ASP.NET Core MVC con Entity Framework Core e Visual Studio. Per informazioni sulla serie di esercitazioni, vedere la [prima esercitazione della serie](intro.md).

Nell'esercitazione precedente sono stati visualizzati dati correlati. In questa esercitazione i dati correlati verranno aggiornati tramite l'aggiornamento di campi di chiave esterna e proprietà di navigazione.

Le figure seguenti illustrano alcune delle pagine che verranno usate.

![Pagina di modifica del corso](update-related-data/_static/course-edit.png)

![Pagina di modifica dell'insegnante](update-related-data/_static/instructor-edit-courses.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a>Personalizzare le pagine di creazione e di modifica per i corsi

Quando viene creata, una nuova entità corso deve essere in relazione con un dipartimento esistente. Per semplificare il raggiungimento di questo obiettivo, il codice con scaffolding include i metodi del controller e le visualizzazioni di creazione e modifica includono un elenco a discesa per la selezione del dipartimento. L'elenco a discesa imposta la proprietà di chiave esterna `Course.DepartmentID`. Questo è tutto ciò che serve a Entity Framework per caricare la proprietà di navigazione `Department` con l'entità Department appropriata. Verrà usato il codice con scaffolding, che però verrà modificato leggermente per aggiungere la gestione degli errori e l'ordinamento dell'elenco a discesa.

In *CoursesController.cs* eliminare i quattro metodi Create ed Edit e sostituirli con il codice seguente:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

Dopo il metodo `Edit` HttpPost, creare un nuovo metodo che carichi le informazioni di dipartimento per l'elenco a discesa.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

Il metodo `PopulateDepartmentsDropDownList` ottiene un elenco di tutti i dipartimenti ordinato per nome, crea una raccolta `SelectList` per un elenco a discesa e passa tale raccolta alla visualizzazione in `ViewBag`. Il metodo accetta il parametro facoltativo `selectedDepartment`, che consente al codice chiamante di specificare l'elemento che deve essere selezionato quando viene eseguito il rendering dell'elenco a discesa. La visualizzazione passerà il nome "DepartmentID" all'helper tag `<select>`, che quindi saprà di dover cercare nell'oggetto `ViewBag` una raccolta `SelectList` denominata "DepartmentID".

Il metodo `Create` HttpGet chiama il metodo `PopulateDepartmentsDropDownList` senza impostare l'elemento selezionato, perché per un nuovo corso il dipartimento non è ancora stabilito:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

Il metodo `Edit` HttpGet imposta l'elemento selezionato, in base all'ID del dipartimento già assegnato al corso in fase di modifica:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

I metodi HttpPost sia per `Create` che per `Edit` includono anche codice che imposta l'elemento selezionato quando la pagina viene visualizzata di nuovo dopo un errore. Ciò garantisce che, quando la pagina viene visualizzata di nuovo per mostrare il messaggio di errore, il dipartimento selezionato rimane selezionato.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Aggiungere .AsNoTracking ai metodi Details e Delete

Per ottimizzare le prestazioni delle pagine dei dettagli e di eliminazione del corso, aggiungere chiamate a `AsNoTracking` nei metodi `Details` e `Delete` HttpGet.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Modificare le visualizzazioni dei corsi

In *Views/Courses/Create.cshtml* aggiungere l'opzione "Select Department" (Selezionare il dipartimento) all'elenco a discesa **Department** (Dipartimento), modificare la didascalia da **DepartmentID** (ID dipartimento) a **Department** (Dipartimento) e aggiungere un messaggio di convalida.

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

In *Views/Courses/Edit.cshtml* apportare al campo Department (Dipartimento) la stessa modifica effettuata in *Create.cshtml*.

In *Views/Courses/Edit.cshtml* aggiungere anche il campo per il numero di corso prima del campo **Title** (Titolo). Poiché il numero di corso è la chiave primaria, viene visualizzato, ma non può essere modificato.

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

Per il numero di corso è già presente un campo nascosto (`<input type="hidden">`) nella visualizzazione di modifica. L'aggiunta di un helper tag `<label>` non elimina la necessità del campo nascosto, poiché senza di questo il numero di corso non viene incluso nei dati inviati quando l'utente fa clic su **Save** (Salva) nella pagina **Edit** (Modifica).

In *Views/Courses/Delete.cshtml* aggiungere il campo del numero di corso nella parte superiore e modificare l'ID del dipartimento nel nome del dipartimento.

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

In *Views/Courses/Details.cshtml* apportare la stessa modifica appena effettuata in *Delete.cshtml*.

### <a name="test-the-course-pages"></a>Testare le pagine del corso

Eseguire l'app, selezionare la scheda **Courses** (Corsi), fare clic su **Create New** (Crea nuovo) e immettere i dati per un nuovo corso:

![Pagina di creazione del corso](update-related-data/_static/course-create.png)

Scegliere **Crea**. La pagina di indice dei corsi viene visualizzata con il nuovo corso aggiunto all'elenco. Il nome del dipartimento nell'elenco della pagina di indice deriva dalla proprietà di navigazione, che mostra che la relazione è stata stabilita correttamente.

Fare clic su **Edit** (Modifica) per un corso nella pagina di indice dei corsi.

![Pagina di modifica del corso](update-related-data/_static/course-edit.png)

Modificare i dati nella pagina e fare clic su **Save** (Salva). La pagina di indice dei corsi verrà visualizzata con i dati del corso aggiornati.

## <a name="add-an-edit-page-for-instructors"></a>Aggiungere una pagina di modifica per gli insegnanti

Quando si modifica il record di un insegnante, è necessario essere in grado di aggiornare l'assegnazione dell'ufficio. L'entità Instructor ha una relazione uno-a-zero-o-uno con l'entità OfficeAssignment. Ciò significa che il codice deve gestire le situazioni seguenti:

* Se l'utente cancella l'assegnazione di un ufficio e questa originariamente rappresentava l'unico valore, eliminare l'entità OfficeAssignment.

* Se l'utente immette l'assegnazione di un ufficio e prima non c'era alcun valore, creare una nuova entità OfficeAssignment.

* Se l'utente modifica il valore di un'assegnazione di ufficio, modificare il valore in un'entità OfficeAssignment esistente.

### <a name="update-the-instructors-controller"></a>Aggiornare il controller Instructors

In *InstructorsController.cs* modificare il codice nel metodo `Edit` HttpGet in modo che carichi la proprietà di navigazione `OfficeAssignment` dell'entità Instructor e chiami `AsNoTracking`:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

Sostituire il metodo `Edit` HttpPost con il codice seguente per gestire gli aggiornamenti delle assegnazioni di ufficio:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Il codice effettua queste operazioni:

-  Modifica il nome del metodo in `EditPost` perché ora la firma è la stessa del metodo `Edit` HttpGet (l'attributo `ActionName` specifica che l'URL `/Edit/` viene ancora usato).

-  Ottiene l'entità Instructor corrente dal database tramite il caricamento eager per la proprietà di navigazione `OfficeAssignment`. Ciò corrisponde a quanto effettuato nel metodo `Edit` HttpGet.

-  Aggiorna l'entità Instructor recuperata con valori dallo strumento di associazione di modelli. L'overload `TryUpdateModel` consente di creare un elenco elementi consentiti per le proprietà da includere. In questo modo è possibile evitare l'overposting, come illustrato nella [seconda esercitazione](crud.md).

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

-   Se la posizione dell'ufficio è vuota, impostare la proprietà Instructor.OfficeAssignment su Null, in modo che la riga correlata nella tabella OfficeAssignment venga eliminata.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- Salva le modifiche nel database.

### <a name="update-the-instructor-edit-view"></a>Aggiornare la visualizzazione di modifica dell'insegnante

In *Views/Instructors/Edit.cshtml* aggiungere un nuovo campo per la modifica della posizione dell'ufficio alla fine, prima del pulsante **Save** (Salva):

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Eseguire l'app, selezionare la scheda **Instructors** (Insegnanti) e quindi fare clic su **Edit** (Modifica) per un insegnante. Modificare **Office Location** (Posizione ufficio) e fare clic su **Save** (Salva).

![Pagina di modifica dell'insegnante](update-related-data/_static/instructor-edit-office.png)

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Aggiungere assegnazioni di corso nella pagina di modifica dell'insegnante

Gli insegnanti possono tenere un numero qualsiasi di corsi. A questo punto la pagina di modifica dell'insegnante verrà migliorata aggiungendo la possibilità di modificare le assegnazioni di corso tramite un gruppo di caselle di controllo, come illustrato nello screenshot seguente:

![Pagina di modifica dell'insegnante con corsi](update-related-data/_static/instructor-edit-courses.png)

Tra le entità Course e Instructor c'è una relazione molti-a-molti. Per aggiungere e rimuovere relazioni, aggiungere e rimuovere entità dal set di entità di join CourseAssignments.

L'interfaccia utente che consente di modificare i corsi assegnati a un insegnante è costituita da un gruppo di caselle di controllo. È visualizzata una casella di controllo per ogni corso nel database e le caselle corrispondenti ai corsi attualmente assegnati all'insegnante sono selezionate. L'utente può selezionare e deselezionare le caselle di controllo per modificare le assegnazioni dei corsi. Se il numero di corsi fosse molto superiore, sarebbe probabilmente consigliabile usare un altro metodo di presentazione dei dati nella visualizzazione, ma si userebbe lo stesso metodo di modifica di un'entità di join per creare o eliminare relazioni.

### <a name="update-the-instructors-controller"></a>Aggiornare il controller Instructors

Per fornire i dati alla visualizzazione dell'elenco di caselle di controllo, si userà una classe modello di visualizzazione.

Creare *AssignedCourseData.cs* nella cartella *SchoolViewModels* e sostituire il codice esistente con il codice seguente:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

In *InstructorsController.cs* sostituire il metodo `Edit` HttpGet con il codice seguente. Le modifiche sono evidenziate.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Il codice aggiunge il caricamento eager per la proprietà di navigazione `Courses` e chiama il nuovo metodo `PopulateAssignedCourseData` per fornire informazioni per la matrice di caselle di controllo tramite la classe modello di visualizzazione `AssignedCourseData`.

Il codice nel metodo `PopulateAssignedCourseData` legge tutte le entità Course per caricare un elenco di corsi tramite la classe modello di visualizzazione. Per ogni corso, il codice verifica se è presente nella proprietà di navigazione `Courses` dell'insegnante. Per creare un ricerca efficiente per la verifica dell'assegnazione di un corso all'insegnante, i corsi assegnati all'insegnante vengono inseriti in una raccolta `HashSet`. La proprietà `Assigned` è impostata su true per i corsi assegnati all'insegnante. La visualizzazione usa questa proprietà per determinare quali caselle di controllo devono essere visualizzate come selezionate. L'elenco, infine, viene passato alla visualizzazione in `ViewData`.

Aggiungere quindi il codice che viene eseguito quando l'utente fa clic su **Save** (Salva). Sostituire il metodo `EditPost` con il codice seguente e aggiungere un nuovo metodo che aggiorni la proprietà di navigazione `Courses` dell'entità Instructor.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

La firma del metodo è ora diversa dal metodo `Edit` HttpGet, quindi il nome del metodo cambia di nuovo da `EditPost` a `Edit`.

Poiché la visualizzazione non ha una raccolta di entità Course, lo strumento di associazione di modelli non può aggiornare automaticamente la proprietà di navigazione `CourseAssignments`. Anziché usare lo strumento di associazione di modelli per aggiornare la proprietà di navigazione `CourseAssignments`, questa operazione viene eseguita nel nuovo metodo `UpdateInstructorCourses`. È pertanto necessario escludere la proprietà `CourseAssignments` dall'associazione di modelli. Ciò non richiede modifiche al codice che chiama `TryUpdateModel`, poiché si sta usando l'overload dell'elenco elementi consentiti e `CourseAssignments` non è presente nell'elenco di inclusione.

Se nessuna casella di controllo è selezionata, il codice in `UpdateInstructorCourses` inizializza la proprietà di navigazione `CourseAssignments` con una raccolta vuota e torna:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Il codice quindi esegue il ciclo di tutti i corsi nel database e controlla ogni corso a fronte di quelli assegnati all'insegnante rispetto a quelli selezionati nella visualizzazione. Per facilitare l'esecuzione di ricerche efficienti, le ultime due raccolte sono archiviate all'interno di oggetti `HashSet`.

Se la casella di controllo di un corso è selezionata ma il corso non è presente nella proprietà di navigazione `Instructor.CourseAssignments`, il corso viene aggiunto alla raccolta nella proprietà di navigazione.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Se la casella di controllo di un corso non è selezionata ma il corso è presente nella proprietà di navigazione `Instructor.CourseAssignments`, il corso viene rimosso dalla proprietà di navigazione.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Aggiornare le visualizzazioni dell'insegnante

In *Views/Instructors/Edit.cshtml* aggiungere un campo **Courses** (Corsi) con una matrice di caselle di controllo aggiungendo il codice seguente immediatamente dopo gli elementi `div` per il campo **Office**  (Ufficio) e prima dell'elemento `div` per il pulsante **Save** (Salva).

<a id="notepad"></a>
> [!NOTE]
> Quando si incolla il codice in Visual Studio, le interruzioni di riga vengono modificate in modo tale da danneggiare il codice.  Premere Ctrl + Z una volta per annullare la formattazione automatica.  Ciò corregge le interruzioni di riga, che vengono visualizzate come illustrato qui. Il rientro non deve necessariamente essere perfetto, ma le righe `@</tr><tr>`, `@:<td>`, `@:</td>` e `@:</tr>` devono trovarsi in una sola riga, come illustrato. In caso contrario, viene visualizzato un errore di runtime. Dopo aver selezionato il blocco di nuovo codice, premere Tab tre volte per allineare il nuovo codice con il codice esistente. È possibile controllare lo stato del problema [qui](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Questo codice crea una tabella HTML con tre colonne. In ogni colonna è presente una casella di controllo seguita da una didascalia costituita dal numero di corso e dal titolo. Le caselle di controllo hanno tutte lo stesso nome ("selectedCourses"). Questo informa lo strumento di associazione di modelli che devono essere considerate come un gruppo. L'attributo value di ogni casella di controllo è impostato sul valore di `CourseID`. Quando la pagina viene pubblicata, lo strumento di associazione di modelli passa al controller una matrice costituita dai valori `CourseID` delle sole caselle di controllo selezionate.

Quando viene eseguito il rendering iniziale delle caselle di controllo, quelle corrispondenti ai corsi assegnati all'insegnante hanno attributi checked, che le rendono selezionate (visualizzate come selezionate).

Eseguire l'app, selezionare la scheda **Instructors** (Insegnanti) e fare clic su **Edit** (Modifica) per un insegnante per visualizzare la pagina **Edit** (Modifica).

![Pagina di modifica dell'insegnante con corsi](update-related-data/_static/instructor-edit-courses.png)

Modificare alcune assegnazioni di corsi e fare clic su Save (Salva). Le modifiche effettuate si riflettono nella pagina di indice.

> [!NOTE]
> L'approccio qui adottato per la modifica dei dati dei corsi degli insegnanti funziona bene quando è presente un numero limitato di corsi. Per raccolte molto più grandi, sarebbero necessari un'interfaccia utente diversa e un altro metodo di aggiornamento.

## <a name="update-the-delete-page"></a>Aggiornare la pagina Delete (Elimina)

In *InstructorsController.cs* eliminare il metodo `DeleteConfirmed` e sostituirlo con il codice seguente.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Questo codice determina le modifiche seguenti:

* Esegue il caricamento eager per la proprietà di navigazione `CourseAssignments`.  È necessario includere questa proprietà o EF non sarà a conoscenza delle entità `CourseAssignment` correlate e non le eliminerà.  Per evitare la necessità di leggerle qui, è possibile configurare l'eliminazione a catena nel database.

* Se l'insegnante da eliminare è assegnato come responsabile di un dipartimento, tale assegnazione viene rimossa dal dipartimento.

## <a name="add-office-location-and-courses-to-the-create-page"></a>Aggiungere posizione dell'ufficio e corsi alla pagina Create (Crea)

In *InstructorsController.cs*, eliminare i metodi `Create` HttpGet e HttpPost e quindi sostituirli con il codice seguente:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Questo codice è simile a quello dei metodi `Edit`, ad eccezione del fatto che inizialmente non è selezionato alcun corso. Il metodo `Create` HttpGet chiama il metodo `PopulateAssignedCourseData` non perché possono essere presenti corsi selezionati, ma per fornire una raccolta vuota per il ciclo `foreach` nella visualizzazioni (in caso contrario il codice di visualizzazione genera un'eccezione di riferimento Null).

Il metodo `Create` HttpPost aggiunge i corsi selezionati alla proprietà di navigazione `CourseAssignments` prima di controllare la presenza di errori di convalida e aggiungere il nuovo insegnante al database. I corsi vengono aggiunti anche in caso di errori di modello. Quindi se si verificano errori di questo tipo (ad esempio se l'utente digita una data non valida) e la pagina viene visualizzata di nuovo con un messaggio di errore, tutte le selezioni relative ai corsi effettuate vengono ripristinate automaticamente.

Si noti che, perché sia possibile aggiungere corsi alla proprietà di navigazione `CourseAssignments`, è necessario inizializzare la proprietà come raccolta vuota:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Anziché all'interno di codice di controllo, è possibile eseguire questa operazione nel modello Instructor modificando il getter della proprietà in modo da creare automaticamente la raccolta, se questa non esiste, come illustrato nell'esempio seguente:

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

Se si modifica la proprietà `CourseAssignments` in questo modo, è possibile rimuovere il codice di inizializzazione esplicita della proprietà nel controller.

In *Views/Instructor/Create.cshtml* aggiungere una casella di testo per la posizione dell'ufficio per i corsi prima del pulsante Submit (Invia). Come nel caso della pagina Edit (Modifica), [correggere la formattazione se Visual Studio riformatta il codice quando lo si incolla](#notepad).

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Eseguire il test eseguendo l'app e creando un insegnante.

## <a name="handling-transactions"></a>Gestione delle transazioni

Come spiegato nell' [esercitazione su CRUD](crud.md), per impostazione predefinita Entity Framework implementa in modo implicito le transazioni. Per gli scenari in cui è necessario un maggior controllo, ad esempio per includere le operazioni eseguite all'esterno di Entity Framework in una transazione, vedere [Transazioni](/ef/core/saving/transactions).

## <a name="summary"></a>Riepilogo

L'esercitazione di introduzione all'uso di dati correlati è stata completata. Nella prossima esercitazione verrà illustrato come gestire i conflitti di concorrenza.

::: moniker-end

> [!div class="step-by-step"]
> [Precedente](read-related-data.md)
> [Successivo](concurrency.md)
