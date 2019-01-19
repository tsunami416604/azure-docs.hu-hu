---
title: Rendszerindítási diagnosztika az Azure-beli virtuális gépek |} A Microsoft Doc
description: Az Azure-beli virtuális gépek két hibakereső szolgáltatás áttekintése
services: virtual-machines
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 55feef336ade461d4e3936d2f76122e5b4b4c00e
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2019
ms.locfileid: "54401299"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>A rendszerindítási diagnosztika használata az Azure-beli virtuális gépek hibaelhárítása

Lehet, hogy egy virtuális gép nem indítható állapotba kerül, számos oka lehet. A virtuális gépek létrehozása a Resource Manager-alapú üzemi modellel is használhatja a következő hibakeresési funkciók a problémák elhárításához: Konzolkimenet és képernyőkép is támogatott Azure-beli virtuális gépek. 

Linux rendszerű virtuális gépek megtekintheti a portálon a konzol naplófájljának kimenetét. Windows- és Linux virtuális gépek esetében az Azure lehetővé teszi, hogy megtekintsen egy képernyőképet a virtuális gépről a hipervizortól. Mindkét funkció minden régióban az Azure virtuális gépek támogatottak. Ne feledje, akár 10 percet is igénybe vehet, hogy a képernyőképek és a kimenet megjelenjen a tárfiókjában.

Kiválaszthatja a **rendszerindítási diagnosztika** lehetőség segítségével megtekintheti a napló és a képernyőkép.

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

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Engedélyezze a diagnosztikát az Azure Portal használatával létrehozott virtuális gépen

Az alábbi eljárást a Resource Manager-alapú üzemi modellel létrehozott virtuális gép van.

Az a **felügyeleti** lap **figyelés** területén ellenőrizze, hogy **rendszerindítási diagnosztika** be van kapcsolva. Az a **diagnosztikai tárfiók** legördülő listára, válassza ki a storage-fiók, amelyben a diagnosztikai fájlok.
 
![Virtuális gép létrehozása](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> A rendszerindítási diagnosztikai funkciót nem támogatja a premium storage-fiók. A rendszerindítási diagnosztika a premium storage-fiókot használja, ha a StorageAccountTypeNotSupported hibaüzenetet kaphat, ha elindítja a virtuális Gépet.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon üzembe helyezése

Helyez üzembe az Azure Resource Manager-sablon, keresse meg a virtuális gép erőforrást, és fűzze hozzá a diagnosztikai profil szakaszt. Állítsa be az API-verzió fejlécet a "2015-06-15" vagy újabb verzióra. A legújabb verziója "2018-10-01".

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

Erőforrások, sablonok használatával történő telepítéséről további információkért lásd: [a rövid útmutató: Létrehozása és üzembe helyezése Azure Resource Manager-sablonok az Azure portal használatával](/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>A meglévő virtuális gép rendszerindítási diagnosztika engedélyezése 

Ahhoz, hogy a rendszerindítási diagnosztikát a virtuális gép, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd válassza ki a virtuális gépet.
2. Az a **támogatás + hibaelhárítás** szakaszban jelölje be **rendszerindítási diagnosztika**, majd válassza ki a **beállítások** fülre.
3. A **rendszerindítási diagnosztika** beállításait, állítsa a állapotát **a**, és a **tárfiók** legördülő listában válassza ki a tárfiókot. 
4. A módosítás mentéséhez.

    ![Létező virtuális gép frissítése](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

A virtuális gépet, a módosítás érvénybe léptetéséhez újra kell indítani.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Az Azure CLI-vel, a rendszerindítási diagnosztika engedélyezése

Az Azure CLI használatával az Azure virtuális gép rendszerindítási diagnosztika engedélyezése. További információkért lásd: [az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
