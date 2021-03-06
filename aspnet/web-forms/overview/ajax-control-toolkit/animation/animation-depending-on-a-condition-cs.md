---
uid: web-forms/overview/ajax-control-toolkit/animation/animation-depending-on-a-condition-cs
title: Animazione in base a una condizione (c#) | Microsoft Docs
author: wenz
description: Il controllo di animazione in ASP.NET AJAX Control Toolkit non è semplicemente un controllo, ma un intero framework aggiungere animazioni a un controllo. Se un'animazione è...
ms.author: riande
ms.date: 06/02/2008
ms.assetid: b7a28c0d-efb9-443a-80a4-1a5ee54671cd
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/animation-depending-on-a-condition-cs
msc.type: authoredcontent
ms.openlocfilehash: e4705b6c590f153043082759f1269c8f2d927abe
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "41831312"
---
<a name="animation-depending-on-a-condition-c"></a>Animazione in base a una condizione (c#)
====================
da [Christian Wenz](https://github.com/wenz)

[Scaricare il codice](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation4.cs.zip) o [Scarica il PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation4CS.pdf)

> Il controllo di animazione in ASP.NET AJAX Control Toolkit non è semplicemente un controllo, ma un intero framework aggiungere animazioni a un controllo. Se viene eseguita un'animazione o non può dipendere anche una condizione sotto forma di codice JavaScript.


## <a name="overview"></a>Panoramica

Il controllo di animazione in ASP.NET AJAX Control Toolkit non è semplicemente un controllo, ma un intero framework aggiungere animazioni a un controllo. Se viene eseguita un'animazione o non può dipendere anche una condizione sotto forma di codice JavaScript.

## <a name="steps"></a>Passaggi

Prima di tutto, includere il `ScriptManager` nella pagina; quindi, la libreria ASP.NET AJAX viene caricata, rendendo possibile usare il Toolkit di controllo:

[!code-aspx[Main](animation-depending-on-a-condition-cs/samples/sample1.aspx)]

Verrà applicata l'animazione a un pannello del testo che presenta un aspetto simile al seguente:

[!code-aspx[Main](animation-depending-on-a-condition-cs/samples/sample2.aspx)]

Nella classe CSS associata per il pannello, definire un colore di sfondo interessante e anche impostare una larghezza fissa per il pannello:

[!code-css[Main](animation-depending-on-a-condition-cs/samples/sample3.css)]

Quindi, aggiungere il `AnimationExtender` alla pagina, fornendo un' `ID`, il `TargetControlID` attributo e l'obbligatoria `runat="server":`

[!code-aspx[Main](animation-depending-on-a-condition-cs/samples/sample4.aspx)]

All'interno di `<Animations>` nodo, usare `<OnLoad>` per eseguire le animazioni dopo la pagina è stata caricata completamente. Invece di uno delle animazioni, regolare il `<Condition>` entra in gioco l'elemento. Il codice JavaScript fornito come valore del `ConditionScript` attributo viene eseguito in fase di esecuzione. Se restituisce true, l'animazione viene eseguita, in caso contrario non. Il markup seguente fornisce due animazioni, ognuna di esse in esecuzione al 50% dei case al momento casuale. Poiché può esistere solo un'animazione all'interno `<OnLoad>`, i due `<Condition>` animazioni vengono unite mediante la `<Sequence>` elemento:

[!code-aspx[Main](animation-depending-on-a-condition-cs/samples/sample5.aspx)]

Si noti che il segno di minore (`<`) nel `ConditionScript` attributo deve essere sottoposto a escape (). Quando si esegue questo script, nessuna animazione viene eseguito, o uno dei due viene, o eseguire entrambe.


[![Il pannello è dissolvenza in uscita senza alcun ridimensionamento, in modo che non ha esecuzioni animazione secondo, il primo elemento](animation-depending-on-a-condition-cs/_static/image2.png)](animation-depending-on-a-condition-cs/_static/image1.png)

Il pannello è dissolvenza in uscita senza alcun ridimensionamento, in modo che non ha esecuzioni animazione secondo, il primo elemento ([fare clic per visualizzare l'immagine con dimensioni normali](animation-depending-on-a-condition-cs/_static/image3.png))

> [!div class="step-by-step"]
> [Precedente](executing-several-animations-after-each-other-cs.md)
> [Successivo](picking-one-animation-out-of-a-list-cs.md)
