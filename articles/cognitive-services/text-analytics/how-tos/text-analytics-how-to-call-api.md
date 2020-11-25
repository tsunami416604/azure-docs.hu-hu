---
title: Szövegelemzési API hívása
titleSuffix: Azure Cognitive Services
description: Ez a cikk azt ismerteti, hogyan hívható meg az Azure Cognitive Services Text Analytics REST API és a Poster.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 2977946b2e1f37aa356ee075d2caac237170df0f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "95993330"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>A Text Analytics meghívása REST API

Ebben a cikkben a Text Analytics REST API és a [Poster](https://www.postman.com/downloads/) használatával mutatjuk be a főbb fogalmakat. Az API számos szinkron és aszinkron végpontot biztosít a szolgáltatás funkcióinak használatához. 

## <a name="using-the-api-asynchronously"></a>Az API aszinkron használata

A 3.1-es verziótól kezdődően a Text Analytics API két aszinkron végpontot biztosít: 

* A `/analyze` text Analytics végpontja lehetővé teszi, hogy ugyanazokat a szöveges dokumentumokat elemezze egy API-hívásban több szöveges elemzési funkcióval. Korábban több szolgáltatás használatához külön API-hívásokat kell létrehoznia minden egyes művelethez. Ezt a képességet akkor érdemes figyelembe venni, ha több Text Analytics-funkcióval rendelkező dokumentumok nagy készleteit szeretné elemezni.

* A `/health` text Analytics for Health végpontja, amely a klinikai dokumentumokból kinyerheti és címkézheti a kapcsolódó egészségügyi adatokat.  

Az alábbi táblázatból megtudhatja, hogy mely szolgáltatásokat lehet aszinkron módon használni. Vegye figyelembe, hogy csak néhány funkció hívható meg a `/analyze` végpontról. 

| Funkció | Szinkron | Aszinkron |
|--|--|--|
| Nyelvfelismerés | ✔ |  |
| Hangulatelemzés | ✔ |  |
| Vélemény bányászata | ✔ |  |
| Kulcskifejezések kinyerése | ✔ | ✔* |
| Elnevezett entitások felismerése (beleértve a személyes és a PHI-t) | ✔ | ✔* |
| Text Analytics for Health (tároló) | ✔ |  |
| Text Analytics for Health (API) |  | ✔  |

`*` – Aszinkron módon a `/analyze` végponton keresztül.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>Előfeltételek


> [!NOTE]
> Ha a (z) vagy a végpontot szeretné használni, szüksége lesz egy Text Analytics-erőforrásra a standard (k) [árképzési szint](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) használatával `/analyze` `/health` .

1.  Először nyissa meg a [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) , és hozzon létre egy új Text Analytics-erőforrást, ha még nem rendelkezik ilyennel. Ha a vagy a végpontot szeretné használni, válassza a standard (S) árképzési szintet `/analyze` `/health` .

2.  Válassza ki azt a régiót, amelynek használni szeretné a végpontját.

3.  Hozza létre a Text Analytics-erőforrást, és lépjen a lap bal oldalán található kulcsok és végpont panelre. Másolja az API-k meghívásakor később használni kívánt kulcsot. Ezt később adja hozzá a fejléc értékeként `Ocp-Apim-Subscription-Key` .


<a name="json-schema"></a>

## <a name="api-request-format"></a>API-kérelem formátuma

#### <a name="synchronous"></a>[Szinkron](#tab/synchronous)

Az API-kérelmek formátuma megegyezik az összes szinkron művelet esetében. A dokumentumok egy JSON-objektumban nyers strukturálatlan szövegként lesznek elküldve. Az XML nem támogatott. A JSON-séma az alább ismertetett elemekből áll.

| Elem | Érvényes értékek | Kötelező? | Használat |
|---------|--------------|-----------|-------|
|`id` |Az adattípus karakterlánc, de a gyakorlatban a dokumentumok azonosítói általában egész számnak számítanak. | Kötelező | A rendszer az Ön által megadott azonosítókat használja a kimenet felépítéséhez. A kérelemben szereplő minden egyes AZONOSÍTÓhoz a nyelvi kódok, a legfontosabb kifejezések és a hangulati pontszámok jönnek létre.|
|`text` | Strukturálatlan nyers szöveg, legfeljebb 5 120 karakter hosszú lehet. | Kötelező | A nyelvfelismerés a szöveg bármilyen nyelven kifejezhető. Az érzelmek elemzéséhez, a fő kifejezés kinyeréséhez és az entitás azonosításához a szövegnek [támogatott nyelven](../language-support.md)kell lennie. |
|`language` | 2 karakteres [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) -kód [támogatott nyelvhez](../language-support.md) | Változó | Az érzelmek elemzéséhez, a kulcsfontosságú kifejezés kinyeréséhez és az entitások összekapcsolásához szükséges. a nyelvfelismerés nem kötelező. Nincs hiba, ha kizárják, de az elemzés a nélkül meggyengül. A nyelvi kódnak meg kell egyeznie az `text` Ön által megadott értékkel. |

A következő példa egy API-kérést mutat be a szinkron Text Analytics végpontok számára. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="analyze"></a>[Elemzés](#tab/analyze)

> [!NOTE]
> A Text Analytics ügyféloldali kódtár legújabb előzetes verziója lehetővé teszi az aszinkron elemzési műveletek meghívását egy ügyfél-objektum használatával. Példákat a GitHubon talál:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

A `/analyze` végpont lehetővé teszi, hogy kiválassza, hogy a támogatott Text Analytics mely funkciókat szeretné használni egyetlen API-hívásban. Ez a végpont jelenleg a következőket támogatja:

* fő kifejezés kibontása 
* Elnevezett entitások felismerése (beleértve a személyes és a PHI-t)

| Elem | Érvényes értékek | Kötelező? | Használat |
|---------|--------------|-----------|-------|
|`displayName` | Sztring | Választható | Az egyedi azonosító megjelenítendő neveként használja a feladathoz.|
|`analysisInput` | Az `documents` alábbi mezőt tartalmazza | Kötelező | A elküldeni kívánt dokumentumok információit tartalmazza. |
|`documents` | Az `id` alábbi és `text` mezőket tartalmazza | Kötelező | Az elküldött dokumentumok adatait és a dokumentum nyers szövegét tartalmazza. |
|`id` | Sztring | Kötelező | Az Ön által megadott azonosítók a kimenet strukturálása céljából használatosak. |
|`text` | Strukturálatlan nyers szöveg, legfeljebb 125 000 karakter hosszú lehet. | Kötelező | Angol nyelven kell lennie, amely jelenleg csak az egyetlen támogatott nyelv. |
|`tasks` | A a következő Text Analytics funkciókat tartalmazza `entityRecognitionTasks` : `keyPhraseExtractionTasks` vagy `entityRecognitionPiiTasks` . | Kötelező | Egy vagy több használni kívánt Text Analytics-szolgáltatás. Vegye figyelembe, hogy a (z) és a (z `entityRecognitionPiiTasks` ) választható paramétere nem lehet `domain` `pii` `phi` . Ha nincs megadva, a rendszer alapértelmezés szerint a következőt adja meg: `pii` . |
|`parameters` | Az `model-version` alábbi és `stringIndexType` mezőket tartalmazza | Kötelező | Ez a mező a fenti szolgáltatás kiválasztott feladatai között szerepel. A használni kívánt modell verziójával és az index típusával kapcsolatos információkat tartalmaznak. |
|`model-version` | Sztring | Kötelező | Itt adhatja meg, hogy a rendszer melyik verzióját kívánja használni.  |
|`stringIndexType` | Sztring | Kötelező | Adja meg a programozási környezetnek megfelelő szöveges dekódert.  Támogatott típusok: `textElement_v8` (alapértelmezett), `unicodeCodePoint` , `utf16CodeUnit` . További információért tekintse meg a [Szöveg eltolása című cikket](../concepts/text-offsets.md#offsets-in-api-version-31-preview) .  |
|`domain` | Sztring | Választható | Csak a feladathoz tartozó paraméterként érvényes, és beállítható a következőre: `entityRecognitionPiiTasks` `pii` vagy `phi` . Ha nincs megadva, az alapértelmezett érték `pii` .  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

#### <a name="text-analytics-for-health"></a>[Egészségügyi Text Analytics](#tab/health)

Az API-kérelmek formátuma az állapotba helyezett API-hoz Text Analytics a tárolóhoz. A dokumentumok egy JSON-objektumban nyers strukturálatlan szövegként lesznek elküldve. Az XML nem támogatott. A JSON-séma az alább ismertetett elemekből áll.  Kérjük, töltse ki és küldje el a [Cognitive Services kérelem űrlapját](https://aka.ms/csgate) , hogy hozzáférést kérjen a Text Analytics for Health nyilvános előzetes verziójához. Az állapotfigyelő Text Analyticsért nem számítunk fel díjat. 

| Elem | Érvényes értékek | Kötelező? | Használat |
|---------|--------------|-----------|-------|
|`id` |Az adattípus karakterlánc, de a gyakorlatban a dokumentumok azonosítói általában egész számnak számítanak. | Kötelező | A rendszer az Ön által megadott azonosítókat használja a kimenet felépítéséhez. |
|`text` | Strukturálatlan nyers szöveg, legfeljebb 5 120 karakter hosszú lehet. | Kötelező | Vegye figyelembe, hogy jelenleg csak az angol nyelvű szöveg támogatott. |
|`language` | 2 karakteres [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) -kód [támogatott nyelvhez](../language-support.md) | Kötelező | `en`Jelenleg csak a támogatott. |

Az alábbi példa egy API-kérést mutat be az Text Analytics for Health végpontok számára. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Az adatoknak a Text Analytics APIba való küldésével kapcsolatos díjakról és méretekről az [adatokra és a díjszabásra vonatkozó korlátozásokat](../concepts/data-limits.md) ismertető cikkben olvashat.


## <a name="set-up-a-request"></a>Kérelem beállítása 

A poster (vagy egy másik webes API-tesztelési eszköz) területen adja hozzá a használni kívánt szolgáltatás végpontját. Az alábbi táblázat segítségével keresse meg a megfelelő végpont-formátumot, és cserélje le az `<your-text-analytics-resource>` erőforrás-végpontra. Például:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Szinkron](#tab/synchronous)

| Funkció | Kérelemtípus | Erőforrás-végpontok |
|--|--|--|
| Nyelvfelismerés | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Hangulatelemzés | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Vélemény bányászata | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Kulcskifejezések kinyerése | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Nevesített entitások felismerése – általános | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Elnevezett entitások felismerése – személyes adatok | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Nevesített entitások felismerése – PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="analyze"></a>[Elemzés](#tab/analyze)

| Funkció | Kérelemtípus | Erőforrás-végpontok |
|--|--|--|
| Elemzési feladatok elküldése | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| Elemzési állapot és eredmények beolvasása | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

#### <a name="text-analytics-for-health"></a>[Egészségügyi Text Analytics](#tab/health)

| Funkció | Kérelemtípus | Erőforrás-végpontok |
|--|--|--|
| Text Analytics beküldése a Health-feladatokhoz  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| Feladatok állapotának és eredményeinek beolvasása | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| Feladat megszakítása | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

Miután elvégezte a végpontot, a poster (vagy egy másik webes API-tesztelési eszköz) használatával:

1. Válassza ki a használni kívánt szolgáltatáshoz tartozó kérés típusát.
2. Illessze be a kívánt művelet végpontját a fenti táblázatból.
3. A három kérelem fejlécének beállítása:

   + `Ocp-Apim-Subscription-Key`: az Azure Portalból beszerzett hozzáférési kulcs
   + `Content-Type`: alkalmazás/JSON
   + `Accept`: alkalmazás/JSON

    Ha a Poster-t használja, a kérelemnek a következő képernyőképhez hasonlóan kell kinéznie, a `/keyPhrases` végpontot feltételezve.
    
    ![Képernyőkép kérése a végpontról és a fejlécekről](../media/postman-request-keyphrase-1.png)
    
4. Válassza a **nyers** lehetőséget a **törzs** formátumához
    
    ![Képernyőkép kérése a törzs beállításaival](../media/postman-request-body-raw.png)

5. Illesszen be néhány JSON-dokumentumot érvényes formátumban. Használja a fentiekben ismertetett **API-kérelmek formátumát** , és további információkat és példákat a következő témakörökben talál:

      + [Nyelvfelismerés](text-analytics-how-to-language-detection.md)
      + [Kulcskifejezések kinyerése](text-analytics-how-to-keyword-extraction.md)
      + [Hangulat elemzése](text-analytics-how-to-sentiment-analysis.md)
      + [Entitások felismerése](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>A kérelem elküldése

Küldje el az API-kérést. Ha a hívást egy szinkron végpontra kezdeményezte, a válasz azonnal megjelenik egyetlen JSON-dokumentumként a kérelemben megadott összes dokumentum-AZONOSÍTÓhoz.

Ha kezdeményezte az aszinkron `/analyze` vagy a `/health` végpontok hívását, ellenőrizze, hogy kapott-e egy 202-es hibakódot. az eredmények megtekintéséhez választ kell kapnia:

1. Az API-válaszban keresse meg az `Operation-Location` elemet a fejlécből, amely az API-nak küldött feladatot azonosítja. 
2. Hozzon létre egy GET-kérést a használt végponthoz. a végpont formátumához tekintse meg a [fenti táblázatot](#set-up-a-request) , és tekintse át az [API-referenciák dokumentációját](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Például:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. Adja hozzá a `Operation-Location` -t a kérelemhez.

4. A válasz egyetlen JSON-dokumentum lesz, amely a kérelemben megadott összes dokumentum-AZONOSÍTÓhoz tartalmaz egy elemmel.

## <a name="example-api-responses"></a>API-válaszok – példa
 
# <a name="synchronous"></a>[Szinkron](#tab/synchronous)

A szinkron végpontokra adott válaszok a használt végponttól függően változnak. Tekintse meg a következő cikkeket, például a válaszokat.

+ [Nyelvfelismerés](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Kulcskifejezések kinyerése](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Hangulat elemzése](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Entitások felismerése](text-analytics-how-to-entity-linking.md#view-results)

# <a name="analyze"></a>[Elemzés](#tab/analyze)

Ha a művelet sikeres, a GET kérelem a `/analyze` végponthoz a hozzárendelt feladatokat tartalmazó objektumot ad vissza. Például: `keyPhraseExtractionTasks`. Ezek a feladatok tartalmazzák a válasz objektumot a megfelelő Text Analytics szolgáltatásból. További információt a következő cikkekben talál.

+ [Kulcskifejezések kinyerése](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Entitások felismerése](text-analytics-how-to-entity-linking.md#view-results)


```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```

# <a name="text-analytics-for-health"></a>[Egészségügyi Text Analytics](#tab/health)

A Text Analytics for Health aszinkron API-válaszról a következő cikkben talál további információt:

+ [Egészségügyi Text Analytics](text-analytics-for-health.md#hosted-asynchronous-web-api-response)


--- 

## <a name="see-also"></a>Lásd még

* [A Text Analytics áttekintése](../overview.md)
* [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
* [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712)
* [Az Text Analytics ügyféloldali kódtár használata](../quickstarts/text-analytics-sdk.md)
* [Újdonságok](../whats-new.md)
