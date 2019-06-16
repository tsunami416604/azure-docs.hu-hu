---
title: Az Azure Automation és a Log Analytics-munkaterület leképezések
description: Ez a cikk ismerteti a megoldás támogatásához az Automation-fiók és a egy Log Analytics-munkaterület között engedélyezett leképezések
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8afe8fed8912dd365071f1c4c5560c9f5578dcd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133096"
---
# <a name="workspace-mappings"></a>Munkaterület-leképezések

Megoldások, mint az Update Management, Change Tracking és Inventory vagy virtuális gépek indítása/leállítása munkaidőn kívül megoldás engedélyezésekor összekapcsolása a Log Analytics-munkaterületet és Automation-fiók csak bizonyos régiókban támogatottak. Ez a leképezés csak az Automation-fiók és a Log Analytics-munkaterület vonatkozik. Az Automation-fiók vagy a Log Analytics-munkaterületre jelentő az erőforrásokat más régiókban is lehetnek.

## <a name="supported-mappings"></a>Támogatott leképezések

Az alábbi táblázat bemutatja a támogatott leképezések:

|**Log Analytics-munkaterület régiója**|**Az Azure Automation-régió**|
|---|---|
|**USA**||
|USA keleti régiója<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Ázsia és a Csendes-óceáni térség**||
|Délkelet-Ausztrália|Délkelet-Ausztrália|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Európa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**USA-beli államigazgatás**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> EastUS megfeleltetés a Log Analytics-munkaterületek az Automation-fiókokhoz nem egy pontos régiók-hozzárendelést, de a megfelelő megfeleltetés.

<sup>2</sup> kapacitás korlátozások miatt a régió nem érhető el új erőforrás létrehozásakor. Az Automation-fiókok és a Log Analytics-munkaterületekre is érvényes. A régió már létező kapcsolt erőforrások azonban továbbra is működik.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

Ha úgy dönt, hogy már nem szeretne az Automation-fiók integrálása a Log Analytics-munkaterület, megszüntetheti a fiók közvetlenül az Azure Portalról. Mielőtt folytatja, akkor először távolítsa el az Update Management, Change Tracking és Inventory vagy virtuális gépek indítása/leállítása munkaidőn kívül megoldások során Ha használja őket. Ha nem törli őket, ez a folyamat megakadályozza a folytatás. Tekintse át a cikk az adott megoldás importált távolítsa el a szükséges lépések megértéséhez.

Miután eltávolítja ezeket a megoldásokat, az Automation-fiók leválasztása a következő lépéseket végezheti.

> [!NOTE]
> Egyes megoldások, beleértve az Azure SQL-figyelési megoldás a korábbi lehet, hogy létre automation-adategységeket, és is szükség lehet a munkaterület leválasztása előtt el kell távolítani.

1. Az Azure Portalról nyissa meg az Automation-fiókját, és a az Automation-fiók oldalon válassza ki **csatolt munkaterület** a szakaszában **kapcsolódó erőforrások** a bal oldalon.

2. A leválasztás munkaterület oldalán kattintson a **munkaterület leválasztása**. Kapni fog egy kérdés ellenőrzése, hogy folytatja a műveletet.

3. Bár az Azure Automation próbál meg a fiók leválasztása a Log Analytics-munkaterület, nyomon követheti a folyamat állapotát **értesítések** a menüből.

Az Update Management megoldás használata esetén igény szerint, előfordulhat, hogy el kívánja távolítani a következő elemek, amelyek a megoldás eltávolítása után már nem szükséges.

* Frissítés ütemezése – a neve megegyezik a létrehozott frissítéstelepítések lesz)

* A megoldás - számára létrehozott hibrid feldolgozócsoportok egyes lesznek elnevezve hasonlóan a: gép1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Virtuális gépek indítása/leállítása munkaidőn kívül megoldás használata esetén igény szerint, előfordulhat, hogy el kívánja távolítani a következő elemek, amelyek a megoldás eltávolítása után már nem szükséges.

* Elindíthatja és leállíthatja a virtuális gép runbook ütemezése
* Virtuális gép runbookok elindítása és leállítása
* Változók

Másik megoldásként is megszüntetheti a munkaterület az Automation-fiókjából a Log Analytics-munkaterület. Válassza ki a munkaterület **Automation-fiók** alatt **kapcsolódó erőforrások**. Az Automation-fiók oldalon válassza ki a **fiók leválasztása a**.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan veheti a következő megoldásokat:

Frissítse a felügyeleti és a Change Tracking and Inventory:

* Az egy [virtuális gép](../automation-onboard-solutions-from-vm.md)
* Az a [Automation-fiók](../automation-onboard-solutions-from-automation-account.md)
* Amikor [több gép tallózása](../automation-onboard-solutions-from-browse.md)
* Az egy [runbook](../automation-onboard-solutions.md)

Virtuális gépek indítása és leállítása munkaidőn kívül

* [Központi telepítése a virtuális gépek indítása/leállítása munkaidőn kívül](../automation-solution-vm-management.md)