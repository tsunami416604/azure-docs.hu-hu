---
title: Kulcskifejezések kinyerése a Text Analytics REST API használatával
titleSuffix: Azure Cognitive Services
description: Kulcskifejezések kinyerése a Text Analytics REST API használatával az Azure Cognitive Servicesből.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: ec5ff756d7e732430675676868bc754627a2a4a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72429021"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Példa: Kulcskifejezések kinyerése a Text Analytics használatával

A [Key Phrase Extraction API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) strukturálatlan szöveget értékel ki és minden JSON-dokumentumra visszaadja a kulcsszavak listáját.

Ez a funkció akkor hasznos, ha szeretné gyorsan azonosítani a dokumentum gyűjtemény fő témáit. Például „Az étel finom, a személyzet nagyszerű volt” bemeneti szövegre a szolgáltatás visszaadja a fő kulcsszavakat: „étel” és „nagyszerű személyzet”.

További információk: [Támogatott nyelvek](../text-analytics-supported-languages.md).

> [!TIP]
> A Text Analytics egy Linux-alapú Docker-tárolórendszerképet is biztosít a kulcskifejezések kinyeréséhez, így [telepítheti és futtathatja a Text Analytics-tárolót az](text-analytics-how-to-install-containers.md) adatok közelében.

## <a name="preparation"></a>Előkészítés

A kulcskifejezések kinyerése akkor működik a legjobban, ha nagyobb mennyiségű szöveget ad a munkához. Ez ellentétes a hangulatelemzéssel, amely kisebb mennyiségű szövegesetén jobban teljesít. A legjobb eredmény elérése érdekében célszerű a bemenetet ennek megfelelően átszervezni.

A JSON-dokumentumoknak ebben a formátumban kell rendelkezniük: azonosító, szöveg, nyelv

A dokumentum méretének dokumentumonként legfeljebb 5120 karakternek kell lennie, és gyűjteményenként legfeljebb 1000 elem (azonosító) lehet. A kollekció elküldése a kérelem törzsében történik. A következő példa egy kulcsszókeresésre beküldhető szöveget mutat be.

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

## <a name="step-1-structure-the-request"></a>1. lépés: A kérés felépítése

A kérelemdefinícióról a [Szövegelemzési API hívása](text-analytics-how-to-call-api.md)című témakörben talál további információt. A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy **POST** kérést. Tekintse át a kérelem API-dokumentációját: [Key Phrases API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6).

+ Állítsa be a HTTP-végpontot a kulcskifejezések kinyeréséhez egy Azure-beli Text Analytics-erőforrással vagy egy példányosított [text analytics-tárolóval.](text-analytics-how-to-install-containers.md) Az URL-címben szerepelnie `/text/analytics/v2.1/keyPhrases` kell. Például: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v2.1/keyPhrases`.

+ Állítsa be a kérelem fejlécét, hogy tartalmazza a szövegelemzési műveletek [hozzáférési kulcsát.](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2. lépés: A kérés elküldése

Az elemzés a kérelem megkapásakor történik meg. A percenként vagy másodpercenként küldhető kérelmek méretéről és számáról az [áttekintő adatkorlátok](../overview.md#data-limits) című szakaszban olvashat.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.

## <a name="step-3-view-results"></a>3. lépés: Eredmények megtekintése

Minden POST kérés egy JSON formátumú választ ad vissza az azonosítókkal és az észlelt tulajdonságokkal. A visszaadott kulcskifejezések sorrendjét a modell határozza meg belsőleg.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba.

A kulcskifejezések kinyerésének kimenetére példa látható:

```json
    {
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
        ],
        "errors": []
    }
```

Mint említettük, az analizátor megkeresi és elveti a nem alapvető fontosságú szavakat, és megtartja azokat az egyes kifejezéseket vagy kifejezéseket, amelyek egy mondat tárgyaként vagy tárgyában jelennek meg.

## <a name="summary"></a>Összefoglalás

Ebben a cikkben a Cognitive Services Text Analytics használatával ismertetheti a kulcskifejezések kinyerésének fogalmait és munkafolyamatait. Összegezve:

+ A [Kulcsszókeresés API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) csak egyes nyelvekre érhető el.
+ A kérelem törzsében lévő JSON-dokumentumok azonosítót, szöveget és nyelvi kódot tartalmaznak.
+ POST-kérés a `/keyphrases` végpontra, az előfizetésre érvényes személyre szabott [hozzáférési kulcs és végpont](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) használatával.
+ A válaszkimenet, amely az egyes dokumentumazonosítók hoz tartozó kulcsszavakból és kifejezésekből áll, továbbítható bármely olyan alkalmazásba, amely elfogadja a JSON-t, beleértve a Microsoft Office Excelt és a Power BI-t is, hogy csak néhányat említsünk.

## <a name="see-also"></a>Lásd még

 [Szövegelemzés – áttekintés](../overview.md) [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Text Analytics API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
