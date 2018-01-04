---
title: "Rendszerindítási diagnosztika a Linux virtuális gépek Azure-ban |} Microsoft Doc"
description: "A Linux virtuális gépek Azure-ban két hibakeresési funkcióinak áttekintése"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: delhan
ms.openlocfilehash: 70254d39b5c6326166f7e29fdfc99533835502f9
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
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

- [Kapcsolatos problémák](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)
- [Kernel-problémák](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)
- [FSTAB hibák](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/ )

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Diagnosztika engedélyezése egy új virtuális gépen
1. Amikor új virtuális gépet hoz létre a Betekintő portálon, válassza az **Azure Resource Manager** lehetőséget az üzemi modell legördülő menüből:
 
    ![Resource Manager](./media/boot-diagnostics/screenshot3.jpg)

2. Konfigurálja a Figyelés beállítást, és válassza ki a tárfiókot, ahova el kívánja helyezni ezeket a diagnosztikai fájlokat.
 
    ![Virtuális gép létrehozása](./media/boot-diagnostics/screenshot4.jpg)

3. Ha egy Azure Resource Manager-sablonból végzi a központi telepítést, keresse meg a virtuális gép erőforrást, és fűzze hozzá a diagnosztikai profil szakaszt. Fontos, hogy a „2015-06-15” API-verzió fejlécet használja.

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

## <a name="update-an-existing-virtual-machine"></a>Meglévő virtuális gép frissítése

Ahhoz, hogy a rendszerindítási diagnosztika a portálon keresztül, egy meglévő virtuális gépet a portálon keresztül is frissítheti. Válassza a Rendszerindítási diagnosztika lehetőséget, majd kattintson a Mentés elemre. A módosítás a virtuális gép újraindítása után lép életbe.

![Létező virtuális gép frissítése](./media/boot-diagnostics/screenshot5.png)