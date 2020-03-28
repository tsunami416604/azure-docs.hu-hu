---
title: Hangulatelemzés végrehajtása a Text Analytics REST API-val
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan észlelheti a szövegben lévő hangulatot az Azure Cognitive Services Text Analytics REST API-val.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/09/2020
ms.author: aahi
ms.openlocfilehash: b3c112876bfd2578e6ebaa95c6902aa9b8f832d9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79203457"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Útmutató: Hangulatészlelése a Text Analytics API használatával

A Text Analytics API Hangulatelemzés funkciója kiértékeli a szöveget, és minden mondathoz visszaküldi a hangulatpontszámokat és a címkéket. Ez akkor hasznos, kimutatására pozitív és negatív hangulat a szociális média, az ügyfelek véleménye, vitafórumok és így tovább. Az API által használt AI-modelleket a szolgáltatás biztosítja, csak a tartalmat elemzésre kell küldenie.

> [!TIP]
> A Text Analytics egy Linux-alapú Docker-tárolórendszerképet is biztosít a nyelvfelismeréshez, így [telepítheti és futtathatja a Text Analytics-tárolót az](text-analytics-how-to-install-containers.md) adatok közelében.

A Hangulatelemzés a nyelvek széles körét támogatja, és több előzetes verziót is tartalmaz. További információk: [Támogatott nyelvek](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Alapelvek

A Text Analytics API egy gépi tanulási besorolási algoritmust használ a 0 és 1 közötti hangulatpontszám létrehozásához. Az 1-hez közeli értékek pozitív, míg a 0-hoz közeliek negatív hangulatot jelölnek. A hangulatelemzés a teljes dokumentumon történik, a szövegben lévő egyes entitások helyett. Ez azt jelenti, hogy a hangulatpontszámok at dokumentum- vagy mondatszinten adják vissza. 

Az alkalmazott modell előre bevan képezve a szöveg- és hangulattársítások kiterjedt korpuszával. Az elemzési technikák kombinációját használja, beleértve a szövegfeldolgozást, a beszédrész-elemzést, a szóelhelyezést és a szótársításokat. Az algoritmussal kapcsolatos további információk: [A Text Analytics bemutatása](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). Jelenleg nem lehet saját betanítási adatokat megadni. 

A pontozási pontosság ot valószínűleg javítja, ha a dokumentumok kevesebb mondatot tartalmaznak, nem pedig nagy szövegblokkot. Az objektivitás megfelelőségvizsgálati fázis során a modell meghatározza, hogy a dokumentum egészében objektív-e vagy hangulatot tartalmaz. A dokumentum, amely többnyire objektív nem halad a hangulatészlelési fázis, ami 0,50 pontszámot, további feldolgozás nélkül. A folyamatban lévő dokumentumok esetében a következő fázis 0,50 feletti vagy alatti pontszámot hoz létre. A pontszám a dokumentumban észlelt vélemény mértékfokátó.

## <a name="sentiment-analysis-versions-and-features"></a>Hangulatelemzési verziók és -szolgáltatások

A Text Analytics API a hangulatelemzés két verzióját kínálja – a 2-es és a v3-as. Hangulatelemzés v3 (Nyilvános előzetes verzió) jelentős javulást biztosít az API szövegének kategorizálása és pontozása pontosságát és részleteit.

> [!NOTE]
> * A Hangulatelemzés v3 kérelemformátum és [az adatkorlátok](../overview.md#data-limits) megegyeznek az előző verzióval.
> * A Hangulatelemzés v3 a következő `Australia East` `Central Canada`régiókban érhető `East US 2` `North Europe`el: , `West Europe`, `West US 2` `Central US` `East Asia`, `East US`, , `Southeast Asia`, `South Central US`, , `UK South`, , , , és .

| Szolgáltatás                                   | Hangulatelemzés v2 | Hangulatelemzés v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Az egyedi és kötegelt kérelmek módszerei    | X                     | X                     |
| A teljes dokumentum hangulatpontszámai  | X                     | X                     |
| Az egyes mondatok hangulatpontszámai |                       | X                     |
| Hangulat címkézése                        |                       | X                     |
| Modellverziószámozás                   |                       | X                     |

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

### <a name="sentiment-scoring"></a>Hangulatpontozás

A Hangulatelemzés v3 a szöveget hangulatfeliratokkal osztályolja (lásd alább). A visszaadott pontszámok a modell azon megbízhatóságát tükrözik, hogy a szöveg pozitív, negatív vagy semleges. A magasabb értékek nagyobb bizalmat jelentenek. 

### <a name="sentiment-labeling"></a>Hangulat címkézése

Hangulatelemzés v3 adhat vissza pontszámok és címkék egy mondat és a dokumentum szintjén. A pontszámok és `positive`a `negative`címkék `neutral`a , és . A dokumentum szintjén `mixed` a hangulatcímke is visszaadható pontszám nélkül. A dokumentum hangulatát az alábbiakban határozzuk meg:

| Mondat hangulat                                                                            | Visszaadott dokumentumcímke |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Legalább egy `positive` mondat szerepel a dokumentumban. A többi mondat `neutral`... | `positive`              |
| Legalább egy `negative` mondat szerepel a dokumentumban. A többi mondat `neutral`... | `negative`              |
| Legalább egy `negative` mondat és `positive` legalább egy mondat szerepel a dokumentumban.    | `mixed`                 |
| A dokumentumban lévő `neutral`összes mondat a .                                                  | `neutral`               |

### <a name="model-versioning"></a>Modellverziószámozás

> [!NOTE]
> A hangulatelemzéshez szükséges modellverziószámozás verziószámozása verziószámozással érhető el a verziószámtól `v3.0-preview.1`kezdve.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Példa C# kód

A [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs)található egy C# példa alkalmazás, amely meghívja a Hangulatelemzés ezen verzióját.


#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

### <a name="sentiment-scoring"></a>Hangulatpontozás

A hangulatelemző a szöveget túlnyomórészt pozitívnak vagy negatívnak minősíti. 0 és 1 közötti pontszámot rendel hozzá. A 0,5 közeli értékek semlegesek vagy határozatlanok. A 0,5-ös pontszám semlegességet jelez. Ha egy karakterlánc nem elemezhető hangulatra, vagy nincs hangulata, a pontszám mindig pontosan 0,5. Ha például egy spanyol nyelvű sztringet ad meg angol nyelvi kóddal, a pontszám 0,5 lesz.

---

## <a name="sending-a-rest-api-request"></a>REST API-kérelem küldése 

### <a name="preparation"></a>Előkészítés

A hangulatelemzés jobb minőségű eredményt ad, ha kisebb mennyiségű szöveget ad meg neki, amelyen dolgozni szeretne. Ez a ellentétes a kulcsszókereséssel, amely nagyobb mennyiségű szöveg esetén teljesít jobban. A legjobb eredmény elérése érdekében célszerű a bemenetet ennek megfelelően átszervezni.

JSON-dokumentumokkal ebben a formátumban kell lennie: azonosító, szöveg és nyelv.

A dokumentum méretének dokumentumonként 5120 karakternél nem lehet nagyobb. Gyűjteményenként legfeljebb 1000 elem (azonosítók) lehet. A kollekció elküldése a kérelem törzsében történik.

## <a name="structure-the-request"></a>A kérelem strukturálása

Hozzon létre egy POST kérést. A [Postman](text-analytics-how-to-call-api.md) vagy az **API-tesztkonzol** segítségével gyorsan strukturálhatja és elküldheti az API-t az alábbi hivatkozási hivatkozásokban. 

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

[Hangulatelemzés v3-as hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

[Hangulatelemzés v2 referencia](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Állítsa be a HTTPS-végpontot a hangulatelemzéshez egy Azure-beli Text Analytics-erőforrás sal vagy egy példányosított [text analytics-tárolóval.](text-analytics-how-to-install-containers.md) A használni kívánt verzióhoz a megfelelő URL-címet kell megadnia. Példa:

> [!NOTE]
> Az azure-portálon megtalálhatja a kulcs átési végpontját a Text Analytics-erőforráshoz. Ezek az erőforrás **gyorskezdőlapján,** az **erőforrás-kezelés**csoportban találhatók. 

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Állítsa be a kérelem fejlécét, hogy tartalmazza a Text Analytics API-kulcsot. A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

### <a name="example-sentiment-analysis-request"></a>Példa hangulatelemzési kérelemre 

A következő egy példa hangulatelemzésre beküldhető tartalomra. A kérelem formátuma megegyezik az API mindkét verziójában.
    
```json
{
    "documents": [
    {
        "language": "en",
        "id": "1",
        "text": "Hello world. This is some input text that I love."
    },
    {
        "language": "en",
        "id": "2",
        "text": "It's incredibly sunny outside! I'm so happy."
    }
    ],
}
```

### <a name="post-the-request"></a>A kérelem feladása

Az elemzés a kérelem megkapásakor történik meg. A percenként és másodpercenként küldhető kérelmek méretéről és számáról az [áttekintés adatkorlátok](../overview.md#data-limits) című részében olvashat.

A Text Analytics API állapot nélküli. A rendszer nem tárol adatokat a fiókjában, és az eredményeket azonnal visszaadja a válasznak.


### <a name="view-the-results"></a>Eredmények megtekintése

A hangulatelemző a szöveget túlnyomórészt pozitívnak vagy negatívnak minősíti. 0 és 1 közötti pontszámot rendel hozzá. A 0,5 közeli értékek semlegesek vagy határozatlanok. A 0,5-ös pontszám semlegességet jelez. Ha egy karakterlánc nem elemezhető hangulatra, vagy nincs hangulata, a pontszám mindig pontosan 0,5. Ha például egy spanyol nyelvű sztringet ad meg angol nyelvi kóddal, a pontszám 0,5 lesz.

A kimenetet visszaadása azonnali. Az eredményeket streamelheti egy olyan alkalmazásba, amely elfogadja a JSON-t, vagy mentheti a kimenetet egy fájlba a helyi rendszeren. Ezután importálja a kimenetet egy alkalmazásba, amellyel rendezheti, keresheti és kezelheti az adatokat. A többnyelvű és emoji támogatásnak köszönhetően a válasz szövegeltolásokat tartalmazhat. További [információ: Az ellentámadások feldolgozása](../concepts/text-offsets.md) további információkért.

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Hangulatelemzés v3 példa válasz

A Hangulatelemzés v3 válaszai minden egyes elemzett mondat és dokumentum hangulatfeliratát és pontszámát tartalmazzák. `documentScores`nem ad vissza, ha `mixed`a dokumentum hangulatfelirata.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Hangulatelemzés v2 példa válasz

A Hangulatelemzés v2 válaszai minden egyes elküldött dokumentumhoz tartalmaznak hangulatpontszámokat.

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Összefoglalás

Ebben a cikkben a Szövegelemzés API használatával ismertetheti a szövegelemzési API használatával a hangulatelemzéshez használt fogalmakat és munkafolyamatokat. Összegezve:

+ A hangulatelemzés két változatban érhető el a kiválasztott nyelvekhez.
+ A kérelem törzsében lévő JSON-dokumentumok azonosítót, szöveget és nyelvi kódot tartalmaznak.
+ A POST-kérelem `/sentiment` egy végponthoz egy személyre szabott [hozzáférési kulcs és egy végpont](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) használatával, amely érvényes az előfizetéshez.
+ Válasz kimenet, amely egy vélemény pontszám minden dokumentum-azonosító, streamelhető bármely alkalmazás, amely elfogadja a JSON.Response output, consistis of a sentiment score for each document ID, can be streamedtos to any app that accepts JSON. Például az Excel és a Power BI.

## <a name="see-also"></a>Lásd még

* [Szövegelemzés – áttekintés](../overview.md)
* [A Text Analytics ügyféltár használata](../quickstarts/text-analytics-sdk.md)
* [Újdonságok](../whats-new.md)
