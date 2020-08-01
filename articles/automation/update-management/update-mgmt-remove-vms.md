---
title: Virtuális gépek eltávolítása a Azure Automation Update Management
description: Ez a cikk azt ismerteti, Hogyan távolítható el a Update Management felügyelt gépek.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450142"
---
# <a name="remove-vms-from-update-management"></a>Virtuális gépek eltávolítása az Update Managementből

Amikor befejezte a frissítések kezelését a környezetben lévő virtuális gépeken, leállíthatja a virtuális gépek kezelését a [Update Management](update-mgmt-overview.md) szolgáltatással.

## <a name="sign-into-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Virtuális gépek eltávolítása

1. Az Automation-fiókban válassza **a frissítés kezelése lehetőséget az** **Update Management**alatt.

2. A következő paranccsal azonosíthatja egy olyan gép UUID-azonosítóját, amelyet el szeretne távolítani a felügyelet alól.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Az **általános**területen lévő log Analytics munkaterületen nyissa meg a hatókör-konfiguráció mentett kereséseit `MicrosoftDefaultScopeConfig-Updates` .

4. A mentett kereséshez `MicrosoftDefaultComputerGroup` kattintson a jobb oldalon található három pontra, majd válassza a **Szerkesztés**lehetőséget.

5. Távolítsa el a virtuális gép UUID-azonosítóját.

6. Ismételje meg a lépéseket minden más virtuális gép eltávolításához.

7. Mentse a mentett keresést a Szerkesztés befejezése után.

>[!NOTE]
>A gépek még a regisztráció törlése után is megjelennek, mert az elmúlt 24 órában mért összes gépet bejelentjük. A gép leválasztása után 24 órát kell várnia, mielőtt azok már nem jelennek meg.

## <a name="next-steps"></a>További lépések

A virtuális gép kezelésének újbóli engedélyezéséhez tekintse meg [a Update Management engedélyezése Azure Portal](update-mgmt-enable-portal.md) vagy a [Update Management engedélyezése Azure-beli virtuális](update-mgmt-enable-vm.md)gépről című témakört.