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
ms.openlocfilehash: 9341458336e4c95b84590eadbc86073e7dbf09a0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419554"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>A rendszerindítási diagnosztika használata az Azure-beli virtuális gépek hibaelhárítása

Támogatása két hibakereső szolgáltatás már elérhető az Azure-ban: konzolkimenet és Képernyőkép az Azure virtual machines Resource Manager üzemi modell támogatása. 

Ha a saját rendszerképét az Azure-ban, vagy valamelyik platform rendszerképét indítja, számos oka lehet, miért lekérdezi a virtuális gép rendszerindításra képtelen állapotba lehet. Ezeket a funkciókat, amelyekkel könnyedén diagnosztizálhatja és helyreállíthatja a virtuális gépeket rendszerindítási hibák engedélyezése.

Linux rendszerű virtuális gépek a portálról a konzol naplófájljának kimenetét egyszerűen megtekintheti. Windows- és Linux virtuális gépek Azure is lehetővé teszi, hogy megtekintsen egy képernyőképet a virtuális gépről a hipervizortól. Mindkét szolgáltatás támogatott az Azure-beli virtuális gépek minden régióban. Ne feledje, akár 10 percet is igénybe vehet, hogy a képernyőképek és a kimenet megjelenjen a tárfiókjában.

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

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Diagnosztika engedélyezése egy új virtuális gépen
1. Amikor egy új virtuális gép létrehozása az Azure Portalon, válassza ki a **Azure Resource Manager** az üzembe helyezési modell legördülő listából:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. A **beállítások**, engedélyezze a **rendszerindítási diagnosztika**, majd válassza ki, hogy szeretné-e helyezni ezeket a diagnosztikai fájlokat a storage-fiók.
 
    ![Virtuális gép létrehozása](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > A rendszerindítási diagnosztikai funkciót nem támogatja a premium storage-fiók. A rendszerindítási diagnosztika a premium storage-fiókot használja, ha a StorageAccountTypeNotSupported hibaüzenetet kaphat, ha elindítja a virtuális Gépet.
    >
    > 

3. Helyez üzembe az Azure Resource Manager-sablon, keresse meg a virtuális gép erőforrást, és fűzze hozzá a diagnosztikai profil szakaszt. Fontos, hogy a „2015-06-15” API-verzió fejlécet használja.

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. A diagnosztikai profil lehetővé teszi, hogy kiválassza a tárfiókot, ahol el kívánja helyezni ezeket a naplókat.

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

Minta virtuális gép üzembe helyezése a rendszerindítási diagnosztika engedélyezve van, tekintse meg a tárházunkat.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>A meglévő virtuális gép rendszerindítási diagnosztika engedélyezése 

Ahhoz, hogy a rendszerindítási diagnosztikát a virtuális gép, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd válassza ki a virtuális gépet.
2. A **támogatás + hibaelhárítás**válassza **rendszerindítási diagnosztika** > **beállítások**, állítsa a állapotát **a**, majd Válasszon egy tárfiókot. 
4. Győződjön meg arról, hogy a rendszerindítási diagnosztikai beállítás van kiválasztva, és mentse a módosítást.

    ![Létező virtuális gép frissítése](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. A módosítás a virtuális gép újraindítása után lép életbe.


