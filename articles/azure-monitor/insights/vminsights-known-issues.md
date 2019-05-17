---
title: A virtuális gépek (előzetes verzió) ismert problémái az Azure Monitor |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Monitor szolgáltatással kapcsolatos ismert problémák a virtuális gépek esetében az Azure-ban, amely ötvözi az egészségügyi alkalmazás függőségészlelése és alkalmazásteljesítmény-figyelés az Azure virtuális gép operációs rendszerének megoldást.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: magoedte
ms.openlocfilehash: 86a56e71b89e7408d1bc8ca0ee1dc8112bea368f
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522145"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>A virtuális gépek (előzetes verzió) az Azure Monitor szolgáltatással kapcsolatos ismert problémák

Ez a cikk ismerteti az Azure Monitor szolgáltatással kapcsolatos ismert problémák a virtuális gépek esetében az Azure-ban, amely ötvözi az állapotot, az alkalmazás-összetevők felderítése és az Azure virtuális gép operációs rendszerének alkalmazásteljesítmény-figyelő megoldás. 

## <a name="health"></a>Egészségügy 
Az alábbi ismert problémák az állapotfigyelő szolgáltatás jelenlegi kiadása:

- Egy Azure virtuális gép eltávolítása vagy törlése, ha egy ideig a virtuális gép nézetben jelenik meg. Ezenkívül egy eltávolított vagy törölt virtuális gép állapotát kattintva megnyílik a **egészségügyi diagnosztikai** megtekintheti, és ezután kezdeményezi a betöltés hurkot. A törölt virtuális gép nevét kiválasztva megnyílik egy ablaktábla és a egy üzenet, amely megállapítja, hogy a virtuális gép törölve lett.
- Konfigurációs módosítások, például frissítése egy küszöbértéket, akár 30 percet igénybe vehet, még akkor is, ha a portálon vagy a számítási feladatok a figyelő API előfordulhat, hogy azokat azonnal frissítse. 
- A Health diagnosztikai élmény frissítések gyorsabb, mint a más nézetekhez. Az adatokat késhetnek, amikor azok között. 
- Linux rendszerű virtuális gépekhez az ajánlati a virtuális gép egyetlen nézetben a állapotára vonatkozó feltételek lap címe rendelkezik a virtuális gép, a felhasználó által megadott virtuális gép neve helyett a teljes tartománynév. 
- Miután letiltja a támogatott módszerek egyikének használatával virtuális gépek figyelése, és megpróbálja ismét üzembe helyezné, ugyanazon a munkaterületen kell telepíteni. Ha egy másik munkaterületet, és próbálja meg a virtuális Gépeket az állapot megtekintése, akkor előfordulhat, hogy megjelenítése kiszámíthatatlan működést.
- A megoldás-összetevők eltávolítása a munkaterületről, után előfordulhat, hogy továbbra is megjelenik az Azure virtuális gépek; állapot kifejezetten teljesítmény és a térkép-adatok, nyissa meg a portálon vagy nézetet. Adatok végül le fog állni jelennek meg a kis idő múlva; a teljesítményt és a térkép megtekintése állapot megjelenítése a virtuális gépek azonban továbbra is az állapotmegtekintő nézet. A **kipróbálása** lehetőség lesz elérhető a helyreállítási környezet előkészítése a csak a teljesítmény és a térkép nézetek.

## <a name="next-steps"></a>További lépések
A követelmények és engedélyezésének a virtuális gépek figyelése módszerekkel, tekintse át [engedélyezése az Azure Monitor-beli virtuális gépek](vminsights-enable-overview.md).
