---
title: Batch-tesztelés – LUIS
titleSuffix: Azure Cognitive Services
description: Tesztelés a batch segítségével folyamatosan dolgozunk azon, pontosítsa és javítható a beszédfelismerés annak az alkalmazás.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393886"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Batch-tesztelés a 1000 hosszúságú kimondott szöveg a LUIS portálon

A Batch-tesztelés ellenőrzi, hogy aktív-e a betanított verzió, és megméri az előrejelzés pontosságát. A Batch-tesztek segítségével megtekintheti az egyes szándékok és entitások pontosságát az aktív verzióban, és megjelenítheti az eredményeket egy diagrammal. Tekintse át a Batch-teszt eredményeit, hogy megfelelő lépéseket tegyen a pontosság javítása érdekében, például ha az alkalmazás gyakran nem tudja azonosítani a megfelelő szándékot vagy címkéző entitásokat a hosszúságú kimondott szöveg.

## <a name="group-data-for-batch-test"></a>A batch-teszt csoport adatai

Fontos, hogy batch tesztelésére utterances nem ismeri a LUIS. Ha rendelkezik hosszúságú kimondott szöveg, ossza fel a hosszúságú kimondott szöveg három csoportba: példa a hosszúságú kimondott szöveg hozzáadására, a közzétett végponttól kapott hosszúságú kimondott szöveg, valamint a LUIS-teszt betanítása után használt hosszúságú kimondott szöveg. 

## <a name="a-data-set-of-utterances"></a>Hosszúságú kimondott szöveg adathalmaza

A Batch-teszteléshez elküldheti az *adatkészletként*ismert hosszúságú kimondott szöveg batch-fájlt. Az adatkészlet egy JSON-formátumú fájl, amely legfeljebb 1 000 címkézett, **nem duplikált** hosszúságú kimondott szöveg tartalmaz. Egy alkalmazásban akár 10 adatkészletet is kipróbálhat. Ha további tesztelésre van szüksége, töröljön egy adatkészletet, majd adjon hozzá egy újat.

|**Szabályok**|
|--|
|\* Nincsenek ismétlődő kimondott szöveg|
|1000 utterances vagy kevesebb|

\* Ismétlődések karakterlánc pontos egyezést, nem a rendszer először tokenekre bontott egyezéstípust minősülnek. 

## <a name="entities-allowed-in-batch-tests"></a>Entitások engedélyezett a batch-tesztek

A modellben az egyéni entitások jelennek meg a batch teszt entitások szűrő akkor sem, ha nincs megfelelő entitásokat a batch-fájl adatok.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Batch-fájlformátum

A kötegfájl utterances áll. Minden egyes Kimondás esetén a várt szándékú előrejelzéssel együtt kell szerepelnie az észlelni kívánt, [géppel megtanult entitásoknak](luis-concept-entity-types.md#types-of-entities) . 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Batch-szintaxis sablon az entitásokkal való leképezéshez

Az alábbi sablon használatával indítsa el a parancsfájlba:

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

A batch-fájl a **startPos** és a **endPos** tulajdonság használatával jegyezze fel az entitások elejét és végét. Az értékek nulláról induló és kell nem kezdődik vagy fejeződjön be egy szóközt. Ez különbözik a lekérdezés naplókat, amelyek startIndex és endIndex tulajdonságokkal. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Batch-szintaxis sablon az entitások nélküli leképezésekhez

A következő sablonnal elindíthatja a batch-fájlt entitások nélkül:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Ha nem szeretne entitásokat tesztelni, foglalja bele a `entities` tulajdonságot, és adja meg az értéket üres tömbként, `[]`.


## <a name="common-errors-importing-a-batch"></a>Gyakori hibák a batch importálása

Gyakori hibák a következők: 

> * Több mint 1000 kimondott szöveg
> * Az utterance (kifejezés) JSON-objektum, amely nem rendelkezik az entitások tulajdonság. A tulajdonságot akkor lehet üres tömb.
> * A több entitás feliratú szavak
> * Entitás címke elindítása és a egy szóközzel végződő.

## <a name="batch-test-state"></a>Batch-teszt állapota

LUIS az egyes adathalmazok utolsó tesztelésének állapotát követi nyomon. Ez magában foglalja a legutóbbi futtatás mérete (a köteg utterances száma), dátum, és a legutóbbi eredmény (utterances sikeresen előre jelzett száma).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>A Batch terhelésiteszt-eredményei

A batch-vizsgálat eredményének a pontdiagram grafikon, egy hiba mátrix néven. Ez a diagram a batch-fájlt és az aktuális modell előre jelzett szándékot és entitások megcímkézzen 4-módon összehasonlítása. 

A **hamis pozitív** és **hamis negatív** szakaszban lévő adatpontok olyan hibákat jeleznek, amelyeket meg kell vizsgálni. Ha az összes adatpont a **valódi pozitív** és **igaz negatív** szakaszban van, akkor az alkalmazás pontossága tökéletes ezen az adathalmazon.

![Diagram négy részből áll](./media/luis-concept-batch-test/chart-sections.png)

Ez a diagram segít helytelenül alapján a jelenlegi képzést, amely előrejelzi a LUIS utterances található. Az eredmények jelennek meg a diagram régiónként. Válassza ki a diagramon, tekintse át az utterance (kifejezés) adatokat, vagy válassza ki az adott régióban utterance (kifejezés) eredményeinek áttekintése régió neve egyedi pontokat.

![Kötegelt tesztelés](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Eredmények hibái

A batch-teszt utalnak, hogy a rendszer nem jelzett másolóeszközt feljegyzett leképezések. Hibák a diagram a két piros szakaszban szerepelnek. 

A hamis pozitív szakasz azt jelzi, hogy az utterance (kifejezés) egyezik a leképezés vagy entitás pedig nem kellett volna. A téves negatív azt jelzi, hogy az utterance (kifejezés) nem egyezik a leképezés vagy entitás amikor rendelkeznie kell. 

## <a name="fixing-batch-errors"></a>A batch hibáinak javítása

Ha hiba van a batch-tesztelés, is vagy további beszédmódok hozzáadása egy beszédszándék és/vagy az entitáshoz, LUIS, győződjön meg arról, közötti leképezések megkülönböztetés érdekében további utterances címkézését. Ha hozzáadta a hosszúságú kimondott szöveg-t, és címkézte őket, és továbbra is előrejelzési hibákat kap a Batch-tesztelésben, érdemes lehet egy, a tartományra jellemző szókincsgel kiegészíteni egy [kifejezés-lista](luis-concept-feature.md) szolgáltatást, amely segítséget nyújt a Luis számára. 

## <a name="next-steps"></a>Következő lépések

* Útmutató [a Batch teszteléséhez](luis-how-to-batch-test.md)
