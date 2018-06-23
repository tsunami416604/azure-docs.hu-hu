---
title: Szöveg Analytics REST API-t (Microsoft Azure kognitív szolgáltatások) – útmutató nyelvi az észlelést |} Microsoft Docs
description: Hogyan szöveg Analytics REST API használatával a Microsoft Azure kognitív Services bemutató oktatóanyag nyelvi észleléséhez.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: f8e2d9a36533c298addcf42d3cb2061e9c2d1ac7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347438"
---
# <a name="how-to-detect-language-in-text-analytics"></a>Hogyan azonosíthatók a nyelvi Szövegelemzések

A [nyelvi észlelési API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) szöveges bemeneti, és minden egyes dokumentumhoz kiértékeli, és a nyelvi azonosítót ad vissza, amelyben az elemzés erősségével jelző pontszámot. Szövegelemzések legfeljebb 120 nyelvek felismeri.

Ez a funkció akkor hasznos, a tartalmat tárolja, hogy a gyűjtés tetszőleges szöveg, ahol nyelvi ismeretlen. Annak meghatározásához, milyen nyelven szerepel a bemeneti dokumentum az elemzés eredményeinek tudja értelmezni. A válasz is a pontszám, amely tükrözi a abban, hogy a modell (0 és 1 közötti értéket) adja vissza.

## <a name="preparation"></a>Előkészítése

JSON-dokumentumok rendelkeznie kell a következő formátumban: azonosító, a szöveg

Dokumentum mérete dokumentumonként a 5000 karakterből állhat, illetve azt, hogy legfeljebb 1000 gyűjteményenként (azonosítók) elemet. A gyűjtemény elküldve a kérelem törzsében. A következő példában látható egy tartalom, lehet, hogy küldje el a nyelvi észleléséhez.

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
                "text": "Этот документ находится на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>1. lépés: A kérés struktúra

A kérelem definíciója részletek megtalálhatók a [hogyan hívhatja meg a szöveg Analytics API](text-analytics-how-to-call-api.md). Kényelmi vannak megfelelően a következő szempontokat:

+ Hozzon létre egy **POST** kérelmet. Tekintse át a kérelem az API dokumentációjának: [nyelvi észlelési API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)

+ Állítsa be a HTTP-végpont a nyelvi észleléséhez. Tartalmaznia kell a `/languages` erőforrás: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`

+ Állítsa be a hozzáférési kulcsot a következő Szövegelemzések műveletek közé tartozik a fejléc. További információkért lásd: [végpontok kereséséhez, és hozzáférési kulcsokkal](text-analytics-how-to-access-key.md).

+ A kérelem törzsében szereplő adja meg az elemzés az Felkészülés JSON documents gyűjtemény

> [!Tip]
> Használja [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg a **API-tesztelési konzol** a a [dokumentáció](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) struktúra egy kérelmet, és KÖZZÉTEHETI a szolgáltatás.

## <a name="step-2-post-the-request"></a>2. lépés: Küldje a kérelmet

Elemzés a kérelem történik. A szolgáltatás fogadja a legfeljebb 100 kérelmek / perc. Minden egyes kérelem legfeljebb 1 MB méretű is lehet.

A visszaírási, hogy állapot nélküli-e a szolgáltatás. Adatot nem tárolja a fiókját. Eredményeinek azonnal a válaszban.


## <a name="step-3-view-results"></a>3. lépés: Az eredmények megtekintése

Minden POST kérelemhez vissza JSON formátumú válasz azonosítókkal és Tulajdonságok észlelhetők.

Kimeneti azonnal adja vissza. Az eredmények JSON elfogadó alkalmazás adatfolyamként vagy kimenetét mentse fájlba a helyi rendszer, és importálja azt az alkalmazást, amely lehetővé teszi rendezni, keresése és az adatok kezelését.

A példa egy kérelem eredményeit a következő JSON hasonlóan kell kinéznie. Figyelje meg, hogy a rendszer egy dokumentum több elemet. Angol nyelvű eredménye. Nyelvi azonosítókkal közé tartozik egy rövid nevet, a nyelvkódot [ISO 639-1](https://www.iso.org/standard/22109.html) formátumban.

Egy pozitív pontszám 1.0 a legmagasabb lehetséges megbízhatósági szint az elemzés fejezi ki.



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

Ha a analyzer nem tudja elemezni a bemeneti (tegyük fel, hogy Ön által küldött egy szövegblokk, amely kizárólag számokat arab), adja vissza, `(Unknown)`.

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
### <a name="mixed-language-content"></a>Vegyes tartalom nyelv

Vegyes nyelvi tartalmakra egyazon dokumentumban a tartalmat a legnagyobb ábrázolását, de egy alacsonyabb pozitív minősítést értékelést marginális erősségével tükröző nyelvét adja eredményül. A következő példában a bemeneti érték egy angol, német és francia keveréke. A analyzer rendelkezik minden szegmensben meghatározni az elsődleges nyelvi karakterek száma.

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

Ennek kimenete a elsődleges nyelvét, a kisebb, mint 1.0 pontszám abban, hogy egy gyengébb szintjét jelző áll.

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

Ebben a cikkben megtanulta, fogalmak és a munkafolyamat Szövegelemzések használatát kognitív szolgáltatások nyelvi észleléséhez. A fő pontok korábban alapján, és bemutatta gyors emlékeztető a következők:

+ [Nyelvi észlelési API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) 120 nyelveken érhető el.
+ A kérelem törzsében szereplő JSON-dokumentumok egy azonosítója és a szöveges tartalmaznak.
+ POST-kérelmet, hogy egy `/languages` végpont, egy személyre szabott [hozzáférési kulcs és egy végpontot](text-analytics-how-to-access-key.md) érvényes az előfizetéséhez.
+ Válasz kimenete, amely tartalmazza az egyes dokumentumazonosító: nyelvi azonosítókkal, továbbítható bármely alkalmazásba, amely támogatja a JSON-NÁ, beleértve az Excel és a Power bi-ban, hogy néhányat említsünk.

## <a name="see-also"></a>Lásd még 

 [Szöveg elemzés áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Szöveg Analytics termék oldalát](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Véleményeket elemzése](text-analytics-how-to-sentiment-analysis.md)
