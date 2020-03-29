---
title: Moderálási felülvizsgálatok létrehozása a REST API konzollal – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Az Azure Content Moderator API-k használatával kép- vagy szöveges felülvizsgálatokat hozhat létre az emberi moderáláshoz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757142"
---
# <a name="create-human-reviews-rest"></a>Emberi vélemények létrehozása (REST)

[Vélemények](./review-api.md#reviews) tárolja és megjeleníti a tartalmat az emberi moderátorok, hogy értékelje. Amikor egy felhasználó befejezi az áttekintést, az eredmények egy adott visszahívási végpontra kerülnek. Ebben az útmutatóban megtudhatja, hogyan állíthatja be az értékeléseket az API-konzolon keresztül az értékelés REST API-k használatával. Miután megértette az API-k szerkezetét, könnyedén átviheti ezeket a hívásokat bármely REST-kompatibilis platformra.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be vagy hozzon létre fiókot a [Tartalommoderátor-ellenőrzési eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.

## <a name="create-a-review"></a>Vélemény létrehozása

Vélemény létrehozásához lépjen a **[Véleményezés - API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** referencialapjára, és válassza ki a kulcsrégió gombját (ezt a Végpont URL-címében, a Véleményezés [eszköz](https://contentmoderator.cognitive.microsoft.com/)Hitelesítő **adatok** lapján találja). Ez elindítja az API-konzolt, ahol könnyedén hozhat létre és futtathat REST API-hívásokat.

![Véleményezés – Régiókiválasztása beszerezni](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST-hívás paramétereinek megadása

Adja meg a **teamName**és az **Ocp-Apim-Subscription-Key értékét:**

- **teamName**: Az a csapatazonosító, amelyet a [Véleményezés eszközfiók](https://contentmoderator.cognitive.microsoft.com/) beállításakor hozott létre (amely a Véleményezés eszköz Hitelesítő adatai képernyőjén az **Azonosító** mezőben található).
- **Ocp-Apim-Subscription-Key**: A tartalommoderátor kulcs. Ezt a [Véleményezés eszköz](https://contentmoderator.cognitive.microsoft.com) **Beállítások** lapján találja.

### <a name="enter-a-review-definition"></a>Véleményezési definíció megadása

A **Kérelem törzse** mező szerkesztésével írja be a JSON-kérelmet a következő mezőkkel:

- **Metaadatok:** Egyéni kulcs-érték párok vissza kell adni a visszahívási végpontra. Ha a kulcs a [Véleményezés eszközben](https://contentmoderator.cognitive.microsoft.com)definiált rövid kód, akkor címkeként jelenik meg.
- **Tartalom**: Kép és videó tartalom esetén ez egy URL-karakterlánc, amely a tartalomra mutat. Szöveges tartalom esetén ez a tényleges szöveges karakterlánc.
- **ContentId**: Egyéni azonosítókarakterlánc. Ezt a karakterláncot a rendszer továbbítja az API-nak, és a visszahíváson keresztül adja vissza. A belső azonosítók vagy metaadatok és a moderálási feladat eredményeinek társítása hasznos lehet.
- **CallbackEndpoint**: (Nem kötelező) A visszahívási információk fogadásához szükséges URL-cím, amikor az ellenőrzés befejeződött.

Az alapértelmezett kérelemtörzs példákat mutat be a létrehozható különböző típusú értékelésekre:

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

### <a name="submit-your-request"></a>Küldje el kérését
  
Válassza a **Küldés**lehetőséget. Ha a művelet sikeres, a `200 OK`Válasz **állapota** a , és a **Válasz tartalommezője** azonosítót jelenít meg az ellenőrzéshez. Másolja az azonosítót a következő lépésekben való használatra.

![Véleményezés – A konzolválasz tartalomának létrehozása mező az ellenőrzési azonosítót jeleníti meg](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Az új felülvizsgálat vizsgálata

A [Véleményezés eszközben](https://contentmoderator.cognitive.microsoft.com)válassza a > **Image**/**Képszövegvideó**/**Video** **áttekintése**lehetőséget (a használt tartalomtól függően). A feltöltött tartalomnak meg kell jelennie, készen az emberi ellenőrzésre.

![Review eszköz képe egy focilabda](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Véleményezési részletek beszerezni

Egy meglévő ellenőrzés részleteinek lekéréséhez lépjen a [Véleményezés – API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) referencialapjára, és válassza ki a régió (a kulcs felügyeletének régiójában) gombját.

![Munkafolyamat – Régiókijelölés beszereznie](images/test-drive-region.png)

Adja meg a REST hívás paramétereit a fenti szakaszban. Ehhez a lépéshez **a véleményezési** azonosító az az egyedi azonosítókarakterlánc, amelyet az ellenőrzés létrehozásakor kapott.

![Véleményezés – Konzol létrehozása Eredmények beszerezése](images/test-drive-review-3.PNG)
  
Válassza a **Küldés**lehetőséget. Ha a művelet sikeres, a `200 OK`Válasz **állapota** a , és a **Válasz tartalom** mezőjének az ellenőrzés részleteit JSON formátumban jeleníti meg, például a következők:

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

Vegye figyelembe a válasz következő mezőit:

- **Állapot**
- **recécÉcímkék:** Ez akkor jelenik meg, ha az emberi felülvizsgálati csoport manuálisan adta hozzá a címkéket (a **createdBy** mező látható).
- **metaadatok**: Ez azokat a címkéket jeleníti meg, amelyeket eredetileg hozzáadott az ellenőrzéssorán, mielőtt az emberi felülvizsgálati csoport módosításokat hajtott volna végre.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan hozhat létre tartalommoderálási felülvizsgálatokat a REST API használatával. Ezután integrálja az értékeléseket egy végpontok között moderálási forgatókönyvbe, például az [e-kereskedelmi moderálási](./ecommerce-retail-catalog-moderation.md) oktatóanyagba.