---
title: Virtuális gépek eltávolítása a Azure Automation Update Management
description: Ez a cikk azt ismerteti, Hogyan távolítható el a virtuális gépek a Update Managementból.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 9bb9ee93be4e045b52355255ecb86e54e48e5c5d
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117417"
---
# <a name="remove-vms-from-update-management"></a>Virtuális gépek eltávolítása az Update Managementből

Amikor befejezte a frissítések telepítését a környezetben lévő virtuális gépekre, eltávolíthatja őket a [Update Management](automation-update-management.md) szolgáltatásból.

1. Az Automation-fiókban válassza **a frissítés kezelése lehetőséget az** **Update Management**alatt.

2. A következő paranccsal azonosíthatja egy olyan virtuális gép UUID-azonosítóját, amelyet el szeretne távolítani a felügyelet alól.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Az **általános**területen lévő log Analytics munkaterületen nyissa meg a hatókör-konfiguráció mentett kereséseit `MicrosoftDefaultScopeConfig-Updates` .

4. A mentett kereséshez `MicrosoftDefaultComputerGroup` kattintson a jobb oldalon található három pontra, majd válassza a **Szerkesztés**lehetőséget. 

5. Távolítsa el a virtuális gép UUID-azonosítóját.

6. Ismételje meg a lépéseket minden más virtuális gép eltávolításához.

7. Mentse a mentett keresést a Szerkesztés befejezése után. 

## <a name="next-steps"></a>További lépések

* A Update Management használatának folytatásához tekintse [meg az Azure-beli virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)című témakört.
* Az általános szolgáltatással kapcsolatos problémák megoldásához tekintse meg a [Update Management problémák elhárítása](troubleshoot/update-management.md)című témakört.
* A Windows Update agenttel kapcsolatos problémákkal kapcsolatban lásd: a [Windows Update Agent problémáinak elhárítása](troubleshoot/update-agent-issues.md).
* A Linux frissítési ügynökkel kapcsolatos problémákkal kapcsolatban lásd: a [Linux frissítési ügynökkel kapcsolatos problémák elhárítása](troubleshoot/update-agent-issues-linux.md).