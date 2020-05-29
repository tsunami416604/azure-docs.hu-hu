---
title: Kulcs kifejezésének kinyerése a Text Analytics használatával REST API
titleSuffix: Azure Cognitive Services
description: A legfontosabb kifejezések kinyerése az Azure Cognitive Services Text Analytics REST API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: c1ca14b8471ef6257c0603e61d78e789e846f0ae
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142401"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Példa: a fő kifejezések kinyerése Text Analytics használatával

A [Key Phrase Extraction API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) strukturálatlan szöveget értékel ki és minden JSON-dokumentumra visszaadja a kulcsszavak listáját.

Ez a funkció akkor hasznos, ha szeretné gyorsan azonosítani a dokumentum gyűjtemény fő témáit. Például „Az étel finom, a személyzet nagyszerű volt” bemeneti szövegre a szolgáltatás visszaadja a fő kulcsszavakat: „étel” és „nagyszerű személyzet”.

További információk: [Támogatott nyelvek](../text-analytics-supported-languages.md).

> [!TIP]
> A Text Analytics egy Linux-alapú Docker-tároló rendszerképet is biztosít a fő kifejezés kinyeréséhez, így az adatokhoz [az Text Analytics tárolót is telepítheti és futtathatja](text-analytics-how-to-install-containers.md) .

## <a name="preparation"></a>Előkészítés

A Key kifejezés kinyerése a legjobban akkor működik, ha nagyobb mennyiségű szöveget ad meg a munkához. Ez az érzelmek elemzésével ellentétes, ami jobb a kisebb mennyiségű szövegnél. A legjobb eredmény elérése érdekében célszerű a bemenetet ennek megfelelően átszervezni.

A JSON-dokumentumoknak ebben a formátumban kell szerepelniük: azonosító, szöveg, nyelv

A dokumentum méretének 5 120 vagy kevesebb karakterből kell állnia, és egy gyűjteményben legfeljebb 1 000 elem (azonosító) adható meg. A kollekció elküldése a kérelem törzsében történik. A következő példa egy kulcsszókeresésre beküldhető szöveget mutat be.

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

További információ a kérelem meghatározásáról: [a Text Analytics API meghívása](text-analytics-how-to-call-api.md). A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy **POST** kérést. Tekintse át a kérelem API-dokumentációját: [Key kifejezések API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases).

+ Állítsa be a HTTP-végpontot a kulcsfontosságú kifejezés kinyeréséhez az Azure-ban vagy egy példányban [text Analytics tárolóban](text-analytics-how-to-install-containers.md)Text Analytics erőforrás használatával. Meg kell adnia `/text/analytics/v3.0/keyPhrases` az URL-címet. Például: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

+ Állítsa be a kérelem fejlécét, hogy tartalmazza a Text Analytics műveletekhez tartozó [hozzáférési kulcsot](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) .

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2. lépés: A kérés elküldése

Az elemzés a kérelem megkapásakor történik meg. A percenként vagy másodpercenként küldendő kérelmek méretével és számával kapcsolatos információkért tekintse meg az áttekintés című témakör [adatkorlátok](../overview.md#data-limits) című szakaszát.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.

## <a name="step-3-view-results"></a>3. lépés: Eredmények megtekintése

Minden POST kérés egy JSON formátumú választ ad vissza az azonosítókkal és az észlelt tulajdonságokkal. A visszaadott kulcsokra vonatkozó kifejezések sorrendjét a modell határozza meg belsőleg.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba.

Itt látható egy példa a Key kifejezés kinyerésének kimenetére:

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

Ahogy azt a feljegyezte, az elemző megkeresi és elveti a nem alapvető szavakat, és megtartja a mondat tárgyát vagy tárgyát képező egyszeres kifejezéseket vagy kifejezéseket.

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta a kulcsfontosságú kifejezések kinyeréséhez szükséges fogalmakat és munkafolyamatokat a Cognitive Services Text Analytics használatával. Összegezve:

+ A [Kulcsszókeresés API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) csak egyes nyelvekre érhető el.
+ A kérelem törzsében található JSON-dokumentumok közé tartozik az azonosító, a szöveg és a nyelvi kód.
+ POST-kérés a `/keyphrases` végpontra, az előfizetésre érvényes személyre szabott [hozzáférési kulcs és végpont](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) használatával.
+ A válasz kimenete, amely az egyes dokumentumok AZONOSÍTÓinak legfontosabb szavaiból és kifejezésből áll, továbbítható bármely olyan alkalmazásnak, amely elfogadja a JSON-t, beleértve a Microsoft Office Excel és Power BI is, hogy csak néhányat említsünk.

## <a name="see-also"></a>Lásd még

 [Text Analytics áttekintése –](../overview.md) [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Következő lépések

* [Text Analytics áttekintése](../overview.md)
* [Az Text Analytics ügyféloldali kódtár használata](../quickstarts/text-analytics-sdk.md)
* [Újdonságok](../whats-new.md)
