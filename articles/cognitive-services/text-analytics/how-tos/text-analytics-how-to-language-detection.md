---
title: Nyelv felismerése a Text Analytics REST API-val
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services Text Analytics REST API-jával észlelheti a nyelvet.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: d34f3a03e1bcd35c270d13c4dda57d0394a36e4b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70387794"
---
# <a name="example-detect-language-with-text-analytics"></a>Példa: Nyelv felismerése a Szövegelemzéssel

Az Azure Text Analytics REST API [Nyelvfelismerési](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) szolgáltatás a szövegbevitelt minden dokumentumhoz kiértékeli, és az elemzés erősségét jelző pontszámmal adja vissza a nyelvi azonosítókat.

Ez a funkció véletlen szöveget gyűjtő tartalom áruházak számára hasznos, amikor a nyelv ismeretlen. Az analízis eredményei elemezhetők annak meghatározására, hogy milyen nyelvet használ a bemeneti dokumentum. A válasz is ad vissza egy pontszámot, amely tükrözi a modell bizalmát. A pontszám értéke 0 és 1 között van.

A Nyelvfelismerés szolgáltatás számos nyelvet, változatot, dialektust, valamint néhány regionális vagy kulturális nyelvet képes észlelni. A szolgáltatás nyelveinek pontos listája nincs közzétéve.

Ha a tartalmat ritkábban használt nyelven fejezi k, próbálja meg a Nyelvfelismerés funkciót, és ellenőrizheti, hogy az visszaad-e egy kódot. A nem észlelhető nyelvekre a `unknown`válasz a .

> [!TIP]
> A Text Analytics egy Linux-alapú Docker-tárolórendszerképet is biztosít a nyelvfelismeréshez, így [telepítheti és futtathatja a Text Analytics-tárolót az](text-analytics-how-to-install-containers.md) adatok közelében.

## <a name="preparation"></a>Előkészítés

A JSON-dokumentumoknak ebben a formátumban kell lennie: azonosító és szöveg.

A dokumentum méretének dokumentumonként 5120 karakternél nem lehet nagyobb. Gyűjteményenként legfeljebb 1000 elem (azonosítók) lehet. A kollekció elküldése a kérelem törzsében történik. A következő minta egy példa a nyelvfelismerésre beküldhető tartalomra:

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

A kérésdefinícióról további információt [a Szövegelemzési API felhívása című témakörben talál.](text-analytics-how-to-call-api.md) A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy POST kérést. A kérelem API-dokumentációjának megtekintéséhez tekintse meg a [Language Detection API-t.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)

+ Állítsa be a HTTP-végpontot a nyelvfelismeréshez. Használjon Text Analytics-erőforrást az Azure-ban vagy egy példányosított [Text Analytics-tárolót.](text-analytics-how-to-install-containers.md) Az URL-címben szerepelnie `/text/analytics/v2.1/languages` kell. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`.

+ Állítsa be a kérelem fejlécét, hogy tartalmazza a szövegelemzési műveletek [hozzáférési kulcsát.](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2. lépés: Post a kérelmet

Az elemzés a kérelem megkapásakor történik meg. A percenként és másodpercenként küldhető kérelmek méretéről és számáról az [áttekintés adatkorlátok](../overview.md#data-limits) című részében olvashat.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.


## <a name="step-3-view-the-results"></a>3. lépés: Az eredmények megtekintése

Minden POST-kérelem JSON-formátumú választ ad vissza az azonosítókkal és az észlelt tulajdonságokkal.

A kimenetet visszaadása azonnali. Az eredményeket streamelheti egy olyan alkalmazásba, amely elfogadja a JSON-t, vagy mentheti a kimenetet egy fájlba a helyi rendszeren. Ezután importálja a kimenetet egy alkalmazásba, amellyel rendezheti, keresheti és kezelheti az adatokat.

A példa kérés eredményének a következő JSON-hoz hasonlóan kell kinéznie. Figyelje meg, hogy ez egy dokumentum, több elemet tartalmazó. A kimenet angol nyelvű. A nyelvi azonosító rövid nevet és [ISO 639-1](https://www.iso.org/standard/22109.html) formátumú nyelvkódot tartalmaz.

Az 1.0 pozitív pontszám a lehetséges legnagyobb megbízhatósági szintet jelenti az elemzésre.

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "Spanish",
                        "iso6391Name": "es",
                        "score": 1
                    }
                ]
            },
            {
                "id": "3",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            },
            {
                "id": "4",
                "detectedLanguages": [
                    {
                        "name": "Chinese_Simplified",
                        "iso6391Name": "zh_chs",
                        "score": 1
                    }
                ]
            },
            {
                "id": "5",
                "detectedLanguages": [
                    {
                        "name": "Russian",
                        "iso6391Name": "ru",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="ambiguous-content"></a>Nem egyértelmű tartalom

Bizonyos esetekben nehéz lehet a bevitel alapján félretenni a nyelveket. A `countryHint` paraméter segítségével kétbetűs országkódot adhat meg. Alapértelmezés szerint az API az "US" az alapértelmezett countryHint, hogy távolítsa el ezt a `countryHint = ""` viselkedést vissza állíthatja ezt a paramétert beállításával ezt az értéket üres karakterlánc .

Például a "Lehetetlen" közös mind az angol, mind a francia nyelvben, és ha korlátozott kontextusban adják meg, a válasz az "USA" ország tippén alapul. Ha ismert, hogy a szöveg eredete Franciaország, ez megadható tippként.

**Bemenet**

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

A szolgálat nak most további kontextusa van ahhoz, hogy jobban megítélje: 

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
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Ha az analizátor nem tudja elemezni `(Unknown)`a bemenetet, akkor visszaadja. Ilyen például az, ha olyan szövegblokkot küld be, amely kizárólag arab számokból áll.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "score": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Vegyes nyelvű tartalom

Az ugyanazon a dokumentumon belüli vegyes nyelvű tartalom azt a nyelvet adja vissza, amelynek a legnagyobb reprezentációja van a tartalomban, de alacsonyabb pozitív minősítéssel rendelkezik. A minősítés az értékelés marginális erejét tükrözi. A következő példában a bemeneti adat angol, német és francia nyelv kombinációja. Az elemző minden szegmensben megszámolja a karakterek számát az uralkodó nyelv meghatározásához.

**Bemenet**

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

Az eredmény az uralkodó nyelvből áll, 1,0-nál kisebb pontszámmal, ami gyengébb megbízhatósági szintet jelez.

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 0.9375
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>Összefoglalás

Ebben a cikkben az Azure Cognitive Services Text Analytics használatával ismertetheti a nyelvfelismerés fogalmait és munkafolyamatait. A következő pontokat magyarázták és mutatták be:

+ [A nyelvfelismerés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) számos nyelven, változatban, dialektusban, valamint néhány regionális vagy kulturális nyelven elérhető.
+ A kérelemtörzsben lévő JSON-dokumentumok azonosítót és szöveget tartalmaznak.
+ A POST-kérelem `/languages` egy végponthoz egy személyre szabott [hozzáférési kulcs és egy végpont](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) használatával, amely érvényes az előfizetéshez.
+ A válaszkimenet az egyes dokumentumazonosítók nyelvi azonosítóiból áll. A kimenet bármely olyan alkalmazásba streamelhető, amely elfogadja a JSON-t. Példa: az Excel és a Power BI, hogy csak néhányat említsünk.

## <a name="see-also"></a>Lásd még

 [Szövegelemzés – áttekintés](../overview.md) [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vélemények elemzése](text-analytics-how-to-sentiment-analysis.md)
