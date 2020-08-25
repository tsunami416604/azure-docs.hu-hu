---
title: Nyelv felismerése a Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services Text Analytics REST API használatával azonosíthatja a nyelvet.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 5b3893dce2d20b1de0a78f11263d880e262098d2
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "84142384"
---
# <a name="example-detect-language-with-text-analytics"></a>Példa: nyelv felismerése Text Analytics

Az Azure REST API Text Analytics [nyelvfelismerés](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) funkciója minden dokumentumhoz kiértékeli a szövegbeviteli adatokat, és egy olyan pontszámmal rendelkező nyelvi azonosítót ad vissza, amely az elemzés erősségét jelzi.

Ez a funkció véletlen szöveget gyűjtő tartalom áruházak számára hasznos, amikor a nyelv ismeretlen. Az analízis eredményei elemezhetők annak meghatározására, hogy milyen nyelvet használ a bemeneti dokumentum. A válasz a modell megbízhatóságát tükröző pontszámot is visszaad. A pontszám értéke 0 és 1 között van.

A Nyelvfelismerés funkció számos nyelvet, változatot, dialektust és néhány regionális vagy kulturális nyelvet képes felderíteni. A szolgáltatáshoz tartozó nyelvek pontos listája nincs közzétéve.

Ha a tartalom ritkábban használt nyelven van kifejezve, kipróbálhatja a Nyelvfelismerés funkciót, és megtekintheti, hogy visszaadja-e a kódot. A nem észlelhető nyelvek válasza a következő: `unknown` .

> [!TIP]
> A Text Analytics egy Linux-alapú Docker-tároló rendszerképet is biztosít a nyelvfelismerés számára, így a Text Analytics tárolót az adatokhoz közelebb is [telepítheti és futtathatja](text-analytics-how-to-install-containers.md) .

## <a name="preparation"></a>Előkészítés

A JSON-dokumentumoknak ebben a formátumban kell szerepelniük: azonosító és szöveg.

A dokumentum méretének 5 120 karakternél rövidebbnek kell lennie a dokumentumban. Egy gyűjteményhez legfeljebb 1 000 elem (azonosító) tartozhat. A kollekció elküldése a kérelem törzsében történik. A következő minta egy példa arra, hogy milyen tartalmat lehet beküldeni a nyelvfelismerés számára:

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>1. lépés: A kérés felépítése

A kérelem meghatározásával kapcsolatos további információkért lásd [a Text Analytics API meghívása](text-analytics-how-to-call-api.md)című témakört. A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy POST kérést. A kérelem API-dokumentációjának áttekintéséhez tekintse meg a [NYELVFELISMERÉS API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)-t.

+ Állítsa be a HTTP-végpontot a nyelvfelismeréshez. Használjon Text Analytics erőforrást az Azure-ban vagy egy példányban [text Analytics tárolóban](text-analytics-how-to-install-containers.md). Meg kell adnia `/text/analytics/v3.0/languages` az URL-címet. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`.

+ Állítsa be a kérelem fejlécét, hogy tartalmazza a Text Analytics műveletekhez tartozó [hozzáférési kulcsot](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) .

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2. lépés: a kérelem közzététele

Az elemzés a kérelem megkapásakor történik meg. További információ a másodpercenként elküldhető kérelmek méretéről és számáról: az [adatkorlátozások](../overview.md#data-limits) szakasz az áttekintésben.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.


## <a name="step-3-view-the-results"></a>3. lépés: az eredmények megtekintése

Az összes POST kérelem egy JSON-formátumú választ ad vissza az azonosítókkal és az észlelt tulajdonságokkal.

A kimenetet visszaadása azonnali. Az eredményeket egy olyan alkalmazásba is továbbíthatja, amely fogadja a JSON-t, vagy mentse a kimenetet egy fájlba a helyi rendszeren. Ezután importálja a kimenetet egy olyan alkalmazásba, amelyet az adatrendezéshez, kereséshez és kezeléshez használhat.

A példa kérés eredményének a következő JSON-hoz hasonlóan kell kinéznie. Figyelje meg, hogy egyetlen dokumentum több elemmel. A kimenet angol nyelvű. A nyelvi azonosító rövid nevet és [ISO 639-1](https://www.iso.org/standard/22109.html) formátumú nyelvkódot tartalmaz.

Az 1.0 pozitív pontszám a lehetséges legnagyobb megbízhatósági szintet jelenti az elemzésre.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "French",
                "iso6391Name": "fr",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "4",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "5",
            "detectedLanguage": {
                "name": "Russian",
                "iso6391Name": "ru",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

### <a name="ambiguous-content"></a>Nem egyértelmű tartalom

Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `countryHint` paraméter segítségével megadhatja a kétbetűs ország/régió kódját. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztringre állítja `countryHint = ""` .

Előfordulhat például, hogy a "Impossible" kifejezés az angol és a francia nyelvre is vonatkozik, és ha korlátozott kontextusban van megadva, a válasz az "USA" ország-/régió-emlékeztetőn fog alapulni. Ha ismert, hogy a szöveg eredete Franciaország, ez megadható tippként.

**Bevitel**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

A szolgáltatás most már rendelkezik további kontextussal a jobb döntés érdekében: 

**Kimenet**

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "confidenceScore": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "confidenceScore": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Ha az analizátor nem tudja elemezni a bemenetet, a függvény visszaadja `(Unknown)` . Ilyen például, ha olyan szöveges blokkot küld be, amely kizárólag Arab számokból áll.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "confidenceScore": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Vegyes nyelvi tartalom

Az ugyanabban a dokumentumban található vegyes nyelvi tartalom a tartalom legnagyobb ábrázolását, de alacsonyabb pozitív minősítéssel rendelkező nyelvet ad vissza. A minősítés az értékelés marginális erősségét tükrözi. A következő példában a bemeneti adat angol, német és francia nyelv kombinációja. Az elemző minden szegmensben megszámolja a karakterek számát az uralkodó nyelv meghatározásához.

**Bevitel**

```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
      ]
    }
```

**Kimenet**

Az eredményül kapott kimenet a domináns nyelvből áll, amelynek a pontszáma kisebb, mint 1,0, ami gyengébb megbízhatósági szintet jelez.

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "confidencescore": 0.94
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>Összefoglalás

Ebben a cikkben az Azure-Cognitive Services Text Analytics használatával megtanulta a nyelvfelismerés fogalmait és munkafolyamatát. A következő pontokat ismertették és mutatták be:

+ A [nyelvfelismerés](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) számos nyelven, változatban, dialektusban, illetve egyes regionális vagy kulturális nyelveken érhető el.
+ A kérelem törzsében található JSON-dokumentumok közé tartozik egy azonosító és egy szöveg.
+ A POST kérelem a `/languages` végponthoz egy személyre szabott [hozzáférési kulccsal és egy](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , az előfizetéséhez érvényes végpontot használ.
+ A válasz kimenete az egyes dokumentumok AZONOSÍTÓinak nyelvi azonosítóit tartalmazza. A kimenet továbbítható bármely olyan alkalmazás számára, amely elfogadja a JSON-t. Az alkalmazások közé tartoznak például az Excel és a Power BI, hogy csak néhányat említsünk.

## <a name="see-also"></a>Lásd még

* [A Text Analytics áttekintése](../overview.md)
* [Az Text Analytics ügyféloldali kódtár használata](../quickstarts/text-analytics-sdk.md)
* [Újdonságok](../whats-new.md)
