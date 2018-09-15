---
title: A Text Analytics API-val entitáskapcsolás használata
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan azonosítani és megoldani a Text Analytics REST API használatával.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/12/2018
ms.author: ashmaka
ms.openlocfilehash: ad2168806f9ddd124faf66cdb5a0f51ed13dfadc
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604739"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>A Text Analytics (előzetes verzió) kapcsolt entitások azonosítása

A [Entitáskapcsolási API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) strukturálatlan szöveges vesz igénybe, és egyes JSON-dokumentumok listáját adja vissza használatát entitásokat is tartalmaz, hivatkozásokkal további információhoz a weben (Wikipédia és a Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>Entitás Linking vs. Megnevezett entitások felismerése

A természetes nyelvi feldolgozás, az entitáskapcsolás és a nevesített entitások felismerése (NER) könnyen összetéveszthetők is. A Text Analytics előzetes verziójának `entities` csak entitáskapcsolás-végpont támogatott.

Entitáskapcsolás rendszer azon képessége, azonosíthatja és a egy entitás (pl. meghatározása-e a "Mars" globális vagy a latin god háború használja) szöveg található az identitás megkülönböztetéséhez. Ez a folyamat, amely felismeri a kapcsolódó entitások – Wikipedia szolgál a Tudásbázisban alapszintű ismerete szükséges a `entities` végpontot Text Analytics.

### <a name="language-support"></a>Nyelvi támogatás

Entitáskapcsolás különféle nyelveken használatához az egyes nyelvekhez tartozó Tudásbázis használatával. A Text Analytics entitáskapcsolás, ez azt jelenti, hogy minden nyelv által támogatott a `entities` végpont kapcsolja a megfelelő Wikipedia forrásgyűjteményébe az adott nyelveken. Korpuszok mérete nyelvek közé esik, mivel várható, hogy a funkció a visszaírási entitáskapcsolás is változnak.


## <a name="preparation"></a>Előkészítés

JSON-dokumentumok rendelkeznie kell a következő formátumban: azonosító, a szöveg, a nyelv

Jelenleg támogatott nyelveket, tekintse meg [ebben a listában](../text-analytics-supported-languages.md).

Dokumentum mérete kell lennie a 5000 karakter / dokumentum, és legfeljebb 1000 rendelkezhet gyűjteményenként (azonosítók) elemet. A gyűjtemény elküldésekor a kérelem törzsében. A következő példa olyan bemutatásáért, előfordulhat, hogy küldje el az entitás hivatkozási célból tartalom.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                },
               {"id": "2",
                "language": "en",
                "text": "The Seattle Seahawks won the Super Bowl in 2014."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>1. lépés: A kérés struktúra

Kérelem definíciója a részletek megtalálhatók a [a szövegelemzési API hívása](text-analytics-how-to-call-api.md). Kényelmi vannak megfelelően a következő szempontokat:

+ Hozzon létre egy **POST** kérelmet. A kérelem API-dokumentációban: [Entitáskapcsolási API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Állítsa a kulcskifejezések kinyerése a HTTP-végpontot. Tartalmaznia kell a `/entities` erőforrás: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Állítsa be a hívóbetűt a Text Analytics műveletek közé tartozik a fejléc. További információkért lásd: [végpontok keresése és hozzáférési kulcsokkal](text-analytics-how-to-access-key.md).

+ A kérelem törzsében szereplő adja meg a JSON-dokumentumok ehhez az elemzéshez előkészített gyűjtemény

> [!Tip]
> Használata [Postman](text-analytics-how-to-call-api.md) , vagy nyissa meg a **API tesztelési konzollal** a a [dokumentáció](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) struktúra egy kérelmet, és KÖZZÉTESZI azokat a szolgáltatást.

## <a name="step-2-post-the-request"></a>2. lépés: A kérés küldése

Elemzés a kérelem történik. A szolgáltatás percenként legfeljebb 100 kéréseket fogad. Minden egyes kérés legfeljebb 1 MB lehet.

Ne felejtse el, hogy az állapot nélküli-e a szolgáltatás. A fiókban tárolt adatok nem. Eredmény akkor azonnal a válaszban.

## <a name="step-3-view-results"></a>3. lépés: Az eredmények megtekintése

Minden POST kérelemhez egy JSON formátumú válasz azonosítókkal és észlelt tulajdonságokat adja vissza.

Kimeneti azonnal adja vissza. Az eredményeket JSON elfogadó alkalmazás adatfolyam vagy kimenetét mentse el egy fájlt a helyi rendszer, és importálja azt egy alkalmazás, amely lehetővé teszi, hogy rendezni, keresése és az adatok kezelésére.

Egy példa a kimenetre entitáskapcsolás a következő látható:

```
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

Ha elérhető, akkor a válasz az egyes észlelt entitásokhoz tartalmaz a Wikipédia-azonosító, a Wikipedia URL-cím és a Bing azonosító. Ezek segítségével tovább javíthatja az alkalmazás a hivatkozott entitáson kapcsolatos információkat.


## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és a Cognitive Services Text Analytics használatával entitáskapcsolás munkafolyamatokat. Az Összegzés:

+ [Entitáskapcsolási API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) érhető el a kiválasztott nyelveken.
+ A kérelem törzsében szereplő JSON-dokumentumok közé tartozik egy azonosító, a szöveg és a nyelvi kódot.
+ POST-kérelmet, hogy egy `/entities` végpontra, a személyre szabott [kulcs és a egy végpont elérésére](text-analytics-how-to-access-key.md) Ez érvényes az előfizetéshez.
+ Válasz kimenete, amely entitáskészlet (beleértve a megbízhatósági pontszámok eltolások és webes hivatkozások, minden egyes dokumentum-azonosító) áll használható bármely alkalmazásban

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzési API-val](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
