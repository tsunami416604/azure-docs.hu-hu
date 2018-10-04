---
title: Entitások felismerése használata a szövegelemzési API
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerhetik fel a Text Analytics REST API használatával.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: b2916e5c414562c55c35c9c5e7ab378963e004be
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248071"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics-preview"></a>Megnevezett entitások felismerése használata a Text Analytics (előzetes verzió)

A [entitás Recognition API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) strukturálatlan szöveges vesz igénybe, és egyes JSON-dokumentumok listáját adja vissza használatát entitásokat is tartalmaz, hivatkozásokkal további információhoz a weben (Wikipédia és a Bing). 

## <a name="entity-linking-and-named-entity-recognition"></a>Entitáskapcsolás és nevesített entitások felismerése

A Text Analytics `entities` mindkét végpont supprts nevű (NER) entitásfelismeréssel és -entitáskapcsolás.

### <a name="entity-linking"></a>Entitáskapcsolás
Entitáskapcsolás rendszer azon képessége, azonosíthatja és a egy entitás (pl. meghatározása-e a "Mars" globális vagy a latin god háború használja) szöveg található az identitás megkülönböztetéséhez. Ez a folyamat, amely felismeri a kapcsolódó entitások – Wikipedia szolgál a Tudásbázisban alapszintű ismerete szükséges a `entities` végpontot Text Analytics.

A Text Analytics [2.1. dátumú előzetes Sémaverzióra](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634), csak entitáskapcsolás érhető el.

### <a name="named-entity-recognition-ner"></a>Nevesített entitások felismerése (NER)
Megnevezett entitások felismerése (NER) rendszer azon képessége, azonosíthatja a szöveg különböző entitásokat, és előre meghatározott osztályokba kategorizálja őket. Az entitások támogatott osztályokat alább láthatók.

Text Analytics 2.1-es verziója előzetes verzióban érhető el (`https://[region].api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`), entitáskapcsolás és nevesített entitások felismerése (NER) érhető el.

### <a name="language-support"></a>Nyelvi támogatás

Entitáskapcsolás különféle nyelveken használatához az egyes nyelvekhez tartozó Tudásbázis használatával. A Text Analytics entitáskapcsolás, ez azt jelenti, hogy minden nyelv által támogatott a `entities` végpont kapcsolja a megfelelő Wikipedia forrásgyűjteményébe az adott nyelveken. Korpuszok mérete nyelvek közé esik, mivel várható, hogy a funkció a visszaírási entitáskapcsolás is változnak.

## <a name="supported-types-for-named-entity-recognition"></a>Nevesített entitások felismerése támogatott típusai

| Típus  | Altípus | Példa |
|:-----------   |:------------- |:---------|
| Személy        | N/A\*         | "Jeff", "Bill Gates"     |
| Hely      | N/A\*         | "Redmond, Washington", "Párizs"  |
| Szervezet  | N/A\*         | "Microsoft"   |
| Mennyiség      | Szám        | "6", "hat"     | 
| Mennyiség      | Százalék    | "50 %", "ötven százalék"| 
| Mennyiség      | Sorszám       | "2.", "a második"     | 
| Mennyiség      | NumberRange   | "4 – 8"     | 
| Mennyiség      | Kor           | "90 napnál nem régebbi", "30 évnél fiatalabb"    | 
| Mennyiség      | Currency (Pénznem)      | "$10.99"     | 
| Mennyiség      | Dimenzió     | "10 mérföld", "40 cm"     | 
| Mennyiség      | Hőmérséklet   | "32 fokos"    |
| DateTime      | N/A\*         | "6:30 = 1997031213 2012. február 4."      | 
| DateTime      | Dátum          | "2., 2017 május", "05/02/2017"   | 
| Dátum és idő     | Time          | "8 am", "8:00"  | 
| DateTime      | DateRange     | "Május 2. május 5-én a"    | 
| DateTime      | timeRange     | "du. 6, 7 pm"     | 
| DateTime      | Időtartam      | "1 perc 45 másodpercig"   | 
| DateTime      | Beállítás           | "minden kedden"     | 
| DateTime      | Időzóna      |    | 
| URL-cím           | N/A\*         | „http://www.bing.com”    |
| E-mail         | N/A\*         | „support@contoso.com” |
\* Attól függően, a bemeneti és a kinyert entitásokat, bizonyos entitások előfordulhat, hogy kihagyja a `SubType`.



## <a name="preparation"></a>Előkészítés

JSON-dokumentumok rendelkeznie kell a következő formátumban: azonosító, a szöveg, a nyelv

Jelenleg támogatott nyelveket, tekintse meg [ebben a listában](../text-analytics-supported-languages.md).

