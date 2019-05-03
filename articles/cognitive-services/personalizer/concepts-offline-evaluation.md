---
title: Kapcsolat kiértékelése – Personalizer
titleSuffix: Azure Cognitive Services
description: Ezen visszajelzések létrehozása C# rövid útmutató a Personalizer szolgáltatással.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 29caea481b1999086440db2021b86d949ce6cbc6
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027235"
---
# <a name="offline-evaluation"></a>Kapcsolat kiértékelése

Kapcsolat nélküli értékelési olyan módszer, amely lehetővé teszi, hogy tesztelése, és felmérheti a Personalizer szolgáltatás hatékonyságát a kód módosítása vagy a felhasználói élményt érintő nélkül. Offline értékelési adatokat, hasonlítsa össze a különböző holtversenyben a rang API-hoz, az alkalmazás által küldött végrehajtása után történtek használ.

Kapcsolat nélküli kiértékelése történik egy adott időtartományban. A tartomány az aktuális idő szerint késői befejezéséhez. A tartomány kezdete nem lehet több, mint a megadott napok száma [adatmegőrzés](how-to-settings.md).

A kapcsolat nélküli értékelési segítségére lehetnek a következő kérdések megválaszolásával:

* Mennyire hatékonyak Personalizer holtversenyben sikeres testre szabási célból?
    * Mik azok az átlagos eszközökhöz érhető el, hogy a Personalizer online gépi tanulásra vonatkozó szabályzat?
    * Hogyan Personalizer összehasonlítása hatékonyságát, amit az alkalmazás tenné még meg alapértelmezés szerint
    * Mi lett volna a személyre szabás véletlenszerű választást összehasonlító hatékonyságát?
    * Mi lett volna a különböző tanulási házirendek manuálisan a megadott összehasonlító hatékonyságát?
* A környezet szolgáltatásokat több vagy kevesebb közre sikeres személyre szabása?
* A műveletek mely funkciókat több vagy kevesebb közre sikeres személyre szabása?

Emellett Offline értékelési használható felderítése több optimalizált tanulási házirendeket, amelyek Personalizer segítségével jobb eredményeket elérni a jövőben.

A kapcsolat nélküli értékelések tartalmaz útmutatást azon események feltárási használandó százalékának.

## <a name="prerequisites-for-offline-evaluation"></a>A kapcsolat nélküli értékelési előfeltételei

Az alábbiakban a reprezentatív offline kiértékelése fontos szempontjai:

* Elég adatokkal rendelkeznek. Az ajánlott minimális érték legalább 50 000 eseményeket.
* Időszakok reprezentatív alkalmazásfelhasználók viselkedése és a forgalom az adatgyűjtést.

## <a name="discovering-the-optimized-learning-policy"></a>Az optimalizált learning házirend felderítése

Personalizer az offline értékelési folyamat használatával egy több optimális learning szabályzat automatikusan felderíteni.

Után hajt végre a kapcsolat nélküli kiértékelése, láthatja, hogy a jelenlegi online-szabályzat képest az új házirenddel Personalizer összehasonlító hatékonyságát. Ezután a alkalmazni, hogy azonnal Personalizer hatékonyan learning házirendhez, vagy töltse le a jövőbeli elemzések vagy használatra.

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Az offline kiértékelési eredmények ismertetése

Ha offline próbaverzióját futtatja, nagyon fontos elemzéséhez _megbízhatósági rozsah_ az eredmények. Ha igen széles, az azt jelenti, az alkalmazás még nem kapott elég adat a pontos vagy jelentős ellenszolgáltatás becslések a. A rendszer több adatot gyűlnek, és hosszabb ideig offline értékelések futtatása, a konfidencia szűkebb válnak.

## <a name="how-offline-evaluations-are-done"></a>Hogyan offline értékelések történik.

Kapcsolat nélküli értékelések végzett nevezett módszerrel **Counterfactual értékelési**. 

Personalizer épül, feltételezve, hogy a felhasználói viselkedés (és így jutalmakat) nem lehetséges visszamenőleges előrejelzési (Personalizer nem tudja, mit kell történt a felhasználó korábban lett meg valami eltérő mi volt láthatják), és csak a további mért jutalmakat. 

Ez az értékelések használt fogalmi folyamat:

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

A kapcsolat nélküli kiértékelése csak az észlelt felhasználói viselkedés használja. Ez a folyamat elveti a nagy mennyiségű adatot, különösen akkor, ha az alkalmazás rangsor műveletek nagy számú-hívást.


## <a name="evaluation-of-features"></a>Szolgáltatások értékelése

Offline értékelések is adja meg az a Funkciók, hogy mennyi információ műveletek vagy a helyi rendszer súlyú magasabb jutalmakat a. Az adatokat az adott időszakban és az adatok az értékelés kiszámítása, és idővel változhat.

Javasoljuk, hogy funkció értékelések megnézzük, és kéri:

* Milyen egyéb, további, a szolgáltatások az alkalmazás- vagy biztosíthatja a mentén is, amelyek hatékonyabb?
* Milyen funkciókat eltávolítható alacsony hatékonysága miatt? Alacsony hatékonysága funkciók hozzáadása _zaj_ a machine Learning.
* Vannak-e a véletlenül részét képező szolgáltatásokat? Erre példa: személyazonosításra alkalmas adatok (PII), ismétlődő azonosítók stb.
* -E a nemkívánatos szolgáltatásokat, nem lehet jogszabályi vagy felelős miatt személyre szempontok használni? Vannak-e, hogy proxy funkciók (, szorosan tükrözéséhez vagy az összekapcsolását) nemkívánatos funkciók?


## <a name="next-steps"></a>További lépések

[Personalizer konfigurálása](how-to-settings.md)
