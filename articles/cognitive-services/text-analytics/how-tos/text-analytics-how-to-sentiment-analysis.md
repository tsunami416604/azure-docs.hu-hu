---
title: Szöveg Analytics REST API-t (Microsoft Azure kognitív szolgáltatások) – útmutató véleményeket elemzése |} Microsoft Docs
description: Megtudhatja, hogyan észleli a szöveg Analytics REST API használatával a Microsoft Azure kognitív Services bemutató oktatóanyag céggel kapcsolatos véleményeket.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 12/11/2017
ms.author: heidist
ms.openlocfilehash: 7ffd8bbe47409b459fdd308cd8d670d32f56649b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347442"
---
# <a name="how-to-detect-sentiment-in-text-analytics"></a>Hogyan azonosíthatók a céggel kapcsolatos véleményeket Szövegelemzések

A [véleményeket elemzés API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) szöveges bevitel kiértékeli, és minden egyes dokumentumhoz (negatív) 0 és 1 (pozitív) közötti véleményeket pontszámot adja vissza.

Ez a funkció akkor hasznos, a közösségi média, az ügyfél értékelést és fórumain felismeri a pozitív és negatív céggel kapcsolatos véleményeket. Tartalom biztosítja. a szolgáltatás által biztosított modellek és betanítási adata.

Véleményeket elemzés jelenleg angol, német, német és francia. Egyéb nyelvek még csak előzetes verziójúak. További információkért lásd: [támogatott nyelv](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Alapelvek

Szövegelemzések használják a machine learning-osztályozó algoritmus 0 és 1 közötti véleményeket pontszámot létrehozásához. Pontszámok közelebb 1 pozitív véleményeket azt jelzik, amíg pontszámok közelebb 0 jelző negatív céggel kapcsolatos véleményeket. A modell egy kiterjedt szervezethez véleményeket társítások szöveg van pretrained. Jelenleg nincs lehetőség arra, hogy a saját betanítási adata. A modell technikák kombinációjával szöveg elemzés, beleértve a szöveg feldolgozása, része a beszédfelismerés elemzés, word elhelyezési és word társítások során használ. Az algoritmus kapcsolatos további információkért lásd: [Szövegelemzések bevezetéséről](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

A teljes dokumentumot, szemben a szöveg egy adott entitás véleményeket kibontása véleményeket elemzés történik. A gyakorlatban nincs csökkenő tendenciát pontossága pontozó javítják a dokumentumok tartalmazzák a nagy szövegblokk helyett egy vagy két mondatokat. Egy objektivitás felmérési fázis során a modell meghatározza, hogy e egész dokumentum célja, és tartalmazza a céggel kapcsolatos véleményeket. A dokumentum, amely nincs többnyire objektív biztosítja a céggel kapcsolatos véleményeket észlelési kifejezés.50 pontszám, további feldolgozással eredményezve folyamatban. Az adatcsatorna Folytatás dokumentumok esetében a következő fázis állít elő,.50, attól függően, hogy a céggel kapcsolatos véleményeket észlelte a dokumentum bizonyos fokú alatti vagy feletti pontszámot.

## <a name="preparation"></a>Előkészítése

Véleményeket elemzés eredménye egy magasabb minőségi amikor elérje a szöveg kisebb csoportjai adjon neki. Ez az ellentétes a fő kifejezés kinyerési, melyik teljesít jobban nagyobb címblokkok szöveg. Ahhoz, hogy a legjobb eredmények elérése érdekében a mindkét művelettől, fontolja meg a át lesznek strukturálva a bemenetek ennek megfelelően.

JSON-dokumentumok rendelkeznie kell a következő formátumban: azonosító, a szöveg, a nyelvi

Dokumentum mérete dokumentumonként a 5000 karakterből állhat, illetve azt, hogy legfeljebb 1000 gyűjteményenként (azonosítók) elemet. A gyűjtemény elküldve a kérelem törzsében. Például előfordulhat, hogy küldje el a céggel kapcsolatos véleményeket elemzés tartalom a következő:

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

+ Hozzon létre egy **POST** kérelmet. Tekintse át a kérelem az API dokumentációjának: [véleményeket elemzés API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)

+ Állítsa be a HTTP-végpont kulcs kifejezés kiolvasásához. Tartalmaznia kell a `/sentiment` erőforrás: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`

+ Állítsa be a hozzáférési kulcsot a következő Szövegelemzések műveletek közé tartozik a fejléc. További információkért lásd: [végpontok kereséséhez, és hozzáférési kulcsokkal](text-analytics-how-to-access-key.md).

+ Adja meg az elemzés az Felkészülés JSON documents gyűjtemény a kérés törzsében.

> [!Tip]
> Használja [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg a **API-tesztelési konzol** a a [dokumentáció](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) szerkezeti a kérelmet, és KÖZZÉTEHETI a szolgáltatás.

## <a name="step-2-post-the-request"></a>2. lépés: Küldje a kérelmet

Elemzés a kérelem történik. A szolgáltatás fogadja a legfeljebb 100 kérelmek / perc. Minden egyes kérelem legfeljebb 1 MB méretű is lehet.

A visszaírási, hogy állapot nélküli-e a szolgáltatás. Adatot nem tárolja a fiókját. Eredményeinek azonnal a válaszban.


## <a name="step-3-view-results"></a>3. lépés: Az eredmények megtekintése

A céggel kapcsolatos véleményeket analyzer túlnyomórészt pozitív vagy negatív hozzárendelése egy 0 és 1 közé pontszám megadott szöveg osztályozza. Hamarosan 0,5 értékei semleges vagy meghatározatlan. A pontszám 0,5 semlegesség jelzi. Ha a karakterlánc nem elemezhető a céggel kapcsolatos véleményeket, és nem céggel kapcsolatos véleményeket rendelkezik, a pontszám mindig van-e 0,5 pontosan. Például ha egy angol nyelvű kód spanyol karakterláncnak át, a pontszám érték 0,5.

Kimeneti azonnal adja vissza. Az eredmények JSON elfogadó alkalmazás adatfolyamként vagy kimenetét mentse fájlba a helyi rendszer, és importálja azt az alkalmazást, amely lehetővé teszi rendezni, keresése és az adatok kezelését.

A következő példa bemutatja a válasz a dokumentum gyűjtemény ebben a cikkben.

```
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

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és a munkafolyamat Szövegelemzések használatát kognitív szolgáltatások véleményeket elemzés céljából. Összefoglalva:

+ [Véleményeket elemzés API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) kiválasztott nyelveken érhető el.
+ A kérelem törzsében szereplő JSON-dokumentumok közé tartozik egy azonosítót, a szöveg és a nyelvi kódot.
+ POST-kérelmet, hogy egy `/sentiment` végpont, egy személyre szabott [hozzáférési kulcs és egy végpontot](text-analytics-how-to-access-key.md) érvényes az előfizetéséhez.
+ Válasz kimenete, amely a céggel kapcsolatos véleményeket pontszám áll minden dokumentum azonosítóhoz, továbbítható bármely alkalmazásba, amely támogatja a JSON-NÁ, beleértve az Excel és a Power bi-ban, hogy néhányat említsünk.

## <a name="see-also"></a>Lásd még 

 [Szöveg elemzés áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Szöveg Analytics termék oldalát](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bontsa ki a legfontosabb kifejezések](text-analytics-how-to-keyword-extraction.md)
