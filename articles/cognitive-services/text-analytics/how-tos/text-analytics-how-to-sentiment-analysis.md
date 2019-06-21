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
ms.openlocfilehash: d4269a99a8e535692e4897630a7edd9b27347d41
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304038"
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

## <a name="step-2-post-the-request"></a>2\. lépés: A kérelem küldése

Az elemzés a kérelem megkapásakor történik meg. Tekintse meg a [data korlátai](../overview.md#data-limits) szakasz az áttekintésben a méret és a kérelmek percenkénti küldhet és a második kapcsolatos információkat.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.


## <a name="step-3-view-results"></a>3\. lépés: Eredmények megtekintése

A hangulatelemző a szöveget nagyobbrészt pozitívnak vagy negatívnak sorolja be, és 0 és 1 közötti pontszámot rendel hozzá. A 0,5 közeli értékek semlegesek vagy határozatlanok. A 0,5-ös pontszám semlegességet jelez. Ha a karakterlánc nem elemezhető hangulat szempontjából vagy nincs hangulata, a pontszám mindig pontosan 0,5. Ha például egy spanyol nyelvű szöveget ad meg angol nyelvi kóddal, a pontszám 0,5 lesz.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba.

Az alábbi példa a cikkbeli dokumentumgyűjteményre kapott választ mutatja.

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

## <a name="summary"></a>Összefoglalás

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
