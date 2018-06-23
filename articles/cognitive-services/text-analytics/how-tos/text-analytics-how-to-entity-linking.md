---
title: Útmutató használható entitás Linking szöveg Analytics REST API-t (Microsoft Azure kognitív szolgáltatások) |} Microsoft Docs
description: Megtudhatja, hogyan azonosítani és megoldani a szöveg Analytics REST API használatával a Microsoft Azure kognitív Services bemutató oktatóanyag entitásokat.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: 55bec1a0223b70749a97a30e2da92ef15128038c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347759"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Szövegelemzések (előzetes verzió) a csatolt entitások azonosítása

A [entitás Linking API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) strukturálatlan szöveg vesz igénybe, és minden JSON-dokumentumában, a(z) álló listát ad vissza entitásokat használatát mutató hivatkozásokat tartalmaz további információkat a weben (Wikipedia és a Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>A Linking vs entitás. Megnevezett entitások felismerése

A természetes nyelvű feldolgozása az entitás csatolása és névvel rendelkező entitás felismerési (NER) könnyen összetéveszthetők is. A szöveg Analytics előzetes verziójában `entities` végpont, csak az entitás linking esetén támogatott.

Linking azonosítása és identitása (pl. meghatározása e a "Mars" használatban van a bolygónk vagy háborús a latin Isten) szövegben található entitás értelmezni. Ez a folyamat szükséges a alap, amely a kapcsolódó entitás - Wikipedia használatos a Tudásbázisban felismerés ismerete a `entities` végpont Szövegelemzések.

### <a name="language-support"></a>Nyelvi támogatás

Különböző nyelveken linking entitás használatához egy megfelelő Tudásbázis segítségével mindkét nyelven. Összekapcsolásáról Szövegelemzések az entitás, ez azt jelenti, hogy minden által támogatott nyelvi a `entities` végpont a megfelelő Wikipedia corpus ezen a nyelven fogja csatolni. Mivel nyelvek között corpora méretétől függően változik, várható, hogy a funkció a visszaírási linking entitás is eltérőek lehetnek.


## <a name="preparation"></a>Előkészítése

JSON-dokumentumok rendelkeznie kell a következő formátumban: azonosító, a szöveg, a nyelvi

A jelenleg támogatott nyelvek, lásd: [ebben a listában](../text-analytics-supported-languages.md).

Dokumentum mérete dokumentumonként a 5000 karakterből állhat, illetve azt, hogy legfeljebb 1000 gyűjteményenként (azonosítók) elemet. A gyűjtemény elküldve a kérelem törzsében. A következő példa olyan bemutatásáért, előfordulhat, hogy küldje el az entitás hivatkozási célból tartalom.

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

A kérelem definíciója részletek megtalálhatók a [hogyan hívhatja meg a szöveg Analytics API](text-analytics-how-to-call-api.md). Kényelmi vannak megfelelően a következő szempontokat:

+ Hozzon létre egy **POST** kérelmet. Tekintse át a kérelem az API dokumentációjának: [entitás Linking API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Állítsa be a HTTP-végpont kulcs kifejezés kiolvasásához. Tartalmaznia kell a `/entities` erőforrás: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Állítsa be a hozzáférési kulcsot a következő Szövegelemzések műveletek közé tartozik a fejléc. További információkért lásd: [végpontok kereséséhez, és hozzáférési kulcsokkal](text-analytics-how-to-access-key.md).

+ A kérelem törzsében szereplő adja meg az elemzés az Felkészülés JSON documents gyűjtemény

> [!Tip]
> Használja [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg a **API-tesztelési konzol** a a [dokumentáció](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) struktúra egy kérelmet, és KÖZZÉTEHETI a szolgáltatás.

## <a name="step-2-post-the-request"></a>2. lépés: Küldje a kérelmet

Elemzés a kérelem történik. A szolgáltatás fogadja a legfeljebb 100 kérelmek / perc. Minden egyes kérelem legfeljebb 1 MB méretű is lehet.

A visszaírási, hogy állapot nélküli-e a szolgáltatás. Adatot nem tárolja a fiókját. Eredményeinek azonnal a válaszban.

## <a name="step-3-view-results"></a>3. lépés: Az eredmények megtekintése

Minden POST kérelemhez vissza JSON formátumú válasz azonosítókkal és Tulajdonságok észlelhetők.

Kimeneti azonnal adja vissza. Az eredmények JSON elfogadó alkalmazás adatfolyamként vagy kimenetét mentse fájlba a helyi rendszer, és importálja azt az alkalmazást, amely lehetővé teszi rendezni, keresése és az adatok kezelését.

A kimeneti linking entitás példa tovább:

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

Ha elérhető, akkor a válasz minden észlelt entitás tartalmaz a Wikipedia Azonosítót, a Wikipedia URL-cím és a Bing azonosítója. Ezek segítségével tovább javíthatja a csatolt entitás vonatkozó információkat az alkalmazásba.


## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és a munkafolyamat entitás kapcsolásához Szövegelemzések kognitív szolgáltatások használatával. Összefoglalva:

+ [Entitás Linking API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) kiválasztott nyelveken érhető el.
+ A kérelem törzsében szereplő JSON-dokumentumok közé tartozik egy azonosítót, a szöveg és a nyelvi kódot.
+ POST-kérelmet, hogy egy `/entities` végpont, egy személyre szabott [hozzáférési kulcs és egy végpontot](text-analytics-how-to-access-key.md) érvényes az előfizetéséhez.
+ Válasz kimenete, amely csatolt entitások (beleértve a várható, pontszámokat eltolások és webes hivatkozásokat az egyes dokumentum azonosítója) áll használható bármely alkalmazásban

## <a name="see-also"></a>Lásd még 

 [Szöveg elemzés áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Szöveg Analytics termék oldalát](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzések API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
