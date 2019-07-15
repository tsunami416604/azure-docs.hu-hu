---
title: Észlelje a nyelvet a Text Analytics REST API-val |} A Microsoft Docs
description: Nyelv felismerése az Azure Cognitive Services Text Analytics REST API-val.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e1adeb34cf999f471bb183e4d7de9c65427252bb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986516"
---
# <a name="example-detect-language-with-text-analytics"></a>Példa: Szövegelemzés nyelv felismerése

A [nyelvfelismerés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) funkciója a az Azure Analytics REST API egyes dokumentumok szövegbevitel kiértékeli, és adja vissza egy pontszám, amely azt jelzi, hogy az elemzés erőssége a nyelvi azonosítók.

Ez a funkció véletlen szöveget gyűjtő tartalom áruházak számára hasznos, amikor a nyelv ismeretlen. Az analízis eredményei elemezhetők annak meghatározására, hogy milyen nyelvet használ a bemeneti dokumentum. A válasz is, amely tükrözi a modell a megbízhatósági pontszámot ad vissza. A pontszám értéke 0 és 1 között.

A nyelv észlelése funkció számos különféle nyelveken, variantní hodnoty, dialektusok és néhány regionális vagy kulturális nyelv észlelését. Ennek a funkciónak a nyelvek listája pontosan nyilvánosságra kerülését.

Ha a tartalom kevésbé gyakran használt nyelven, megpróbálhatja a nyelvfelismerés szolgáltatást, hogy tekintse meg, ha egy kódot ad vissza. A válasz nem észlelhető nyelvek `unknown`.

> [!TIP]
> Szövegelemzés is biztosít egy Linux-alapú Docker tároló rendszerképének nyelvfelismerés, így [telepítheti és futtathatja a Text Analytics tároló](text-analytics-how-to-install-containers.md) közel az adatokat.

## <a name="preparation"></a>Előkészítés

JSON-dokumentumok kell rendelkeznie a következő formátumban: Azonosító és a szöveg.

A dokumentumok méretétől dokumentum alatt 5,120 karakterrel kell lennie. Használhat akár 1000 gyűjteményenként (azonosítók) elemet. A kollekció elküldése a kérelem törzsében történik. Az alábbi minta példaként szolgál a tartalmat, előfordulhat, hogy küldje el a nyelv észlelése:

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

További információ a kérelem definíciója: [a szövegelemzési API hívása](text-analytics-how-to-call-api.md). A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy POST-kérelmet. Tekintse át a kérelem API-dokumentáció, tekintse meg a [nyelvi API-t](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Állítsa be a HTTP-végpontot a nyelvfelismeréshez. Szövegelemzés erőforrás használja az Azure- vagy egy példányosított [Szövegelemzés tároló](text-analytics-how-to-install-containers.md). Tartalmaznia kell a `/languages` erőforrás: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`.

+ A kérés fejlécet állítsa be úgy, hogy tartalmazza a Text Analytics műveletekhez a hozzáférési kulcsot. További információkért lásd: [végpontok keresése és hozzáférési kulcsokkal](text-analytics-how-to-access-key.md).

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót.

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2\. lépés: A kérelem KÜLDÉSE

Az elemzés a kérelem megkapásakor történik meg. Információ a mérete és a kérelmek percenkénti küldhet és második, tekintse meg a [data korlátai](../overview.md#data-limits) az Áttekintés szakasz.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.


## <a name="step-3-view-the-results"></a>3\. lépés: Eredmények megtekintése

Minden POST kérelemhez a azonosítóval rendelkező JSON-formátumú választ, és a Tulajdonságok észlelt.

A kimenetet visszaadása azonnali. Az eredményeket JSON elfogadó alkalmazás adatfolyam, vagy mentse a kimenetet egy fájlt a helyi rendszer. Ezután importálja a kimeneti, amellyel rendezni, keresése és alakítanak át az adatokat egy alkalmazásba.

A példa kérés eredményének a következő JSON-hoz hasonlóan kell kinéznie. Figyelje meg, hogy-e egy dokumentum több elemet. A kimenet angol nyelvű. A nyelvi azonosító rövid nevet és [ISO 639-1](https://www.iso.org/standard/22109.html) formátumú nyelvkódot tartalmaz.

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

Ha az elemző a bemenet nem elemezhető, akkor adja vissza `(Unknown)`. Ilyen például, ha egy szövegterület, amely kizárólag számokat arab áll elküldését.

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
### <a name="mixed-language-content"></a>Vegyes nyelvű tartalom

Vegyes nyelvi tartalom ugyanazon a dokumentumon belül a legnagyobb ábrázolása az a tartalmat, de kisebb pozitív minősítést nyelvét adja eredményül. A minősítés marginális erőssége az értékelést tükrözi. A következő példában a bemeneti adat angol, német és francia nyelv kombinációja. Az elemző minden szegmensben megszámolja a karakterek számát az uralkodó nyelv meghatározásához.

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

A kimenet a domináns nyelvét, és egy kisebb, mint 1.0 pontszámmal rendelkező azt jelzi, hogy a gyengébb megbízhatósági szintű áll.

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

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és a nyelvfelismerést munkafolyamata az Azure Cognitive Services Text Analytics használatával. A következő szempontokat is ismertetése és mutatja be:

+ [Nyelv észlelése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) számos különféle nyelveken, variantní hodnoty, dialektusok és néhány regionális vagy kulturális nyelven érhető el.
+ A kérelem törzsében szereplő JSON-dokumentumok-Azonosítót és a szöveg tartalmazza.
+ A POST-kérés, hogy egy `/languages` végpont használatával személyre szabott [kulcs és a egy végpont elérésére](text-analytics-how-to-access-key.md) Ez érvényes az előfizetéshez.
+ Válasz kimenete áll nyelvi azonosítók minden egyes dokumentum azonosítóját. A kimenet továbbítható legyen, amely JSON-alkalmazásaiban. Példa alkalmazások közé tartoznak, az Excel és a Power bi-ban, hogy néhányat említsünk.

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vélemények elemzése](text-analytics-how-to-sentiment-analysis.md)
