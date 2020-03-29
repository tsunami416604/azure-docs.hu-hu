---
title: Vélemények, munkafolyamatok és feladatok fogalmak – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben megismerheti a Felülvizsgálati eszköz alapfogalmait; véleményeket, munkafolyamatokat és feladatokat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221149"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Tartalommoderálási felülvizsgálatok, munkafolyamatok és feladatok

A Tartalommoderátor kombinálja a gépáltal támogatott moderálást a human-in-the-loop képességekkel, hogy optimális moderálási folyamatot hozzon létre a valós helyzetekben. Ezt a felhőalapú [felülvizsgálati eszközsegítségével](https://contentmoderator.cognitive.microsoft.com)teszi. Ebben az útmutatóban megismerheti a Véleményezés eszköz alapfogalmait: értékelések, munkafolyamatok és feladatok.

## <a name="reviews"></a>Felülvizsgálatok

Az ellenőrzés során a rendszer feltölti a tartalmat a Véleményezés eszközbe, és megjelenik a **Véleményezés** lapon. Innen a felhasználók módosíthatják az alkalmazott címkéket, és szükség szerint alkalmazhatják saját egyéni címkéiket. Amikor egy felhasználó beküldi az értékelést, az eredményeket a rendszer elküldi egy adott visszahívási végpontnak, és eltávolítja a tartalmat a webhelyről.

![Böngészőben megnyitott eszközwebhely áttekintése a Véleményezés lapon](./Review-Tool-user-Guide/images/image-workflow-review.png)

Tekintse meg a [Felülvizsgálati eszköz útmutatóját](./review-tool-user-guide/review-moderated-images.md) az értékelések létrehozásának megkezdéséhez, vagy tekintse meg a [REST API útmutatóját,](./try-review-api-review.md) amelyből megtudhatja, hogyan teheti ezt programozott módon.

## <a name="workflows"></a>Munkafolyamatok

A munkafolyamat egy felhőalapú, testre szabott tartalomszűrő. A munkafolyamatok különböző szolgáltatásokhoz kapcsolódhatnak, hogy különböző módokon szűrjék a tartalmat, majd megtegyékeljék a megfelelő lépéseket. A Tartalommoderátor-összekötővel a munkafolyamat automatikusan moderálási címkéket alkalmazhat, és véleményeket hozhat létre a beküldött tartalommal.

### <a name="view-workflows"></a>Munkafolyamatok megtekintése

A meglévő munkafolyamatok megtekintéséhez nyissa meg a [Véleményezés eszközt,](https://contentmoderator.cognitive.microsoft.com/) és válassza a **Beállítások** > **munkafolyamatok**lehetőséget.

![Alapértelmezett munkafolyamat](images/default-workflow-listed.PNG)

A munkafolyamatok teljes mértékben JSON-karakterláncként írhatók le, ami programozott módon hozzáférhetővé teszi őket. Ha a munkafolyamat **Szerkesztés e beállítását választja,** majd a **JSON lapot választja,** a következőjéhez hasonló JSON-kifejezés jelenik meg:

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

Tekintse meg a [Felülvizsgálati eszköz útmutatóját](./review-tool-user-guide/workflows.md) a munkafolyamatok létrehozásának és használatának megkezdéséhez, vagy tekintse meg a [REST API útmutatóját,](./try-review-api-workflow.md) amelyből megtudhatja, hogyan teheti ezt programozott módon.

## <a name="jobs"></a>Feladatok

A moderálási feladat egyfajta burkolóként szolgál a tartalommoderálás, a munkafolyamatok és az értékelések funkcióihoz. A feladat a Tartalommoderátor képmoderálási API-val vagy a szövegmoderálási API-val ellenőrzi a tartalmat, majd összeveti a kijelölt munkafolyamattal. A munkafolyamat eredményei alapján előfordulhat, hogy a [Véleményezés eszközben](./review-tool-user-guide/human-in-the-loop.md)felülvizsgálják a tartalmat. Bár a vélemények és a munkafolyamatok is létrehozhatók és konfigurálhatók a megfelelő API-kkal, a feladat API lehetővé teszi a teljes folyamat részletes jelentésének beszerzése (amely elküldhető egy adott visszahívási végpontra).

Tekintse meg a [REST API-útmutató a](./try-review-api-job.md) feladatok használatának megkezdéséhez.

## <a name="next-steps"></a>További lépések

* Tesztelje a [Feladat API-konzolt,](try-review-api-job.md)és használja a REST API-kódmintákat. Ha ismeri a Visual Studio és a C# alkalmazást, olvassa el a [Jobs .NET rövid útmutatót](moderation-jobs-quickstart-dotnet.md)is. 
* Az ellenőrzések, első lépések a [Felülvizsgálati API-konzol,](try-review-api-review.md)és használja a REST API-kód mintákat. Ezután tekintse meg a .NET rövid útmutató véleményezési [szakaszát.](dotnet-sdk-quickstart.md)
* Videó-véleményezések esetén használja a [Videó-áttekintés rövid útmutatóját,](video-reviews-quickstart-dotnet.md)és ismerje meg, hogyan [vehet fel átiratokat a videóellenőrzésbe.](video-transcript-reviews-quickstart-dotnet.md)
