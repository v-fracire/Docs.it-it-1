---
uid: web-forms/overview/ajax-control-toolkit/cascadingdropdown/presetting-list-entries-with-cascadingdropdown-cs
title: Preimpostazione le voci dell'elenco con CascadingDropDown (c#) | Documenti Microsoft
author: wenz
description: Il controllo CascadingDropDown AJAX Control Toolkit estende un controllo DropDownList in modo che le modifiche in una DropDownList carichi associati valori in anoth...
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 04c79748-0f21-4a3b-aba5-e1ce3161c32e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/cascadingdropdown/presetting-list-entries-with-cascadingdropdown-cs
msc.type: authoredcontent
ms.openlocfilehash: d86c5887a8b175a60c32a0970482266b7d690162
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
<a name="presetting-list-entries-with-cascadingdropdown-c"></a><span data-ttu-id="dbe39-103">Voci dell'elenco preimpostazione con CascadingDropDown (c#)</span><span class="sxs-lookup"><span data-stu-id="dbe39-103">Presetting List Entries with CascadingDropDown (C#)</span></span>
====================
<span data-ttu-id="dbe39-104">da [Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="dbe39-104">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="dbe39-105">[Scaricare codice](http://download.microsoft.com/download/9/0/7/907760b1-2c60-4f81-aeb6-ca416a573b0d/cascadingdropdown2.cs.zip) o [Scarica il PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/cascadingDropDown2CS.pdf)</span><span class="sxs-lookup"><span data-stu-id="dbe39-105">[Download Code](http://download.microsoft.com/download/9/0/7/907760b1-2c60-4f81-aeb6-ca416a573b0d/cascadingdropdown2.cs.zip) or [Download PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/cascadingDropDown2CS.pdf)</span></span>

> <span data-ttu-id="dbe39-106">Il controllo CascadingDropDown AJAX Control Toolkit estende un controllo DropDownList in modo che le modifiche in una DropDownList carichi associati valori in un altro DropDownList.</span><span class="sxs-lookup"><span data-stu-id="dbe39-106">The CascadingDropDown control in the AJAX Control Toolkit extends a DropDownList control so that changes in one DropDownList loads associated values in another DropDownList.</span></span> <span data-ttu-id="dbe39-107">Con un minimo di codice è possibile che un elemento di elenco è preselezionato una volta caricati in modo dinamico i dati.</span><span class="sxs-lookup"><span data-stu-id="dbe39-107">With a little bit of code it is possible that a list element is preselected once the data has been dynamically loaded.</span></span>


## <a name="overview"></a><span data-ttu-id="dbe39-108">Panoramica</span><span class="sxs-lookup"><span data-stu-id="dbe39-108">Overview</span></span>

<span data-ttu-id="dbe39-109">Il controllo CascadingDropDown AJAX Control Toolkit estende un controllo DropDownList in modo che le modifiche in una DropDownList carichi associati valori in un altro DropDownList.</span><span class="sxs-lookup"><span data-stu-id="dbe39-109">The CascadingDropDown control in the AJAX Control Toolkit extends a DropDownList control so that changes in one DropDownList loads associated values in another DropDownList.</span></span> <span data-ttu-id="dbe39-110">(Ad esempio, un unico elenco fornisce un elenco degli stati ed elenco successivo viene riempita con città importanti in tale stato.) Con un minimo di codice è possibile che un elemento di elenco è preselezionato una volta caricati in modo dinamico i dati.</span><span class="sxs-lookup"><span data-stu-id="dbe39-110">(For instance, one list provides a list of US states, and the next list is then filled with major cities in that state.) With a little bit of code it is possible that a list element is preselected once the data has been dynamically loaded.</span></span>

## <a name="steps"></a><span data-ttu-id="dbe39-111">Passaggi</span><span class="sxs-lookup"><span data-stu-id="dbe39-111">Steps</span></span>

<span data-ttu-id="dbe39-112">Per attivare le funzionalità di ASP.NET AJAX e il Toolkit di controllo, il `ScriptManager` controllo deve essere inserito in un punto qualsiasi della pagina (ma entro il `<form>` elemento):</span><span class="sxs-lookup"><span data-stu-id="dbe39-112">In order to activate the functionality of ASP.NET AJAX and the Control Toolkit, the `ScriptManager` control must be put anywhere on the page (but within the `<form>` element):</span></span>

[!code-aspx[Main](presetting-list-entries-with-cascadingdropdown-cs/samples/sample1.aspx)]

<span data-ttu-id="dbe39-113">Quindi, è necessario un controllo di DropDownList:</span><span class="sxs-lookup"><span data-stu-id="dbe39-113">Then, a DropDownList control is required:</span></span>

[!code-aspx[Main](presetting-list-entries-with-cascadingdropdown-cs/samples/sample2.aspx)]

<span data-ttu-id="dbe39-114">Per questo elenco, viene aggiunto un extender CascadingDropDown, fornendo informazioni sull'URL e metodo servizio web:</span><span class="sxs-lookup"><span data-stu-id="dbe39-114">For this list, a CascadingDropDown extender is added, providing web service URL and method information:</span></span>

[!code-aspx[Main](presetting-list-entries-with-cascadingdropdown-cs/samples/sample3.aspx)]

<span data-ttu-id="dbe39-115">Quindi il programma di estensione CascadingDropDown chiama in modo asincrono un servizio web con la firma del metodo seguente:</span><span class="sxs-lookup"><span data-stu-id="dbe39-115">The CascadingDropDown extender then asynchronously calls a web service with the following method signature:</span></span>

[!code-csharp[Main](presetting-list-entries-with-cascadingdropdown-cs/samples/sample4.cs)]

<span data-ttu-id="dbe39-116">Il metodo restituisce una matrice di tipo valore CascadingDropDown.</span><span class="sxs-lookup"><span data-stu-id="dbe39-116">The method returns an array of type CascadingDropDown value.</span></span> <span data-ttu-id="dbe39-117">Il costruttore del tipo prevede innanzitutto la didascalia della voce di elenco e quindi il valore (HTML `value` attributo).</span><span class="sxs-lookup"><span data-stu-id="dbe39-117">The type's constructor expects first the list entry's caption and then the value (HTML `value` attribute).</span></span> <span data-ttu-id="dbe39-118">Se il terzo argomento è impostato su true, l'elenco viene automaticamente selezionato nel browser.</span><span class="sxs-lookup"><span data-stu-id="dbe39-118">If the third argument is set to true, the list element is automatically selected in the browser.</span></span>

[!code-aspx[Main](presetting-list-entries-with-cascadingdropdown-cs/samples/sample5.aspx)]

<span data-ttu-id="dbe39-119">Il caricamento della pagina nel browser riempirà nell'elenco a discesa con tre fornitori, la seconda fase preselezionata.</span><span class="sxs-lookup"><span data-stu-id="dbe39-119">Loading the page in the browser will fill the dropdown list with three vendors, the second one being preselected.</span></span>


<span data-ttu-id="dbe39-120">[![L'elenco viene compilato e preselezionato automaticamente](presetting-list-entries-with-cascadingdropdown-cs/_static/image2.png)](presetting-list-entries-with-cascadingdropdown-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="dbe39-120">[![The list is filled and preselected automatically](presetting-list-entries-with-cascadingdropdown-cs/_static/image2.png)](presetting-list-entries-with-cascadingdropdown-cs/_static/image1.png)</span></span>

<span data-ttu-id="dbe39-121">L'elenco viene compilato e preselezionato automaticamente ([fare clic per visualizzare l'immagine ingrandita](presetting-list-entries-with-cascadingdropdown-cs/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="dbe39-121">The list is filled and preselected automatically ([Click to view full-size image](presetting-list-entries-with-cascadingdropdown-cs/_static/image3.png))</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="dbe39-122">[Precedente](using-cascadingdropdown-with-a-database-cs.md)
[Successivo](using-auto-postback-with-cascadingdropdown-cs.md)</span><span class="sxs-lookup"><span data-stu-id="dbe39-122">[Previous](using-cascadingdropdown-with-a-database-cs.md)
[Next](using-auto-postback-with-cascadingdropdown-cs.md)</span></span>