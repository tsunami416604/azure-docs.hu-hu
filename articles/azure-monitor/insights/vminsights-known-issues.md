---
title: A virtuális gépek (előzetes verzió) ismert problémái az Azure Monitor |} A Microsoft Docs
description: Ez a cikk az Azure-beli Azure-beli virtuális gépek operációs rendszerének állapotát, az alkalmazások függőségi felderítését és a teljesítmény monitorozását ötvöző Azure Monitor for VMsokkal kapcsolatos ismert problémákat ismerteti.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/02/2019
ms.openlocfilehash: 711b3707d536c4858578817589670edf0f467b64
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670729"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>A virtuális gépek (előzetes verzió) az Azure Monitor szolgáltatással kapcsolatos ismert problémák

Ez a cikk az Azure-beli Azure-beli virtuális gépek operációs rendszerének állapotát, az alkalmazás-összetevők felderítését és a teljesítmény monitorozását ismertető Azure Monitor for VMsokkal kapcsolatos ismert problémákat ismerteti. 

## <a name="health"></a>Állapot 
Az alábbi, az állapotfigyelő szolgáltatás aktuális kiadásával kapcsolatos ismert problémák:

- Ha egy Azure-beli virtuális gépet eltávolítanak vagy törölnek, egy kis ideig megjelenik a virtuálisgép-lista nézetben. Emellett az eltávolított vagy törölt virtuális gép állapotára kattintva megnyílik az **állapot-diagnosztika** nézet, majd megkezdi a betöltési ciklust. A törölt virtuális gép nevét kiválasztva megnyílik egy ablaktábla és a egy üzenet, amely megállapítja, hogy a virtuális gép törölve lett.
- Konfigurációs módosítások, például frissítése egy küszöbértéket, akár 30 percet igénybe vehet, még akkor is, ha a portálon vagy a számítási feladatok a figyelő API előfordulhat, hogy azokat azonnal frissítse. 
- Az állapot-diagnosztika a többi nézetnél gyorsabban frissül. Előfordulhat, hogy az információk késleltetve lesznek a közöttük való váltáskor. 
- Linux rendszerű virtuális gépek esetében az egyetlen virtuálisgép-nézet állapotára vonatkozó feltételeket felsoroló oldal címe a felhasználó által megadott virtuálisgép-név helyett a virtuális gép teljes tartománynevével rendelkezik. 
- Miután letiltotta egy virtuális gép figyelését a támogatott metódusok egyikével, és újra próbálkozik a telepítéssel, ugyanezen a munkaterületen kell telepítenie. Ha másik munkaterületet választ, és megpróbálja megtekinteni az adott virtuális gép állapotát, az inkonzisztens viselkedést jelezhet.
- Miután eltávolította a megoldás-összetevőket a munkaterületről, továbbra is megtekintheti az Azure-beli virtuális gépek állapotát. speciális teljesítmény és leképezési adat, ha a portálon a nézetre navigál. Az adatok végül nem jelennek meg a teljesítmény és a Térkép nézetből egy kis idő múlva. az állapot nézet azonban továbbra is megjeleníti a virtuális gépek állapotának állapotát. A **Try Now** lehetőség csak a teljesítmény és a nézetek leképezése után lesz elérhető.

## <a name="next-steps"></a>Következő lépések
A virtuális gépek figyelésének engedélyezéséhez szükséges követelmények és módszerek megismeréséhez tekintse át az [Azure monitor for VMS engedélyezése](vminsights-enable-overview.md)című témakört.
