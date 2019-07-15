---
title: Hangulatelemzés az Azure Cognitive Services Text Analytics REST API-val |} A Microsoft Docs
description: Ismerje meg, hogyan hangulat felismerése a Text Analytics REST API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: c3004dd3910dd5fdafc933efa213c9f097310e87
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001712"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Példa: Hangulat felismerése szövegelemzés

A [Azure Sentiment Analysis API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) szövegbevitel kiértékeli, és a egy véleménypontszámot, egyes dokumentumok adja vissza. Pontszámok tartomány 0 (negatív) és 1 (pozitív).

Ez a funkció akkor hasznos, ha a közösségi média, a vevő értékelések és a vitafórumokon kell felismeri a pozitív és negatív hangulatokat. Tartalom biztosítja. A szolgáltatás által biztosított modelleket és a betanítási adatok.

A Sentiment Analysis API jelenleg angol, német, spanyol és francia támogatja. Más nyelvek előzetes verzióban érhetők el. További információk: [Támogatott nyelvek](../text-analytics-supported-languages.md).

> [!TIP]
> Az Azure szövegelemzési API-t is biztosít a Linux-alapú Docker tároló rendszerképének hangulatelemzés, így [telepítheti és futtathatja a Text Analytics tároló](text-analytics-how-to-install-containers.md) közel az adatokat.

## <a name="concepts"></a>Alapelvek

A Text Analytics gépi tanulási osztályozó algoritmussal generálja a 0 és 1 közötti hangulatpontszámot. Az 1-hez közeli értékek pozitív, míg a 0-hoz közeliek negatív hangulatot jelölnek. A modellt nagy mennyiségű hangulattársításos szöveggel előre betanították. Jelenleg nem lehet biztosítani a saját betanítási adatok. A modell olyan kombinált műveletekben szöveg elemzése során használ. Ennek technikái a következők: szöveg feldolgozása, része a beszéd, elemzés, word elhelyezési és a word-társítások. Az algoritmussal kapcsolatos további információk: [A Text Analytics bemutatása](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

A hangulatelemzés a teljes dokumentum történik, szemben azzal, amikor a szöveg egy konkrét eleméhez gyűjtjük ki a hangulatot. A gyakorlatban ami pontozó pontosság javításához, ha a dokumentumok tartalmazzák a nagy blokkblobok szöveg helyett egy vagy két mondatokat van. Az objektivitás megfelelőségvizsgálati fázis során a modell meghatározza, hogy a dokumentum egészében objektív-e vagy hangulatot tartalmaz. Egy dokumentum, amely csak a cél a vélemények észlelési fázishoz, 0,50 pontszámot, további feldolgozással, ami nem halad. A folyamat továbbra is dokumentumokhoz a következő fázis felett vagy alatt 0,50 pontszámot állít elő. A pontszám attól függ, hogy a dokumentum a felismert véleménynek mértékét.

## <a name="preparation"></a>Előkészítés

Hangulatelemzés jobb minőségű eredményt hoz létre, amikor működését az a szöveg kisebb adattömbökben engedélyezi azt. Ez a ellentétes a kulcsszókereséssel, amely nagyobb mennyiségű szöveg esetén teljesít jobban. A legjobb eredmény elérése érdekében célszerű a bemenetet ennek megfelelően átszervezni.

JSON-dokumentumok kell rendelkeznie a következő formátumban: Azonosító, a szöveg és a nyelvet.

Dokumentum mérete a dokumentum a 5,120 karakterrel kell lennie. Használhat akár 1000 gyűjteményenként (azonosítók) elemet. A kollekció elküldése a kérelem törzsében történik. Az alábbi minta tartalmat, előfordulhat, hogy küldje el a hangulatelemzés példája:

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

További információ a kérelem definíciója: [a szövegelemzési API hívása](text-analytics-how-to-call-api.md). A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy POST-kérelmet. Tekintse át a kérelem API-dokumentáció, tekintse meg a [Sentiment Analysis API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Állítsa be a HTTP-végpontot hangulatelemzés használatával Text Analytics erőforrás Azure-ban vagy egy példányosított [Szövegelemzés tároló](text-analytics-how-to-install-containers.md). Tartalmaznia kell a `/sentiment` erőforrás: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`.

+ A kérés fejlécet állítsa be úgy, hogy tartalmazza a Text Analytics műveletekhez a hozzáférési kulcsot. További információkért lásd: [végpontok keresése és hozzáférési kulcsokkal](text-analytics-how-to-access-key.md).

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

> [!Tip]
> Használja [Postman](text-analytics-how-to-call-api.md) , vagy nyissa meg a **API tesztelési konzollal** a a [dokumentáció](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) struktúra a kérelmet, és közzéteszi azokat a szolgáltatás.

## <a name="step-2-post-the-request"></a>2\. lépés: A kérelem küldése

Az elemzés a kérelem megkapásakor történik meg. Információ a mérete és a kérelmek percenkénti küldhet és második, tekintse meg a [data korlátai](../overview.md#data-limits) az Áttekintés szakasz.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.


## <a name="step-3-view-the-results"></a>3\. lépés: Eredmények megtekintése

A róluk szóló véleményeket analyzer sorolja be a szöveget túlnyomórészt pozitív vagy negatív. 0 – 1 tartományban lévő pontszámot rendel. A 0,5 közeli értékek semlegesek vagy határozatlanok. A 0,5-ös pontszám semlegességet jelez. Ha a karakterlánc nem elemezhető véleményeket, vagy nincs vélemények rendelkezik, a pontszám, mindig 0,5 pontosan. Ha például egy spanyol nyelvű szöveget ad meg angol nyelvi kóddal, a pontszám 0,5 lesz.

A kimenetet visszaadása azonnali. Az eredményeket JSON elfogadó alkalmazás adatfolyam, vagy mentse a kimenetet egy fájlt a helyi rendszer. Ezután importálja a kimeneti, amellyel rendezni, keresése és alakítanak át az adatokat egy alkalmazásba.

Az alábbi példa a válasz a dokumentum egy dokumentumgyűjteményben ebben a cikkben látható:

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

A [Hangulatelemzés következő verziójára](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) jelenleg nyilvános előzetes verzióban érhető el. Jelentős fejlesztéseket nyújt a pontosság és az API-k szöveges kategorizálási és pontozás részletei. 

> [!NOTE]
> * A Hangulatelemzés v3-as kérés formátumát és [data korlátai](../overview.md#data-limits) ugyanazok, mint a korábbi verzióra.
> * Jelenleg Hangulatelemzés v3: 
>    * Jelenleg csak angol nyelven.  
>    * Az alábbi régiókban érhető el: `Central US`, `Central Canada`, és `East Asia`.

|Funkció |Leírás  |
|---------|---------|
|Pontosság jobb legyen     | Az előző verziókhoz képest jelentősen javult a pozitív, semleges, negatív és vegyes hangulat szöveges dokumentumokban való észlelése.           |
|A dokumentum és mondatszintű véleménypontszámot     | A hangulatot egy dokumentumon és annak egyes mondatain belül is észleli. Ha a dokumentum több mondatból áll, akkor minden mondathoz külön hangulatpontszám lesz rendelve.         |
|Vélemények kategória és pontszám     | Az API-t most vélemények kategóriák szöveg mellett egy értelmezhetőségi pontszámot ad vissza. A kategória `positive`, `negative`, `neutral`, és `mixed`.       |
| Továbbfejlesztett kimeneti | Hangulatelemzés mostantól egy teljes szöveges dokumentum és az egyes mondatokat információkat adja vissza. |

### <a name="sentiment-labeling"></a>Vélemények címkézés

Vélemények elemzése v3 pontszámokat és a egy mondatot, és a dokumentum szintjén címkék adhat vissza. A pontszámok és a címkék `positive`, `negative`, és `neutral`. A dokumentum szintjén a `mixed` vélemények címkét (nem a pontszám) is adhatók vissza. A dokumentum a róluk szóló véleményeket határozza meg a mondatok pontszámokat összesíti.

| Mondat vélemények                                                        | Visszaadott dokumentumok felirat |
|---------------------------------------------------------------------------|----------------|
| Legalább egy pozitív mondatot, és a többi a mondatok olyan semleges. | `positive`     |
| Legalább egy negatív mondatot, és a többi a mondatok olyan semleges.  | `negative`     |
| Legalább egy negatív mondatot, és legalább egy pozitív mondat helyett szerepel.         | `mixed`        |
| Az összes mondatokat olyan semleges.                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>Vélemények elemzése v3 példa kérelem

A következő JSON-Hangulatelemzés az új verzió szóló kérelem példája. A kérelem a formázást ugyanaz, mint a korábbi verziója:

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

### <a name="sentiment-analysis-v3-example-response"></a>Vélemények elemzése v3 példaválasz

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

Egy példa annak C# alkalmazás, amely meghívja a Hangulatelemzés jelen verziója a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és hangulatelemzés munkafolyamata az Azure Cognitive Services Text Analytics használatával. Összegezve:

+ A [Sentiment Analysis API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) érhető el a kiválasztott nyelveken.
+ A kérelem törzsében szereplő JSON-dokumentumok közé tartozik egy azonosító, a szöveg és a nyelvi kódot.
+ A POST-kérés, hogy egy `/sentiment` végpont használatával személyre szabott [kulcs és a egy végpont elérésére](text-analytics-how-to-access-key.md) Ez érvényes az előfizetéshez.
+ Válasz kimenete, amely egy értelmezhetőségi pontszámot tartalmaz az egyes dokumentumazonosító, továbbítható legyen, amely JSON-alkalmazásaiban. Példa alkalmazások közé tartoznak, az Excel és a Power bi-ban, hogy néhányat említsünk.

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kulcsszavak kinyerése](text-analytics-how-to-keyword-extraction.md)
