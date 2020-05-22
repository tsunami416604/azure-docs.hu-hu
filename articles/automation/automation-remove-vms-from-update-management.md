---
title: Virtuális gépek eltávolítása a Azure Automation Update Management
description: Ez a cikk azt ismerteti, Hogyan távolítható el a virtuális gépek a Update Managementból.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 29d6edd5463de78bba039e4ed6219575d924ac10
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749143"
---
# <a name="remove-vms-from-update-management"></a>Virtuális gépek eltávolítása a Update Managementból

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

Amikor befejezte a frissítések telepítését a környezetben lévő virtuális gépekre, eltávolíthatja őket a [Update Management](automation-update-management.md) szolgáltatásból.

1. Az Automation-fiókban válassza **a frissítés kezelése lehetőséget az** **Update Management**alatt.

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

* [Azure-beli virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)
* [Munkaterület leválasztása az Automation-fiókból Update Management](automation-unlink-workspace-update-management.md)
* [Update Management engedélyezése Automation-fiókból](automation-onboard-solutions-from-automation-account.md)
* [Update Management engedélyezése a Azure Portal](automation-onboard-solutions-from-browse.md)
* [Update Management engedélyezése runbook](automation-onboard-solutions.md)
* [Update Management engedélyezése Azure-beli virtuális gépről](automation-onboard-solutions-from-vm.md)
* [Update Management problémák elhárítása](troubleshoot/update-management.md)
* [A Windows Update Agent problémáinak elhárítása](troubleshoot/update-agent-issues.md)
* [A Linux frissítési ügynökkel kapcsolatos problémák elhárítása](troubleshoot/update-agent-issues-linux.md)