Dokumentum mérete kell lennie a 5000 karakter / dokumentum, és legfeljebb 1000 rendelkezhet gyűjteményenként (azonosítók) elemet. A gyűjtemény elküldésekor a kérelem törzsében. A következő példa olyan bemutatásáért, előfordulhat, hogy küldje el az entitás hivatkozási célból tartalom.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
                },
               {"id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>1. lépés: A kérés struktúra

Kérelem definíciója a részletek megtalálhatók a [a szövegelemzési API hívása](text-analytics-how-to-call-api.md). Kényelmi vannak megfelelően a következő szempontokat:

+ Hozzon létre egy **POST** kérelmet. A kérelem API-dokumentációban: [Entitáskapcsolási API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Állítsa a kulcskifejezések kinyerése a HTTP-végpontot. Tartalmaznia kell a `/entities` erőforrás: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

+ Állítsa be a hívóbetűt a Text Analytics műveletek közé tartozik a fejléc. További információkért lásd: [végpontok keresése és hozzáférési kulcsokkal](text-analytics-how-to-access-key.md).

+ A kérelem törzsében szereplő adja meg a JSON-dokumentumok ehhez az elemzéshez előkészített gyűjtemény

> [!Tip]
> Használata [Postman](text-analytics-how-to-call-api.md) , vagy nyissa meg a **API tesztelési konzollal** a a [dokumentáció](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) struktúra egy kérelmet, és KÖZZÉTESZI azokat a szolgáltatást.

## <a name="step-2-post-the-request"></a>2. lépés: A kérés küldése

Elemzés a kérelem történik. A szolgáltatás percenként legfeljebb 100 kéréseket fogad. Minden egyes kérés legfeljebb 1 MB lehet.

Ne felejtse el, hogy az állapot nélküli-e a szolgáltatás. A fiókban tárolt adatok nem. Eredmény akkor azonnal a válaszban.

## <a name="step-3-view-results"></a>3. lépés: Az eredmények megtekintése

Minden POST kérelemhez egy JSON formátumú válasz azonosítókkal és észlelt tulajdonságokat adja vissza.

Kimeneti azonnal adja vissza. Az eredményeket JSON elfogadó alkalmazás adatfolyam vagy kimenetét mentse el egy fájlt a helyi rendszer, és importálja azt egy alkalmazás, amely lehetővé teszi, hogy rendezni, keresése és az adatok kezelésére.

Egy példa a kimenetre entitáskapcsolás a következő látható:

```json
{
    "Documents": [
        {
            "Id": "1",
            "Entities": [
                {
                    "Name": "Jeff",
                    "Matches": [
                        {
                            "Text": "Jeff",
                            "Offset": 0,
                            "Length": 4
                        }
                    ],
                    "Type": "Person"
                },
                {
                    "Name": "three dozen",
                    "Matches": [
                        {
                            "Text": "three dozen",
                            "Offset": 12,
                            "Length": 11
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50",
                    "Matches": [
                        {
                            "Text": "50",
                            "Offset": 49,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50%",
                    "Matches": [
                        {
                            "Text": "50%",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        },
        {
            "Id": "2",
            "Entities": [
                {
                    "Name": "Great Depression",
                    "Matches": [
                        {
                            "Text": "The Great Depression",
                            "Offset": 0,
                            "Length": 20
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Great Depression",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                    "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                },
                {
                    "Name": "1929",
                    "Matches": [
                        {
                            "Text": "1929",
                            "Offset": 30,
                            "Length": 4
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "By 1933",
                    "Matches": [
                        {
                            "Text": "By 1933",
                            "Offset": 36,
                            "Length": 7
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "Gross domestic product",
                    "Matches": [
                        {
                            "Text": "GDP",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Gross domestic product",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                    "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                },
                {
                    "Name": "United States",
                    "Matches": [
                        {
                            "Text": "America",
                            "Offset": 56,
                            "Length": 7
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "United States",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                    "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                    "Type": "Location"
                },
                {
                    "Name": "25",
                    "Matches": [
                        {
                            "Text": "25",
                            "Offset": 72,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "25%",
                    "Matches": [
                        {
                            "Text": "25%",
                            "Offset": 72,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        }
    ],
    "Errors": []
}
```


## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és a Cognitive Services Text Analytics használatával entitáskapcsolás munkafolyamatokat. Az Összegzés:

+ [Entitások API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) érhető el a kiválasztott nyelveken.
+ A kérelem törzsében szereplő JSON-dokumentumok közé tartozik egy azonosító, a szöveg és a nyelvi kódot.
+ POST-kérelmet, hogy egy `/entities` végpontra, a személyre szabott [kulcs és a egy végpont elérésére](text-analytics-how-to-access-key.md) Ez érvényes az előfizetéshez.
+ Válasz kimenete, amely entitáskészlet (beleértve a megbízhatósági pontszámok eltolások és webes hivatkozások, minden egyes dokumentum-azonosító) áll használható bármely alkalmazásban

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzési API-val](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)
