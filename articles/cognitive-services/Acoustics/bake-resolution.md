---
title: Projekt Akusztika Bake felbontás
titlesuffix: Azure Cognitive Services
description: Ez a fogalmi áttekintés közben Akusztika sütés durva és finom megoldások közötti különbség ismerteti.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 04/05/2019
ms.author: KyleStorck
ms.openlocfilehash: 7dbf63ba39c5dcdebb363cfc37a45f0216a07497
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471828"
---
# <a name="project-acoustics-bake-resolution"></a>Projekt Akusztika Bake felbontás
Ez a fogalmi áttekintés közben Akusztika sütés durva és finom megoldások közötti különbség ismerteti. A munkafolyamat-sütési mintavételek lépés során ezt a beállítást válassza.

## <a name="Coarse-vs-Fine-Resolution"></a>Durva vs finom felbontás

Az egyetlen különbség a durva és finom feloldásának beállításai között a gyakoriságot, amellyel a szimuláció történik. Részletes kétszer magas, mint durva gyakoriságot használja. Ez számos következmények az az akusztikai szimuláció:

* A legnagyobb a durva kétszer finom lehető leghosszabbak, és ezért a voxels kétszer akkora.
* A szimuláció idő voxel mérete, ami egy durva bake 16-szor gyorsabban finom bake közvetlenül kapcsolódik.
* Nem lehet a portálok a (például ajtók vagy windows) kisebb, mint voxel szimulált. Durva beállítás okozhat egyes ezek kisebb portáljára, nem kell szimulált; ezért nem haladnak keresztül eredményes futásidőben. Láthatja, ha ez történik a voxels megtekintésével.
* A szimuláció kisebb gyakorisággal kevesebb diffraction sarkok és élek eredményez.
* Megbízható forrásból nem található "szürke" voxels (azaz voxels, amely tartalmazza a geometriai) belül. Ennek eredményeképpen nincs hang. Megbízható forrásból helyezi, így azok nem belül a nagyobb voxels durva finom beállítás használata esetén, mint a nagyobb nehézséget jelent.
* A nagyobb voxels fog végberendezésébe több portálok, az alább látható módon. Az első rendszerkép használatával hoztak durva, míg a második azonos kezdőpanelje finom megoldás használatával. A piros jelöléseket aszinkronitást nincs sokkal kevesebb behatolás kezdőpanelje finom beállítások használatával. A kék vonal kezdőpanelje alapján a geometriai, míg a piros vonalat a hatékony akusztikai portál voxel méretét határozzák meg. Hogyan a behatolás játszik az adott helyzetben függ teljesen hogyan a voxels illeszkedik a geometriai, a portál, amelynek a méretét és az objektumok a jelenetben helyét határozza meg.

![Képernyőkép a durva voxels egy kezdőpanelje, az Unreal kitöltése](media/unreal-coarse-bake.png)

![Az egy kezdőpanelje, az Unreal finom voxels képernyőképe](media/unreal-fine-bake.png)

## <a name="next-steps"></a>További lépések

Próbálja ki a durva és finom feloldásának beállításai segítségével saját a [Unreal](unreal-baking.md) vagy [Unity](unity-baking.md) beépülő modulokat.