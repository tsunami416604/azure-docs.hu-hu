---
title: Kulcskifejezések kinyerése a Text Analytics REST API használatával |} A Microsoft Docs
description: Hogyan kinyerheti a kulcsfontosságú kifejezéseket, az Azure Cognitive services Text Analytics REST API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: bbf72847dd9d9a29bf1f2fa0574b83194d07a5c6
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245609"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Példa: Hogyan kinyerheti a kulcsfontosságú kifejezéseket, Szövegelemzés használata

A [Key Phrase Extraction API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) strukturálatlan szöveget értékel ki és minden JSON-dokumentumra visszaadja a kulcsszavak listáját. 

Ez a funkció akkor hasznos, ha szeretné gyorsan azonosítani a dokumentum gyűjtemény fő témáit. Például „Az étel finom, a személyzet nagyszerű volt” bemeneti szövegre a szolgáltatás visszaadja a fő kulcsszavakat: „étel” és „nagyszerű személyzet”.

Jelenleg a Key Phrase Extraction az angol, német, spanyol és japán nyelvet támogatja. Más nyelvek előzetes verzióban érhetők el. További információk: [Támogatott nyelvek](../text-analytics-supported-languages.md).

> [!TIP]
> Szövegelemzés is biztosít a Linux-alapú Docker tároló rendszerképének kulcsszókeresés, így [telepítheti és futtathatja a Text Analytics tároló](text-analytics-how-to-install-containers.md) közel az adatokat.

## <a name="preparation"></a>Előkészítés

Kulcskifejezések kinyerése akkor működik a legjobban, amikor nagyobb mennyiségű dolgozhatnak szöveg engedélyezi azt. Ez a ellentétes a hangulatelemzés, melyik teljesít jobban a szöveg kisebb mennyiségű. A legjobb eredmény elérése érdekében célszerű a bemenetet ennek megfelelően átszervezni.

A JSON-dokumentumnak ilyen formátumban kell lennie: azonosító, szöveg, nyelv

A dokumentum méretének 5000 karakter alatt kell maradnia, és legfeljebb 1000 elem (azonosító) lehet egy kollekcióban. A kollekció elküldése a kérelem törzsében történik. A következő példa egy kulcsszókeresésre beküldhető szöveget mutat be.

```json
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
    
## <a name="step-1-structure-the-request"></a>1. lépés: A kérelem struktúra

A kérés definícióval kapcsolatos részletek megtalálhatók a [Text Analytics API hívásának módja](text-analytics-how-to-call-api.md) részben. A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy **POST** kérést. Tekintse át a kérelem API-dokumentáció: [Kulcskifejezések API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ Állítsa be a HTTP-végpontot Text Analytics erőforrás használata az Azure- vagy egy példányosított kulcsszókeresés [Szövegelemzés tároló](text-analytics-how-to-install-containers.md). Tartalmaznia kell a `/keyPhrases` erőforrást: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`

+ A kérés fejlécet állítsa be úgy, hogy tartalmazza a Text Analytics műveletekhez a hozzáférési kulcsot. További információkért lásd: [Végpontok és hozzáférési kulcsok megkeresése](text-analytics-how-to-access-key.md).

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2. lépés: A kérelem küldése

Az elemzés a kérelem megkapásakor történik meg. A szolgáltatás percenként legfeljebb 100 kérést fogad. Mindegyik kérés legfeljebb 1 MB lehet.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.

## <a name="step-3-view-results"></a>3. lépés: Eredmények megtekintése

Minden POST kérés egy JSON formátumú választ ad vissza az azonosítókkal és az észlelt tulajdonságokkal.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba.

Itt látható egy példa a kimenetre, a kulcsszókeresés:

```json
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

Amint említettük, az elemző törli a lényegtelen szavakat és azokat a szavakat vagy kifejezéseket tartja meg, amelyek a mondat lényegéhez, tárgyához tartozónak tűnnek. 

## <a name="summary"></a>Összegzés

Ebben a cikkben a kulcsszókeresés elveivel és folyamatával ismerkedett meg a Cognitive Services Text Analytics használatával. Összegezve:

+ A [Kulcsszókeresés API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) csak egyes nyelvekre érhető el.
+ A kérés törzsében szereplő JSON-dokumentumok azonosítót, szöveget és nyelvkódot tartalmaznak.
+ POST-kérés a `/keyphrases` végpontra, az előfizetésre érvényes személyre szabott [hozzáférési kulcs és végpont](text-analytics-how-to-access-key.md) használatával.
+ A válasz kimenet, amely minden dokumentumazonosítóhoz tartalmazza a kulcsszavakat és kifejezéseket, továbbítható bármilyen JSON-t elfogadó alkalmazáshoz, beleértve az Excelt és a Power BI-t, hogy csak néhányat említsünk.

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Text Analytics API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
