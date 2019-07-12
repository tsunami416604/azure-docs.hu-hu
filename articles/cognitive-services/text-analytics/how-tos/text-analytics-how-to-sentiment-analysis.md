---
title: Hangulatelemzés az Azure Cognitive Services szövegelemzési használatával |} A Microsoft Docs
description: Ismerje meg, hogyan ismerheti fel a hangulatot a Text Analytics REST API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e17b68dfd63952d0c8c81415b090b047c5808e2e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797804"
---
# <a name="example-how-to-detect-sentiment-with-text-analytics"></a>Példa: Hangulat felismerése a Text Analytics használatával

A [Hangulatelemzés API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) kiértékeli a bemeneti szöveget és minden dokumentumra visszaad egy 0 (negatív) és 1 (pozitív) közötti hangulat pontszámot.

Ez a funkció akkor hasznos, ha a közösségi média, a vevő értékelések és a vitafórumokon kell felismeri a pozitív és negatív hangulatokat. Ön a tartalmat biztosítja, a modelleket és a betanítási adatokat a szolgáltatás biztosítja.

A Hangulatelemzés jelenleg az angol, német, spanyol és francia nyelvet támogatja. Más nyelvek előzetes verzióban érhetők el. További információk: [Támogatott nyelvek](../text-analytics-supported-languages.md).

> [!TIP]
> Szövegelemzés is biztosít a Linux-alapú Docker tároló rendszerképének hangulatelemzés, így [telepítheti és futtathatja a Text Analytics tároló](text-analytics-how-to-install-containers.md) közel az adatokat.

## <a name="concepts"></a>Alapelvek

