---
title: A forgatókönyv értékelése – Personalizer
titleSuffix: Azure Cognitive Services
description: Minden olyan helyzetet, ahol az alkalmazás válassza a jobb oldali elemek, a művelet vagy termék megjelenítése – annak érdekében, hogy a élmény jobbá, a jobb üzleti eredmények elérése érdekében vagy a termelékenység növelése personalizer alkalmazhatók.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: e51ef9afd0e49b690a4f9cab09fdfbd3e86eee66
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441037"
---
# <a name="where-can-you-use-personalizer"></a>Hol használhatja a Personalizert?

Használat Personalizer bármilyen helyzetben, ahol az alkalmazása szükséges, válassza ki a jobb oldali elemek, a művelet vagy a termék megjelenítése – annak érdekében, hogy a felhasználói élményt jobb, jobb üzleti eredmények elérése érdekében, vagy növelje hatékonyságát. 

Personalizer gépi tanulási a felhasználó számára megjelenítendő végrehajtandó művelet kiválasztásához. A kijelölt jelentősen eltérőek lehetnek attól függően, a mennyiség, minőségi és a szolgáltatásnak továbbított adatok.

### <a name="checklist-for-applying-personalizer"></a>Ellenőrzőlista az Personalizer alkalmazása


Personalizer alkalmazhat helyzetekben ahol:

* Az alkalmazás valamilyen üzleti vagy használhatósági cél tartozik.
* Az alkalmazás, ahol egy környezetfüggő döntés, megjelenítése, hogy mi tételére felhasználók javulni fog a cél elérésében tett olyan helyen van.
* A legjobb választás lehet, és csoportos felhasználói viselkedést és az összes ellenszolgáltatás pontszám nek ismernie kell.
* A machine learning használatát a személyre szabás követi [felelős használati irányelvek](ethics-responsible-use.md) és a választott lehetőségek.
* A környezetfüggő döntést a legjobb lehetőség (művelet) prioritása a választási lehetőségek korlátozott számú jelöl.
* Arról, hogy a rangsorolt választás dolgozott az alkalmazás felhasználói viselkedést bizonyos elemeit méri, és a kifejezése lehet meghatározni a _pontszám díjazza_. Ez az 1 és 1 közötti szám.
* Túl sok válozók vagy külső tényezők a ellenszolgáltatás pontszám nem betöltheti. A kísérletezéssel töltött idő elég alacsony, hogy a ellenszolgáltatás pontszám kiszámítása, miközben továbbra is fontos.
* A rang kontextusa fejezhető ki legalább 5 listájaként [funkciók](concepts-features.md) úgy gondolja, hogy a megfelelő választás lenne segítségével, és, amelyek nem tartalmaznak személyes azonosításra alkalmas adatokat. (PII).
* Minden tartalom választott kapcsolatos információk _művelet_, legalább 5 listájaként [funkciók](concepts-features.md) , hogy úgy gondolja, hogy elvégzi Personalizer súgó a megfelelő választás.
* Az alkalmazás hosszú ideig is megőrizheti adatait elég gyűlnek legalább 100 000 interakciók előzményeit.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>A Machine learning Personalizer alkalmazására vonatkozó szempontok

A megerősítő tanulás, visszajelzés által tanított megközelítés, amely a machine Learning, adjon meg hozzá personalizer alapján történik. 

Personalizer helyzetekben ajánlott tekintjük át, ahol:

* Nincs elegendő események figyelemmel optimális személyre szabása, ha a probléma idővel (például a beállítások a hírek és reagálás) drifts. Personalizer alkalmazkodik a méretváltozásokhoz folyamatos módosítása a való világból, de eredmények nem optimális, ha nem érhető el elegendő eseményeket és ismerkedjen meg a felderíteni és a új minták létrehozását az adatokat. Válasszon egy alkalmazási helyzet, hogy elég gyakran történik. Fontolja meg a használati esetek, amely naponta legalább 500-szor keres.
* Környezet és műveletek van-e elegendő [funkciók](concepts-features.md) a tanulás egyszerűbbé tételével.
* Nincsenek rang / hívás kevesebb mint 50 műveleteket.
* Az adatmegőrzési beállításokat Personalizer elegendő adatgyűjtéshez offline értékelések és házirend-optimalizálás engedélyezése. Ez általában az adatpontok legalább 50 000.

## <a name="monitor-effectiveness-of-personalizer"></a>A figyelő hatékonyságát Personalizer

Rendszeres időközönként figyelheti a Personalizer hatékonyságát a elvégzésével [offline értékelések](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Javaslati motorok Personalizer használata

Számos vállalat javaslati motorok, marketing- és hálózatának megerősítése eszközök, célközönség-szegmentálást és a fürtözést, együttműködési szűrést és más módon használatával az ügyfelek számára egy nagy katalógusból származó termékek javasoljuk.

A [Microsoft Recommenders GitHub-adattár](https://github.com/Microsoft/Recommenders) példákat és ajánlott eljárásokat tartalmaz a javaslattételi rendszerek, a Jupyter notebooks megadott készítéséhez. Adatok előkészítése, modellek létrehozása, kiértékelése, hangolása és a javaslati motorok, számos közös módszerénél xDeepFM, többek között (KKT), ALS, RBM, DKN modellezést működő példák biztosítja.

Ha telepítve, egy ajánlattételi modul beépítését personalizer együttműködhet.

* Javaslati motorok igénybe a nagy mennyiségű elemek (például 500 000), és a egy részére (például az első 20) származó több száz vagy akár több ezer olyan lehetőség javasol.
* Personalizer kis számú nagy mennyiségű információt találhat azokat a műveleteket fogadja, és valós idejű adott gazdag környezetben, rangsorolja őket, bár a legtöbb javaslati motorok, csak a felhasználók, termékek és az interakciók néhány attribútumok használata.
* Personalizer úgy tervezték, hogy önállóan Fedezze fel a felhasználói beállítások folyamatosan, ahol tartalom módosul gyorsan, hírek, például az élő események jobb eredmények előállításához élő közösségi tartalom, a napi frissítések, vagy időszakos tartalmával.

Egy általában egy ajánlásokat készítő motor (például az első 20 terméket az egyes ügyfél) kimenetét és használni, amely a bemeneti műveleteinek Personalizer.

## <a name="next-steps"></a>További lépések

[Felelős használja a & etikai](ethics-responsible-use.md).