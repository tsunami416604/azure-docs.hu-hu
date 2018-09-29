---
title: Akusztika – Cognitive Services – gyakran ismételt kérdések
description: Ez az oldal nyújt projekt Akusztika, beleértve a kapcsolatban gyakran felmerülő kérdésekre adott válaszok letöltésiutasítás- és os folyamat.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: a71e867bd23cf64b2ac7fc8cd1c54c55d92ce924
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431788"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

## <a name="what-is-project-acoustics"></a>Mi az a Project Acoustics?

A projekt Akusztika Unity beépülő modul egy Akusztika rendszer, amely kiszámítja a hangot wave viselkedés-futtatókörnyezet, mintha statikus világítás előtt. A felhő hajtja végre a wave feladatát fizika számításokat, így CPU-környezet futásidejű költségeit alacsony.  

## <a name="where-can-i-download-the-plugin"></a>Honnan tölthetem le a beépülő modult?

Ha szeretné kiértékelni az akusztikai beépülő modult, regisztráljon [itt](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) az előzetes tervezői verzióra.

## <a name="is-azure-used-at-runtime"></a>Azure futásidőben használatos?

Nem, felhőintegrációs szolgál a precompute szakasz során csak a jelenet beállítása.
 
## <a name="what-is-simulation-input"></a>Mit jelent a szimuláció bemeneti? 

A szimuláció bemeneti a 3D jelenetek, a virtuális környezet vagy a játék szintjén. Projekt Akusztika, amelyek a hanganyag fizika szorosan, beleértve a zökkenőmentes hangelnyelés és a szórás 3D-es wave szimulációkat végez.
 
## <a name="what-is-the-runtime-cost"></a>Mi az a környezet futásidejű költségeit?

Akusztika készül 0,01 %-os CPU / forrás / keret vesz igénybe. Memóriahasználatot jelenet méretétől függ, és 100 MB-os és 10 közötti.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Van szükségem a szolgáltatói geometriai leegyszerűsítése érdekében? Vezérlő háromszög száma? Győződjön meg arról, rácsvonalak vízmentes?

Nem. A rendszer közvetlenül képes-e feldolgozni a részletes szint geometriai. Belső feldolgozási voxelized lesz.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Mi az a futásidejű keresési táblából?

A hozzáférés-vezérlő bejegyzések fájl nem akusztikai paraméterek között számos olyan forrás- és a figyelő hely párok tábláját.
 
## <a name="can-it-handle-moving-sources"></a>Kezelheti, áthelyezése források?

Igen, a **Microsoft Acoustics** Unity spatializer beépülő modul consults szintűre frissül a keresési táblázatban az egyes hang feldolgozási osztásjelek a forrás- és a figyelő helyeket. A spatializer DSP zökkenőmentesen akusztikai feldolgozási paraméterei fog megfelelni a frissíti.
 
## <a name="can-it-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Kezelheti, a dinamikus geometriai? Ajtó bezárása? Elképesztő funkciók azonnal falak?

Nem. Akusztikai paraméterei a következők illesztésiszűrő játék szintű statikus állapota alapján. Azt elhagyó ajtó geometriai Akusztika kívül javasolt, és ezután alkalmazza a további hangelnyelés meghatározott technikákkal destructible és mozgatható játék objektumok állapota alapján.
 
## <a name="does-it-handle-materials"></a>Anyagok kezeli azt?

Igen. Anyagok a paraméterkészletben fizikai anyagi a szinten absorptivity vezetési útvonalról.
 
## <a name="what-do-the-probes-represent"></a>Milyen felelnek meg a "mintavételezők"?

Mintavételezők egy mintavételi lehetséges player helyek. Egyes Hálózatfigyelő a mintavétel helyen származó jelenet egy külön wave szimuláció jelöli. Futásidőben a figyelő helyének akusztikai paraméterek vannak interpolált mintavételi közeli helyről.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Miért költségek ennél jóval számítási a felhőben? Mi nem azt vásárlása velem?

Projekt Akusztika biztosít pontos és megbízható akusztikai paraméterek még ultramagas összetett virtuális környezeteibe, minden architekturális szempontból figyelembe véve. Zökkenőmentes összes manuális tevékenység nélkül nem érhető el/akadály és dinamikus visszhang variation kötetek anélkül biztosít. Eközben a fennmaradó világos processzor, futtatás közben.

## <a name="what-exactly-happens-during-baking"></a>Mi pontosan "sütés" során történik?

A rendszer figyelembe veszi a potenciális player helyek egységesen távolságban elhelyezett "mintavételi" minta pozíciók álló készletet hoz létre. Minden egyes mintavétel független feladatokra áll egy bake-szintek: A rendszer figyelembe veszi a cuboid "Szimuláció régió" a mintavétel: ingyenes, és nem egy adott régióban legfeljebb 25 cm felbontású részletes wave szimuláció.

## <a name="next-steps"></a>További lépések
* Fedezze fel a [minta jelenet renderelése;](sample-walkthrough.md)

