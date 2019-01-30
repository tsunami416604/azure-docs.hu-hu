---
title: Kötegelt tesztelés
titleSuffix: Language Understanding - Azure Cognitive Services
description: Tesztelés a batch segítségével folyamatosan dolgozunk azon, pontosítsa és javítható a beszédfelismerés annak az alkalmazás.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: a5e3d52727d9f86631760a6ba33bae2172bb532b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212552"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>A LUIS-portál 1000 utterances tesztelésével Batch

Batch tesztelés érvényesíti a [aktív](luis-concept-version.md#active-version) betanított modell méréséhez az előrejelzés pontosságát. Az egyes szándékot és entitás pontosságát megtekintése a diagramon az aktuális betanított modell egy batch-teszt segítségével. Tekintse át a batch-vizsgálati eredmények pontosságának, például további példa beszédmódok hozzáadása megjelölésű, ha az alkalmazás gyakran nem tudja azonosítani a helyes cél növelése érdekében a megfelelő műveletet.

## <a name="group-data-for-batch-test"></a>A batch-teszt csoport adatai

Fontos, hogy batch tesztelésére utterances nem ismeri a LUIS. Ha utterances az adatkészlet, megcímkézzen három csoportokba oszthatja: utterances megjelölésű hozzáadott, a közzétett végpontról érkezett utterances és használt batch-próbára LUIS, ha be van tanítva kimondott szöveg. 

## <a name="a-dataset-of-utterances"></a>Egy adatkészlet a kimondott szöveg

Küldje el a fájlt egy kötegfájlban utterances, más néven egy *adatkészlet*, kötegelt teszteléséhez. Az adatkészlet maximum 1000 feliratú tartalmazó JSON-formátumú fájlt **egyszer előforduló** kimondott szöveg. Legfeljebb 10 adatkészletek tesztelheti egy alkalmazásban. Ha szeretne további tesztelése, adatkészlet törlése, és adja hozzá az egy újat.

|**Szabályok**|
|--|
|* Nincsenek ismétlődő kimondott szöveg|
|1000 utterances vagy kevesebb|

* Ismétlődések karakterlánc pontos egyezést, nem a rendszer először tokenekre bontott egyezéstípust minősülnek. 

## <a name="entities-allowed-in-batch-tests"></a>Entitások engedélyezett a batch-tesztek

A modellben az egyéni entitások jelennek meg a batch teszt entitások szűrő akkor sem, ha nincs megfelelő entitásokat a batch-fájl adatok.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Batch-fájlformátum

A kötegfájl utterances áll. Minden kimondásakor rendelkeznie kell egy várt szándék előrejelzési együtt bármely [gép megismert entitások](luis-concept-entity-types.md#types-of-entities) várhatóan észlelhető. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>A Batch szintaxis sablon szándékok és entitások

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

A szkript a **startPos** és **endPos** megjegyezni elején és végén egy entitás tulajdonságai. Az értékek nulláról induló és kell nem kezdődik vagy fejeződjön be egy szóközt. Ez különbözik a lekérdezés naplókat, amelyek startIndex és endIndex tulajdonságokkal. 

## <a name="batch-syntax-template-for-intents-without-entities"></a>A Batch szintaxis sablon nélkül entitások leképezések

Az alábbi sablon használatával indítsa el a kötegfájlt entitások nélkül:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Ha nem szeretné tesztelni az entitások, például a `entities` tulajdonságot, és állítsa be az üres tömbként `[]`.


## <a name="common-errors-importing-a-batch"></a>Gyakori hibák a batch importálása

Gyakori hibák a következők: 

> * Több mint 1000 kimondott szöveg
> * Az utterance (kifejezés) JSON-objektum, amely nem rendelkezik az entitások tulajdonság. A tulajdonságot akkor lehet üres tömb.
> * A több entitás feliratú szavak
> * Entitás címke elindítása és a egy szóközzel végződő.

## <a name="batch-test-state"></a>Batch-teszt állapota

A LUIS minden adathalmaz utolsó teszt állapotát követi nyomon. Ez magában foglalja a legutóbbi futtatás mérete (a köteg utterances száma), dátum, és a legutóbbi eredmény (utterances sikeresen előre jelzett száma).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>A Batch terhelésiteszt-eredményei

A batch-vizsgálat eredményének a pontdiagram grafikon, egy hiba mátrix néven. Ez a diagram a batch-fájlt és az aktuális modell előre jelzett szándékot és entitások megcímkézzen 4-módon összehasonlítása. 

Az adatpontok a **hamis pozitív** és **téves negatív** szakaszok jelzi a hibákat, amelyeket meg kell vizsgálni. Összes adatpont esetén a **valódi pozitívak** és **igaz negatív** részei, akkor az alkalmazás pontossága tökéletes megoldás az adatkészlethez.

![Diagram négy részből áll](./media/luis-concept-batch-test/chart-sections.png)

Ez a diagram segít helytelenül alapján a jelenlegi képzést, amely előrejelzi a LUIS utterances található. Az eredmények jelennek meg a diagram régiónként. Válassza ki a diagramon, tekintse át az utterance (kifejezés) adatokat, vagy válassza ki az adott régióban utterance (kifejezés) eredményeinek áttekintése régió neve egyedi pontokat.

![Kötegelt tesztelés](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Eredmények hibái

A batch-teszt utalnak, hogy a rendszer nem jelzett másolóeszközt feljegyzett leképezések. Hibák a diagram a két piros szakaszban szerepelnek. 

A hamis pozitív szakasz azt jelzi, hogy az utterance (kifejezés) egyezik a leképezés vagy entitás pedig nem kellett volna. A téves negatív azt jelzi, hogy az utterance (kifejezés) nem egyezik a leképezés vagy entitás amikor rendelkeznie kell. 

## <a name="fixing-batch-errors"></a>A batch hibáinak javítása

Ha hiba van a batch-tesztelés, is vagy további beszédmódok hozzáadása egy beszédszándék és/vagy az entitáshoz, LUIS, győződjön meg arról, közötti leképezések megkülönböztetés érdekében további utterances címkézését. Ha hozzáadta a kimondott szöveg, és feliratú őket, és továbbra is get előrejelzési hibák a batch tesztelése, érdemes lehet hozzáadni egy [kifejezéslista](luis-concept-feature.md) szolgáltatása, amely rendelkezik a tartomány-specifikus szöveg szóhasználati, ismerje meg, gyorsabb LUIS segítségével. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [kötegelt tesztelése](luis-how-to-batch-test.md)
