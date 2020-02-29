---
title: Text Analytics REST APIekkel kapcsolatos érzelmek elemzése
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan derítheti fel a szövegbeli érzelmeket az Azure Cognitive Services Text Analytics REST API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 60f6443e041c2f3a76a9027bebd33dc29a07f445
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197470"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Útmutató: érzelmek észlelése a Text Analytics API használatával

A Text Analytics API Hangulatelemzés funkciója kiértékeli a szöveget, és az egyes mondatokhoz tartozó érzelmi pontszámokat és címkéket adja vissza. Ez hasznos lehet a közösségi média, az ügyfelek és a vitafórumok pozitív és negatív észlelésére. Az API által használt AI-modelleket a szolgáltatás nyújtja, csak tartalmat kell elküldeni az elemzéshez.

> [!TIP]
> A Text Analytics egy Linux-alapú Docker-tároló rendszerképet is biztosít a nyelvfelismerés számára, így a Text Analytics tárolót az adatokhoz közelebb is [telepítheti és futtathatja](text-analytics-how-to-install-containers.md) .

A Hangulatelemzés számos különböző nyelvet támogat, több előzetes verzióban. További információk: [Támogatott nyelvek](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Alapelvek

A Text Analytics API egy Machine learning-besorolási algoritmust használ, amely egy 0 és 1 közötti hangulati pontszámot eredményez. Az 1-hez közeli értékek pozitív, míg a 0-hoz közeliek negatív hangulatot jelölnek. Az érzelmek elemzése a teljes dokumentumon történik, a szövegben szereplő egyes entitások helyett. Ez azt jelenti, hogy a hangulati pontszámok egy dokumentum vagy egy mondat szintjén lesznek visszaadva. 

A használt modell előre betanítva, a szöveg-és a hangulati társítások széles választékával. Az elemzési módszerek kombinációját használja, többek között a szöveg feldolgozását, a beszédek elemzését, a szavak elhelyezését és a Word-társításokat. Az algoritmussal kapcsolatos további információk: [A Text Analytics bemutatása](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). Jelenleg nem lehet saját betanítási adatait megadnia. 

A pontozás pontossága olyankor nő, amikor a dokumentumok kevesebb mondatot tartalmaznak, nem pedig egy nagy blokkot. Az objektivitás megfelelőségvizsgálati fázis során a modell meghatározza, hogy a dokumentum egészében objektív-e vagy hangulatot tartalmaz. Egy olyan dokumentum, amely többnyire objektív, nem halad az észlelési fázisra, ami egy 0,50 pontszámot eredményez, és nincs szükség további feldolgozásra. A folyamat során folytatott dokumentumok esetében a következő fázis a 0,50-es vagy újabb pontszámot generálja. A pontszám a dokumentumban észlelt érzelmek szintjétől függ.

## <a name="sentiment-analysis-versions-and-features"></a>Verziók és szolgáltatások Hangulatelemzés

A Text Analytics API a Hangulatelemzés-v2 és v3 két verzióját kínálja. A Hangulatelemzés v3 (nyilvános előzetes verzió) jelentős mértékben javítja az API szövegének kategorizálása és pontozása pontosságát és részletességét.

> [!NOTE]
> * Az Hangulatelemzés v3 kérelem formátuma és [adatkorlátja](../overview.md#data-limits) megegyezik az előző verzióval.
> * A Hangulatelemzés v3 a következő régiókban érhető el: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`és `West US 2`.

| Szolgáltatás                                   | Hangulatelemzés v2 | Hangulatelemzés v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Egyszeri és batch-kérelmek módszerei    | X                     | X                     |
| A teljes dokumentum hangulati pontszámai  | X                     | X                     |
| Az egyéni mondatok hangulati pontszámai |                       | X                     |
| Érzelmek címkézése                        |                       | X                     |
| Modell verziószámozása                   |                       | X                     |

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

### <a name="sentiment-scoring"></a>Érzelmek pontozása

Hangulatelemzés v3 a szövegeket az érzelmekkel kapcsolatos címkékkel osztályozza (lásd alább). A visszaadott pontszámok a modell megbízhatóságát jelentik, hogy a szöveg pozitív, negatív vagy semleges. A magasabb értékek nagyobb megbízhatóságot jelentenek. 

### <a name="sentiment-labeling"></a>Érzelmek címkézése

A Hangulatelemzés v3 a pontszámokat és címkéket a mondatok és a dokumentumok szintjén adhatja vissza. A pontszámok és a címkék `positive`, `negative`és `neutral`. A dokumentum szintjén a `mixed` hangulat címkét is vissza lehet adni pontszám nélkül. A dokumentum hangulatát a következők határozzák meg:

| Mondat hangulata                                                                            | Visszaadott dokumentum címkéje |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Legalább egy `positive` mondat szerepel a dokumentumban. A többi mondat `neutral`. | `positive`              |
| Legalább egy `negative` mondat szerepel a dokumentumban. A többi mondat `neutral`. | `negative`              |
| Legalább egy `negative` mondat és legalább egy `positive` mondat szerepel a dokumentumban.    | `mixed`                 |
| A dokumentumban szereplő összes mondat `neutral`.                                                  | `neutral`               |

### <a name="model-versioning"></a>Modell verziószámozása

> [!NOTE]
> Az érzelmi elemzés modell-verziószámozása a `v3.0-preview.1`verziótól kezdődően érhető el.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Példa C# kódja

Megtalálhatja a C# [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs)Hangulatelemzés ezen verzióját meghívó alkalmazást.


#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

### <a name="sentiment-scoring"></a>Érzelmek pontozása

Az a hangulat-elemző a szöveget túlnyomórészt pozitívként vagy negatívként osztályozza. 0 és 1 közötti pontszámot rendel hozzá. A 0,5 közeli értékek semlegesek vagy határozatlanok. A 0,5-ös pontszám semlegességet jelez. Ha egy sztringet nem lehet elemezni az érzelmekkel kapcsolatban, vagy nincs hangulata, a pontszám mindig 0,5 pontosan. Ha például egy spanyol nyelvű sztringet ad meg angol nyelvi kóddal, a pontszám 0,5 lesz.

---

## <a name="sending-a-rest-api-request"></a>REST API kérelem küldése 

### <a name="preparation"></a>Előkészítés

Az érzelmek elemzése nagyobb minőségi eredményt eredményez, ha kisebb mennyiségű szöveget ad meg a munkához. Ez a ellentétes a kulcsszókereséssel, amely nagyobb mennyiségű szöveg esetén teljesít jobban. A legjobb eredmény elérése érdekében célszerű a bemenetet ennek megfelelően átszervezni.

A JSON-dokumentumoknak ebben a formátumban kell szerepelniük: azonosító, szöveg és nyelv.

A dokumentum méretének 5 120 karakternél rövidebbnek kell lennie a dokumentumban. Egy gyűjteményhez legfeljebb 1 000 elem (azonosító) tartozhat. A kollekció elküldése a kérelem törzsében történik.

## <a name="structure-the-request"></a>A kérelem szerkezete

Hozzon létre egy POST-kérelmet. A [Poster](text-analytics-how-to-call-api.md) vagy az API- **tesztelési konzolt** a következő hivatkozásokra kattintva gyorsan strukturálhatja és küldheti el. 

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

[Hangulatelemzés v3 – dokumentáció](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

[Hangulatelemzés v2 – dokumentáció](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Állítsa be a HTTPS-végpontot az érzelmek elemzéséhez az Azure-ban vagy egy példányban létrehozott [text Analytics tárolóban](text-analytics-how-to-install-containers.md)Text Analytics erőforrás használatával. Meg kell adnia a használni kívánt verzió helyes URL-címét. Például:

> [!NOTE]
> Az Azure Portalon megtalálhatja Text Analytics erőforrásának kulcsát és végpontját. Ezek az erőforrás **gyors üzembe helyezés** lapján, az **Erőforrás-kezelés**területen találhatók. 

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

A Text Analytics API kulcsot tartalmazó kérelem fejlécének beállítása. A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

### <a name="example-sentiment-analysis-request"></a>Példa Hangulatelemzés kérelemre 

A következő egy példa hangulatelemzésre beküldhető tartalomra. A kérelem formátuma megegyezik az API mindkét verziójával.
    
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

### <a name="post-the-request"></a>A kérelem közzététele

Az elemzés a kérelem megkapásakor történik meg. További információ a másodpercenként elküldhető kérelmek méretéről és számáról: az [adatkorlátozások](../overview.md#data-limits) szakasz az áttekintésben.

A Text Analytics API állapot nélküli. A fiókjában nem tárolunk semmilyen adatmennyiséget, és az eredményeket azonnal visszaadja a válaszban.


### <a name="view-the-results"></a>Eredmények megtekintése

Az a hangulat-elemző a szöveget túlnyomórészt pozitívként vagy negatívként osztályozza. 0 és 1 közötti pontszámot rendel hozzá. A 0,5 közeli értékek semlegesek vagy határozatlanok. A 0,5-ös pontszám semlegességet jelez. Ha egy sztringet nem lehet elemezni az érzelmekkel kapcsolatban, vagy nincs hangulata, a pontszám mindig 0,5 pontosan. Ha például egy spanyol nyelvű sztringet ad meg angol nyelvi kóddal, a pontszám 0,5 lesz.

A kimenetet visszaadása azonnali. Az eredményeket egy olyan alkalmazásba is továbbíthatja, amely fogadja a JSON-t, vagy mentse a kimenetet egy fájlba a helyi rendszeren. Ezután importálja a kimenetet egy olyan alkalmazásba, amelyet az adatrendezéshez, kereséshez és kezeléshez használhat.

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Példa Hangulatelemzés v3-es válaszra

Hangulatelemzés v3 válaszai az egyes elemzett mondatokra és dokumentumokra vonatkozó véleményeket és pontszámokat tartalmaznak. `documentScores` nem ad vissza, ha a dokumentum hangulati címkéje `mixed`.

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

#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Példa Hangulatelemzés v2 válaszra

A Hangulatelemzés v2 válaszai az egyes elküldött dokumentumokhoz tartozó hangulati pontszámokat tartalmaznak.

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

## <a name="summary"></a>Összegzés

Ebben a cikkben a Text Analytics API segítségével megtanulta az érzelmek elemzéséhez szükséges fogalmakat és munkafolyamatokat. Összegezve:

+ A Hangulatelemzés két verzióban érhető el a kiválasztott nyelvekhez.
+ A kérelem törzsében található JSON-dokumentumok közé tartozik az azonosító, a szöveg és a nyelvi kód.
+ A POST kérelem egy `/sentiment`-végpontra vonatkozik, amely egy személyre szabott [hozzáférési kulcsot és egy](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , az előfizetéséhez érvényes végpontot használ.
+ A válasz kimenete, amely az egyes dokumentumok AZONOSÍTÓinak hangulati pontszámát tartalmazza, továbbítható bármely olyan alkalmazásnak, amely elfogadja a JSON-t. Például: Excel és Power BI.

## <a name="see-also"></a>Lásd még

* [A Text Analytics áttekintése](../overview.md)
* [Az Text Analytics ügyféloldali kódtár használata](../quickstarts/text-analytics-sdk.md)
* [Újdonságok](../whats-new.md)
