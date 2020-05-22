---
title: Virtuális gépek eltávolítása Azure Automation Change Tracking és leltárból
description: Ez a cikk azt ismerteti, Hogyan távolítható el a virtuális gépek a Change Trackingról és a leltárból.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 1bd94beaa40f6ff793b50e261138cc31453f7016
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749164"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Virtuális gépek eltávolítása Change Tracking és leltárból

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

Amikor befejezte a környezetben lévő virtuális gépek változásainak telepítését, eltávolíthatja őket a [change Tracking és a leltár](change-tracking.md) szolgáltatásból.

1. Az Automation-fiókban válassza a **változások követése** vagy **leltározás** elemet a **konfiguráció**felügyelete alatt.

2. A következő paranccsal azonosíthatja egy olyan virtuális gép UUID-azonosítóját, amelyet el szeretne távolítani a felügyelet alól.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Az **általános**log Analytics munkaterületen nyissa meg a mentett kereséseket.

4. A mentett kereséshez `MicrosoftDefaultComputerGroup` kattintson a jobb oldalon található három pontra, majd válassza a **Szerkesztés**lehetőséget. 

5. Távolítsa el a virtuális gép UUID-azonosítóját.

6. Ismételje meg a lépéseket minden más virtuális gép eltávolításához.

7. Mentse a mentett keresést a Szerkesztés befejezése után. 

## <a name="next-steps"></a>További lépések

* [A Change Tracking és a leltár áttekintése](change-tracking.md)
* [Change Tracking és leltár kezelése](change-tracking-file-contents.md)
* [Munkaterület leválasztása az Automation-fiókból a Change Tracking és a leltárhoz](automation-unlink-workspace-change-tracking.md)
* [Change Tracking és leltár engedélyezése Automation-fiókból](automation-enable-changes-from-auto-acct.md)
* [Change Tracking és leltár engedélyezése a Azure Portal](automation-enable-changes-from-browse.md)
* [Change Tracking és leltár engedélyezése runbook](automation-enable-changes-from-runbook.md)
* [Change Tracking és leltár engedélyezése Azure-beli virtuális gépről](automation-enable-changes-from-vm.md)
* [Azure-beli virtuális gépek változásainak hibáinak megoldása](automation-tutorial-troubleshoot-changes.md)
* [A Change Tracking és a leltárral kapcsolatos problémák elhárítása](troubleshoot/change-tracking.md)