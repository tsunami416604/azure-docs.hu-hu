---
title: Egy Azure virtuális gép előkészítésére frissítéskezelés, a változások követése, és a készlet megoldások
description: Megtudhatja, hogyan való előkészítéséről a frissítéskezelés, a változások követése, és a készlet megoldásokkal Azure virtuális gép részét képező Azure Automation.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: f270b2ccea51e83bc6475051b8667bf73d7fd717
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221512"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>A bevezetni frissítéskezelés, a változások követése, és a készlet megoldások Azure virtuális gépről

Azure Automation-megoldások kezelése az operációs rendszer biztonsági frissítések, nyomon követheti a módosításokat, és készítsen leltárt a számítógépekre telepített biztosít. Több módon is előkészítésére gépek. Ön discoveryt a megoldás a virtuális gép [az Automation-fiók a](automation-onboard-solutions-from-automation-account.md), [több gép tallózással](automation-onboard-solutions-from-browse.md), vagy egy [runbook](automation-onboard-solutions.md). Ez a cikk foglalkozik bevezetési ezek a megoldások Azure virtuális gépről.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="enable-the-solutions"></a>A megoldások

Ugrás a meglévő virtuális gépből. A **műveletek**, jelölje be **frissítéskezelés**, **készlet**, vagy **változáskövetési**.

Ahhoz, hogy a megoldás csak a virtuális gép számára, győződjön meg arról, hogy **engedélyezése a virtuális gép** van kiválasztva. Előkészítéséről a megoldás több gép válassza **engedélyezése a virtuális gépek ebben az előfizetésben**, majd válassza ki **kattintással jelölje ki a gépeket engedélyezése**. Megtudhatja, hogyan lehet bevezetésében több gép egyszerre: [előkészítésére frissítéskezelés, a változások követése és a készlet megoldások](automation-onboard-solutions-from-automation-account.md).

Az Azure Log Analytics-munkaterület és Automation-fiókot, majd válassza ki és **engedélyezése** ahhoz, hogy a megoldás. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![A bevezetni a frissítés felügyeleti megoldás](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Nyissa meg az egyéb megoldások, és válassza ki **engedélyezése**. A Naplóelemzési és automatizálási fiókot legördülő listákból le vannak tiltva, mivel ezek a megoldások a ugyanazon a munkaterületen és Automation-fiókot használja, mint az előzőekben engedélyezett megoldás.

> [!NOTE]
> **Változáskövetés** és **készlet** ugyanahhoz a megoldáshoz használja. Ha ezek a megoldások egyikét engedélyezve van, a másik is engedélyezve van.

## <a name="scope-configuration"></a>Hatókör konfigurációjának

Egyes megoldások a munkaterületen hatókör-konfigurációt használ, amelyekre a számítógépeket, amelyek a megoldás beszerzése. A hatókör beállításait egy vagy több mentett keresések, hogy bizonyos számítógépek hatókörének korlátozása használt csoportja. A hatókör-konfigurációja, az Automation-fiók alatt eléréséhez **kapcsolódó erőforrások**, jelölje be **munkaterület**. A munkaterületen a **MUNKATERÜLET adatforrások**, jelölje be **hatókör konfigurációk**.

Ha a kijelölt munkaterülethez még nem rendelkezik a frissítéskezelés vagy a változáskövetési megoldás, a következő hatókör konfigurációk jönnek létre:

* **MicrosoftDefaultScopeConfig-változáskövetés**

* **MicrosoftDefaultScopeConfig-frissítések**

Ha a kijelölt munkaterülethez már a megoldás, a megoldás nem újratelepíteni, és a hatókör beállításait nem adódik.

Válassza ki a folytatást jelző pontokra (**...** ) a konfigurációkat, és válassza ki bármelyik **szerkesztése**. Az a **Szerkesztés hatókör konfigurációjának** ablaktáblán válassza előbb **válassza ki a számítógépcsoportokat**. A **számítógépcsoportok** ablaktábla megjeleníti azokat a mentett kereséseket hozhatók létre a hatókör beállításait.

## <a name="saved-searches"></a>Mentett keresések

Amikor egy számítógépet adnak a frissítéskezelés, a változások követése vagy a készlet megoldást, a számítógépet adnak a munkaterület két mentett keresés. A mentett keresések olyan lekérdezések, amelyek tartalmazzák a számítógépeket, amelyekre ezek a megoldások van kijelölve.

Lépjen a munkaterülethez. A **általános**, jelölje be **mentett keresések**. Ezek a megoldások által használt két mentett keresések az alábbi táblázatban láthatók:

|Name (Név)     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Változáskövetés       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

Válassza a mentett keresések megtekintése a lekérdezést, amellyel a csoport feltöltése. Az alábbi ábrán a lekérdezés és az eredményeket:

![Mentett keresések](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>További lépések

Továbbra is a megoldások megtudhatja, hogyan használhatja ezeket az oktatóanyagok:

* [Az oktatóanyag - frissítések kezelése a virtuális gép számára](automation-tutorial-update-management.md)
* [Az oktatóanyag - szoftver, a virtuális gép](automation-tutorial-installed-software.md)
* [Az oktatóanyag - hibáinak elhárítása a virtuális gép változások](automation-tutorial-troubleshoot-changes.md)
