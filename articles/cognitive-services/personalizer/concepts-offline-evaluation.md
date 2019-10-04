---
title: Offline értékelés – személyre szabás
titleSuffix: Azure Cognitive Services
description: Hozzon létre visszajelzési C# hurkot ebben a rövid útmutatóban a személyre szabott szolgáltatással.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 5e9e745d73623e03e2530e1712a50e6670ee7ed3
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662855"
---
# <a name="offline-evaluation"></a>Offline értékelés

Az offline kiértékelés egy olyan módszer, amely lehetővé teszi a személyre szabott szolgáltatás hatékonyságának tesztelését és értékelését a kód módosítása vagy a felhasználói élmény befolyásolása nélkül. Az offline kiértékelés az alkalmazásból a rangsor API-ba elküldett múltbeli adatokkal hasonlítja össze a különböző rangsorolt feladatok végrehajtását.

Az offline értékelés a Dátumtartomány alapján történik. A tartomány az aktuális időpontig véget ér. A tartomány kezdete nem lehet nagyobb, mint az adatmegőrzéshez megadott napok száma [](how-to-settings.md).

Az offline értékelés a következő kérdések megválaszolásához nyújt segítséget:

* Mennyire hatékony a személyre szabás a sikeres személyre szabáshoz?
    * Milyen átlagos előnyökkel érhető el a személyre szabott online gépi tanulási szabályzat?
    * Hogyan hasonlítható össze a személyre szabott funkció, hogy az alkalmazás milyen hatékonysággal legyen végrehajtva?
    * Mi lett volna a személyre szabott véletlenszerű választás komparatív hatékonysága?
    * Mi volt a manuálisan meghatározott különböző képzési szabályzatok komparatív hatékonysága?
* A környezet mely funkciói járulnak hozzá a sikeres személyre szabáshoz?
* A műveletek mely funkciói járulnak hozzá a sikeres személyre szabáshoz?

Emellett az offline értékelés használatával több optimalizált tanulási szabályzatot is felderítheti, amelyeket a személyre szabott a jövőben az eredmények javítására használhat.

Az offline értékelések nem biztosítanak útmutatást a feltáráshoz használandó események százalékos arányához.

## <a name="prerequisites-for-offline-evaluation"></a>Offline kiértékelés előfeltételei

A következő fontos szempontokat kell figyelembe venni a reprezentatív kapcsolat nélküli kiértékeléshez:

* Elegendő adattal rendelkezik. Az ajánlott minimum legalább 50 000 esemény.
* Adatok gyűjtése a reprezentatív felhasználói viselkedéssel és adatforgalommal rendelkező időszakokból.

## <a name="discovering-the-optimized-learning-policy"></a>Az optimalizált tanulási szabályzat felfedése

A személyre szabott kiértékelési folyamat használatával automatikusan felderíthető az optimális tanulási szabályzat.

Az offline értékelés elvégzése után a személyre szabás az aktuális online házirenddel összehasonlítva az új szabályzattal megtekinthető. Ezt követően alkalmazhatja ezt a tanulási szabályzatot, hogy azonnal érvénybe vigye a személyre szabott, a letöltéssel és a modellek és a házirend paneljén való feltöltéssel. A későbbi elemzésekhez vagy használathoz is letöltheti.

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Az offline kiértékelés eredményeinek ismertetése

Ha offline kiértékelést futtat, nagyon fontos, hogy elemezze az eredmények megbízhatósági határait. Ha széleskörűek, ez azt jelenti, hogy az alkalmazás nem kapott elegendő adatmennyiséget a jutalmak becsléséhez, hogy azok pontosak vagy jelentősek legyenek. Mivel a rendszer további adatmennyiséget gyűjt, és offline értékeléseket futtat hosszabb időszakokon keresztül, a megbízhatósági intervallumok keskenyebbek lesznek.

## <a name="how-offline-evaluations-are-done"></a>Hogyan történik az offline értékelések

Az offline értékelések az **alternatív értékelés**nevű metódus használatával hajthatók végre. 

A személyre szabott szolgáltatás arra a feltételezésre épül, hogy a felhasználók viselkedése (és így a jutalmak) nem lehet visszamenőlegesen előre jelezni (a személyre szabás nem tudja, mi történt, ha a felhasználó a korábban megjelenő eltérőt mutatott), és csak tanulni mért jutalmak. 

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

Az offline értékelés csak a megfigyelt felhasználói viselkedést használja. Ezzel a folyamattal nagy mennyiségű adattal kell eldobnia, különösen akkor, ha az alkalmazás nagy számú művelettel rendelkező rangsorolt hívásokat végez.


## <a name="evaluation-of-features"></a>A funkciók kiértékelése

Az offline értékelések segítségével megadható, hogy a műveletek vagy a környezetek adott funkcióinak mekkora részét kell megszabni a magasabb szintű jutalmaknak. Az adatok kiszámítása a megadott időszakon és adatokon alapuló értékelés alapján történik, és az idő függvényében változhat.

Javasoljuk, hogy tekintse meg a funkciók értékeléseit, és kérje a következőket:

* Az alkalmazás vagy a rendszer további funkciói lehetővé teszik, hogy az alkalmazások és a rendszerek milyen vonalakban legyenek hatékonyabbak?
* Milyen funkciókat lehet eltávolítani az alacsony hatékonyság miatt? Az alacsony hatékonyságú funkciók a gépi tanulásban növelik a _zajt_ .
* Vannak olyan funkciók, amelyek véletlenül beletartoznak a szolgáltatásba? Ilyenek például a személyes azonosításra alkalmas adatok, a duplikált azonosítók stb.
* Vannak olyan nemkívánatos funkciók, amelyek nem használhatók a jogszabályi vagy a felelősségteljes használati megfontolások miatt? Vannak-e olyan szolgáltatások, amelyek proxyként (azaz szorosan tükrözve vagy korrelálva) nemkívánatos funkciókkal rendelkeznek?


## <a name="next-steps"></a>További lépések

[](how-to-settings.md)
A megszemélyesítő[Offline értékelések](how-to-offline-evaluation.md) konfigurálása a [személyre szabás működésének](how-personalizer-works.md) megismerése
