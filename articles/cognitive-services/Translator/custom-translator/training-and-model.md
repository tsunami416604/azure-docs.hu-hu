---
title: Mi az képzés és a modell? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Egy modell, a rendszer, amely egy adott nyelven párhoz fordítási biztosít. A sikeres képzési eredménye egy modellt. A modell betanításakor három egymást kölcsönösen kizáró adatkészletek szükségesek betanítási adatkészletet, adatkészlet hangolása és tesztelés az adatkészletet.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: v-rada
ms.openlocfilehash: bd4921cb959e3ea2e893c6837fb47792d3585ca9
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220127"
---
# <a name="what-are-trainings-and-models"></a>Mik a betanítások és modellek?

Egy modell, a rendszer, amely egy adott nyelven párhoz fordítási biztosít.
A sikeres képzési eredménye egy modellt. A modell betanításakor három egymást kölcsönösen kizáró adatkészletek szükség: betanítási adatkészletet, adatkészlet és a tesztelés adatkészlet. Szótár adatokat is megadható.

Ha csak az üzenetsor-kezelési képzési biztosított betanítási adatok, a egyéni a fordítót automatikusan összeállíthat finomhangolása és egy tesztelési. Ez 5000 mondatokat kizárása a betanítási adatok, és minden egyes összegyűjtése, a hangolási 2500 és tesztelési csoportok.

## <a name="training-dataset-for-custom-translator"></a>Betanítási adatkészletet egyéni a fordítót a

Dokumentumok betanítási készlete által használt az egyéni a fordítót alapjaként a modell létrehozásához. Betanítási futtatás során ezeket a dokumentumokat a mondatok vannak igazítva (vagy párosított). Szabadságjogokkal összeállítása a betanítási dokumentumok köre a is igénybe vehet. Megadhat egy modell érintőlegesen relevancia vannak, amely Ön szerint dokumentumok. Újra kizárja őket egy másik-azonosítókra gyakorolt hatást a [(kétnyelvű értékelési Understudy) BLEU pontszám](what-is-bleu-score.md). Mindaddig, amíg megőrzi a hangolási beállítása és a teszt set állandó, nyugodtan kísérletezhet a gyakorlókészlethez felépítését. Ez a megközelítés akkor módosítsa a fordítási rendszer minőségét hatékony módszert.

A projekten belül több betanítások futtathat, és hasonlítsa össze a [BLEU pontszámok](what-is-bleu-score.md) összes betanítási futtatás során. Az összehasonlítás több betanítások futtatja, amikor ugyanazon hangolása érdekében / Tesztadatok minden alkalommal van megadva. Ügyeljen arra, hogy az eredmények manuálisan is vizsgálata a ["Tesztelés"](how-to-view-system-test-results.md) fülre.

## <a name="tuning-dataset-for-custom-translator"></a>Egyéni a fordítót adatkészlet hangolása

Ebben a készletben szereplő párhuzamos dokumentumok használják az egyéni a fordítót az optimális eredmények elérése érdekében a fordítási rendszer finomhangolása.

A beállítási csoport a betanítás során hangolására szolgálnak az összes paramétereket és a fordítási rendszer optimális értékek súlyozását. Válassza ki, a hangolási óvatosan állítsa be: a beállítási csoportot kell modelleznie, a dokumentumok tartalmát, a szeretne lefordítani a jövőben. A beállítási csoport állítja elő a fordítások minőségét egy jelentős hatással van. Hangolási lehetővé teszi, hogy a fordítási rendszerét a fordítások legközelebbi a mintákat, a hangolási adatkészlet meg. Nem kell több mint 2500 mondatokat, hangolása beállítása. Az optimális a fordítás minősége ajánlott a mondatok legfontosabb választott kiválasztásával manuálisan válassza ki a hangolási beállítása.

A beállítási csoport létrehozásakor válassza ki a mondatokat, amelyek a jövőbeli mondatok fordítása várhatóan beállítás után sokatmondóbbak és reprezentatív hosszát. Szavak és kifejezések fordítása, amely a jövőbeni fordítások várt hozzávetőleges terjesztési kívánt rendelkező mondatok is kell választania. A gyakorlatban egy mondatban hossza 8 – 18 szavak eredményez a legjobb eredmények elérése érdekében, mert ezek a mondatok elegendő környezeti nincsenek megjelenítéséhez, és adja meg, amely jelentős, anélkül, hogy túl összetett kifejezést hossza tartalmaz.

Kiderül, hogy a mondatok hangolási készletében használandó típusát a prose: tényleges fluent mondatokat. Nem cellák, nem poems, nem listája azokról a dolgokról, csak nem absztrakt, vagy számokat egy mondatban - rendszeres nyelv.

Ha manuálisan végrehajtja a hangolási adatkészlet, ha nem rendelkezhet azonos a mondatok, mint a képzés és tesztelési adatokat bármely. A beállítási csoport jelentős hatást gyakorol a fordítások minőségének - gondosan válassza ki a mondatok.

Ha nem biztos melyik a megfelelő, a hangolási van állítva, csak a betanítási készletének kiválasztása, és lehetővé teszik egyéni Translator, a hangolási készletének kiválasztása. Ha engedélyezi az egyéni Translator, válassza ki a hangolási beállítása automatikusan, kétnyelvű képzési dokumentumaiból mondatokat véletlenszerűen kiválasztott részhalmazát használja, és ezeket a mondatok kizárása a képzési anyagokat magát.

## <a name="testing-dataset-for-custom-translator"></a>Egyéni a fordítót adatkészlet tesztelése

A tesztelési készlethez párhuzamos dokumentumok a BLEU (kétnyelvű értékelési Understudy) pontszám számítási szolgálnak. Ezt az értéket a fordítási rendszer minőségének jelzi. Ezt az értéket ténylegesen bemutatja, hogyan végezhető el a fordítási rendszer képzést keletkezett a fordítások illeszkedjenek a hivatkozási mondatokat, a teszt adatkészletben.

A BLEU pontszám, egy mérték, a különbözeti az automatikus fordítás és a referencia-fordítás között. Az érték értéke 0 és 100. A pontszám a 0 azt jelzi, hogy a hivatkozás egyetlen szó nem látható a fordítás. A pontszám: 100 azt jelzi, hogy az automatikus fordítás pontosan megegyezik-e a hivatkozás: ugyanazon szó szerepel a pontos ugyanazon a helyen. A pontszámot kap az összes mondatokat, a tesztelési készlethez BLEU pontszám átlaga.

A teszt csoportot tartalmaznia kell a legcélszerűbb fordításokat, a párok a megfelelő adatforrás nyelvi mondatokat, amelyeknél a Célnyelv mondatok párhuzamos dokumentumokat. Előfordulhat, hogy szeretné használni, amellyel a hangolási set compose ugyanezeket a feltételeket. A tesztelési készlethez azonban nem befolyásolja a fordítási rendszer minőségének felett van. A kizárólag a BLEU pontszám készítése, az Ön számára, valamint a semmi más szolgál.

Több mint 2500 mondatokat, a tesztelési készlethez nincs szükség. Ha hagyja, hogy a rendszer automatikusan válassza ki a tesztelési készlethez, azt fogja használja kétnyelvű képzési dokumentumaiból mondatokat véletlenszerűen kiválasztott részhalmazát, és ezeket a mondatok kizárása a képzési anyagokat magát.

Megtekintheti a tesztelési készlethez egyéni fordításait, és hasonlítsa össze a fordítások a tesztelési készlethez megadott nyissa meg a tesztelési lapot a modellen belül.
