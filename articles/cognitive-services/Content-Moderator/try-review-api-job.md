---
title: Moderálási feladatok használata a REST API konzollal – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Az API-ellenőrzés feladatműveleteivel teljes körű tartalommoderálási feladatokat kezdeményezhet az Azure Content Moderator szolgáltatásban a kép- vagy szöveges tartalomhoz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72935941"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Moderálási feladatok definiálása és használata (REST)

A moderálási feladat egyfajta burkolóként szolgál a tartalommoderálás, a munkafolyamatok és az értékelések funkcióihoz. Ez az útmutató bemutatja, hogyan használhatja a REST API-k feladathasználatával a tartalommoderálási feladatok kezdeményezéséhez és ellenőrzéséhez. Miután megértette az API-k szerkezetét, könnyedén átviheti ezeket a hívásokat bármely REST-kompatibilis platformra.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be vagy hozzon létre fiókot a [Tartalommoderátor-ellenőrzési eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.
- (Nem kötelező) [Egyéni munkafolyamat definiálása](./Review-Tool-User-Guide/Workflows.md) a feladathoz; az alapértelmezett munkafolyamatot is használhatja.

## <a name="create-a-job"></a>Feladat létrehozása

Moderálási feladat létrehozásához lépjen a [Feladat - API-hivatkozási](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) lapra, és válassza ki az előfizetési régió gombját (ezt a Végpont URL-címében találja a [Véleményezés eszköz](https://contentmoderator.cognitive.microsoft.com/) **Hitelesítő adatok** lapján). Ez elindítja az API-konzolt, ahol könnyedén hozhat létre és futtathat REST API-hívásokat.

![Feladat – lapterület kijelölésének létrehozása](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>REST-hívás paramétereinek megadása

A REST-hívás létrehozásához adja meg a következő értékeket:

- **teamName**: Az a csapatazonosító, amelyet a [Véleményezés eszközfiók](https://contentmoderator.cognitive.microsoft.com/) beállításakor hozott létre (amely a Véleményezés eszköz Hitelesítő adatai képernyőjén az **Azonosító** mezőben található).
- **ContentType**: Ez lehet "Kép", "Szöveg" vagy "Videó".
- **ContentId**: Egyéni azonosítókarakterlánc. Ezt a karakterláncot a rendszer továbbítja az API-nak, és a visszahíváson keresztül adja vissza. A belső azonosítók vagy metaadatok és a moderálási feladat eredményeinek társítása hasznos lehet.
- **Munkafolyamatneve**: A korábban létrehozott munkafolyamat neve (vagy az alapértelmezett munkafolyamat "alapértelmezett" neve).
- **CallbackEndpoint**: (Nem kötelező) A visszahívási információk fogadásához szükséges URL-cím, amikor az ellenőrzés befejeződött.
- **Ocp-Apim-Subscription-Key**: A tartalommoderátor kulcs. Ezt a [Véleményezés eszköz](https://contentmoderator.cognitive.microsoft.com) **Beállítások** lapján találja.

### <a name="fill-in-the-request-body"></a>Töltse ki a kérelem törzsét

A REST-hívás törzse egy **ContentValue**mezőt tartalmaz. Ha módosítja a szöveget, illessze be a nyers szövegtartalmát, vagy adjon meg egy képet vagy videót, ha moderálja a képet/videót. A következő mintakép URL-címét használhatja:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Feladat – Konzollekérdezési paraméterek, fejlécek és törzskérés létrehozása mező](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Küldje el kérését

Válassza a **Küldés**lehetőséget. Ha a művelet sikeres, a `200 OK`Válasz **állapota** a , és a **Válasz tartalommezője** azonosítót jelenít meg a feladathoz. Másolja az azonosítót a következő lépésekben való használatra.

![Véleményezés – A konzolválasz tartalomának létrehozása mező az ellenőrzési azonosítót jeleníti meg](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Állásállapotának beszereznie

Egy futó vagy befejezett feladat állapotának és részleteinek lehívásához lépjen a Feladat – API referencialap [ra,](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) és válassza ki a régió (a kulcs felügyeletének régiójában) gombját.

![Feladat - Régió kiválasztása](images/test-drive-region.png)

Adja meg a REST hívás paramétereit a fenti szakaszban. Ehhez a lépéshez a **JobId** az a egyedi azonosítókarakterlánc, amelyet a feladat létrehozásakor kapott. Válassza a **Küldés**lehetőséget. Ha a művelet sikeres, a `200 OK`Válasz **állapota** a , és a **Válasz tartalom** mezője JSON formátumban jeleníti meg a feladatot, a következőkhöz hasonlóan:

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

![Feladat - REST-hívás válaszának behívása](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Az új felülvizsgálat(ok) vizsgálata

Ha a tartalomfeladat értékelést eredményezett, a [Véleményezés eszközben](https://contentmoderator.cognitive.microsoft.com)tekintheti meg. Válassza **a** > **Képszöveg**/**Text**/**videó** megtekintése lehetőséget (a használt tartalomtól függően). A tartalomnak meg kell jelennie, készen az emberi felülvizsgálatra. Miután egy emberi moderátor áttekinti az automatikusan hozzárendelt címkéket és előrejelzési adatokat, és elküldi a végső moderálási döntést, a feladatok API elküldi az összes ilyen információt a kijelölt visszahívási végpont végpont.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan hozhat létre és kérdezheti le a tartalommoderálási feladatokat a REST API használatával. Ezután integrálja a feladatokat egy végpontok között moderálási forgatókönyvbe, például az [e-kereskedelmi moderálási](./ecommerce-retail-catalog-moderation.md) oktatóanyagba.