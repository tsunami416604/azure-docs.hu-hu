---
title: Az Azure Automation és a Log Analytics munkaterület-leképezései
description: Ez a cikk az Automation-fiók és a Megoldás támogatásához egy Log Analytics-munkaterület között engedélyezett leképezéseket ismerteti.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7f6508648be1e857a29f46b57e8309a7ec797291
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681832"
---
# <a name="workspace-mappings"></a>Munkaterület-leképezések

Ha olyan megoldásokat engedélyez, mint a frissítéskezelés, a változáskövetés és a készletrendszer, vagy a virtuális gépek indítása/leállítása munkaidőn kívül, csak bizonyos régiók támogatják a Log Analytics-munkaterület és az Automation-fiók összekapcsolásához. Ez a leképezés csak az Automation-fiókra és a Log Analytics-munkaterületre vonatkozik. Az Automation-fióknak vagy a Log Analytics-munkaterületnek jelentéskészítő erőforrások más régiókban is elhelyezkedhetnek.

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

<sup>1</sup> A Log Analytics-munkaterületek Automation-fiókokhoz való kelet-hozzárendelése nem pontos régió-régió leképezés, hanem a helyes hozzárendelés.

<sup>2</sup> A kapacitáskorlátok miatt a régió nem érhető el új erőforrások létrehozásakor. Ez magában foglalja az Automation-fiókokat és a Log Analytics-munkaterületeket. A régióban már meglévő összekapcsolt erőforrásoknak azonban továbbra is működniük kell.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics-munkaterülettel, leválaszthatja fiókját közvetlenül az Azure Portalról. A folytatás előtt először el kell távolítania a frissítéskezelést, a változáskövetést és a készletrendszert, valamint a kezdő/leállított virtuális gépeket munkaidőn kívüli megoldások során, ha használja őket. Ha nem távolítja el őket, nem tudja végrehajtani a leválasztási műveletet. Tekintse át a cikket az adott megoldás, amelyet importált, hogy megértsék a szükséges lépéseket, hogy távolítsa el.

Miután eltávolította ezeket a megoldásokat, az automation-fiók leválasztásához hajtsa végre az alábbi lépéseket.

> [!NOTE]
> Egyes megoldások, beleértve az Azure SQL figyelési megoldás korábbi verzióit, előfordulhat, hogy automatizálási eszközöket hoztak létre, és előfordulhat, hogy el kell távolítani a munkaterület leválasztása előtt.

1. Az Azure Portalon nyissa meg az Automation-fiókot. Az Automation-fiók lapon válassza a **Csatolt munkaterület** lehetőséget a Kapcsolódó **erőforrások csoportban.**

2. A munkaterület leválasztása lapon kattintson a **Munkaterület leválasztása gombra.** A folytatást kérő kérdés jelenik meg.

3. Bár az Azure Automation megpróbálja leválasztani a fiókot a Log Analytics-munkaterületről, nyomon követheti a folyamatot a menü **értesítések** csoportban.

4. Ha az Update Management megoldást használta, szükség esetén a megoldás eltávolítása után a következő elemeket is eltávolíthatja.

    * Ütemezések frissítése – Mindegyiknek van egy neve, amely megegyezik a létrehozott frissítési központi telepítéssel.
    * A megoldáshoz létrehozott hibrid feldolgozócsoportok – `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`Mindegyik nek hasonló neve van.

5. Ha a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás során használta, tetszés szerint eltávolíthatja a következő elemeket, amelyekre nincs szükség a megoldás eltávolítása után.

    * Virtuálisgép-runbook-ütemezések indítása és leállítása
    * Virtuálisgép-runbookok indítása és leállítása
    * Változók

Azt is megteheti, hogy leválassza munkaterületét az Automation-fiókról a munkaterületen belül. 

1. A munkaterületen válassza az **Automatizálási fiók** lehetőséget a **Kapcsolódó erőforrások csoportban.** 
2. Az Automatizálási fiók lapon válassza a **Fiók leválasztása**lehetőséget.

## <a name="next-steps"></a>További lépések

* További információ a frissítéskezelés, a változáskövetés és a készletmegoldások fedélzeti telepítéséről:

    * Virtuális [gépről](../automation-onboard-solutions-from-vm.md)
    * Az [Automation-fiókból](../automation-onboard-solutions-from-automation-account.md)
    * [Több gép böngészése közben](../automation-onboard-solutions-from-browse.md)
    * [Runbookból](../automation-onboard-solutions.md)

* Ismerje meg, hogyan lehet a virtuális gépek indítása/leállítása a munkaidőn kívüli megoldás során:

    * [Virtuális gépek telepítése/leállítása munkaidőn kívül](../automation-solution-vm-management.md)
