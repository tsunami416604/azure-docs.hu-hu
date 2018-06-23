---
title: Közepes szintű szöveg Azure tartalom moderátor a szöveg moderálás API használatával |} Microsoft Docs
description: Szöveg moderálás test-Drive az online konzolon a szöveg moderálás API használatával.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: ed696c31a886626819414c45eb7995edaf161fff
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347766"
---
# <a name="moderate-text-from-the-api-console"></a>Az API-konzolról mérsékelt szöveg

Használja a [szöveg moderálás API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) Azure tartalom moderátor megvizsgálja a szöveges tartalom. A művelet ellenőrzi a tartalom a profán kifejezéseket, és összehasonlítja a tartalom egyéni és megosztott feketelistákon ellen.


## <a name="get-your-api-key"></a>Az API-kulcs beszerzése
Az API-nak az online konzolon is test-drive, meg kell az Előfizetés-kulcs. Ez a található a **beállítások** lap a **Ocp-Apim-előfizetés-kulcs** mezőbe. További információkért lásd: [áttekintése](overview.md).

## <a name="navigate-to-the-api-reference"></a>Keresse meg az API-referencia
Lépjen a [szöveg moderálás API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  A **szöveg - képernyő** lap megnyitásakor.

## <a name="open-the-api-console"></a>Az API-konzol megnyitása
A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét. 

  ![Szöveg – a képernyő lap terület kiválasztása](images/test-drive-region.png)

  A **szöveg - képernyő** API-konzol megnyitása.

## <a name="select-the-inputs"></a>Válassza ki a bemeneti adatok

### <a name="parameters"></a>Paraméterek
Válassza ki, hogy a szöveg képernyőjén használni kívánt lekérdezési paraméterekhez. Ebben a példában a használata esetén az alapértelmezett értéket **nyelvi**. Akkor is üresen is hagyhatja, mert a művelet automatikusan észleli a valószínűleg nyelvi végrehajtásának részeként.

> [!NOTE]
> Az a **nyelvi** paraméter, rendelje hozzá `eng` , vagy hagyja üresen, hogy a gép támogatású **besorolás** választ (előzetes verziójú funkciók). **Ez a funkció csak angol támogatja**.
>
> A **Profanitás feltételek** észlelése, használja a [ISO 639-3 kód](http://www-01.sil.org/iso639-3/codes.asp) szerepel a támogatott nyelvekhez cikket, vagy hagyja üresen.

A **automatikus javítás**, **PII**, és **(előzetes verzió) besorolása**, jelölje be **igaz**. Hagyja a **ListId** mező üres.

  ![Szöveg – a képernyő konzol lekérdezés-paraméterek](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tartalom típusa
A **Content-Type**, válassza ki a képernyőn kívánt tartalom. Ebben a példában a használja az alapértelmezett **egyszerű szöveg** tartalomtípus. Az a **Ocp-Apim-előfizetés-kulcs** mezőbe írja be az Előfizetés-kulcs.

### <a name="sample-text-to-scan"></a>Minta szöveges vizsgálata
Az a **Request body** mezőbe írja be a szöveget. Az alábbi példában egy szándékos elírás a szövegben.

> [!NOTE]
> Az érvénytelen társadalombiztosítási szám a következő minta szöveges szándékosan így. A célja átadja a minta bemeneti és kimeneti formátumot.

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>Szöveges besorolás szolgáltatása

Az alábbi példában látható tartalom moderátor szövegét gépet támogatású besorolást választ. Ennek segítségével, potenciálisan nemkívánatos tartalomtípusok. A megjelölt tartalom megjelölése kifogásolhatóként környezettől függően előfordulhat, hogy tekinteni. Mellett az egyes kategóriák valószínűségét igényinek, a tartalom emberi áttekintése javasolhatja azt. A szolgáltatás egy trained model esetleges visszaélést, különbözeti vagy megkülönböztető nyelvi azonosítására használ. Ez magában foglalja a zsargon, rövidített szavakat, felülvizsgálati sértő és szándékosan hibásan szavakat. 

#### <a name="explanation"></a>Magyarázat

- `Category1` a nyelv, amely ivarilag explicit vagy bizonyos esetekben felnőtt tekinthetők lehetséges jelenlétét jelöli.
- `Category2` a nyelv, amely ivarilag kétértelmű vagy bizonyos esetekben érett tekinthetők lehetséges jelenlétét jelöli.
- `Category3` a nyelv, amely bizonyos esetekben bántónak tekinthető lehetséges jelenlétét jelöli.
- `Score` 0 és 1 közé esik. Minél nagyobb a pontszám, annál magasabb a modell becslése, hogy a kategória alkalmazhatók. Ez az előnézet manuálisan kódolt eredményekkel helyett statisztikai modell támaszkodik. Azt javasoljuk, hogy a saját tartalom határozza meg, hogyan legyen a különböző kategóriájú az igényeinek megfelelően tesztelték.
- `ReviewRecommended` IGAZ vagy hamis függően a belső pontszám küszöbértékek. Az ügyfelek fel kell mérnie hogy ezt az értéket, vagy adja meg a tartalom házirendek alapján egyéni küszöbértékeket.

### <a name="analyze-the-response"></a>A válasz elemzése
A következő válasz különböző feltárása a API jeleníti meg. Lehetséges Profanitás, személyazonosításra alkalmas, besorolást (előzetes verzió), valamint az automatikus javított verzió tartalmazza.

> [!NOTE]
> A gép támogatású "Osztályozás" a szolgáltatás jelenleg előzetes verzióban érhető, és csak az angol támogatja.

```
{
    "OriginalText": "Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
    "NormalizedText": "Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation": null,
    "PII": {
            "Email": [{
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 32
                }],
            "IPA": [{
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 72
                }],
            "Phone": [{
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 56
                }, {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                }, {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                }, {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                }, {
                "CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
            }],
         "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                "Index": 89
            }],
            "SSN": [{
                "Text": "999999999",
                "Index": 56
            }, {
                "Text": "999-99-9999",
                "Index": 266
            }]
        },
    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
        },
        "Category2": {
            "Score": 0.12747249007225037
        },
        "Category3": {
            "Score": 0.98799997568130493
        }
        },
    "Language": "eng",
    "Terms": [{
            "Index": 21,
            "OriginalIndex": 21,
            "ListId": 0,
         "Term": "crap"
        }],
    "Status": {
            "Code": 3000,
            "Description": "OK",
            "Exception": null
        },
     "TrackingId": "2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

A JSON-NÁ válaszul minden szakasz részletesen ismerteti, lásd a [szöveg moderálás API – áttekintés](text-moderation-api.md).

## <a name="next-steps"></a>További lépések

A REST API-t használja a kódban, vagy indítsa el a [szöveg moderálás .NET gyors üzembe helyezés](text-moderation-quickstart-dotnet.md) integrálása az alkalmazást.
