---
title: Az Azure Automation és a Log Analytics munkaterület-leképezései
description: Ez a cikk az Automation-fiók és a Log Analytics-munkaterület között a megoldás támogatásához engedélyezett leképezéseket ismerteti.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849513"
---
# <a name="workspace-mappings"></a>Munkaterület-leképezések

Ha olyan megoldásokat engedélyez, mint a frissítéskezelés, a változáskövetés és a készlet, vagy a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás során, csak bizonyos régiók támogatják a Log Analytics-munkaterület és az Automation-fiók összekapcsolásához. Ez a leképezés csak az Automation-fiókra és a Log Analytics-munkaterületre vonatkozik. Az Automation-fiókba vagy a Log Analytics-munkaterületre jelentéskészítő erőforrások más régiókban is elhelyezkedhetnek.

## <a name="supported-mappings"></a>Támogatott leképezések

Az alábbi táblázat a támogatott hozzárendeléseket mutatja be:

|**Log Analytics-munkaterületi terület**|**Azure automation régió**|
|---|---|
|**USA**||
|Kelet-US<sup>1</sup>|KeletUS2|
|Nyugat-us2|Nyugat-us2|
|WestCentralUS<sup>2 (Nyugat-közép-nyugati közép- és nyugati</sup>|WestCentralUS<sup>2 (Nyugat-közép-nyugati közép- és nyugati</sup>|
|**Kanada**||
|KanadaKözponti|KanadaKözponti|
|**Ázsia és a Csendes-óceáni térség**||
|AusztráliaDélkelet|AusztráliaDélkelet|
|Délkelet-Ázsia|Délkelet-Ázsia|
|Közép-India|Közép-India|
|JapánKelet|JapánKelet|
|**Európa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia között|USGovVirginia között|

<sup>1</sup> A Log Analytics-munkaterületek EastUS-hozzárendelése az Automation-fiókokhoz nem pontos régió-régió leképezés, hanem a helyes leképezés.

<sup>2</sup> A kapacitáskorlátok miatt a régió nem érhető el új erőforrások létrehozásakor. Ez magában foglalja az Automation-fiókok és a Log Analytics-munkaterületeket. A régióban már meglévő összekapcsolt erőforrásoknak azonban továbbra is működniük kell.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics-munkaterülettel, leválaszthatja fiókját közvetlenül az Azure Portalról. Mielőtt továbblépne, először el kell távolítania a frissítéskezelést, a változáskövetést és a készletrendszert, illetve a kezdő/leállító virtuális gépeket munkaidőn kívüli megoldások során, ha használja őket. Ha nem távolítja el őket, ez a folyamat nem folytatódik. Tekintse át a cikket az adott megoldás importált, hogy megértsék a szükséges lépéseket, hogy távolítsa el azt.

Miután eltávolította ezeket a megoldásokat, az automation-fiók leválasztásához hajtsa végre az alábbi lépéseket.

> [!NOTE]
> Egyes megoldások, beleértve az Azure SQL figyelési megoldás korábbi verzióit, előfordulhat, hogy automatizálási eszközöket hoztak létre, és előfordulhat, hogy a munkaterület leválasztása előtt el kell távolítani őket.

1. Az Azure Portalon nyissa meg az Automation-fiókot, és az Automation-fiók lapon válassza a **Csatolt munkaterület** lehetőséget a bal oldali **Kapcsolódó erőforrások** szakaszban.

2. A munkaterület leválasztása lapon kattintson a **Munkaterület leválasztása gombra.** A folytatást ellenőrző üzenet jelenik meg.

3. Bár az Azure Automation megpróbálja leválasztani a fiókot a Log Analytics-munkaterületről, nyomon követheti a folyamatot a menü **értesítések** csoportban.

Ha az Update Management megoldást használta, szükség esetén a megoldás eltávolítása után a következő elemeket is eltávolíthatja.

* Ütemezések frissítése – Mindegyiknek olyan neve lesz, amely megfelel a létrehozott frissítési központi telepítéseknek)

* A megoldáshoz létrehozott hibrid munkavégző csoportok – `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`Mindegyik a rendszerhez hasonlóan lesz elnevezve.Hybrid worker groups created for the solution – Each will be named similars to).

Ha a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás során, szükség esetén érdemes lehet eltávolítani a következő elemeket, amelyek már nincs szükség a megoldás eltávolítása után.

* Virtuálisgép-runbook-ütemezések indítása és leállítása
* Virtuálisgép-runbookok indítása és leállítása
* Változók

Azt is megteheti, hogy leválassza a munkaterületet az Automation-fiókról a Log Analytics-munkaterületről. A munkaterületen válassza az **Automatizálási fiók** lehetőséget a **Kapcsolódó erőforrások csoportban.** Az Automatizálási fiók lapon válassza a **Fiók leválasztása**lehetőséget.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan kell a következő megoldásokat beszállni:

A kezelés és a változáskövetés és a készlet frissítése:

* Virtuális [gépről](../automation-onboard-solutions-from-vm.md)
* Az [Automation-fiókból](../automation-onboard-solutions-from-automation-account.md)
* [Több gép böngészése közben](../automation-onboard-solutions-from-browse.md)
* [Runbookból](../automation-onboard-solutions.md)

Virtuális gépek indítása és leállítása munkaidőn kívül

* [Virtuális gépek telepítése/leállítása munkaidőn kívül](../automation-solution-vm-management.md)
