---
title: A Text Analytics REST API-val nyelv észlelése |} A Microsoft Docs
description: Hogyan észleli a nyelvet, az Azure Cognitive Services Text Analytics REST API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: 6f1e71b75aa68c8f4ea1fa8ed373da25dbb3c24b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304048"
---
# <a name="example-how-to-detect-language-with-text-analytics"></a>Példa: Nyelvfelismerés a Text Analytics használatával

A [nyelvfelismerés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) funkció API-szöveg a bemeneti, és minden egyes dokumentum esetében kiértékeli, és az elemzés erőssége jelző pontszámot ad vissza, nyelvi azonosítók.

Ez a funkció véletlen szöveget gyűjtő tartalom áruházak számára hasznos, amikor a nyelv ismeretlen. Az analízis eredményei elemezhetők annak meghatározására, hogy milyen nyelvet használ a bemeneti dokumentum. A válasz visszaad egy pontszámot is, amely tükrözi a modell (0 és 1 közötti értékű) megbízhatóságát.

Nem tesszük közzé ezt a szolgáltatást a nyelvek pontos listáját, de képes észlelni a számos különféle nyelveken, variantní hodnoty, dialektusok és néhány területi kulturális nyelv. 

Ha a tartalom kevésbé gyakran használt nyelven, megpróbálhatja nyelvfelismerés megtekintheti, ha egy kódot ad vissza. A válasz nem észlelhető nyelvek `unknown`.

> [!TIP]
> Szövegelemzés is biztosít egy Linux-alapú Docker tároló rendszerképének nyelvfelismerés, így [telepítheti és futtathatja a Text Analytics tároló](text-analytics-how-to-install-containers.md) közel az adatokat.

## <a name="preparation"></a>Előkészítés

JSON-dokumentumok kell rendelkeznie a következő formátumban: SMS-azonosító

Dokumentum mérete kell lennie a 5,120 karakter / dokumentum, és legfeljebb 1000 rendelkezhet gyűjteményenként (azonosítók) elemet. A kollekció elküldése a kérelem törzsében történik. A következő egy példa nyelvfelismerésre beküldhető tartalomra.

   ```
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

## <a name="step-1-structure-the-request"></a>1\. lépés: A kérelem struktúra

A kérés definícióval kapcsolatos részletek megtalálhatók a [Text Analytics API hívásának módja](text-analytics-how-to-call-api.md) részben. A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy **POST** kérést. Tekintse át a kérelem API-dokumentáció: [API nyelvfelismerés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)

+ Állítsa be a HTTP-végpontot nyelvfelismerés, Szövegelemzés erőforrás használata az Azure- vagy egy példányosított [Szövegelemzés tároló](text-analytics-how-to-install-containers.md). Tartalmaznia kell a `/languages` erőforrást: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`

+ A kérés fejlécet állítsa be úgy, hogy tartalmazza a Text Analytics műveletekhez a hozzáférési kulcsot. További információkért lásd: [Végpontok és hozzáférési kulcsok megkeresése](text-analytics-how-to-access-key.md).

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2\. lépés: A kérelem küldése

Az elemzés a kérelem megkapásakor történik meg. Tekintse meg a [data korlátai](../overview.md#data-limits) szakasz az áttekintésben a méret és a kérelmek percenkénti küldhet és a második kapcsolatos információkat.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.


## <a name="step-3-view-results"></a>3\. lépés: Eredmények megtekintése

Minden POST kérés egy JSON formátumú választ ad vissza az azonosítókkal és az észlelt tulajdonságokkal.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba.

A példa kérés eredményének a következő JSON-hoz hasonlóan kell kinéznie. Figyelje meg, hogy ez egy dokumentum több elemmel. A kimenet angol nyelvű. A nyelvi azonosító rövid nevet és [ISO 639-1](https://www.iso.org/standard/22109.html) formátumú nyelvkódot tartalmaz.

Az 1.0 pozitív pontszám a lehetséges legnagyobb megbízhatósági szintet jelenti az elemzésre.



```
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
```

### <a name="ambiguous-content"></a>Nem egyértelmű tartalom

Ha az elemző nem tudja elemezni a bemenetet (tegyük fel például, hogy kizárólag arab számokból álló szövegrészt küldött el) akkor adja vissza ezt: `(Unknown)`.

```
    {
      "id": "5",
      "detectedLanguages": [
        {
          "name": "(Unknown)",
          "iso6391Name": "(Unknown)",
          "score": "NaN"
        }
      ]
```
### <a name="mixed-language-content"></a>A vegyes nyelvi tartalom

Az egy dokumentumon belüli vegyes nyelvi tartalomra a szöveget legnagyobb részt kitevő nyelvet adja vissza, de kisebb pozitív minősítéssel, ami tükrözi az értékelés gyenge erősségét. A következő példában a bemeneti adat angol, német és francia nyelv kombinációja. Az elemző minden szegmensben megszámolja a karakterek számát az uralkodó nyelv meghatározásához.

**Input** (Bemenet)

```
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

A kapott eredmény a domináns nyelv, de a gyengébb megbízhatósági szintet jelző 1.0 alatti pontszámmal.

```
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

Ebben a cikkben a nyelvfelismerés elveivel és folyamatával ismerkedett meg a Cognitive Services Text Analytics használatával. Az alábbiakban röviden felidézzük a korábban ismertetett és bemutatott főbb pontokat:

+ [Nyelv észlelése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) számos különféle nyelveken, variantní hodnoty, dialektusok és néhány területi kulturális nyelv érhető el.
+ A kérelem törzsében szereplő JSON-dokumentumok-Azonosítót és a szöveg tartalmazza.
+ POST-kérés a `/languages` végpontra, az előfizetésre érvényes személyre szabott [hozzáférési kulcs és végpont](text-analytics-how-to-access-key.md) használatával.
+ A válasz kimenet, amely minden dokumentumazonosítóhoz tartalmazza a nyelvi azonosítót, továbbítható bármilyen JSON-t elfogadó alkalmazáshoz, beleértve az Excelt és a Power BI-t, hogy csak néhányat említsünk.

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vélemények elemzése](text-analytics-how-to-sentiment-analysis.md)
