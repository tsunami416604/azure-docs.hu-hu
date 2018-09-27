---
title: Az API-konzol - Content Moderator a tartalom-jóváhagyás feladatok futtatása
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan tartalom-jóváhagyás feladatok futtatása az API-konzolon.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: sajagtap
ms.openlocfilehash: 240b26cd86a6985825e3145c5bc43ef31524d7b7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227109"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Az API-konzolról moderálás feladat indítása

A felülvizsgálati API [feladat operations](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) vagy szöveges tartalom az Azure Content Moderator feladatok teljes körű tartalom-jóváhagyás kezdeményezése. 

A moderálás feladat megvizsgálja a tartalom a tartalom Moderator Image moderálási API vagy a szöveg moderálási API használatával. Ezt követően a moderálás feladat lehet létrehozni a felülvizsgálatok a felülvizsgálati eszköz használja (a vizsgálóeszközt meghatározott) munkafolyamatok. 

Miután egy emberi moderátor ellenőrzi a hozzárendelt címkék és előrejelzési adatokat, és elküldi a végső jóváhagyás döntés, a felülvizsgálati API az API-végpont összes információt küldi el.

## <a name="prerequisites"></a>Előfeltételek

Keresse meg a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/). Iratkozzon fel, ha még nem meg még. A felülvizsgálati eszköz belül [egy egyéni munkafolyamatokat](Review-Tool-User-Guide/Workflows.md) használatára a jelen `Job` műveletet.

## <a name="use-the-api-console"></a>Az API-konzol használata
Próbálhatják ki őket az API-t az online konzol használatával, meg kell adnia a konzol néhány értékek van szükség:
    
- `teamName`: Használja a `Id` mezőt a vizsgálóeszköz hitelesítő adatok képernyőn. 
- `ContentId`: Ez a karakterlánc az API-nak átadott és a visszahívás keresztül visszaadott. **ContentId** akkor hasznos, ha társítása moderálás feladat az eredmények belső azonosítók vagy metaadat- `Workflowname`: nevére a [létrehozott munkafolyamat](Review-Tool-User-Guide/Workflows.md) az előző szakaszban.
- `Ocp-Apim-Subscription-Key`: Található meg a **beállítások** fülre. További információkért lásd: [áttekintése](overview.md).

Az API-hozzáférés konzol származik a **hitelesítő adatok** ablak.

### <a name="navigate-to-the-api-reference"></a>Keresse meg az API-referencia
Az a **hitelesítő adatok** ablakban válassza [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5).

  A `Job.Create` lap megnyitásakor.

### <a name="select-your-region"></a>Válassza ki a régiót
A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti.
  ![Feladat - létrehozása lap régió kiválasztása](images/test-drive-job-1.png)

  A `Job.Create` API-konzol megnyitása. 

### <a name="enter-parameters"></a>Paraméterek megadása

Adja meg a szükséges lekérdezési paramétereket, és az előfizetési kulcs értékét. Az a **kérelem törzse** adja meg a vizsgálni kívánt információk helyét. Ebben a példában használjuk ezt [képet](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Feladat - konzol lekérdezési paramétereket, fejléceket és kérelem törzse mezőbe létrehozása](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>A kérelem beküldése
Kattintson a **Küldés** gombra. Létrejön egy feladat azonosítója. Másolja ezt a következő lépésekben használni.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Nyissa meg az első feladat részleteit megjelenítő oldalon
Válassza ki **első**, majd nyissa meg az API-t, amely megfelel az Ön régiójában gomb kiválasztásával.

  ![Feladat - eredményeket hozhatnak létre konzol Get](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Tekintse át a válasz

Adjon meg értéket a **teamName** és **JobID**. Adja meg az előfizetési kulcs, és válassza ki **küldése**. A következő választ látható minta feladat állapotának és részleteinek megtekintéséhez.

```
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": 
        [
            {
            "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
            }
        ]
    }
```

## <a name="navigate-to-the-review-tool"></a>A felülvizsgálati eszköz
A Content Moderator irányítópultján válassza ki a **felülvizsgálati** > **lemezkép**. A vizsgált kép akkor jelenik meg, emberi felülvizsgálati kész.

  ![Tekintse át a három kerékpárosok eszköz képe](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>További lépések

A REST API használata a kódban, vagy kezdje a [feladatok .NET – rövid útmutató](moderation-jobs-quickstart-dotnet.md) integrálhatja az alkalmazást.
