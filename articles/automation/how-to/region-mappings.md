---
title: A csatolt Log Analytics munkaterület támogatási régiói
description: Ez a cikk azt ismerteti, hogyan lehet támogatni az Automation-fiók és a Log Analytics munkaterület közötti régió-hozzárendeléseket.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 229fab5550d7b03fcbba80b5f4d9433d3b31e5b1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680440"
---
# <a name="support-regions-for-linked-log-analytics-workspace"></a>A csatolt Log Analytics munkaterület támogatási régiói

Azure Automationban engedélyezheti a virtuális gépek Update Management, Change Tracking és leltározását, valamint Start/Stop VMs during off-hours szolgáltatásait. Azonban csak bizonyos régiók támogatottak egy Log Analytics munkaterület és egy Automation-fiók összekapcsolásához az előfizetésben. A régió-hozzárendelések csak az Automation-fiókra és a Log Analytics munkaterületre vonatkoznak. A Log Analytics munkaterület és az Automation-fióknak ugyanahhoz az előfizetéshez kell tartoznia, de az adott régióban üzembe helyezett különböző erőforráscsoportok is lehetnek. További információ: [log Analytics munkaterület és Automation-fiók](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

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

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics munkaterülettel, közvetlenül a Azure Portalból is leválaszthatja a fiókját. A továbblépés előtt először [le kell tiltania](move-account.md#disable-features) Update Management, Change Tracking és leltárt, és Start/Stop VMS During off-hours, ha használja őket. Ha nem tiltja le a szolgáltatásokat, a leválasztási művelet nem hajtható végre. 

Ha a funkciók le vannak tiltva, az Automation-fiók összekapcsolásához kövesse az alábbi lépéseket.

> [!NOTE]
> Előfordulhat, hogy egyes funkciók, például az Azure SQL monitoring megoldás korábbi verzióiban olyan Automation-eszközöket hoztak létre, amelyeket el kell távolítani a munkaterület leválasztása előtt.

1. Nyissa meg az Automation-fiókját a Azure Portal. Az Automation-fiók lapon a **kapcsolódó erőforrások**területen válassza a **csatolt munkaterület** lehetőséget.

2. A munkaterület megszüntetése lapon válassza a **munkaterület**leválasztása lehetőséget. Ha folytatni kívánja a folytatást, a rendszer rákérdez az ellenőrzésre.

3. Habár Azure Automation a fiók leválasztása a Log Analytics munkaterületről, a menü **értesítések** részén nyomon követheti a folyamat állapotát.

4. Ha Update Management használ, szükség esetén előfordulhat, hogy el szeretné távolítani a már nem szükséges alábbi elemeket:

    * Frissítési ütemtervek: mindegyiknek van egy olyan neve, amely megfelel egy Ön által létrehozott frissítés-telepítésnek.
    * A szolgáltatáshoz létrehozott hibrid feldolgozói csoportok: mindegyikhez hasonló név tartozik `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. Ha Start/Stop VMs during off-hours használ, opcionálisan eltávolíthatja a következő elemeket, amelyekre már nincs szükség:

    * VM runbook-ütemtervek elindítása és leállítása
    * VM-runbookok elindítása és leállítása
    * Változók

Azt is megteheti, hogy leválasztja a munkaterületet az Automation-fiókjából a munkaterületen belül.

1. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások**területen. 
2. Az Automation-fiók lapon válassza a **fiók megszüntetése**lehetőséget.

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg Update Management [Update Management áttekintésében](../automation-update-management.md).
* Ismerkedjen meg a Change Tracking és a leltárral [change Tracking és leltár áttekintésében](../change-tracking.md).
* Ismerkedjen meg Start/Stop VMs during off-hours [Start/Stop VMS During off-hours áttekintésében](../automation-solution-vm-management.md).
