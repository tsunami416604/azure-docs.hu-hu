---
title: "Megtudhatja, hogyan előkészítésére frissítéskezelés, a változások követése, és a készlet megoldásokat az Azure Automationben"
description: "Ismerje meg, hogyan érheti egy Azure virtuális gép Azure Automation részét képező frissítéskezelés, a változások követése, és a készlet megoldásokkal"
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 02/28/2018
ms.topic: article
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 670a0c2a11ebfe09406233ab4b454b2e9c2ba0e0
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>A bevezetni frissítéskezelés, a változások követése, és a készlet megoldások

Azure Automation-megoldások kezelése az operációs rendszer biztonsági frissítések, követni a változásokat, és készítsen leltárt a számítógépekre telepített biztosít. Többféleképpen is előkészítésére gépekre, akkor discoveryt a megoldás [egy virtuális gép](automation-onboard-solutions-from-vm.md), az Automation-fiók vagy a [runbook](automation-onboard-solutions.md). Ez a cikk foglalkozik bevezetési ezeknek a megoldásoknak a az Automation-fiók.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen

## <a name="enable-solutions"></a>Megoldások

Nyissa meg az Automation-fiók, és válassza ki vagy **készlet** vagy **a változáskövetés** alatt **konfigurációkezelés**.

Válassza ki a naplóelemzési munkaterület és Automation-fiók, és kattintson a **engedélyezése** ahhoz, hogy a megoldás. A megoldás ahhoz, hogy akár 15 percet vesz igénybe.

