---
title: Munkaterület-hozzárendelések Azure Automation és Log Analytics
description: Ez a cikk az Automation-fiók és a Log Analytics munkaterület közötti, a megoldás támogatásához engedélyezett leképezéseket ismerteti
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849513"
---
# <a name="workspace-mappings"></a>Munkaterület-hozzárendelések

Ha olyan megoldásokat engedélyez, mint például a Update Management, a Change Tracking és a leltár vagy a Start/Stop VMs during off-hours megoldás, akkor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. Ez a leképezés csak az Automation-fiókra és a Log Analytics munkaterületre vonatkozik. Az Automation-fiókjába vagy Log Analytics-munkaterületre jelentett erőforrások más régiókban is lehetnek.

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

<sup>2</sup> a kapacitás korlátozásai miatt a régió nem érhető el új erőforrások létrehozásakor. Ez magában foglalja az Automation-fiókokat és a Log Analytics munkaterületeket. A régióban azonban a már meglévő társított erőforrások továbbra is működőképesek maradnak.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics munkaterülettel, közvetlenül a Azure Portalból is leválaszthatja a fiókját. Mielőtt továbblépne, először el kell távolítania a Update Management, a Change Tracking és a leltárt, vagy a Start/Stop VMs during off-hours megoldásokat, ha azokat használja. Ha nem távolítja el őket, a folyamat nem fog folytatódni. Tekintse át az importált konkrét megoldásról szóló cikket az eltávolításához szükséges lépések megismeréséhez.

A megoldások eltávolítása után a következő lépések végrehajtásával leválaszthatja az Automation-fiókját.

> [!NOTE]
> Előfordulhat, hogy néhány megoldás, például az Azure SQL-figyelési megoldás korábbi verziói automatizálási eszközöket hoztak létre, és a munkaterület leválasztása előtt is el kell távolítani őket.

1. A Azure Portal nyissa meg az Automation-fiókját, és az Automation-fiók lapon válassza a **csatolt munkaterület** lehetőséget a bal oldalon található **kapcsolódó erőforrások** szakaszban.

2. A munkaterület leválasztása lapon kattintson a **munkaterület leválasztása**elemre. A folytatáshoz meg kell kapnia egy figyelmeztetést.

3. Míg Azure Automation megkísérli leválasztani a fiókot a Log Analytics munkaterületen, nyomon követheti a menü **értesítések** részén látható előrehaladást.

Ha a Update Management megoldást használta, érdemes lehet eltávolítani a következő elemeket, amelyekre már nincs szükség a megoldás eltávolítása után.

* Frissítési ütemtervek – minden olyan névvel rendelkezik, amely megfelel a létrehozott frissítési központi telepítésnek.

* A megoldáshoz létrehozott hibrid feldolgozói csoportok – mindegyik neve hasonló lesz `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`).

Ha a Start/Stop VMs during off-hours megoldást használta, érdemes lehet eltávolítani a következő elemeket, amelyekre már nincs szükség a megoldás eltávolítása után.

* VM runbook-ütemtervek elindítása és leállítása
* VM-runbookok elindítása és leállítása
* Változók

Azt is megteheti, hogy kikapcsolja a munkaterületet az Automation-fiókjából a Log Analytics munkaterületről. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások**területen. Az Automation-fiók lapon válassza a **fiók megszüntetése**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ismerje meg a következő megoldások bevezetését:

Update Management és Change Tracking és leltár:

* [Virtuális gépről](../automation-onboard-solutions-from-vm.md)
* Az [Automation-fiókból](../automation-onboard-solutions-from-automation-account.md)
* [Több gép tallózása](../automation-onboard-solutions-from-browse.md)
* [Runbook](../automation-onboard-solutions.md)

Virtuális gépek munkaidőn kívüli elindítása/leállítása

* [Start/Stop VMs during off-hours üzembe helyezése](../automation-solution-vm-management.md)
