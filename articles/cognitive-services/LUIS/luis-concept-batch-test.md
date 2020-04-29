---
title: Batch-tesztelés – LUIS
titleSuffix: Azure Cognitive Services
description: A Batch-tesztelés használatával folyamatosan dolgozhat az alkalmazáson, és javíthatja a nyelvi megértését.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220049"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Batch-tesztelés a 1000 hosszúságú kimondott szöveg a LUIS portálon

A Batch-tesztelés ellenőrzi, hogy aktív-e a betanított verzió, és megméri az előrejelzés pontosságát. A Batch-tesztek segítségével megtekintheti az egyes szándékok és entitások pontosságát az aktív verzióban, és megjelenítheti az eredményeket egy diagrammal. Tekintse át a Batch-teszt eredményeit, hogy megfelelő lépéseket tegyen a pontosság javítása érdekében, például ha az alkalmazás gyakran nem tudja azonosítani a megfelelő szándékot vagy címkéző entitásokat a hosszúságú kimondott szöveg.

## <a name="group-data-for-batch-test"></a>A Batch-teszthez tartozó adatcsoportosítási szolgáltatás

Fontos, hogy a Batch-teszteléshez használt hosszúságú kimondott szöveg Újdonságok legyenek a LUIS-ben. Ha rendelkezik hosszúságú kimondott szöveg, ossza fel a hosszúságú kimondott szöveg három csoportba: példa a hosszúságú kimondott szöveg hozzáadására, a közzétett végponttól kapott hosszúságú kimondott szöveg, valamint a LUIS-teszt betanítása után használt hosszúságú kimondott szöveg. 

## <a name="a-data-set-of-utterances"></a>Hosszúságú kimondott szöveg adathalmaza

A Batch-teszteléshez elküldheti az *adatkészletként*ismert hosszúságú kimondott szöveg batch-fájlt. Az adatkészlet egy JSON-formátumú fájl, amely legfeljebb 1 000 címkézett, **nem duplikált** hosszúságú kimondott szöveg tartalmaz. Egy alkalmazásban akár 10 adatkészletet is kipróbálhat. Ha további tesztelésre van szüksége, töröljön egy adatkészletet, majd adjon hozzá egy újat.

|**Szabályok**|
|--|
|* Nincs duplikált hosszúságú kimondott szöveg|
|1000 hosszúságú kimondott szöveg vagy kevesebb|

* A duplikált elemek pontos karakterlánc-egyezéseknek tekintendők, és nem felelnek meg először a jogkivonatnak. 

## <a name="entities-allowed-in-batch-tests"></a>A Batch-tesztekben engedélyezett entitások

A modellben lévő összes egyéni entitás megjelenik a Batch test entitások szűrőben akkor is, ha nincsenek megfelelő entitások a Batch-fájlban.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Batch-fájlformátum

A batch-fájl hosszúságú kimondott szöveg áll. Minden egyes Kimondás esetén a várt szándékú előrejelzéssel együtt kell szerepelnie az észlelni kívánt, [géppel megtanult entitásoknak](luis-concept-entity-types.md#types-of-entities) . 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Batch-szintaxis sablon az entitásokkal való leképezéshez

A batch-fájl elindításához használja a következő sablont:

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

A batch-fájl a **startPos** és a **endPos** tulajdonság használatával jegyezze fel az entitások elejét és végét. Az értékek nulla-alapúak, és nem kezdődhetnek és nem végződhet szóközre. Ez eltér a lekérdezési naplóktól, amelyek a startIndex és a endIndex tulajdonságokat használják. 

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

Ha nem szeretne entitásokat tesztelni, foglalja bele a `entities` tulajdonságot, és állítsa üres tömbként az értéket `[]`.


## <a name="common-errors-importing-a-batch"></a>A kötegek importálása során előforduló gyakori hibák

Gyakori hibák a következők: 

> * Több mint 1 000 hosszúságú kimondott szöveg
> * Olyan teljes JSON-objektum, amely nem rendelkezik entitások tulajdonsággal. A tulajdonság lehet üres tömb.
> * Több entitásban címkézett szó (ek)
> * Az entitás felirata egy szóközzel kezdődik vagy végződik.

## <a name="batch-test-state"></a>Batch-teszt állapota

LUIS az egyes adathalmazok utolsó tesztelésének állapotát követi nyomon. Ebbe beletartozik a méret (hosszúságú kimondott szöveg száma a kötegben), a legutóbbi Futtatás dátuma és az utolsó eredmény (a sikeresen előrejelzett hosszúságú kimondott szöveg száma).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>A Batch-tesztek eredményei

A Batch-teszt eredménye egy pontdiagram. Ez a gráf a Batch-fájlban található hosszúságú kimondott szöveg és az aktuális modell előre jelzett szándékának és entitásának 4 irányú összehasonlítása. 

A **hamis pozitív** és **hamis negatív** szakaszban lévő adatpontok olyan hibákat jeleznek, amelyeket meg kell vizsgálni. Ha az összes adatpont a **valódi pozitív** és **igaz negatív** szakaszban van, akkor az alkalmazás pontossága tökéletes ezen az adathalmazon.

![A diagram négy része](./media/luis-concept-batch-test/chart-sections.png)

Ez a diagram segít megtalálni az hosszúságú kimondott szöveg, hogy a LUIS a jelenlegi betanítása alapján helytelenül Jósolja meg. Az eredmények a diagram egy régiójában jelennek meg. Válassza ki az egyes pontokat a diagramon a részletes információk áttekintéséhez, vagy válassza a régió neve lehetőséget a kiértékelés eredményének a régióban való áttekintéséhez.

![Kötegelt tesztelés](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Hibák az eredményekben

A Batch-tesztben szereplő hibák azokat a leképezéseket jelzik, amelyek nem a Batch-fájlban feljegyzett módon vannak jelezve. A hibák a diagram két piros részében szerepelnek. 

A hamis pozitív szakasz azt jelzi, hogy egy Kimondás egy szándéknak vagy entitásnak felel meg, ha nem lenne. A hamis negatív érték azt jelzi, hogy a kizáró érték nem egyezik meg a szándékkal vagy az entitással. 

## <a name="fixing-batch-errors"></a>Batch-hibák javítása

Ha hibák léptek fel a Batch-tesztelésben, hozzáadhat további hosszúságú kimondott szöveg egy szándékhoz, és/vagy megcímkézheti a további hosszúságú kimondott szöveg az entitással, hogy segítsen a LUIS számára a szándékok közötti diszkriminációban. Ha hozzáadta a hosszúságú kimondott szöveg-t, és címkézte őket, és továbbra is előrejelzési hibákat kap a Batch-tesztelésben, érdemes lehet egy, a tartományra jellemző szókincsgel kiegészíteni egy [kifejezés-lista](luis-concept-feature.md) szolgáltatást, amely segítséget nyújt a Luis számára. 

## <a name="next-steps"></a>További lépések

* Útmutató [a Batch teszteléséhez](luis-how-to-batch-test.md)
