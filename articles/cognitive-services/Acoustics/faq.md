---
title: Projekt Akusztika – gyakori kérdések
titlesuffix: Azure Cognitive Services
description: Ez az oldal nyújt projekt Akusztika, beleértve a kapcsolatban gyakran felmerülő kérdésekre adott válaszok letöltésiutasítás- és os folyamat.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: c43c81d42a39bda504b02eb6c053a16a2cf53aec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138048"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Projekt Akusztika – gyakori kérdések

## <a name="what-is-project-acoustics"></a>Mi az a Project Acoustics?

A projekt Akusztika suite beépülő modulokat a rendszer egy Akusztika, amely kiszámítja a hangot wave viselkedés-futtatókörnyezet, mintha statikus világítás előtt. A felhő hajtja végre a wave feladatát fizika számításokat, így CPU-környezet futásidejű költségeit alacsony.  

## <a name="where-can-i-download-the-plugin"></a>Honnan tölthetem le a beépülő modult?

A projekt Akusztika letöltheti a [projekt Akusztika letöltőközpontlapon](https://www.microsoft.com/en-us/download/details.aspx?id=57346).

## <a name="does-project-acoustics-support-x-platform"></a>Nem támogatja a projekt Akusztika <x> platform?

A projekt Akusztika haladásával eszközplatform-támogatás a vásárlók igényei alapján. Forduljon hozzánk bizalommal a [projekt Akusztika fórumok](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics) további platformok a támogatással kapcsolatos események beküldéséhez.

## <a name="is-azure-used-at-runtime"></a>Azure futásidőben használatos?

Nem, felhőintegrációs szolgál a precompute szakasz során csak a jelenet beállítása.
 
## <a name="what-is-simulation-input"></a>Mit jelent a szimuláció bemeneti? 

A szimuláció bemeneti a 3D jelenetek, a virtuális környezet vagy a játék szintjén. Projekt Akusztika, amelyek a hanganyag fizika szorosan, beleértve a zökkenőmentes hangelnyelés és a szórás 3D-es wave szimulációkat végez.
 
## <a name="what-is-the-runtime-cost"></a>Mi az a környezet futásidejű költségeit?

Akusztika készül 0,01 %-os CPU / forrás / keret vesz igénybe. Memóriahasználatot jelenet méretétől függ, és 100 MB-os és 10 közötti.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Van szükségem a szolgáltatói geometriai leegyszerűsítése érdekében? Vezérlő háromszög száma? Győződjön meg arról, rácsvonalak vízmentes?

Nem. A rendszer közvetlenül képes-e feldolgozni a részletes szint geometriai. Belső feldolgozási voxelized lesz.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Mi az a futásidejű keresési táblából?

A hozzáférés-vezérlő bejegyzések fájl tartalmaz egy táblázatot akusztikai paraméterek között számos olyan forrás és a figyelő hely párok, valamint voxelized jelenet geometriai paraméter interpolációs használt.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Projekt Akusztika mozgó forrásból képes kezelni?

Igen, a projekt Akusztika consults szintűre frissül a keresési táblában, és frissíti az egyes az órajelhez a hang DSP, így képes kezelni, források és -figyelő áthelyezése.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Projekt Akusztika dinamikus geometriai képes kezelni? Ajtó bezárása? Elképesztő funkciók azonnal falak?

Nem. Akusztikai paraméterei a következők illesztésiszűrő játék szintű statikus állapota alapján. Javasoljuk az ajtó geometriai kívül Akusztika elhagyó, és majd a további hangelnyelés destructible állapota alapján alkalmazza, és mozgatható játék objektumok használatával létrehozott technikákat.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Projekt Akusztika használ akusztikai anyagok?

Igen. Anyagok a paraméterkészletben fizikai anyagi a szinten absorptivity vezetési útvonalról.
 
## <a name="what-do-the-probes-represent"></a>Milyen felelnek meg a "mintavételezők"?

Mintavételezők egy mintavételi lehetséges player helyek. Egyes Hálózatfigyelő a mintavétel helyen származó jelenet egy külön wave szimuláció jelöli. Futásidőben a figyelő helyének akusztikai paraméterek vannak interpolált mintavételi közeli helyről.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Miért költségek ennél jóval számítási a felhőben? Mi nem azt vásárlása velem?

Projekt Akusztika biztosít pontos és megbízható akusztikai paraméterek még ultramagas összetett virtuális környezeteibe, minden architekturális szempontból figyelembe véve. Zökkenőmentes hangelnyelés és akadály és dinamikus visszhang változata manuális munka nélküli rajzolási kötetek biztosít. Eközben a fennmaradó világos processzor, futtatás közben.

## <a name="what-exactly-happens-during-baking"></a>Mi pontosan "sütés" során történik?

Egy bake akusztikai wave szimulációk, minden egyes figyelő mintavételi középre cuboid szimuláció régiók áll.

## <a name="next-steps"></a>További lépések
* Próbálja ki a [Akusztika Unity Project mintatartalmakat próbálhat](unity-quickstart.md) vagy [Unreal minta tartalom](unreal-quickstart.md)