A Text Analytics gépi tanulási osztályozó algoritmussal generálja a 0 és 1 közötti hangulatpontszámot. Az 1-hez közeli értékek pozitív, míg a 0-hoz közeliek negatív hangulatot jelölnek. A modellt nagy mennyiségű hangulattársításos szöveggel előre betanították. Jelenleg saját betanítási adatok megadására nincs lehetőség. A modell a szövegelemzés során több technika kombinációját alkalmazza, beleértve a szövegfeldolgozást, a beszédrészlet elemzést, a szóelhelyezést és szótársításokat. Az algoritmussal kapcsolatos további információk: [A Text Analytics bemutatása](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

A hangulatelemzés a teljes dokumentum történik, szemben azzal, amikor a szöveg egy konkrét eleméhez gyűjtjük ki a hangulatot. A gyakorlatban a pontozás pontossága inkább nő, ha a dokumentum csak egy-két mondatot tartalmaz, és nem nagyobb szöveget. Az objektivitás megfelelőségvizsgálati fázis során a modell meghatározza, hogy a dokumentum egészében objektív-e vagy hangulatot tartalmaz. A nagyrészt objektív dokumentum nem kerül tovább a hangulatelemzési fázisba, hanem 0,50 pontszámot kap további elemzés nélkül. A folyamatban továbblépő dokumentumokra a következő fázis 0,50 fölötti vagy alatti pontszámot generál a dokumentumban talált érzelem foka alapján.

## <a name="preparation"></a>Előkészítés

A hangulatelemzés jobb minőségű eredményt produkál, ha kisebb szövegdarabokat kap a munkához. Ez a ellentétes a kulcsszókereséssel, amely nagyobb mennyiségű szöveg esetén teljesít jobban. A legjobb eredmény elérése érdekében célszerű a bemenetet ennek megfelelően átszervezni.

JSON-dokumentumok kell rendelkeznie a következő formátumban: Szöveg, nyelvi azonosító

Dokumentum mérete kell lennie a 5,120 karakter / dokumentum, és legfeljebb 1000 rendelkezhet gyűjteményenként (azonosítók) elemet. A kollekció elküldése a kérelem törzsében történik. A következő egy példa hangulatelemzésre beküldhető tartalomra.

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>1\. lépés: A kérelem struktúra

A kérés definícióval kapcsolatos részletek megtalálhatók a [Text Analytics API hívásának módja](text-analytics-how-to-call-api.md) részben. A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy **POST** kérést. Tekintse át a kérelem API-dokumentáció: [Hangulatelemzés API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

+ Állítsa be a HTTP-végpontot hangulatelemzés, a Text Analytics erőforrás használatával Azure-ban vagy egy példányosított [Szövegelemzés tároló](text-analytics-how-to-install-containers.md). Tartalmaznia kell a `/sentiment` erőforrást: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`

+ A kérés fejlécet állítsa be úgy, hogy tartalmazza a Text Analytics műveletekhez a hozzáférési kulcsot. További információkért lásd: [Végpontok és hozzáférési kulcsok megkeresése](text-analytics-how-to-access-key.md).

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2\. lépés: A kérelem küldése

Az elemzés a kérelem megkapásakor történik meg. Tekintse meg a [data korlátai](../overview.md#data-limits) szakasz az áttekintésben a méret és a kérelmek percenkénti küldhet és a második kapcsolatos információkat.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.


## <a name="step-3-view-results"></a>3\. lépés: Eredmények megtekintése

A hangulatelemző a szöveget nagyobbrészt pozitívnak vagy negatívnak sorolja be, és 0 és 1 közötti pontszámot rendel hozzá. A 0,5 közeli értékek semlegesek vagy határozatlanok. A 0,5-ös pontszám semlegességet jelez. Ha a karakterlánc nem elemezhető hangulat szempontjából vagy nincs hangulata, a pontszám mindig pontosan 0,5. Ha például egy spanyol nyelvű szöveget ad meg angol nyelvi kóddal, a pontszám 0,5 lesz.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba.

Az alábbi példa a cikkbeli dokumentumgyűjteményre kapott választ mutatja.

```json
{
    "documents": [
        {
            "score": 0.9999237060546875,
            "id": "1"
        },
        {
            "score": 0.0000540316104888916,
            "id": "2"
        },
        {
            "score": 0.99990355968475342,
            "id": "3"
        },
        {
            "score": 0.980544924736023,
            "id": "4"
        },
        {
            "score": 0.99996328353881836,
            "id": "5"
        }
    ],
    "errors": []
}
```

## <a name="sentiment-analysis-v3-public-preview"></a>Vélemények elemzése v3-as nyilvános előzetes verzió

A [Hangulatelemzés következő verziójára](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) már elérhető nyilvános előzetes verzió, jelentős fejlesztéseket biztosít a pontosság és az API-k szöveges kategorizálás részletei és pontozási folyamatokat. 

> [!NOTE]
> * A vélemények elemzése v3-as kérés formátumát és [data korlátai](../overview.md#data-limits) ugyanazok, mint a korábbi verzióra.
> * Jelenleg hangulatelemzés V3: 
>    * Jelenleg csak a az angol nyelvet támogatja.  
>    * Az alábbi régiókban érhető el: `Central US`, `Central Canada`, `East Asia` 

|Funkció |Leírás  |
|---------|---------|
|Pontosság jobb legyen     | A pozitív, semleges, negatív és vegyes vélemények észlelése szöveges dokumentumok a korábbi verziókhoz képest jelentős fejlesztéseket tartalmaz.           |
|A dokumentum és mondatszintű Véleménypontszámot     | Az egyes mondatok és a dokumentum hangulatának. Ha a dokumentum több mondatok is tartalmaz, minden mondat is hozzá van rendelve egy véleménypontszámot.         |
|Vélemények kategória és pontszám     | Az API-t most adja vissza a vélemény kategóriák (`positive`, `negative`, `neutral` és `mixed`) szöveg mellett egy véleménypontszámot.        |
| Továbbfejlesztett kimeneti | Hangulatelemzés mostantól egy teljes szöveges dokumentum és az egyes mondatokat kapcsolatos információkat ad vissza. |

### <a name="sentiment-labeling"></a>Vélemények címkézés

Hangulatelemzés V3 térhet vissza a pontszámokat és címkék (`positive`, `negative`, és `neutral`) mondatot, és a dokumentum szintjén. A dokumentum szintjén a `mixed` vélemények címkét (nem pontszám) is adhatók vissza. A dokumentum a róluk szóló véleményeket határozza meg a mondatok pontszámok összesíti.

| Mondat vélemények                                                        | Visszaadott dokumentumok felirat |
|---------------------------------------------------------------------------|----------------|
| Legalább egy pozitív mondatot, és a többi a mondatok olyan semleges. | `positive`     |
| Legalább egy negatív mondatot, és a többi a mondatok olyan semleges.  | `negative`     |
| Legalább egy negatív mondatot, és legalább egy pozitív mondat helyett szerepel.         | `mixed`        |
| Az összes mondatokat olyan semleges.                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>Vélemények elemzése V3 kérelem (példa)

A következő JSON-hangulatelemzés az új verzió szóló kérelem példája. Vegye figyelembe, hogy a kérelem a formázást ugyanaz, mint az előző verzió:

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
  ]
}
```

### <a name="sentiment-analysis-v3-example-response"></a>Hangulatelemzés V3 példaválasz

Bár a kérés formátuma megegyezik az előző verzió, a válasz formátuma megváltozott. A következő JSON egy példaválasz az API új verzióját a következő:

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

### <a name="example-c-code"></a>Példa C# kódot

Egy példa annak C# alkalmazás, amely meghívja a hangulatelemzés jelen verziója a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="summary"></a>Összegzés

Ebben a cikkben a hangulatelemzés elveivel és folyamatával ismerkedett meg a Cognitive Services Text Analytics használatával. Összegezve:

+ A [Hangulatelemzés API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) csak egyes nyelvekre érhető el.
+ A kérelem törzsében szereplő JSON-dokumentumok közé tartozik egy azonosító, a szöveg és a nyelvi kódot.
+ POST-kérés a `/sentiment` végpontra, az előfizetésre érvényes személyre szabott [hozzáférési kulcs és végpont](text-analytics-how-to-access-key.md) használatával.
+ A válasz kimenet, amely minden dokumentumazonosítóhoz tartalmazza a hangulat pontszámot, továbbítható bármilyen JSON-t elfogadó alkalmazáshoz, beleértve az Excelt és a Power BI-t, hogy csak néhányat említsünk.

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kulcsszavak kinyerése](text-analytics-how-to-keyword-extraction.md)
