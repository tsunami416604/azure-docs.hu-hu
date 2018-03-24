---
title: Rendszerindítási diagnosztika a Linux virtuális gépek Azure-ban |} Microsoft Doc
description: A Linux virtuális gépek Azure-ban két hibakeresési funkcióinak áttekintése
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: timlt
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
ms.openlocfilehash: bf8e1b338012898ed3de3f443cf492b6890af796
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
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

## <a name="next-steps"></a>További lépések

Ha a virtuális gép rendszerindítási diagnosztika használatakor a "Nem sikerült lekérni a tartalmat a napló" hibát látja, tekintse meg [nem sikerült lekérni a tartalmat a napló hiba a virtuális gép rendszerindítási diagnosztika](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur).