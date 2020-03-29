---
title: Mi a képzés és a modell? - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A modell a rendszer, amely fordítást biztosít egy adott nyelvpárhoz. A sikeres képzés eredménye egy modell. Egy modell betanításakor három egymást kölcsönösen kizáró adatkészlet szükséges betanítási adatkészlet, hangolási adatkészlet és tesztelési adatkészlet.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 71f1e3f460fa58b999af0a60c8cffa90c8ac8cd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219455"
---
# <a name="what-are-trainings-and-models"></a>Mik azok a képzések és modellek?

A modell a rendszer, amely fordítást biztosít egy adott nyelvpárhoz.
A sikeres képzés eredménye egy modell. Modell betanításakor három egymást kölcsönösen kizáró dokumentumtípusra van szükség: betanítás, hangolás és tesztelés. Szótári dokumentumtípus is megadható. Hivatkozzon a [mondatigazításra.](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences)

Ha csak a betanítási adatok at a betanítás idomítása, egyéni fordító automatikusan összehangolási és tesztelési adatokat. A betanítási dokumentumokból a mondatok véletlenszerű részhalmazát fogja használni, és ezeket a mondatokat kizárja magából a betanítási adatokból.

## <a name="training-document-type-for-custom-translator"></a>Betanítási dokumentum típusa egyéni fordítóhoz

A képzési készletben szereplő dokumentumokat az egyéni fordító használja a modell létrehozásának alapjaként. A betanítás végrehajtása során az ezekben a dokumentumokban lévő mondatok egyvonalban vannak (vagy párosítva vannak). A képzési dokumentumok összeállítása szabadságon van. Egy modellbe olyan dokumentumokat is felvehet, amelyekről úgy gondolja, hogy érintőleges jelentőséggel bírnak. Ismét zárja ki őket egy másik, hogy a hatás [bleu (kétnyelvű értékelés beugró) pontszám](what-is-bleu-score.md). Mindaddig, amíg a hangolókészletet és a tesztkészletet állandóan tartja, nyugodtan kísérletezzen a betanítási készlet összetételével. Ez a megközelítés hatékony módja a fordítási rendszer minőségének módosításának.

Több edzést is futtathat egy projekten belül, és összehasonlíthatja a [BLEU pontszámokat](what-is-bleu-score.md) az összes képzési futtatásban. Ha több betanítást futtat az összehasonlításhoz, győződjön meg arról, hogy minden alkalommal ugyanazok a hangolási/ tesztelési adatok vannak megadva. Is győződjön meg róla, hogy vizsgálja meg az eredményeket manuálisan a ["Tesztelés"](how-to-view-system-test-results.md) fülön.

## <a name="tuning-document-type-for-custom-translator"></a>Dokumentumtípus finomhangolása egyéni fordítóhoz

Az ebben a készletben található párhuzamos dokumentumokat az egyéni fordító használja a fordítási rendszer optimális eredményre való beállításához.

A hangolási adatok at a betanítás során a fordítási rendszer összes paraméterének és súlyának az optimális értékekhez való igazításához használjuk. Gondosan válassza ki a hangolási adatokat: a hangolási adatoknak reprezentatívnak kell lenniük a jövőben lefordítani kívánt dokumentumok tartalmára nézve. A hangolási adatok jelentős hatást gyakorolnak az előállított fordítások minőségére. A hangolás lehetővé teszi, hogy a fordítási rendszer olyan fordításokat biztosítson, amelyek a legközelebb állnak a hangolási adatokban megadott mintákhoz. A hangolási adatokban nem szükséges 2500-nál több mondatot. Az optimális fordítási minőség érdekében ajánlott manuálisan kiválasztani a hangolási készletet a mondatok legreprezentatívabb kiválasztásával.

A hangolási készlet létrehozásakor válassza ki azokat a mondatokat, amelyek a jövőbeli mondatok tartalmas és reprezentatív hossza, amelyeket várhatóan lefordít. Olyan mondatokat is ki kell választania, amelyek olyan szavakat és kifejezéseket tartalmazómondatokat, amelyeket a jövőbeli fordításokban elvárt hozzávetőleges eloszlásban kíván lefordítani. A gyakorlatban a 7-10 szóhosszúságú mondatok a legjobb eredményt hozzák, mivel ezek a mondatok elegendő kontextust tartalmaznak ahhoz, hogy inflexiót mutassanak, és olyan kifejezéshosszt adjanak, amely jelentős, anélkül, hogy túlságosan bonyolult lenne.

A hangolási készletben használandó mondatok típusának jó leírása a próza: a tényleges folyékony mondatok. Nem táblázat sejtek, nem versek, nem felsorolja a dolgokat, nem csak írásjelek, vagy számok egy mondatban - a rendszeres nyelv.

Ha manuálisan választja ki a hangolási adatokat, nem rendelkezhet ugyanazokat a mondatokat, mint a betanítási és tesztelési adatok. A hangolási adatok jelentős hatással vannak a fordítások minőségére - gondosan válassza ki a mondatokat.

Ha nem biztos abban, hogy mit válasszon a hangolási adatokhoz, csak válassza ki a betanítási adatokat, és hagyja, hogy az egyéni fordító válassza ki a hangolási adatokat. Ha hagyja, hogy az egyéni fordító automatikusan válassza ki a hangolási adatokat, a kétnyelvű képzési dokumentumokból származó mondatok véletlenszerű részhalmazát fogja használni, és kizárja ezeket a mondatokat magából a betanítási anyagból.

## <a name="testing-dataset-for-custom-translator"></a>Adatkészlet tesztelése egyéni fordítóhoz

A tesztkészletben szereplő párhuzamos dokumentumok a BLEU (Kétnyelvű értékelő beugró) pontszám kiszámítására szolgálnak. Ez a pontszám a fordítási rendszer minőségét jelzi. Ez a pontszám valójában megmutatja, hogy a fordítási rendszer által végzett fordítások, amelyek ennek a betanításnak a szorosan megegyeznek a tesztadatkészlet referenciamondataival.

A BLEU pontszám az automatikus fordítás és a referenciafordítás közötti delta mérése. Értéke 0 és 100 között mozog. A 0 pont azt jelzi, hogy a fordításban a hivatkozás egyetlen szava sem jelenik meg. A 100-as pontszám azt jelzi, hogy az automatikus fordítás pontosan megegyezik a hivatkozással: ugyanaz a szó pontosan ugyanabban a pozícióban van. A kapott pontszám a vizsgálati adatok összes mondatának BLEU pontszámátlaga.

A tesztadatoknak olyan párhuzamos dokumentumokat kell tartalmazniuk, amelyekben a célnyelvi mondatok a forrás-célpár megfelelő forrásnyelvi mondatainak legkívánatosabb fordításai. Előfordulhat, hogy ugyanazokat a feltételeket szeretné használni, mint a hangolási adatok összeállításához. A vizsgálati adatok azonban nincsenek hatással a fordítási rendszer minőségére. Kizárólag a BLEU pontszám generálására használják.

A tesztelési adatokhoz nem kell 2500-nál több mondat. Ha hagyja, hogy a rendszer automatikusan válassza ki a tesztkészletet, a kétnyelvű képzési dokumentumokból származó mondatok véletlenszerű részhalmazát fogja használni, és kizárja ezeket a mondatokat magából a képzési anyagból.

Megtekintheti a tesztkészlet egyéni fordításait, és összehasonlíthatja azokat a tesztkészletben biztosított fordításokkal, ha egy modellen belül a tesztlapra navigál.
