---
title: Szöveg Analytics REST API-ban (Microsoft Azure kognitív szolgáltatások) – útmutató legfontosabb kifejezés kivonása |} Microsoft Docs
description: Bontsa ki a szöveg Analytics REST API használatával a Microsoft Azure kognitív Services bemutató oktatóanyag legfontosabb kifejezések módjáról.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: 78b100e737242fa9f56e50275ef2038d8895349e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347439"
---
# <a name="how-to-extract-key-phrases-in-text-analytics"></a>Szövegelemzések a legfontosabb kifejezések kicsomagolása

A [kulcs kifejezés kibontási API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) strukturálatlan szöveg kiértékeli, és minden JSON-dokumentum, a legfontosabb kifejezések listáját adja vissza. 

Ez a funkció akkor hasznos, ha szeretné gyorsan azonosíthatja a dokumentumok gyűjtemény főbb pontjai. Például adott bemeneti szöveg "az étele delicious volt, és nem találhatók a csodálatos személyzet", a szolgáltatás adja vissza a fő beszélő pontokat: "étele" és "csodálatos személyzet".

Kulcs kifejezés kibontási jelenleg angol, német, spanyol és japán. Egyéb nyelvek még csak előzetes verziójúak. További információkért lásd: [támogatott nyelv](../text-analytics-supported-languages.md).

## <a name="preparation"></a>Előkészítése

Kulcs kifejezés kibontási működik optimálisan, ha a szöveg elérje a nagyobb mennyiségű adjon neki. Ez az ellentétes a céggel kapcsolatos véleményeket elemzés, melyik teljesít jobban kisebb címblokkok szöveg. Ahhoz, hogy a legjobb eredmények elérése érdekében a mindkét művelettől, fontolja meg a át lesznek strukturálva a bemenetek ennek megfelelően.

JSON-dokumentumok rendelkeznie kell a következő formátumban: azonosító, a szöveg, a nyelvi

Dokumentum mérete dokumentumonként a 5000 karakterből állhat, illetve azt, hogy legfeljebb 1000 gyűjteményenként (azonosítók) elemet. A gyűjtemény elküldve a kérelem törzsében. A következő példa olyan bemutatásáért, előfordulhat, hogy küldje el kulcs kifejezés kiolvasásához tartalom.

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
    
## <a name="step-1-structure-the-request"></a>1. lépés: A kérés struktúra

A kérelem definíciója részletek megtalálhatók a [hogyan hívhatja meg a szöveg Analytics API](text-analytics-how-to-call-api.md). Kényelmi vannak megfelelően a következő szempontokat:

+ Hozzon létre egy **POST** kérelmet. Tekintse át a kérelem az API dokumentációjának: [kulcs kifejezések API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ Állítsa be a HTTP-végpont kulcs kifejezés kiolvasásához. Tartalmaznia kell a `/keyphrases` erőforrás: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`

+ Állítsa be a hozzáférési kulcsot a következő Szövegelemzések műveletek közé tartozik a fejléc. További információkért lásd: [végpontok kereséséhez, és hozzáférési kulcsokkal](text-analytics-how-to-access-key.md).

+ A kérelem törzsében szereplő adja meg az elemzés az Felkészülés JSON documents gyűjtemény

> [!Tip]
> Használja [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg a **API-tesztelési konzol** a a [dokumentáció](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) struktúra egy kérelmet, és KÖZZÉTEHETI a szolgáltatás.

## <a name="step-2-post-the-request"></a>2. lépés: Küldje a kérelmet

Elemzés a kérelem történik. A szolgáltatás fogadja a legfeljebb 100 kérelmek / perc. Minden egyes kérelem legfeljebb 1 MB méretű is lehet.

A visszaírási, hogy állapot nélküli-e a szolgáltatás. Adatot nem tárolja a fiókját. Eredményeinek azonnal a válaszban.

## <a name="step-3-view-results"></a>3. lépés: Az eredmények megtekintése

Minden POST kérelemhez vissza JSON formátumú válasz azonosítókkal és Tulajdonságok észlelhetők.

Kimeneti azonnal adja vissza. Az eredmények JSON elfogadó alkalmazás adatfolyamként vagy kimenetét mentse fájlba a helyi rendszer, és importálja azt az alkalmazást, amely lehetővé teszi rendezni, keresése és az adatok kezelését.

A kulcs kifejezés kiolvasásához kimeneti példa tovább:

```
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

Amint azt a analyzer talál és elveti a nem szükséges szavak, és egy szót vagy kifejezést, és a tulajdonos vagy a mondatok objektum tartja. 

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és a munkafolyamat kulcs kifejezés kiolvasásához Szövegelemzések kognitív szolgáltatások használatával. Összefoglalva:

+ [Kulcs kifejezés kibontási API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) kiválasztott nyelveken érhető el.
+ A kérelem törzsében szereplő JSON-dokumentumok közé tartozik egy azonosítót, a szöveg és a nyelvi kódot.
+ POST-kérelmet, hogy egy `/keyphrases` végpont, egy személyre szabott [hozzáférési kulcs és egy végpontot](text-analytics-how-to-access-key.md) érvényes az előfizetéséhez.
+ Válasz kimenete, amely kulcs szavak és kifejezések minden dokumentum azonosítóhoz, továbbítható bármely alkalmazásba, amely támogatja a JSON-NÁ, beleértve az Excel és a Power bi-ban, hogy néhányat említsünk.

## <a name="see-also"></a>Lásd még 

 [Szöveg elemzés áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Szöveg Analytics termék oldalát](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzések API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
