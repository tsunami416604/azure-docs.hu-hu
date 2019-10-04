---
title: Rendszerindítási diagnosztika az Azure-beli virtuális gépekhez | Microsoft doc
description: Az Azure-beli virtuális gépek két hibakeresési funkciójának áttekintése
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: d34182e09bf453dbec1e9592f131cb9b3b78a086
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086477"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>A rendszerindítási diagnosztika használata az Azure-beli virtuális gépek hibáinak megoldásához

Számos oka lehet annak, hogy egy virtuális gép nem rendszerindító állapotba lép. A Resource Manager-alapú üzemi modellel létrehozott virtuális gépekkel kapcsolatos problémák megoldásához használhatja a következő hibakeresési funkciókat: Konzol kimenete és képernyőkép-támogatás az Azure Virtual Machines szolgáltatáshoz. 

Linux rendszerű virtuális gépek esetén a konzol naplójának kimenetét a portálról tekintheti meg. A Windows és a Linux rendszerű virtuális gépek esetében az Azure lehetővé teszi, hogy megtekintse a virtuális gép képernyőképét a hypervisorból. Mindkét funkció támogatott az Azure Virtual Machines szolgáltatásban az összes régióban. Ne feledje, akár 10 percet is igénybe vehet, hogy a képernyőképek és a kimenet megjelenjen a tárfiókjában.

A **rendszerindítási diagnosztika** lehetőség kiválasztásával megtekintheti a naplót és a képernyőképet.

![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Gyakori rendszerindítási hibák

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Nem található operációs rendszer](https://support.microsoft.com/help/4010142)
- [Rendszerindítási hiba vagy INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Diagnosztika engedélyezése az Azure Portal használatával létrehozott virtuális gépeken

A következő eljárás a Resource Manager-alapú üzemi modell használatával létrehozott virtuális gépekre mutat.

Győződjön meg arról, hogy a **felügyelet lap figyelés** területén be van **kapcsolva a** **rendszerindítási diagnosztika** . A **Diagnostics Storage-fiók** legördülő listából válassza ki azt a Storage-fiókot, amelyben el szeretné helyezni a diagnosztikai fájlokat.
 
![Virtuális gép létrehozása](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> A rendszerindítási diagnosztika funkció nem támogatja a Premium Storage-fiókot. Ha a Premium Storage-fiókot használja a rendszerindítási diagnosztika számára, előfordulhat, hogy a virtuális gép indításakor a StorageAccountTypeNotSupported hibaüzenet jelenik meg.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Üzembe helyezés egy Azure Resource Manager sablonból

Ha Azure Resource Manager-sablonból telepít központilag, navigáljon a virtuális gép erőforrásához, és fűzze hozzá a diagnosztikai profil szakaszhoz. Állítsa az API-verzió fejlécét "2015-06-15" vagy újabb értékre. A legújabb verzió: "2018-10-01".

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

A diagnosztikai profil lehetővé teszi, hogy kiválassza a tárfiókot, ahol el kívánja helyezni ezeket a naplókat.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Az erőforrások sablonok használatával történő telepítésével kapcsolatos további információkért lásd [: gyors útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése a](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)Azure Portal használatával.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Rendszerindítási diagnosztika engedélyezése meglévő virtuális gépen 

A rendszerindítási diagnosztika meglévő virtuális gépen való engedélyezéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd válassza ki a virtuális gépet.
2. A **támogatás + hibaelhárítás** szakaszban válassza a **rendszerindítási diagnosztika**lehetőséget, majd kattintson a **Beállítások** fülre.
3. A **rendszerindítási diagnosztika** beállításaiban módosítsa az állapotot **be értékre, majd**a **Storage-fiók** legördülő listából válassza ki a Storage-fiókot. 
4. Mentse a változást.

    ![Létező virtuális gép frissítése](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

A módosítás érvénybe léptetéséhez újra kell indítania a virtuális gépet.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Rendszerindítási diagnosztika engedélyezése az Azure CLI használatával

Az Azure CLI-vel engedélyezheti a rendszerindítási diagnosztika használatát egy meglévő Azure-beli virtuális gépen. További információ: [az VM boot-Diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
