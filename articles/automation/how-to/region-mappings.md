---
title: Munkaterület-hozzárendelések Azure Automation és Log Analytics
description: Ez a cikk az Automation-fiók és a Log Analytics munkaterület közötti, a megoldás támogatásához engedélyezett leképezéseket ismerteti
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 607cebca3e6e8ddd95900ecdbd7041e5f7bb50cc
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165773"
---
# <a name="workspace-mappings"></a>Munkaterület-leképezések

Update Management, Change Tracking és leltár, illetve Start/Stop VMs during off-hours engedélyezésekor csak bizonyos régiók támogatottak egy Log Analytics munkaterület és egy Automation-fiók összekapcsolásához az előfizetésben. Ez a leképezés csak az Automation-fiókra és a Log Analytics munkaterületre vonatkozik. A Log Analytics munkaterület és az Automation-fióknak ugyanahhoz az előfizetéshez kell tartoznia, de az adott régióban üzembe helyezett különböző erőforráscsoportok is lehetnek.

További információ: [log Analytics munkaterület és Automation-fiók](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Támogatott leképezések

A következő táblázat a támogatott leképezéseket tartalmazza:

|**Log Analytics munkaterület-régió**|**Azure Automation régió**|
|---|---|
|**USA**||
|<sup>1</sup> . EastUS|EastUS2|
|WestUS2|WestUS2|
|<sup>2</sup> . WestCentralUS|<sup>2</sup> . WestCentralUS|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Ázsia és a Csendes-óceáni térség**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Európa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> a log Analytics-munkaterületek automatizálási fiókokba való hozzárendelése nem a régió-hozzárendelés pontos EastUS, hanem a megfelelő leképezés.

<sup>2</sup> a kapacitás korlátozásai miatt a régió nem érhető el új erőforrások létrehozásakor. Ez magában foglalja az Automation-fiókokat és a Log Analytics munkaterületeket. A régióban lévő, korábban már meglévő csatolt erőforrásoknak azonban továbbra is működniük kell.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics munkaterülettel, közvetlenül a Azure Portalból is leválaszthatja a fiókját. A továbblépés előtt először el kell távolítania Update Management, Change Tracking és leltárt, és Start/Stop VMs during off-hours, ha használja őket. Ha nem távolítja el őket, nem fejezheti be a leválasztási műveletet. Tekintse át a cikk utasításait, amelyekkel engedélyezheti az eltávolításához szükséges lépéseket.

A eltávolítása után a következő lépések végrehajtásával leválaszthatja az Automation-fiókját.

> [!NOTE]
> Előfordulhat, hogy néhány megoldás, például az Azure SQL-figyelési megoldás korábbi verziói is létrehozták az Automation-eszközöket, és előfordulhat, hogy a munkaterület leválasztása előtt el kell távolítani őket.

1. Nyissa meg az Automation-fiókját a Azure Portal. Az Automation-fiók lapon a **kapcsolódó erőforrások**területen válassza a **csatolt munkaterület** lehetőséget.

2. A munkaterület leválasztása lapon kattintson a **munkaterület leválasztása**elemre. Ha folytatni kívánja a folytatást, a rendszer felszólítja az ellenőrzésre.

3. Míg Azure Automation megkísérli leválasztani a fiókot a Log Analytics munkaterületen, nyomon követheti a menü **értesítések** részén látható előrehaladást.

4. Ha Update Management használ, szükség esetén előfordulhat, hogy el szeretné távolítani a következő elemeket, amelyekre már nincs szükség a eltávolítása után.

    * Frissítési ütemtervek – mindegyiknek van egy olyan neve, amely megfelel egy Ön által létrehozott frissítés-telepítésnek.
    * A megoldáshoz létrehozott hibrid feldolgozói csoportok – mindegyik neve hasonló a `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`következőhöz:.

5. Ha Start/Stop VMs during off-hours használ, akkor eltávolíthatja a következő elemeket, amelyek nem szükségesek az eltávolításához.

    * VM runbook-ütemtervek elindítása és leállítása
    * VM-runbookok elindítása és leállítása
    * Változók

Azt is megteheti, hogy leválasztja a munkaterületet az Automation-fiókjából a munkaterületen belül.

1. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások**területen. 
2. Az Automation-fiók lapon válassza a **fiók megszüntetése**lehetőséget.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan készítheti elő Update Management és Change Tracking és leltározást:

    * [Virtuális gépről](../automation-onboard-solutions-from-vm.md)
    * Az [Automation-fiókból](../automation-onboard-solutions-from-automation-account.md)
    * [Több gép tallózása](../automation-onboard-solutions-from-browse.md)
    * [Runbook](../automation-onboard-solutions.md)

* Ismerje meg, hogyan készítheti elő a Start/Stop VMs during off-hours:

    * [Start/Stop VMs during off-hours áttekintése](../automation-solution-vm-management.md)
