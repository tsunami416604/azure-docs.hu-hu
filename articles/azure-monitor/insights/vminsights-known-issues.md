---
title: Azure Monitor for VMs (előzetes verzió) ismert problémák | Microsoft Docs
description: Ez a cikk az Azure-beli Azure-beli virtuális gépek operációs rendszerének állapotát, az alkalmazások függőségi felderítését és a teljesítmény monitorozását ötvöző Azure Monitor for VMsokkal kapcsolatos ismert problémákat ismerteti.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/02/2019
ms.openlocfilehash: b59e2d1897557b47bcfeafbc17141f869e2f192e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450681"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Ismert problémák a Azure Monitor for VMs (előzetes verzió)

Ez a cikk az Azure-beli Azure-beli virtuális gépek operációs rendszerének állapotát, az alkalmazás-összetevők felderítését és a teljesítmény monitorozását ismertető Azure Monitor for VMsokkal kapcsolatos ismert problémákat ismerteti. 

## <a name="health"></a>Egészségügy 
Az alábbi, az állapotfigyelő szolgáltatás aktuális kiadásával kapcsolatos ismert problémák:

- Ha egy Azure-beli virtuális gépet eltávolítanak vagy törölnek, egy kis ideig megjelenik a virtuálisgép-lista nézetben. Emellett az eltávolított vagy törölt virtuális gép állapotára kattintva megnyílik az **állapot-diagnosztika** nézet, majd megkezdi a betöltési ciklust. Ha kiválasztja a törölt virtuális gép nevét, megnyílik egy ablaktábla, amely azt jelzi, hogy a virtuális gép törölve lett.
- A konfigurációs változások, például a küszöbértékek frissítése, akár 30 percet is igénybe vehetnek, még akkor is, ha a portál vagy a munkaterhelés-figyelő API-ját azonnal frissíteni lehet. 
- Az állapot-diagnosztika a többi nézetnél gyorsabban frissül. Előfordulhat, hogy az információk késleltetve lesznek a közöttük való váltáskor. 
- Linux rendszerű virtuális gépek esetében az egyetlen virtuálisgép-nézet állapotára vonatkozó feltételeket felsoroló oldal címe a felhasználó által megadott virtuálisgép-név helyett a virtuális gép teljes tartománynevével rendelkezik. 
- Miután letiltotta egy virtuális gép figyelését a támogatott metódusok egyikével, és újra próbálkozik a telepítéssel, ugyanezen a munkaterületen kell telepítenie. Ha másik munkaterületet választ, és megpróbálja megtekinteni az adott virtuális gép állapotát, az inkonzisztens viselkedést jelezhet.
- Miután eltávolította a megoldás-összetevőket a munkaterületről, továbbra is megtekintheti az Azure-beli virtuális gépek állapotát. speciális teljesítmény és leképezési adat, ha a portálon a nézetre navigál. Az adatok végül nem jelennek meg a teljesítmény és a Térkép nézetből egy kis idő múlva. az állapot nézet azonban továbbra is megjeleníti a virtuális gépek állapotának állapotát. A **Try Now** lehetőség csak a teljesítmény és a nézetek leképezése után lesz elérhető.

## <a name="next-steps"></a>Következő lépések
A virtuális gépek figyelésének engedélyezéséhez szükséges követelmények és módszerek megismeréséhez tekintse át az [Azure monitor for VMS engedélyezése](vminsights-enable-overview.md)című témakört.
