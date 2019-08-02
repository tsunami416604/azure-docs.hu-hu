---
title: Forgatókönyv-értékelés – személyre szabás
titleSuffix: Azure Cognitive Services
description: A személyre szabás bármilyen helyzetben alkalmazható, ahol az alkalmazás kiválaszthatja a megfelelő elemet, műveletet vagy terméket a megjelenítéshez, hogy a élmény jobban elérhető legyen, jobb üzleti eredményeket érjen el, vagy javítsa a termelékenységet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 465fba3a466aceaf9ef7b71e4b1957bfdbcad766
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663638"
---
# <a name="where-can-you-use-personalizer"></a>Hol használhatja a Personalizert?

Bármilyen helyzetben használhatja a személyre szabott lehetőséget, amikor az alkalmazásnak ki kell választania a megfelelő elemet, műveletet vagy terméket a megjelenítéshez – a jobb élmény érdekében a jobb üzleti eredmények eléréséhez vagy a hatékonyság növeléséhez. 

A személyre szabott gépi tanulás használatával kiválaszthatja, hogy melyik művelet jelenjen meg a felhasználó számára. A kijelölés a szolgáltatásnak továbbított adatmennyiségtől, minőségtől és elosztástól függően jelentősen változhat.

### <a name="checklist-for-applying-personalizer"></a>Ellenőrzőlista a személyre szabás alkalmazásához


A személyre szabás olyan helyzetekben is alkalmazható, ahol:

* Az alkalmazáshoz üzleti vagy használhatósági cél tartozik.
* Van egy olyan helye az alkalmazásban, ahol a felhasználók számára megjelenítendő kontextusbeli döntés alapján fejlesztheti ezt a célt.
* A legjobb választás a kollektív felhasználói viselkedésből és az összes jutalom pontszámból is elsajátítható.
* A gépi tanulás használata személyre szabáshoz az Ön által választott [felelős használati irányelvek](ethics-responsible-use.md) és döntések követik.
* A kontextusra vonatkozó döntés a lehető legjobb lehetőség (művelet) rangsorolásával állítható be a választható lehetőségek közül.
* Az alkalmazáshoz tartozó rangsorolt választék meghatározása a felhasználói viselkedés bizonyos aspektusának mérésével, valamint a _jutalom pontszámának_kifejezésével határozható meg. Ez egy-1 és 1 közötti szám.
* A jutalom pontszáma nem túl sok összetételt vagy külső tényezőt eredményez. A kísérlet időtartama elég alacsony ahhoz, hogy a jutalom pontszám kiszámítható legyen, miközben továbbra is releváns.
* A rangsorhoz tartozó kontextust úgy fejezheti be, hogy legalább 5 olyan [funkciót](concepts-features.md) tartalmaz, amelyet úgy gondol, hogy kihasználja a megfelelő választást, és nem tartalmaz személyazonosításra alkalmas adatokat. (PII).
* Minden egyes tartalomra vonatkozó döntésről, _műveletről_, legalább 5 olyan szolgáltatásról van [](concepts-features.md) szó, amelyről úgy gondolja, hogy a megfelelő választást fogja segíteni személyre szabni.
* Az alkalmazás képes megőrizni az adatmennyiséget, hogy az előzmények legalább 100 000 interakciót gyűjtsenek.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Gépi tanulási szempontok a személyre szabás alkalmazásához

A személyre szabás a megerősítő tanuláson alapul, és a gépi tanulás olyan megközelítését jelenti, amelyet Visszajelzésként tanít. 

A személyre szabás a legjobban olyan helyzetekben tanul, ahol:

* Van elég esemény az optimális személyre szabáshoz, ha a probléma idővel sodródik (például hírek vagy Fashion beállítások). A személyre szabás alkalmazkodik a valós világ folytonos változásaihoz, de az eredmények nem lesznek optimálisak, ha nem áll rendelkezésre elegendő esemény és információ az új minták felderítéséhez és rendezéséhez. Olyan használati esetet kell választania, amely elég gyakran előfordul. Érdemes lehet olyan használati eseteket keresni, amelyek naponta legalább 500 alkalommal történnek.
* A környezet és a műveletek elegendő [funkcióval](concepts-features.md) rendelkeznek a tanulás megkönnyítéséhez.
* Egy hívásnál kevesebb mint 50 művelet áll rendelkezésre.
* Az adatmegőrzési beállítások lehetővé teszik a személyre szabott adatok összegyűjtését az offline értékelések és a házirendek optimalizálásának elvégzéséhez. Ez általában legalább 50 000 adatpont.

## <a name="monitor-effectiveness-of-personalizer"></a>Személyre szabás

Az [Offline értékelések](concepts-offline-evaluation.md)végrehajtásával rendszeres időközönként nyomon követheti a személyre szabott személy hatékonyságát.

## <a name="use-personalizer-with-recommendation-engines"></a>Személyre szabott javaslat a motorokkal

Számos vállalat ajánlási motorokat, marketing-és kampánycélok-eszközöket, a célközönség szegmentálását és fürtözését, az együttműködési szűrést és más eszközöket ajánl fel a nagy katalógusból az ügyfeleknek szánt termékek használatát.

A [Microsoft Ajánlói GitHub](https://github.com/Microsoft/Recommenders) -tárháza példákat és ajánlott eljárásokat biztosít a Jupyter-jegyzetfüzetként biztosított, ajánlásokat tartalmazó rendszerek létrehozásához. Az útmutató az adatelőkészítéshez, modellek létrehozásához, kiértékeléséhez, finomhangolásához és végrehajtott, számos gyakori megközelítéshez, többek között a xDeepFM, a SAR-hoz, az ALS-hez, a DKN-hez,

A személyre szabott ajánlási motorral működhet, ha jelen van.

* Az ajánlási motorok nagy mennyiségű elemet (például 500 000) tesznek elérhetővé, és egy részhalmazt (például az első 20) a több száz vagy több ezer lehetőség közül.
* A személyre szabott számos művelet sok információt tartalmaz róluk, és valós időben rangsorolja őket egy adott gazdag kontextusban, míg a legtöbb ajánlási motor csak néhány attribútumot használ a felhasználókról, a termékekről és az interakcióról.
* A személyre szabott megoldás lehetővé teszi, hogy minden alkalommal önállóan tárja fel a felhasználói beállításokat, ami jobb eredményeket eredményez, ahol a tartalom gyorsan változik, például hírek, élő események, élő közösségi tartalom, napi frissítéssel ellátott tartalom vagy szezonális tartalom.

A közös használat egy ajánlási motor kimenetének elkészítése (például egy adott ügyfél 20 legfontosabb terméke), és ezt használja a személyre szabott beviteli műveletként.

## <a name="next-steps"></a>További lépések

[Etikai & felelős használat](ethics-responsible-use.md).