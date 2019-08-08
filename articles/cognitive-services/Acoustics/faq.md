---
title: Project Acoustics – gyakori kérdések
titlesuffix: Azure Cognitive Services
description: Ez a lap a projekt akusztikai kérdéseivel kapcsolatban feltett kérdésekre ad választ, beleértve a letöltési utasításokat és a sütni folyamatot.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: f0484fc9fc2af5514ba0f5b61277146a51757057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855024"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Project Acoustics – gyakori kérdések

## <a name="what-is-project-acoustics"></a>Mi az a Project Acoustics?

A beépülő modulok projektje egy akusztikai rendszer, amely a futásidejű működés előtt kiszámítja a hanghullám viselkedését, hasonlóan a statikus világításhoz. A felhő a hullámos fizikai számítások nagy mennyiségű feloldását végzi, így a futásidejű CPU-díj alacsony.  

## <a name="where-can-i-download-the-plugin"></a>Honnan tölthetők le a beépülő modul?

Letöltheti a [Project akusztikai egység beépülő](https://www.microsoft.com/download/details.aspx?id=57346) modulját vagy a [Project Acoustics Unreal beépülő modulját](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Támogatja &lt;a Project Acoustics az&gt; x platformot?

A Project akusztikai platform támogatja az ügyfelek igényei alapján történő kialakulását. Ha további platformokra vonatkozó támogatást szeretne megtudni, lépjen kapcsolatba velünk a [Project akusztikai kérdésekkel](https://github.com/microsoft/ProjectAcoustics/issues) foglalkozó fórumában.

## <a name="is-azure-used-at-runtime"></a>Az Azure-t használja futásidőben?

Nem, a felhő-integráció csak az előszámítási fázisban használatos a jelenet telepítőjének részeként.
 
## <a name="what-is-simulation-input"></a>Mi a szimulációs bemenet? 

A szimulációs bemenet a 3D-s jelenet, a virtuális környezet vagy a játék szintje. A Project Acoustics olyan 3D-s hullámos szimulációkat hajt végre, amelyek alaposan megalakították a fizikai fizikát, beleértve a zökkenőmentes elzáródást és a szórást.
 
## <a name="what-is-the-runtime-cost"></a>Mi a futásidejű díj?

Az akusztika körülbelül 0,01%-os CPU-t vesz igénybe. A RAM-használat a jelenet méretétől függ, és 10 – 100 MB-ig terjedhet.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Egyszerűsíteni kell a szint geometriáját? A háromszögek számának szabályozása? A hálók vízhatlanak legyenek?

Nem. A rendszer közvetlenül a részletes geometriai szintet fogja bevezetni. A rendszer belső feldolgozásra fogja voxelized.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Mi a futásidejű keresési táblázat?

Az ACE-fájl tartalmazza az akusztikus paraméterek egy táblázatát, amely számos forrás-és figyelő-hely párok között, valamint a voxelized a paraméter interpolációhoz használt geometriájában szerepel.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Képes a Project Acoustics kezelni a források áthelyezését?

Igen, a Project Acoustics megkeresi a keresési táblázatot, és frissíti a hanganyagot az egyes kullancsokon, így képes a mozgó források és a figyelő kezelésére.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Képes a Project Acoustics kezelni a dinamikus geometriát? Ajtók bezárása? Elfújta a falakat?

Nem. Az akusztikus paraméterek előre vannak kiszámítva a játék szintjének statikus állapota alapján. Javasoljuk, hogy hagyja ki az ajtó geometriáját az akusztikaból, majd alkalmazzon további elzáródásokat a kitalált módszerekkel a lerombolt és a mozgatható játék objektumai alapján.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>A Project Acoustics akusztikai anyagokat használ?

Igen. Az anyagokat az Ön szintjén található fizikai anyagok neveiből kell kiválasztani, absorptivity kell vezetni.
 
## <a name="what-do-the-probes-represent"></a>Mit jelentenek a "mintavételek"?

A mintavétel a lehetséges játékosok helyeinek mintavételezését jelenti. Mindegyik mintavétel a mintavételi helyről származó jelenet külön hullámos szimulációját jelöli. Futásidőben a figyelőhöz tartozó akusztikai paraméterek a közeli mintavételi helyekről vannak interpolált.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Miért érdemes nagy mennyiségű számítást költeni a felhőbe? Mit vásárol?

A Project akusztika pontos és megbízható akusztikai paramétereket biztosít még az igen összetett virtuális környezetek számára is, amely minden építészeti aspektust figyelembe vesz. Zökkenőmentes elzáródást és elzáródást, valamint dinamikus reverb-variációt biztosít a rajzolási kötetek manuális munkája nélkül. Miközben a CPU-t a Futtatás közben is megmaradt.

## <a name="what-exactly-happens-during-baking"></a>Pontosan mi történik a "sütés" során?

A Bake a téglatest-szimulációk akusztikus hullámos szimulációit tartalmazza az egyes figyelőknél.

## <a name="next-steps"></a>További lépések
* Próbálja ki a [Project akusztikai egység minta tartalmát](unity-quickstart.md) vagy az [Unreal Sample tartalmat](unreal-quickstart.md)

