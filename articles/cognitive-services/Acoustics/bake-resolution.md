---
title: Project Akusztika bake felbontás
titlesuffix: Azure Cognitive Services
description: Ez a fogalmi áttekintés leírja a durva és a finom felbontások közötti különbséget az akusztika sütése közben.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854348"
---
# <a name="project-acoustics-bake-resolution"></a>Project Akusztika bake felbontás
Ez a fogalmi áttekintés leírja a durva és a finom felbontások közötti különbséget az akusztika sütése közben. Ezt a beállítást a sütési munkafolyamat Mintavételek lépése során választja ki.

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>Durva vs finom felbontás

Az egyetlen különbség a durva és a finom felbontási beállítások között a szimuláció gyakorisága. Fine kétszer olyan magas frekvenciát használ, mint durva. Ennek számos hatása van az akusztikai szimulációra:

* A durva hullámhossz kétszer olyan hosszú, mint a finom, ezért a voxelek kétszer akkorák.
* A szimulációs idő közvetlenül kapcsolódik a voxel méretéhez, így a durva sütni körülbelül 16-szor gyorsabb, mint egy finom sütni.
* A voxel méretnél kisebb portálok (például ajtók vagy ablakok) nem szimulálhatók. A durva beállítás miatt a kisebb portálok némelyike nem szimulálható; ezért futásidőben nem adnak át hangot. Láthatjuk, ha ez történik megtekintésével voxels.
* Az alacsonyabb szimulációs frekvencia kevesebb diffrakciót eredményez a sarkok és élek körül.
* A hangforrások nem helyezhetők el a "töltött" voxels (azaz a geometriát tartalmazó voxelek) belsejében. Ennek eredménye nincs hang. Sokkal nehezebb elhelyezni hangforrások, így nem belül a nagyobb voxels durva, mint ez, ha a finom beállítást.
* A nagyobb voxels behatolnak több portálok, az alábbiak szerint. Az első kép durva, míg a második ugyanaz az ajtóban a finom felbontás. Amint azt a piros jelölések jelzik, sokkal kevesebb behatolás van az ajtóba a finom beállítás sal. A kék vonal a geometria által meghatározott ajtó, míg a piros vonal a voxel méret által meghatározott effektív akusztikai portál. Az, hogy ez a behatolás hogyan alakul ki egy adott helyzetben, teljes mértékben attól függ, hogy a voxelek hogyan egyeznek meg a portál geometriájával, amelyet a jelenetben lévő objektumok mérete és helye határoz meg.

![Képernyőkép durva voxels kitöltésével ajtó unreal](media/unreal-coarse-bake.png)

![Képernyőkép a finom voxels egy ajtóban az Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>További lépések

Próbálja ki a durva és finom felbontású beállításokat saját maga az [Unreal](unreal-baking.md) vagy [unity](unity-baking.md) beépülő moduljaink segítségével.
