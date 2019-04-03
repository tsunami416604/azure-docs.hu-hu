---
title: Jóváhagyás felülvizsgálatok létrehozása a REST API-konzol - Content Moderator
titlesuffix: Azure Cognitive Services
description: Az Azure Content Moderator felülvizsgálati API-k használatával hozzon létre vagy szöveges felülvizsgálatának emberi moderálás.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 254269ccedc92b9dfc164cc4665a8a8513682773
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882014"
---
# <a name="create-human-reviews-rest"></a>Hozzon létre az emberi ellenőrzések (REST)

[Értékelések](./review-api.md#reviews) tárolja, és az emberi moderátorok mérje fel a tartalom megjelenítése. Amikor egy felhasználó a felülvizsgálat befejeződött, az eredményeket egy megadott visszahívási végpont érkeznek. Ebben az útmutatóban megismerheti, hogyan állítható be a felülvizsgálatot REST API-k segítségével az API-konzol használatával felülvizsgálatok lesz. Miután megismerkedett az API-k szerkezete, egyszerűen az bármely REST-kompatibilis platform hívásokat a portot is.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/) hely.

## <a name="create-a-review"></a>Felülvizsgálat létrehozása

Felülvizsgálat létrehozása, nyissa meg a **[áttekintése – létrehozása](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API lapra hivatkozik, és válassza ki a gombot a kulcsfontosságú terület (található ez a végpont URL-címe a a **hitelesítő adatok** lap az a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/)). Ezzel elindítja az API-konzol, ahol egyszerűen hozhat létre és futtassa a REST API-hívások.

![Felülvizsgálat - Get-régió kiválasztása](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Adja meg REST-hívás paramétereit

Adjon meg értéket a **teamName**, és **Ocp-Apim-Subscription-Key**:

- **teamName**: A csoport azonosítója, amely beállításakor létrehozott a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/) fiók (található a **azonosító** mezőt a vizsgálóeszköz hitelesítő adatok képernyőn).
- **Ocp-Apim-Subscription-Key**: A Content Moderator kulcs. A megtalálhatja a **beállítások** lapján a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Adja meg a felülvizsgálati definíciója

Szerkessze a **kérelem törzse** mezőben adja meg a JSON-kérelem a következő mezőket:

- **metaadatok**: Egyéni kulcs-érték párokat kell visszaadni a visszahívás-végpontra. Ha a kulcs-e egy meghatározott rövid kód a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com), egy címke jelenik meg.
- **Tartalom**: Képi és videótartalmak esetében ez az egy URL-karakterlánc, a tartalom mutat. Szöveges tartalom esetén ez nem a tényleges szöveges karakterlánc.
- **ContentId**: Egyéni azonosító karakterláncot. Ez a karakterlánc az API-nak átadott és a visszahívás keresztül visszaadott. Ez hasznos belső azonosítók vagy metaadat társít egy moderálás feladat eredményét.
- **CallbackEndpoint**: (Nem kötelező) Visszahívás információk fogadását, a felülvizsgálat befejezésekor az URL-címe.

Az alapértelmezett kéréstörzs felülvizsgálatok hozhat létre a különböző típusú példákat mutat be:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>A kérelem beküldése
  
Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **válasz állapota** van `200 OK`, és a **válasz tartalma** jelenít meg a felülvizsgálati Azonosítóját. Másolja ezt az Azonosítót a következő lépésekben használni.

![Tekintse át,-konzol válasz content jelenít meg a felülvizsgálati azonosító létrehozása](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Vizsgálja meg az új felülvizsgálat

Az a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com), jelölje be **felülvizsgálati** > **kép**/**szöveg** / **Videó** (attól függően, milyen tartalmat használta). A feltöltött tartalmat meg kell jelennie, készen áll a emberi vizsgálóeszközt.

![Tekintse át a foci golyó eszköz képe](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Felülvizsgálati részletek

Egy meglévő áttekintése részleteinek beolvasása, lépjen a [áttekintése – lekérése](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API lapra hivatkozik, és válassza ki a gombot az Ön régiójában (a régiót, amelyben a kulcsát felügyelete).

![A munkafolyamat - Get-régió kiválasztása](images/test-drive-region.png)

Adja meg a REST-hívás paramétereket, mint a fenti szakaszban. Ehhez a lépéshez **reviewId** a felülvizsgálat létrehozásakor kapott egyedi azonosító karakterlánc.

![Tekintse át – a Get-eredményeket hozhatnak létre a konzol](images/test-drive-review-3.PNG)
  
Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **válasz állapota** van `200 OK`, és a **válasz tartalma** box JSON formátumban, például a következőképpen felülvizsgálati részleteit jeleníti meg:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Jegyezze fel a válasz a következő mezőket:

- **status**
- **reviewerResultTags**: Ez jelenik meg, ha bármely címke manuálisan lettek hozzáadva az emberi ellenőrző csapat (látható a **createdBy** mezőben).
- **metaadatok**: Ez azt mutatja, hogy kezdetben a felülvizsgálatot, az emberi ellenőrző csapat végzett módosítások előtti verzióban lettek hozzáadva a címkék.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan hozhat létre a tartalom-jóváhagyás felülvizsgálatok a REST API használatával. Ezután integrálása felülvizsgálatok egy teljes körű moderálás forgatókönyvben például a [E-kereskedelmi moderálás](./ecommerce-retail-catalog-moderation.md) oktatóanyag.