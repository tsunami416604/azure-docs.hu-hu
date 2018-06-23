---
title: Tartalom moderálás feladatok futtatása a Azure tartalom moderátor |} Microsoft Docs
description: 'Útmutató: a tartalom moderálás feladatok futtatása az API-konzolon.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/03/2017
ms.author: sajagtap
ms.openlocfilehash: 6f741be1001ae70d5fdbf6f374204aaad1601abe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347011"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Az API-konzolról moderálás feladat indítása

A felülvizsgálati API Felületet használják [műveletek feladat](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) tartalom moderálás végpont feladatok az Azure tartalom moderátor kép vagy szöveges tartalom kezdeményezéséhez. 

A moderálás feladat ellenőrzi a tartalom a tartalom moderátor kép moderálás API-t vagy a szöveg moderálás API használatával. Ezt követően a moderálás feladat használják az (a felülvizsgálati eszköz meghatározott) munkafolyamatok létrehozásához értékelést a felülvizsgálati eszközben. 

Miután emberi Moderátorra ellenőrzi, hogy az automatikusan hozzárendelt címkék és előrejelzési adatokat, és elküldi a végleges moderálás döntést, tekintse át az API-t minden információt az API-végpont küldi el.

## <a name="prerequisites"></a>Előfeltételek

Keresse meg a [tekintse át az eszköz](https://contentmoderator.cognitive.microsoft.com/). Regisztráljon, ha még nem meg még. A felülvizsgálati eszköz belül [adja meg egy egyéni munkafolyamat](Review-Tool-User-Guide/Workflows.md) ezen használandó `Job` művelet.

## <a name="use-the-api-console"></a>Az API-konzollal
Az API-t test-drive az online konzol használatával, kell néhány értékeket kell megadnia a konzolhoz:
    
- `teamName`: Használja a `Id` mezőjét a felülvizsgálati eszköz hitelesítő adatok képernyőről. 
- `ContentId`: Ez a karakterlánc az API-nak átadott, és vissza a visszahívás keresztül. **ContentId** akkor hasznos, ha a belső azonosítók vagy metaadat társít a moderálás feladatokat. eredményeit- `Workflowname`: neve a [létrehozott munkafolyamat](Review-Tool-User-Guide/Workflows.md) az előző szakaszban.
- `Ocp-Apim-Subscription-Key`: Található meg a **beállítások** fülre. További információkért lásd: [áttekintése](overview.md).

Hozzáférés az API-t konzol származik a **hitelesítő adatok** ablak.

### <a name="navigate-to-the-api-reference"></a>Keresse meg az API-referencia
Az a **hitelesítő adatok** ablakban válassza ki [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5).

  A `Job.Create` lap megnyitásakor.

### <a name="select-your-region"></a>Válassza ki a régiót
A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét.
  ![Sikertelen feladat - terület kiválasztása lap létrehozása](images/test-drive-job-1.png)

  A `Job.Create` API-konzol megnyitása. 

### <a name="enter-parameters"></a>Paraméterek megadása

Adja meg a kötelező lekérdezési paramétereket, és az Előfizetés kulcs értékét. Az a **Request body** adja meg a vizsgálni kívánt információk helyét. Ehhez a példához tegyük ezzel [minta kép](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Sikertelen feladat - konzol lekérdezési paraméterek, a fejlécek és a kérelem törzsében mezőbe létrehozása](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Az igénylés küldéséhez
Kattintson a **Küldés** gombra. Egy feladat jön létre. Másolja a következő lépésekben használata.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Nyissa meg a Get Job részleteit megjelenítő oldalra
Válassza ki **beolvasása**, majd nyissa meg az API-t a gomb, amely megfelel a régió kiválasztásával.

  ![Feladat - konzol Get eredményeket létre](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Tekintse át a válasz

Adja meg az értékeket **teamName** és **JobID**. Adja meg az előfizetés-kulcsot, és válassza **küldése**. A következő válasz minta feladat állapotát és adatait jeleníti meg.

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

## <a name="navigate-to-the-review-tool"></a>Keresse meg a felülvizsgálati eszköz
A tartalom moderátor irányítópultra, válassza ki a **felülvizsgálati** > **kép**. A vizsgált jelenik meg, és készen áll a emberi áttekintése.

  ![Tekintse át a három kerékpárosok eszköz képe](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>További lépések

A REST API-t használja a kódban, vagy indítsa el a [feladatok .NET gyors üzembe helyezés](moderation-jobs-quickstart-dotnet.md) integrálása az alkalmazást.
