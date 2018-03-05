---
title: "Megtudhatja, hogyan előkészítésére frissítéskezelés, a változások követése, és a készlet megoldásokat az Azure virtuális gép"
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
ms.openlocfilehash: a850189406b394e7935763206f9e3a191b415170
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>A bevezetni frissítéskezelés, a változások követése, és a készlet megoldások egy Azure virtuális gépen

Azure Automation-megoldások kezelése az operációs rendszer biztonsági frissítések, követni a változásokat, és készítsen leltárt a számítógépekre telepített biztosít. Többféleképpen is előkészítésére gépekre, akkor discoveryt a megoldás a virtuális gép [az Automation-fiók a](automation-onboard-solutions-from-automation-account.md), vagy a [runbook](automation-onboard-solutions.md). Ez a cikk foglalkozik bevezetési ezek a megoldások egy Azure virtuális gépen.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen

## <a name="enable-the-solutions"></a>A megoldások

Nyissa meg egy létező virtuális gépet, és válassza **frissítéskezelés**, **készlet**, vagy **a változáskövetés** alatt **műveletek**.

Válassza ki a naplóelemzési munkaterületet, és automatizálási fiókot, kattintson **engedélyezése** ahhoz, hogy a megoldás. A megoldás ahhoz, hogy akár 15 percet vesz igénybe.

![A bevezetni frissítés megoldás](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Nyissa meg az egyéb megoldások, és kattintson a **engedélyezése**, a naplóelemzési és legördülő listák le vannak tiltva, mivel ezek ugyanazt a munkaterületet, és automatizálási fiókot az előzőekben engedélyezett megoldásként az Automation-fiókhoz.

![A bevezetni frissítés megoldás](media/automation-onboard-solutions-from-vm/onboard-solutions2.png)

> [!NOTE]
> **Változások követése** és **készlet** ugyanahhoz a megoldáshoz, használja, ha engedélyezve van a másik engedélyezve legyen.

## <a name="scope-configuration"></a>Hatókör konfigurációjának

Egyes megoldások belül a munkaterület hatókör-konfigurációt használ, amelyekre a számítógépeket, amelyek a megoldás beszerzése. A hatókör beállításait olyan egy vagy több mentett keresések, amellyel korlátozhatja a megoldás az adott számítógépekhez. A hatókör-konfigurációja, az Automation-fiók alatt eléréséhez **kapcsolódó erőforrások**, jelölje be **munkaterület** majd munkaterületén **MUNKATERÜLET adatforrások**, Válassza ki **hatókör konfigurációk**.

Alapértelmezés szerint a két hatókör-konfigurációk **MicrosoftDefaultScopeConfig-változáskövetés** és **MicrosoftDefaultScopeConfig-frissítések**.

Kattintson a folytatást jelző pontokra (...) a konfigurációkat, és válassza ki a **szerkesztése**. Az a **Szerkesztés hatókör konfigurációjának** lapon jelölje be **számítógép csoportok kiválasztása** megnyitásához a **számítógépcsoportok** lap. Ezen a lapon látható a mentett kereséseket hozhatók létre a hatókör beállításait.

## <a name="saved-searches"></a>Mentett keresések

Egy számítógép hozzáadásakor a frissítéskezelés, vagy a változások követése és a készlet megoldások kerül a munkaterület két mentett keresés. Ezek a mentett keresések olyan lekérdezések, amelyek tartalmazzák a számítógépeket, amelyekre ezek a megoldások van kijelölve.

Keresse meg a munkaterületet, és válassza ki **mentett keresések** alatt **általános**. Ezek a megoldások által használt két mentett kereséseket a következő táblázatban láthatók:

|Name (Név)     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

Válassza ki a csoport feltöltése a lekérdezés megtekintéséhez vagy mentett keresés. Az alábbi ábrán a lekérdezés és az eredményeket.

![Mentett keresések](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>További lépések

Továbbra is az oktatóanyagok megtudhatja, hogyan használhatja ezeket megoldásokkal.

* [Az oktatóanyag - frissítések kezelése a virtuális gép számára](automation-tutorial-update-management.md)

* [Az oktatóanyag - szoftver, a virtuális gép](automation-tutorial-installed-software.md)

* [Az oktatóanyag - hibáinak elhárítása a virtuális gép változások](automation-tutorial-troubleshoot-changes.md)
