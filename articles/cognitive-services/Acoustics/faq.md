---
title: A Project Akusztika – gyakran ismételt kérdések
titlesuffix: Azure Cognitive Services
description: Ez az oldal választ ad a Project Acoustics-ról gyakran feltett kérdésekre, beleértve a letöltési utasításokat és a sütési folyamatot.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75770203"
---
# <a name="project-acoustics-frequently-asked-questions"></a>A Project Akusztika – gyakran ismételt kérdések

## <a name="what-is-project-acoustics"></a>Mi az a Project Acoustics?

A Project Acoustics plugins egy akusztikai rendszer, amely kiszámítja a hanghullám viselkedését futáselőtt, hasonlít a statikus világításhoz. A felhő nem a nehéz emelés a hullám fizika számítások, így a futásidejű CPU költsége alacsony.  

## <a name="where-can-i-download-the-plugin"></a>Hol lehet letölteni a plugin?

Letöltheti a [Project Acoustics Unity plugint](https://www.microsoft.com/download/details.aspx?id=57346) vagy a [Project Acoustics Unreal plugint.](https://www.microsoft.com/download/details.aspx?id=58090)

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Támogatja a Project &lt;&gt; Acoustics az x platformot?

A Project Acoustics platform támogatása az ügyfelek igényei alapján fejlődik. Kérjük, lépjen kapcsolatba velünk a [Project Acoustics kérdés fórumon,](https://github.com/microsoft/ProjectAcoustics/issues) hogy érdeklődjön a további platformok támogatásáról.

## <a name="is-azure-used-at-runtime"></a>Az Azure-t futásidőben használják?

Nem, a felhőintegráció csak az előreszámítási szakaszban használatos a jelenet beállításának részeként.
 
## <a name="what-is-simulation-input"></a>Mi az a szimulációs bemenet? 

A szimulációs bemenet a 3D-s jelenet, virtuális környezet vagy játék szinten. A Project Acoustics 3D térfogathullám-szimulációkat hajt végre, amelyek szorosan modellezik a hangfizikáját, beleértve a sima elzáródást és a szórást.
 
## <a name="what-is-the-runtime-cost"></a>Mi a futásidejű költség?

Akusztika a CPU körülbelül 0,01%-át veszi igénybe forrásonként és keretenként. A RAM-használat a jelenet méretétől függ, és 10 és 100 MB között lehet.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Egyszerűsítenem kell a szintgeometriát? Az irányítás háromszögeszámít? Vízhatlant csinálsz a meshe-ktől?

Nem. A rendszer közvetlenül befogja a részletes szintgeometriát. Ez lesz voxelized belső feldolgozásra.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Mi található a futásidejű keresse a táblában?

Az ACE fájl tartalmaz egy táblázatot az akusztikus paraméterek között számos forrás és a hallgató helyét pár, valamint voxelized jelenet geometria használt paraméter interpoláció.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>A Project Acoustics képes kezelni a mozgó forrásokat?

Igen, a Project Acoustics konzultál a keresőtáblában, és frissíti az egyes pipák hangdsp-ját, így képes kezelni a mozgó forrásokat és a figyelőt.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>A Project Acoustics képes kezelni a dinamikus geometriát? Bezárni az ajtókat? A falakat elfújták?

Nem. Az akusztikai paraméterek előre számításra kerülnek a játékszint statikus állapota alapján. Javasoljuk, hogy hagyja ki az ajtó geometriáját az akusztikából, majd alkalmazzon további elzáródást a megsemmisíthető és mozgatható játékobjektumok állapota alapján, bevált technikák alkalmazásával.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>A Project Acoustics használ akusztikus anyagokat?

Igen. Anyagok leszedett a fizikai anyag nevét a szinten, vezetési abszorpciós.
 
## <a name="what-do-the-probes-represent"></a>Mit jelentenek a "szondák"?

A szondák a lehetséges játékoshelyek mintavételezése. Minden szonda a helyszínről származó jelenet külön hullámszimulációját jelöli. Futásidőben a figyelő helyének akusztikai paramétereit interpolálja a közeli mintavételi helyekről.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Miért érdemes ennyi számítást költeni a felhőben? Mit veszek belőle?

A Project Acoustics pontos és megbízható akusztikai paramétereket biztosít még az ultra-összetett virtuális környezetekben is, minden építészeti szempontot figyelembe véve. Sima elzáródást, akadályt és dinamikus zengetés variációt biztosít a rajzkötetek kézi munkája nélkül. Mindezt, miközben a processzoron marad futásközben.

## <a name="what-exactly-happens-during-baking"></a>Pontosan mi történik a "sütés" során?

A sütni áll akusztikus hullám szimulációk téglatest szimulációs régiók középpontjában minden hallgató szonda.

## <a name="is-my-source-content-secure"></a>Biztonságos a forrástartalmam?

A Project Acoustics nem tölti fel a forrásjelenet geometriáját a felhőbe. Ehelyett a szimuláció a jelenet voxelization-ján működik, amelyet a mintavételhely-adatokkal kombinálnak, és egy saját formátumban tárolnak.     

## <a name="next-steps"></a>További lépések
* Próbálja ki a [Project Acoustics Unity mintatartalmat](unity-quickstart.md) vagy [az Unreal mintatartalmat](unreal-quickstart.md)

