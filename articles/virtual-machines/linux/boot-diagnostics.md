---
title: Rendszerindítási diagnosztika a Linux virtuális gépek Azure-ban |} Microsoft Doc
description: A Linux virtuális gépek Azure-ban két hibakeresési funkcióinak áttekintése
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 38cc806cb77af60cda10f3aeac2e5ed13b445b8c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>Rendszerindítási diagnosztika használata a Linux virtuális gépek Azure-ban hibaelhárítása

Mostantól két hibakereső szolgáltatás is elérhető az Azure-ban: konzolkimenet és képernyőkép is támogatott az Azure virtuális gépeken a Resource Manager-alapú üzemi modellben. 

Amikor a saját rendszerképét használja az Azure-ban, vagy valamelyik platform rendszerképét indítja, számos oka lehet annak, hogy egy virtuális gép rendszerindításra képtelen állapotba kerül. Ezekkel a szolgáltatásokkal könnyedén diagnosztizálhatja és helyreállíthatja a virtuális gépeket a rendszerindítási hibák után.

Linux virtuális gépek esetén a konzol naplófájljának kimenetét egyszerűen megtekintheti a Portalon:

![Azure Portal](./media/boot-diagnostics/screenshot1.png)
 
Azonban az Azure Windows és Linux virtuális gépeken is lehetővé teszi, hogy megtekintsen egy képernyőképet a virtuális gépről a hipervizortól:

![Hiba](./media/boot-diagnostics/screenshot2.png)

Mindkét szolgáltatás támogatott az Azure virtuális gépeken minden régióban. Ne feledje, akár 10 percet is igénybe vehet, hogy a képernyőképek és a kimenet megjelenjen a tárfiókjában.

## <a name="common-boot-errors"></a>Gyakori rendszerindítási hibák

- [Kapcsolatos problémák](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [Kernel-problémák](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [FSTAB hibák](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Diagnosztika engedélyezése egy új virtuális gépen
1. Ha egy új virtuális gép létrehozása Azure-portálról, válassza ki a **Azure Resource Manager** a központi telepítési modell legördülő listából:
 
    ![Resource Manager](./media/boot-diagnostics/screenshot3.jpg)

2. A **beállítások**, engedélyezze a **rendszerindítási diagnosztika**, majd válassza ki, hogy szeretné-e ezek a diagnosztikai fájlok tárfiók.
 
    ![Virtuális gép létrehozása](./media/boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > A rendszerindítási diagnosztikai funkciót nem támogatja prémium szintű storage-fiók. Rendszerindítási diagnosztika a prémium szintű tárfiókot használja, ha a StorageAccountTypeNotSupported hiba akkor fordulhat elő, amikor elindítja a virtuális Gépet. 
    >
    > 

3. Ha telepíti az Azure Resource Manager sablon alapján, keresse meg a virtuálisgép-erőforrás, és hozzáfűzése a diagnosztikai profil szakasz. Fontos, hogy a „2015-06-15” API-verzió fejlécet használja.

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
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

A minta virtuális gép telepítése a rendszerindítási diagnosztika engedélyezve van, itt a tárházban kivenni.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>A meglévő virtuális gép rendszerindítási diagnosztika engedélyezése 

Ahhoz, hogy a rendszerindítási diagnosztika egy meglévő virtuális gépen, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd válassza ki a virtuális gép.
2. A **támogatási + hibaelhárítási**, jelölje be **rendszerindítási diagnosztika** > **beállítások**, az állapot módosítása **a**, majd Válasszon egy tárfiókot. 
4. Győződjön meg arról, hogy a rendszerindítási diagnosztika beállítás van kiválasztva, és mentse a módosítást.

    ![Létező virtuális gép frissítése](./media/boot-diagnostics/enable-for-existing-vm.png)

3. A módosítás a virtuális gép újraindítása után lép életbe.

## <a name="next-steps"></a>További lépések

Ha a virtuális gép rendszerindítási diagnosztika használatakor a "Nem sikerült lekérni a tartalmat a napló" hibát látja, tekintse meg [nem sikerült lekérni a tartalmat a napló hiba a virtuális gép rendszerindítási diagnosztika](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur).