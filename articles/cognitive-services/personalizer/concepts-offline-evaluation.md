---
title: A Kapcsolat nélküli kiértékelés módszer használata – Personalizer
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan használhatja az offline értékelést az alkalmazás hatékonyságának mérésére és a tanulási ciklus elemzésére.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: f8ceef5e80bf15f0ba52a9c289e617018febfb5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623587"
---
# <a name="offline-evaluation"></a>Offline értékelés

Az offline kiértékelés egy olyan módszer, amely lehetővé teszi a Personalizer szolgáltatás hatékonyságának tesztelését és értékelését anélkül, hogy megváltoztatná a kódot, vagy befolyásolná a felhasználói élményt. Az offline kiértékelés az alkalmazásból a Rang és jutalom API-knak küldött korábbi adatokat használja fel a különböző rangok teljesítményének összehasonlítására.

Az offline kiértékelés dátumtartományban történik. A tartomány az aktuális idő végéig befejeződhet. A tartomány kezdete nem lehet több, mint az [adatmegőrzéshez](how-to-settings.md)megadott napok száma.

Az offline kiértékelés segíthet a következő kérdések megválaszolásában:

* Mennyire hatékonyak a Personalizer sorai a sikeres személyre szabáshoz?
    * Melyek a Personalizer online gépi tanulási szabályzatáltal elért átlagos jutalmak?
    * Hogyan personalizer összehasonlítani a hatékonyságát, amit az alkalmazás volna alapértelmezés szerint?
    * Mi lett volna az összehasonlító hatékonyságát egy véletlenszerű választás személyre szabás?
    * Mi lett volna a különböző, manuálisan meghatározott tanulási politikák összehasonlító hatékonysága?
* A környezet mely jellemzői járulnak hozzá többé-kevésbé a sikeres személyre szabáshoz?
* A műveletek mely funkciói járulnak hozzá többé-kevésbé a sikeres személyre szabáshoz?

Emellett az offline kiértékelés segítségével még optimalizáltabb tanulási szabályzatokat fedezhet fel, amelyeket a Personalizer a jövőben az eredmények javítására használhat.

Az offline kiértékelések nem adnak útmutatást a feltáráshoz használandó események százalékos arányára vonatkozóan.

## <a name="prerequisites-for-offline-evaluation"></a>Az offline kiértékelés előfeltételei

A reprezentatív offline értékelés fontos szempontjai a következők:

* Van elég adat. Az ajánlott minimum legalább 50 000 esemény.
* Adatok gyűjtése a reprezentatív felhasználói viselkedéssel és forgalommal rendelkező időszakokból.

## <a name="discovering-the-optimized-learning-policy"></a>Az optimalizált tanulási szabályzat felfedezése

A Personalizer az offline értékelési folyamat segítségével automatikusan felfedezhet egy optimálisabb tanulási szabályzatot.

Az offline értékelés elvégzése után láthatja a Personalizer összehasonlító hatékonyságát az új házirenddel az aktuális online házirendhez képest. Ezt követően alkalmazhatja ezt a tanulási szabályzatot, hogy azonnal hatályba léptsen a Personalizer alkalmazásban, ha letölti és feltölti a Modellek és irányelvek panelen. Azt is letöltheti a későbbi elemzés vagy használat.

Az értékelésben szereplő jelenlegi politikák:

| Tanulási beállítások | Cél|
|--|--|
|**Online házirend**| A Personalizer jelenlegi tanulási szabályzata |
|**Alapkonfiguráció**|Az alkalmazás alapértelmezett (a rangsorolási hívásokban küldött első művelet által meghatározott)|
|**Véletlen házirend**|Képzeletbeli rangviselkedés, amely mindig véletlenszerűen választ ja ki a műveleteket a megadottakból.|
|**Egyéni házirendek**|További tanulási szabályzatok feltöltve az értékelés megkezdésekor.|
|**Optimalizált házirend**|Ha az értékelés azzal a lehetőséggel kezdődött, hogy felfedezzen egy optimalizált politikát, akkor azt is összehasonlítjuk, és le töltheti, vagy online tanulási politikát tehet, felváltva a jelenlegit.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Az offline értékelési eredmények relevanciájának megértése

Kapcsolat nélküli kiértékelés futtatásakor nagyon fontos az eredmények _megbízhatósági határának_ elemzése. Ha ezek széles, az azt jelenti, hogy az alkalmazás nem kapott elegendő adatot ahhoz, hogy a jutalom becslések pontosak vagy jelentősek legyenek. Ahogy a rendszer több adatot gyűjt, és hosszabb időszakokon keresztül futtatoffline kiértékeléseket, a konkonditúdási intervallumok szűkülnek.

## <a name="how-offline-evaluations-are-done"></a>Az offline értékelések elvégzésének oka

Az offline kiértékelések a **Counterfactual Evaluation**nevű módszerrel történnek.

Personalizer épül a feltételezés, hogy a felhasználók viselkedését (és így jutalmak) lehetetlen megjósolni visszamenőleges (Personalizer nem tudja, mi történt volna, ha a felhasználó már látható valami más, mint amit láttak), és csak tanulni mért jutalmak.

Ez az értékelésekhez használt fogalmi folyamat:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

Az offline kiértékelés csak a megfigyelt felhasználói viselkedést használja. Ez a folyamat nagy mennyiségű adatot vet el, különösen akkor, ha az alkalmazás rangsorolási hívásokat végez nagy számú művelettel.


## <a name="evaluation-of-features"></a>A jellemzők értékelése

Az offline értékelések információt nyújthatnak arról, hogy a műveletek vagy a környezet egyes funkciói közül mennyit mérnek a magasabb jutalmakért. Az adatok számítása a kiértékelés alapján az adott időszakban és az adatok, és időszerint változhat.

Javasoljuk, hogy nézze meg a funkcióértékeléseket, és kérdezze meg:

* Milyen egyéb, további funkciókat nyújthataz alkalmazás vagy rendszer a hatékonyabb akta mentén?
* Milyen funkciók távolíthatók el az alacsony hatékonyság miatt? Az alacsony hatékonyságú funkciók _zajt_ adnak a gépi tanulásnak.
* Vannak olyan funkciók, amelyek véletlenül szerepelnek? Ilyenek például a következők: felhasználó által azonosítható adatok, duplikált azonosítók stb.
* Vannak-e olyan nemkívánatos funkciók, amelyeket nem szabad személyre szabni a szabályozási vagy felelős használati szempontok miatt? Vannak-e olyan funkciók, amelyek proxy (azaz szorosan tükör vagy korrelál) nemkívánatos funkciók?


## <a name="next-steps"></a>További lépések

[A Personalizer](how-to-settings.md)
[konfigurálása Offline kiértékelések](how-to-offline-evaluation.md) megértése a [Personalizer működésének megértésében](how-personalizer-works.md)
