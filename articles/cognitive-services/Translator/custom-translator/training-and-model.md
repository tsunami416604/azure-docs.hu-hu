---
title: Mi az a képzés és a modell? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: A modell a rendszer, amely egy adott nyelvi pár fordítását biztosítja. A sikeres képzés eredménye egy modell. A modellek betanításakor a három egymást kölcsönösen kizáró adatkészlet kötelező betanítási adatkészletet, hangolási adatkészletet és tesztelési adatkészletet.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: af3f795dc5036b23b82562e7af4582bd90b44f47
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595436"
---
# <a name="what-are-trainings-and-models"></a>Mik azok a tréningek és modellek?

A modell a rendszer, amely egy adott nyelvi pár fordítását biztosítja.
A sikeres képzés eredménye egy modell. A modellek betanításakor három kölcsönösen kizáró adatkészletre van szükség: az adatkészletek betanítása, az adatkészlet hangolása és a tesztelési adatkészlet. A szótárakhoz tartozó információk is megadhatók.

Ha a képzések kiszolgálása során csak képzési adatokat adnak meg, az egyéni fordító automatikusan összeállítja a hangolási és tesztelési adatkészleteket. Kizárja az 5 000 mondatokat a betanítási adatokból, és az 2 500-et használja a hangolási és tesztelési készletek összeállításához.

## <a name="training-dataset-for-custom-translator"></a>Egyéni fordító betanítási adatkészlete

A betanítási csoportba tartozó dokumentumokat az egyéni fordító használja a modell kialakításának alapjául. A betanítás végrehajtása során a dokumentumokban található mondatok vannak igazítva (vagy párosítva). A betanítási dokumentumok összeállítása során a szabadságjogokat is elvégezheti. Belefoglalhatja, hogy az Ön által elkövetett dokumentumok egy modellben legyenek érintő jelentőséggel bírnak. Ismét zárja ki őket egy másikban, és tekintse meg a [Bleu (kétnyelvű kiértékelési tanulmány) pontszámának](what-is-bleu-score.md)hatását. Ha megtartja a hangolási készletet és a test set Constant, nyugodtan kísérletezhet a betanítási csoport összeállításával. Ez a megközelítés hatékony módszer a fordítási rendszer minőségének módosítására.

Több betanítást is futtathat egy projekten belül, és összehasonlíthatja a [Bleu pontszámait](what-is-bleu-score.md) az összes betanítási futtatás során. Ha több tanítást is futtat az összehasonlításhoz, győződjön meg arról, hogy az egyes időpontokban ugyanaz a hangolási/tesztelési érték van megadva. Győződjön meg arról is, hogy az eredményeket manuálisan is megvizsgálja a ["tesztelés"](how-to-view-system-test-results.md) lapon.

## <a name="tuning-dataset-for-custom-translator"></a>Adathalmaz finomhangolása egyéni fordítóhoz

A készletben található párhuzamos dokumentumokat az egyéni fordító használja az optimális eredmények érdekében a fordítási rendszer finomhangolásához.

A hangolási készlet az oktatás során használatos a fordítási rendszer összes paraméterének és súlyozásának az optimális értékre való beállításához. Válassza ki a finomhangolási készletet: a hangolási készletnek reprezentatívnak kell lennie a jövőben lefordítani kívánt dokumentumok tartalmára vonatkozóan. A hangolási csoport jelentős hatással van a létrehozott fordítások minőségére. A finomhangolás lehetővé teszi, hogy a fordítási rendszer olyan fordításokat nyújtson, amelyek az Ön által a hangolási adatkészletben megadott mintákhoz legközelebb vannak. Nem szükséges több mint 2500 mondatot beállítani hangolási készletként. Az optimális fordítási minőség érdekében javasoljuk, hogy a mondatok legjellemzőbb kiválasztásának kiválasztásával manuálisan válassza ki a hangolási készletet.

A hangolási készlet létrehozásakor válassza a mondatokat, amelyek a lefordítani kívánt jövőbeli mondatok kifejező és reprezentatív hossza. Olyan mondatokat is érdemes kiválasztania, amelyek olyan szavakat és kifejezéseket tartalmaz, amelyeket a jövőbeli fordításokban várható becsült eloszlásban szeretne lefordítani. A gyakorlatban a 8 és 18 karakter közötti mondat a legjobb eredményeket fogja eredményezni, mivel ezek a mondatok elég kontextust tartalmaznak az inflexiós megjelenítéséhez, és olyan kifejezési hosszúságot biztosítanak, amely jelentős, anélkül, hogy túlságosan összetett lenne.

A hangolási készletben használni kívánt mondatok típusának jó leírása a próza: tényleges, Fluent mondatok. Nem táblázatos cellák, nem pedig versek, nem csak a mondatok és a számok a normál nyelven.

Ha manuálisan kiválasztja a hangolási adatkészletet, akkor nem rendelkezhet ugyanazzal a mondattal, mint a betanítási és tesztelési adatai. A hangolási csoport jelentős hatással van a fordítások minőségére – a mondatokat körültekintően válassza ki.

Ha nem tudja, mit kell választania a hangolási készlethez, csak válassza ki a betanítási készletet, és hagyja, hogy az egyéni fordító válassza ki a hangolási készletet. Ha az egyéni fordító automatikusan kiválasztja a hangolási készletet, akkor a mondatok véletlenszerű részhalmazát fogja használni a kétnyelvű betanítási dokumentumokból, és kizárja ezeket a mondatokat a betanítási anyagból.

## <a name="testing-dataset-for-custom-translator"></a>Egyéni fordítói adatkészlet tesztelése

A tesztelési csoportba tartozó párhuzamos dokumentumok a BLEU (kétnyelvű kiértékelési tanulmány) pontszámának kiszámítására szolgálnak. Ez a pontszám a fordítási rendszerek minőségét jelzi. Ez a pontszám azt mutatja meg, hogy az ebből a képzésből származó fordítási rendszer által végzett fordítások milyen mértékben egyeznek meg a tesztelési adatkészletben szereplő hivatkozási mondatokkal.

A BLEU pontszám az automatikus fordítás és a hivatkozás fordítása közötti különbözet mérése. Az értéke 0 és 100 között van. A 0 pontszám azt jelzi, hogy a hivatkozás egyetlen szava sem jelenik meg a fordításban. A 100-es pontszám azt jelzi, hogy az automatikus fordítás pontosan megfelel a hivatkozásnak: ugyanaz a szó pontosan ugyanazon a helyen van. A kapott pontszám a Testing set összes mondatának átlaga.

A tesztelési készletnek olyan párhuzamos dokumentumokat kell tartalmaznia, amelyekben a célként megadott nyelv mondatok a megfelelő nyelvi mondatok legkívánatosabb Fordításai a párosban. Érdemes lehet ugyanazokat a feltételeket használni, amelyeket a hangolási csoport összeállításához használt. A tesztelési készlet azonban nem befolyásolja a fordítási rendszerek minőségét. Kizárólag a BLEU-pontszám előállítására szolgál, és semmi más.

Tesztelési csoportként legfeljebb 2 500 mondatnak kell lennie. Ha a rendszer automatikusan kiválasztja a tesztelési készletet, akkor a mondatok véletlenszerű részhalmazát fogja használni a kétnyelvű betanítási dokumentumokból, és kizárja ezeket a mondatokat a betanítási anyagból.

Megtekintheti a tesztelési csoport egyéni fordításait, és összehasonlíthatja azokat a tesztelési készletben található fordításokkal, ha a modellen belül a teszt lapra navigál.
