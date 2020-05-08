---
title: Munkaterület-hozzárendelések Azure Automation és Log Analytics
description: Ez a cikk az Automation-fiók és a Log Analytics munkaterület között engedélyezett leképezéseket ismerteti.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6b00b25dc4eaea5bc8a3f5fbd42389aff501f14a
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901009"
---
# <a name="workspace-mappings"></a>Munkaterület-leképezések

Azure Automation a következő megoldásokat engedélyezheti: "Update Management", "Change Tracking és leltár", valamint "a virtuális gépek indítása/leállítása a munkaidőn kívül." Ha mégis így tesz, vegye figyelembe, hogy csak bizonyos régiók támogatottak a Log Analytics munkaterület és az előfizetéshez tartozó Automation-fiók összekapcsolásához. Ez a leképezés csak az Automation-fiókra és a Log Analytics munkaterületre vonatkozik. A Log Analytics munkaterület és az Automation-fióknak ugyanahhoz az előfizetéshez kell tartoznia, de az adott régióban üzembe helyezett különböző erőforráscsoportok is lehetnek.

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

<sup>1</sup> a log Analytics-munkaterületek Automation-fiókokba való EastUS nem pontos a régiók közötti leképezés, de a megfelelő leképezés.

<sup>2</sup> a kapacitás korlátozásai miatt a régió nem érhető el új erőforrások létrehozásakor. Ez magában foglalja az Automation-fiókokat és a Log Analytics munkaterületeket. A régióban lévő, korábban már meglévő csatolt erőforrásoknak azonban továbbra is működniük kell.

## <a name="unlink-a-workspace"></a>Munkaterület leválasztása

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics munkaterülettel, közvetlenül a Azure Portalból is leválaszthatja a fiókját. A továbblépés előtt először el kell távolítania a "Update Management", a "Change Tracking és a leltár" és a "virtuális gépek indítása/leállítása munkaidőn kívül" lehetőséget, ha használja őket. Ha nem távolítja el őket, nem fejezheti be a leválasztási műveletet. Tekintse át az Ön által engedélyezésre kerülő minden megoldásról szóló cikket, hogy megismerje az eltávolításához szükséges lépéseket.

A eltávolítása után a következő lépések végrehajtásával leválaszthatja az Automation-fiókját.

> [!NOTE]
> Bizonyos megoldások, például az Azure SQL monitoring megoldás korábbi verziói is létrehozhatnak Automation-eszközöket, és előfordulhat, hogy a munkaterület leválasztása előtt el kell távolítani őket.

1. Nyissa meg az Automation-fiókját a Azure Portal. Az **Automation-fiók** lapon a **kapcsolódó erőforrások**területen válassza a **csatolt munkaterület** lehetőséget.

2. A **munkaterület** megszüntetése lapon válassza a **munkaterület**leválasztása lehetőséget. Ha folytatni kívánja a folytatást, a rendszer rákérdez az ellenőrzésre.

3. Míg Azure Automation megkísérli leválasztani a fiókot a Log Analytics munkaterületen, nyomon követheti a menü **értesítések** részén látható előrehaladást.

4. Ha a "Update Management" kifejezést használta, előfordulhat, hogy el szeretné távolítani a következő elemeket, amelyekre már nincs szükség a eltávolítása után.

    * Frissítési ütemtervek: mindegyiknek van egy olyan neve, amely megfelel egy Ön által létrehozott frissítés-telepítésnek.
    * A megoldáshoz létrehozott hibrid feldolgozói csoportok: mindegyikhez hasonló név `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`tartozik.

5. Ha a "virtuális gépek elindítása/leállítása a munkaidőn kívül" funkciót használta, akkor eltávolíthatja a következő elemeket, amelyek nem szükségesek az eltávolításához.

    * VM runbook-ütemtervek elindítása és leállítása
    * VM-runbookok elindítása és leállítása
    * Változók

Azt is megteheti, hogy leválasztja a munkaterületet az Automation-fiókjából a munkaterületen belül.

1. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások**területen. 
2. Az **Automation-fiók** lapon válassza a **fiók megszüntetése**lehetőséget.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan kezdheti el a "Update Management" és a "Change Tracking and Inventory" használatát:

    * Egy [virtuális gépről](../automation-onboard-solutions-from-vm.md).
    * Az [Automation-fiókból](../automation-onboard-solutions-from-automation-account.md).
    * [Több gép tallózása](../automation-onboard-solutions-from-browse.md)esetén.
    * Egy [runbook](../automation-onboard-solutions.md).

* Megtudhatja, hogyan kezdheti el a virtuális gépek indítását és leállítását a munkaidőn kívül:

    * [Virtuális gépek indítása és leállítása a munkaidőn kívül – áttekintés](../automation-solution-vm-management.md).
