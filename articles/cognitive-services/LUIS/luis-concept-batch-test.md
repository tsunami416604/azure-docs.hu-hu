---
title: Kötegelt tesztelés - LUIS
titleSuffix: Azure Cognitive Services
description: A kötegelt teszteléssel folyamatosan dolgozhat az alkalmazáson, hogy finomítsa és javítsa a nyelvi megértést.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: e9ad7c52af20762633c710b39a64fbebf0cf6213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220049"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Kötegelt tesztelés 1000 utterances a LUIS portálon

Kötegelt tesztelés ellenőrzi az aktív betanított verzió az előrejelzéspontosságának mérésére. A kötegelt teszt segítségével megtekintheti az egyes szándékok és entitások pontosságát az aktív verzióban, és az eredményeket egy diagrammal jeleníti meg. Tekintse át a kötegelt vizsgálati eredményeket a pontosság javítása érdekében a megfelelő műveleteket, például további példautters hozzáadása egy szándékot, ha az alkalmazás gyakran nem azonosítja a megfelelő szándékot, vagy az utterance (kifejezés) entitások címkézése.

## <a name="group-data-for-batch-test"></a>Csoportadatok a kötegelt vizsgálathoz

Fontos, hogy a kötegelt teszteléshez használt kimondott szövegek új a LUIS. Ha egy adatkészlet kimondott szöveg, ossza fel a kimondott szöveghárom készlet: példa utterances hozzá egy szándékot, a közzétett végponttól kapott kimondott szövegek és a luis teszteléséhez használt utterances tesztelése után van betanítva. 

## <a name="a-data-set-of-utterances"></a>Kimondott szövegadatkészlet

Kötegelt teszteléshez küldje el az utterances, más néven *egy adatkészlet*kötegelt tesztelés kötegelt fájlját. Az adatkészlet egy JSON-formátumú fájl, amely legfeljebb 1000 **címkézett, nem ismétlődő** kimondott szöveget tartalmaz. Egy alkalmazásban legfeljebb 10 adatkészletet tesztelhet. Ha többet kell tesztelnie, töröljön egy adatkészletet, majd adjon hozzá egy újat.

|**Szabályok**|
|--|
|*Nincsenek ismétlődő kimondott szövegek|
|1000 vagy kevesebb kimondott szöveg|

*Az ismétlődések pontos karakterlánc-egyezésnek minősülnek, nem pedig az első ként tokenizált egyezésnek. 

## <a name="entities-allowed-in-batch-tests"></a>Kötegelt vizsgálatokban engedélyezett entitások

A modellben minden egyéni entitás megjelenik a kötegtesztentitások szűrőben, még akkor is, ha a kötegfájl-adatokban nincsenek megfelelő entitások.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Kötegelt fájlformátum

A kötegfájl kimondott szövegből áll. Minden utterance (kifejezés) rendelkeznie kell egy várt szándék előrejelzésmellett minden [gép által megtanult entitások](luis-concept-entity-types.md#types-of-entities) várhatóan észlelni kell. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Kötegszintaxissablon entitásokat feltűnéshez

A kötegfájl elindításához használja az alábbi sablont:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

A kötegfájl a **startPos** és **endPos** tulajdonságok at használja az entitás kezdetének és végének megjegyzéséhez. Az értékek nulla alapúak, és nem szabad szóközön kezdődjenek vagy végződjenek. Ez eltér a lekérdezési naplóktól, amelyek startIndex és endIndex tulajdonságokat használnak. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Kötegszintaxissablon entitások nélküli leképezésekhez

A kötegfájl entitások nélküli indításához használja az alábbi sablont:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Ha nem szeretné tesztelni az entitásokat, adja meg a `entities` tulajdonságot, és állítsa be az értéket üres tömbként, `[]`.


## <a name="common-errors-importing-a-batch"></a>Gyakori hibák köteg importálása

Gyakori hibák a következők: 

> * Több mint 1000 kimondott szöveg
> * Egy utterance (kifejezés) JSON-objektum, amely nem rendelkezik entitások tulajdonsággal. A tulajdonság lehet üres tömb.
> * Több entitásban címkézett szó(k)
> * Az entitáscímke szóközt indít vagy végződik.

## <a name="batch-test-state"></a>Kötegvizsgálati állapot

A LUIS nyomon követi az egyes adatkészletek utolsó tesztjének állapotát. Ez magában foglalja a méretet (a kötegben lévő kimondott szövegek számát), az utolsó futtatás dátumát és az utolsó eredményt (a sikeresen előre jelzett kimondott szövegek száma).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Tételvizsgálati eredmények

A kötegvizsgálati eredmény egy pontdiagram, más néven hibamátrix. Ez a grafikon egy négyirányú összehasonlítása a kimondott szöveg a kötegfájlban, és az aktuális modell előre jelzett szándék és entitások. 

A Hamis **pozitív** és a **Hamis negatív** szakaszok adatpontjai hibákat jeleznek, amelyeket meg kell vizsgálni. Ha az összes adatpont a **True Positive** és a **True Negative** szakaszokon található, akkor az alkalmazás pontossága tökéletes ezen az adatkészleten.

![A diagram négy szakasza](./media/luis-concept-batch-test/chart-sections.png)

Ez a diagram segít megtalálni a kimondott szövegeket, amelyek a LUIS előrejelezi helytelenül alapján az aktuális betanítás. Az eredmények a diagram régiónként jelennek meg. Válassza ki az egyes pontokat a grafikonon az utterance (kifejezés) információk áttekintéséhez, vagy válassza ki a régió nevét az adott régióban az utterance (kifejezés) eredmények áttekintéséhez.

![Kötegelt tesztelés](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Hibák az eredményekben

A kötegelt teszt hibái olyan leképezéseket jeleznek, amelyek nem a kötegfájlban jelzettek. A hibákat a diagram két piros szakasza jelzi. 

A hamis pozitív szakasz azt jelzi, hogy egy utterance (kifejezés) megfelelt egy szándék vagy entitás, ha nem kellett volna. A hamis negatív azt jelzi, hogy egy utterance (kifejezés) nem felelt meg egy szándéknak vagy entitásnak, amikor azt kellett volna. 

## <a name="fixing-batch-errors"></a>Köteghibák kijavítása

Ha hibák vannak a kötegelt tesztelés, hozzáadhat további kimondott szövegeket egy szándékot, és/vagy további kimondott szövegeket címkézhet az entitással, hogy segítsen a LUIS-nak a szándékok közötti megkülönböztetést. Ha hozzáadott utterances, és címkézett őket, és továbbra is kap előrejelzési hibák kötegelt tesztelés, fontolja meg egy [kifejezéslista](luis-concept-feature.md) szolgáltatás tartományspecifikus szókincs, hogy segítsen luis gyorsabb annektáló. 

## <a name="next-steps"></a>További lépések

* További információ [a kötegek teszteléséről](luis-how-to-batch-test.md)
