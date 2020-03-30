---
title: Rendszerindítási diagnosztika virtuális gépekhez az Azure-ban | Microsoft Dokumentum
description: Az Azure-beli virtuális gépek két hibakeresési szolgáltatásának áttekintése
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: fe2427d008b49daa6222ca981994f0dc2fbea355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476586"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>A rendszerindítási diagnosztika használata a virtuális gépek hibaelhárításához az Azure-ban

Számos oka lehet annak, hogy egy virtuális gép nem indítható állapotba kerül. A Resource Manager központi telepítési modellel létrehozott virtuális gépekkel kapcsolatos problémák megoldásához a következő hibakeresési funkciókat használhatja: Konzolkimenet és Képernyőkép támogatása az Azure virtuális gépekhez. 

Linux os virtuális gépek esetén megtekintheti a konzolnapló kimenetét a portálról. Windows és Linux virtuális gépek esetén az Azure lehetővé teszi, hogy a hipervizorból tekintsen egy képernyőképet a virtuális gépről. Mindkét funkció támogatott az Azure virtuális gépek minden régióban. Megjegyzés: a képernyőképek és a kimenet akár 10 percet is igénybe vehet, hogy megjelenjenek a tárfiókban.

A **rendszerindítási diagnosztika** lehetőséget választhatja a napló és a képernyőkép megtekintéséhez.

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

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Diagnosztika engedélyezése az Azure Portal használatával létrehozott virtuális gépen

Az alábbi eljárás az Erőforrás-kezelő telepítési modelljével létrehozott virtuális gépre szolgál.

A **Kezelés** lap **Figyelés** csoportjában győződjön meg arról, hogy a **rendszerindítási diagnosztika** be van kapcsolva. A **Diagnosztikai tárfiók** legördülő listájában válassza ki azt a tárfiókot, amelybe a diagnosztikai fájlokat el szeretné helyezni.
 
![Virtuális gép létrehozása](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> A rendszerindítási diagnosztikai szolgáltatás nem támogatja a prémium szintű tárfiókot. Ha a prémium szintű tárfiókot használja a rendszerindítási diagnosztikához, előfordulhat, hogy a StorageAccountTypeNotSupported hibaüzenet jelenik meg a virtuális gép indításakor.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Üzembe helyezés Azure Resource Manager-sablonból

Ha egy Azure Resource Manager-sablonból telepít, keresse meg a virtuálisgép-erőforrást, és fűzz hozzá a diagnosztikai profil szakaszhoz. Állítsa az API-verzió fejlécét "2015-06-15" vagy újabb verzióra. A legújabb verzió a "2018-10-01".

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

Az erőforrások sablonok használatával történő üzembe helyezéséről további információt a [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portal használatával című témakörben talál.](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Rendszerindítási diagnosztika engedélyezése meglévő virtuális gépen 

A rendszerindítási diagnosztika engedélyezéséhez egy meglévő virtuális gépen hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)majd válassza ki a virtuális gépet.
2. A **Támogatás + hibaelhárítás** csoportban válassza a **Rendszerindítás diagnosztika**lehetőséget, majd a **Beállítások** lapot.
3. A **Rendszerindítási diagnosztika** beállításaiban módosítsa az állapotot **Be**állásra, és a **Tárfiók** legördülő listából válasszon ki egy tárfiókot. 
4. Mentse a módosításokat.

    ![Létező virtuális gép frissítése](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

A módosítás érvénybe léptetéséhez újra kell indítani a virtuális gépet.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>A rendszerindítási diagnosztika engedélyezése az Azure CLI használatával

Az Azure CLI használatával engedélyezheti a rendszerindítási diagnosztikát egy meglévő Azure virtuális gépen. További információ: [az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