![A bevezetni készlet megoldás](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

A változások követése és a készlet megoldás lehetővé teszi a [követni a változásokat](automation-vm-change-tracking.md) és [készlet](automation-vm-inventory.md) a virtuális gépeken. Ebben a lépésben engedélyezi a megoldás a virtuális gépen.

Nyomon követését és a készlet megoldás bevezetése értesítést befejezését követően kattintson a **frissítéskezelés** alatt **konfigurációkezelés**.

A frissítés felügyeleti megoldás lehetővé teszi a frissítések és javítások kezelheti a Windows Azure virtuális gépeken. A rendelkezésre álló frissítések, a szükséges frissítések telepítése ütemezés állapotát felmérheti, és felülvizsgálati központi telepítés eredményeinek ellenőrzése a frissítések alkalmazása sikeresen megtörtént a virtuális géphez. Ez a művelet a virtuális gép számára engedélyezve van a megoldás.

Válassza ki **frissítéskezelés** alatt **FRISSÍTÉSKEZELÉS**. A kiválasztott naplóelemzési munkaterület ugyanazon a munkaterületen használja az előző lépésben. Kattintson a **engedélyezése** bevezetni a frissítés-kezelési megoldásban. A megoldás ahhoz, hogy akár 15 percet vesz igénybe.

![A bevezetni frissítés megoldás](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Hatókör konfigurációjának

Egyes megoldások belül a munkaterület hatókör-konfigurációt használ, amelyekre a számítógépeket, amelyek a megoldás beszerzése. A hatókör beállításait olyan egy vagy több mentett keresések, amellyel korlátozhatja a megoldás az adott számítógépekhez. A hatókör-konfigurációja, az Automation-fiók alatt eléréséhez **kapcsolódó erőforrások**, jelölje be **munkaterület**. Ezt a munkaterület a **MUNKATERÜLET adatforrások**, jelölje be **hatókör konfigurációk**.

Alapértelmezés szerint létrejön a két hatókör konfigurációk **MicrosoftDefaultScopeConfig-változáskövetés** és **MicrosoftDefaultScopeConfig-frissítések**.

## <a name="saved-searches"></a>Mentett keresések

Egy számítógép hozzáadásakor a frissítéskezelés, vagy a változások követése és a készlet megoldások kerül a munkaterület két mentett keresés. Ezek a mentett keresések olyan lekérdezések, amelyek tartalmazzák a számítógépeket, amelyekre ezek a megoldások van kijelölve.

Nyissa meg az Automation-fiók, és válassza ki **mentett keresések** alatt **általános**. Ezek a megoldások által használt két mentett kereséseket a következő táblázatban láthatók:

|Name (Név)     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

Válassza ki a csoport feltöltése a lekérdezés megtekintéséhez vagy mentett keresés. Az alábbi ábrán a lekérdezés és az eredményeket:

![Mentett keresések](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-an-azure-machine"></a>A bevezetni Azure-gépen

Az Automation fiók válassza **készlet** vagy **változáskövetés** alatt **konfigurációkezelés**, vagy **frissítéskezelés** a **FRISSÍTÉSKEZELÉS**.

Kattintson a **+ Azure virtuális gép hozzáadása**, jelöljön ki egy virtuális Gépet a listából. Az a **frissítéskezelés** kattintson **engedélyezése**. Az aktuális virtuális gép hozzáadása a számítógép (csoport) mentett keresés a megoldáshoz.

## <a name="onboard-a-non-azure-machine"></a>A bevezetni egy-Azure gép

Az Automation fiók válassza **készlet** vagy **változáskövetés** alatt **konfigurációkezelés**, vagy **frissítéskezelés** a **FRISSÍTÉSKEZELÉS**.

Kattintson a **hozzáadása az Azure-gép**. Ez megnyílik egy új böngészőablakot, a telepítése és konfigurálása a Microsoft Monitoring Agent a számítógépen, a gép megkezdődhet az reporting megoldás lépéseit. Ha olyan számítógépen, amelyen felügyelete jelenleg a System Center Operations Manager bevezetési, nincs szükség új ügynök, a munkaterület információk bekerültek a meglévő ügynököt.

## <a name="onboard-machines-in-the-workspace"></a>A munkaterület előkészítésére gépek

Az Automation fiók válassza **készlet** vagy **változáskövetés** alatt **konfigurációkezelés**, vagy **frissítéskezelés** a **FRISSÍTÉSKEZELÉS**.

Válassza ki **kezelése gépek**. Ezzel megnyílik a **kezelése gépek** lap. Ezen a lapon lehetővé teszi, hogy engedélyezze a megoldás a select gépek halmazát jelenti, az összes rendelkezésre álló gépek, vagy engedélyezze a megoldás az összes aktuális gép, majd engedélyezze minden jövőbeni gépen.

![Mentett keresések](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="selected-machines"></a>Kijelölt gépek

A megoldás egy vagy több gépek engedélyezéséhez jelölje be **engedélyezése a kijelölt gépekhez** kattintson **hozzáadása** mellett minden számítógép hozzá szeretne adni a megoldáshoz. Ez a feladat hozzáadása a számítógép (csoport) mentett keresési lekérdezés a megoldás a kijelölt számítógép nevét.

### <a name="all-available-machines"></a>Az összes rendelkezésre álló gépek

Ahhoz, hogy a megoldás érhető el az összes számítógépen, válassza ki a **engedélyezése minden elérhető gépen**. Ez letiltja a vezérlő külön-külön számítógépet hozzáadni. Ez a feladat hozzáadása a mentett keresési lekérdezés számítógépcsoportra munkaterület jelentéskészítés gépek összes nevét.

### <a name="all-available-and-future-machines"></a>Az összes elérhető és a jövőbeni gépek

A megoldás összes rendelkezésre álló gép és az összes jövőbeni gép engedélyezéséhez jelölje be **engedélyezése minden elérhető és a jövőbeni gépen**. Ez a lehetőség törli a mentett keresés és hatókör-konfigurációk a munkaterületről. Ekkor megnyílik a megoldás az összes Azure és a munkaterületre jelentő nem Azure-gépeken.

## <a name="next-steps"></a>További lépések

Továbbra is az oktatóanyagok megtudhatja, hogyan használhatja ezeket megoldásokkal.

* [Az oktatóanyag - frissítések kezelése a virtuális gép számára](automation-tutorial-update-management.md)

* [Az oktatóanyag - szoftver, a virtuális gép](automation-tutorial-installed-software.md)

* [Az oktatóanyag - hibáinak elhárítása a virtuális gép változások](automation-tutorial-troubleshoot-changes.md)