---
title: Virtuális gépek eltávolítása a Azure Automation Update Management
description: Ez a cikk azt ismerteti, Hogyan távolítható el a Update Management felügyelt gépek.
services: automation
ms.topic: conceptual
ms.date: 09/09/2020
ms.custom: mvc
ms.openlocfilehash: 66631adbb56a98431e70f956f3e860b16e8f7ea2
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648644"
---
# <a name="remove-vms-from-update-management"></a>Virtuális gépek eltávolítása az Update Managementből

Amikor befejezte a frissítések kezelését a környezetben lévő virtuális gépeken, leállíthatja a virtuális gépek kezelését a [Update Management](update-mgmt-overview.md) szolgáltatással. A kezelésének leállításához szerkessze a mentett keresési lekérdezést a `MicrosoftDefaultComputerGroup` log Analytics munkaterületen, amely az Automation-fiókjához van csatolva.

## <a name="sign-into-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Virtuális gépek eltávolítása

1. A Azure Portal a Azure Portal felső navigációs sávján indítsa el az **Cloud Shell** . Ha nem ismeri a Azure Cloud Shellt, tekintse meg [a Azure Cloud Shell áttekintése](../../cloud-shell/overview.md)című témakört.

2. A következő paranccsal azonosíthatja egy olyan gép UUID-azonosítóját, amelyet el szeretne távolítani a felügyelet alól.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. A Azure Portal navigáljon **log Analytics munkaterületek**elemre. Válassza ki a munkaterületet a listából.

4. A Log Analytics munkaterületen válassza a **naplók** lehetőséget, majd a felső műveletek menüben válassza a **lekérdezési tallózó** elemet.

5. A jobb oldali ablaktáblán található **query Explorerben** bontsa ki a **mentett Queries\Updates** elemet, és válassza ki a mentett keresési lekérdezést a `MicrosoftDefaultComputerGroup` szerkesztéshez.

6. A lekérdezés-szerkesztőben tekintse át a lekérdezést, és keresse meg a virtuális gép UUID azonosítóját. Távolítsa el a virtuális gép UUID-azonosítóját, és ismételje meg az összes eltávolítani kívánt virtuális gép lépéseit.

7. Mentse a mentett keresést a Szerkesztés befejezése után a felső sávon található **Mentés** lehetőség kiválasztásával.

>[!NOTE]
>A gépek még a regisztráció törlése után is megjelennek, mert az elmúlt 24 órában mért összes gépet bejelentjük. A gép eltávolítása után 24 órát kell várnia, mielőtt azok már nem jelennek meg.

## <a name="next-steps"></a>Következő lépések

A virtuális gép kezelésének újbóli engedélyezéséhez tekintse meg [a Update Management engedélyezése Azure Portal](update-mgmt-enable-portal.md) vagy a [Update Management engedélyezése Azure-beli virtuális](update-mgmt-enable-vm.md)gépről című témakört.