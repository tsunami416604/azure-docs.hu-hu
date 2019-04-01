---
title: A REST API-konzol – a Content Moderator moderálás feladatok használata
titlesuffix: Azure Cognitive Services
description: A felülvizsgálati API-feladat műveletei segítségével teljes körű tartalom-jóváhagyás feladatokat az Azure Content Moderator vagy szöveges tartalom.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756095"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definiálja és moderálás feladatok (REST)

A moderálás feladat egy burkoló a tartalom-jóváhagyás, munkafolyamatok és értékelések funkciójának típusú funkcionál. Ez az útmutató bemutatja, hogyan használható a REST API-k feladat kezdeményezni, és ellenőrizze a tartalom-jóváhagyás feladatok. Miután megismerkedett az API-k szerkezete, egyszerűen az bármely REST-kompatibilis platform hívásokat a portot is.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/) hely.
- (Nem kötelező) [Egy egyéni munkafolyamatokat](./Review-Tool-User-Guide/Workflows.md) a feladatot; használata is használhatja az alapértelmezett munkafolyamat.

## <a name="create-a-job"></a>Feladat létrehozása

Moderálás feladat létrehozásához nyissa meg a [feladat – létrehozása](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API lapra hivatkozik, és válassza ki a gombot, a kulcs régiója (található ez a végpont URL-címe a a **hitelesítő adatok** lapján a [áttekintése eszköz](https://contentmoderator.cognitive.microsoft.com/)). Ezzel elindítja az API-konzol, ahol egyszerűen hozhat létre és futtassa a REST API-hívások.

![Feladat - létrehozása lap régió kiválasztása](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Adja meg REST-hívás paramétereit

Adja meg a REST-hívás létrehozásához a következő értékeket:

- **teamName**: A csoport azonosítója, amely beállításakor létrehozott a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/) fiók (található a **azonosító** mezőt a vizsgálóeszköz hitelesítő adatok képernyőn).
- **A ContentType**: Ez lehet "Image", "Szöveg" vagy "A videó".
- **ContentId**: Egyéni azonosító karakterláncot. Ez a karakterlánc az API-nak átadott és a visszahívás keresztül visszaadott. Ez hasznos belső azonosítók vagy metaadat társít egy moderálás feladat eredményét.
- **workflowname**: A korábban létrehozott munkafolyamat (vagy az "alapértelmezett" alapértelmezett munkafolyamat) neve.
- **CallbackEndpoint**: (Nem kötelező) Visszahívás információk fogadását, a felülvizsgálat befejezésekor az URL-címe.
- **Ocp-Apim-Subscription-Key**: A Content Moderator kulcs. A megtalálhatja a **beállítások** lapján a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Adja meg a kérelem törzse

A REST-hívást törzse tartalmazza a egy mezőben **ContentValue**. Illessze be a a nyers szöveg tartalmat, ha meg van szöveg moderálása, vagy adja meg egy kép vagy videó URL-címe, ha Ön éppen moderálása képet vagy videót. Használhatja az alábbi minta URL-címe: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Feladat - konzol lekérdezési paramétereket, fejléceket és kérelem törzse mezőbe létrehozása](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>A kérelem beküldése

Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **válasz állapota** van `200 OK`, és a **válasz tartalma** jelenít meg a feladat Azonosítóját. Másolja ezt az Azonosítót a következő lépésekben használni.

![Tekintse át,-konzol válasz content jelenít meg a felülvizsgálati azonosító létrehozása](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Feladat állapotának beolvasása

Az állapot és a egy futó vagy befejezett feladat részleteit, keresse fel a [feladat – az első](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API lapra hivatkozik, és válassza ki a gombot az Ön régiójában (a régiót, amelyben a kulcsát felügyelete).

![Feladat - Get-régió kiválasztása](images/test-drive-region.png)

Adja meg a REST-hívás paramétereket, mint a fenti szakaszban. Ehhez a lépéshez **JobId** a feladat létrehozásakor kapott egyedi azonosító karakterlánc. Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **válasz állapota** van `200 OK`, és a **válasz tartalma** mező megjeleníti a feladat JSON formátumban, például a következőképpen:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Feladat - Get REST hívást válasz](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Vizsgálja meg az új review(s)

Ha a tartalom feladat eredményezett a felülvizsgálat létrehozása, megtekintheti a a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com). Válassza ki **felülvizsgálati** > **kép**/**szöveg**/**videó** (attól függően, hogy tartalmakat a használt). A tartalmat meg kell jelennie, készen áll az emberi ellenőrző. Miután egy emberi moderátor ellenőrzi a hozzárendelt címkék és előrejelzési adatokat, és elküldi a végső jóváhagyás döntés, a feladatok API küldi el összes ezt az információt a kijelölt visszahívási végpont végpont.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban útmutatóból megtudhatta, hogyan hozhat létre, és a REST API-val tartalom-jóváhagyás feladatok lekérdezése. Ezután integrálása feladatok egy teljes körű moderálás forgatókönyvben például a [E-kereskedelmi moderálás](./ecommerce-retail-catalog-moderation.md) oktatóanyag.