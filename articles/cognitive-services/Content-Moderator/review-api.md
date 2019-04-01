---
title: Értékelések, munkafolyamatok, és a feladatok alapelvei – a Content Moderator
titlesuffix: Azure Cognitive Services
description: Ellenőrzések, a munkafolyamatok és a feladatok ismertetése
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756296"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Tartalom-jóváhagyás ellenőrzések, a munkafolyamatok és a feladatok

A Content Moderator a gépi támogatású moderálását az emberi hurok-képességeivel létrehozhat egy optimális moderálási folyamat a valós életből vett egyesíti. A felhő alapú keresztül teszi ezt [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com). Ez az útmutató azt ismerteti, a felülvizsgálati eszköz alapfogalmakat: ellenőrzések, a munkafolyamatok és a feladatok.

## <a name="reviews"></a>Felülvizsgálatok

Tekintsük át a tartalmat töltenek fel a vizsgálóeszközt és alatt jelenik meg a **tekintse át** fülre. Itt a felhasználók az alkalmazott címkék alter és a saját egyéni címkével lát, szükség szerint. Amikor egy felhasználó beküld egy, az eredményeket egy megadott visszahívási végpont érkeznek, és a tartalmat a webhely törlődik.

![Felülvizsgálati eszköz webhely nyisson meg egy böngészőben a áttekintése lapon](./Review-Tool-user-Guide/images/image-workflow-review.png)

Lásd: a [felülvizsgálati eszköz útmutató](./review-tool-user-guide/review-moderated-images.md) felülvizsgálatok létrehozásának első lépései, vagy tekintse meg a [REST API-útmutató](./try-review-api-review.md) megtudhatja, hogyan ehhez programozott módon.

## <a name="workflows"></a>Munkafolyamatok

Egy munkafolyamat, felhőalapú testre szabott szűrheti a tartalom. A munkafolyamatok számos különböző módon tartalom szűrése és a megfelelő a megfelelő műveletet szolgáltatás képes csatlakozni. A Content Moderator összekötőjével munkafolyamat automatikusan moderálás címkékkel és hozhat létre értékelések elküldött tartalommal.

### <a name="view-workflows"></a>A munkafolyamatok megtekintése

A meglévő munkafolyamatokba megtekintéséhez nyissa meg a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/) válassza **beállítások** > **munkafolyamatok**.

![Alapértelmezett munkafolyamat](images/default-workflow-listed.PNG)

A munkafolyamatok JSON karakterláncként, ami lehetővé teszi számukra elérhető programozott módon teljesen leírását is. Ha a **szerkesztése** a munkafolyamathoz lehetőséget, majd válassza ki a **JSON** lapon látni fog egy JSON-kifejezést a következőhöz hasonló:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Lásd: a [felülvizsgálati eszköz útmutató](./review-tool-user-guide/workflows.md) munkafolyamatok létrehozása és használata első lépései, vagy tekintse meg a [REST API-útmutató](./try-review-api-workflow.md) megtudhatja, hogyan ehhez programozott módon.

## <a name="jobs"></a>Feladatok

A moderálás feladat egy burkoló a tartalom-jóváhagyás, munkafolyamatok és értékelések funkciójának típusú funkcionál. A feladat megvizsgálja a tartalom a Content Moderator képmoderálás API-t vagy szöveges moderálási API-t, és azután ellenőrzi a kijelölt munkafolyamat ellen. A munkafolyamat eredményei alapján lehet, hogy vagy a tartalom vonatkozó felülvizsgálat nem hozható létre a [vizsgálóeszköz](./review-tool-user-guide/human-in-the-loop.md). Értékelések és a munkafolyamatok lehet létrehozni és konfigurálni a megfelelő API-k, amíg a feladat API megszerzését teszi lehetővé egy részletes jelentés a teljes folyamat (amely küldhető a megadott visszahívás-végponthoz).

Tekintse meg a [REST API-val útmutató](./try-review-api-job.md) feladatok használatának megkezdéséhez.

## <a name="next-steps"></a>További lépések

* Próbálja ki a [feladat API-konzol](try-review-api-job.md), és használja a REST API-Kódminták. Ha ismeri a Visual Studio és C#, is tekintse meg a [feladatok .NET – rövid útmutató](moderation-jobs-quickstart-dotnet.md). 
* Az értékelések, használatának első lépései a [felülvizsgálati API-konzol](try-review-api-review.md), és használja a REST API-Kódminták. Ekkor megjelenik a [felülvizsgálatok .NET – rövid útmutató](moderation-reviews-quickstart-dotnet.md).
* A videót felülvizsgálatok, használja a [tekintse át a rövid videó](video-reviews-quickstart-dotnet.md), és ismerje meg, hogyan [átiratok a videó felülvizsgálat hozzáadása](video-transcript-reviews-quickstart-dotnet.md).
