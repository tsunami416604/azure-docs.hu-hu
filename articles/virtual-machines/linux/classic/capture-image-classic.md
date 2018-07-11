---
title: Linux rendszerű virtuális gép lemezképének |} A Microsoft Docs
description: Ismerje meg, hogyan rögzíthet egy képet a Linux-alapú Azure virtuális gép (VM) a klasszikus üzemi modellel létrehozott.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cynthn
ms.openlocfilehash: ae87af45ffc442c0de6c7f703694a994e536cdb8
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929212"
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Klasszikus linuxos virtuális gép rögzítése lemezképként
> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. [ Ismerje meg, hogyan ](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ez a cikk bemutatja, hogyan a klasszikus Azure futtató virtuális gépet (VM) Linux hozhat létre más virtuális gépeket a lemezkép rögzítése. Ez a rendszerkép tartalmazza az operációsrendszer-lemez és a virtuális Géphez csatolt adatlemezek. Hálózati konfiguráció, úgy kell konfigurálni, hogy a virtuális gép létrehozásakor a lemezképből nem tartalmazza.

Az Azure tárolja a kép alatt **lemezképek**, bármely Ön által feltöltött képek együtt. További információk a rendszerképekről további információkért lásd: [kapcsolatos virtuálisgép-lemezképek az Azure-ban][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Előkészületek
A lépések feltételezik, hogy már létrehozta a klasszikus üzemi modellt használó Azure virtuális gép és az operációs rendszer, beleértve a bármely adatlemezek csatolására konfigurálva. Ha szeretne egy virtuális gép létrehozása, olvasása [létrehozása Linux rendszerű virtuális gép][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>A virtuális gép rögzítése
1. [Csatlakozzon a virtuális Géphez](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) tetszőleges egy SSH-ügyfél használatával.
2. Az SSH ablakban írja be a következő parancsot. A kimenet `waagent` némileg eltérőek lehetnek a segédprogram verziójától függően:

    ```bash
    sudo waagent -deprovision+user
    ```

    A fenti paranccsal próbál meg törölni a rendszer, és adja meg a megfelelő reprovisioning. Ez a művelet a következő feladatokat hajtja végre:

   * SSH-állomáskulcsok eltávolítja (ha Provisioning.RegenerateSshHostKeyPair "y", a konfigurációs fájlban)
   * Az /etc/resolv.conf névkiszolgáló-konfiguráció törlése
   * Eltávolítja a `root` a felhasználó jelszava a/etc/árnyékmásolat (ha Provisioning.DeleteRootPassword "y" a konfigurációs fájlban)
   * A gyorsítótárazott DHCP-ügyfélbérletek eltávolítása
   * A localhost.localdomain állomásnév visszaállítása
   * Törli az utolsó kiépített felhasználói fiókot (/var/lib/waagent szerzett) **és a kapcsolódó adatokat**.

     > [!NOTE]
     > A megszüntetés törli a fájlokat és adatokat a "" a lemezkép általánossá tétele. Csak az alábbi paranccsal egy új sablonként lemezkép rögzítése kívánt virtuális gépen. Nem garantálja, hogy a lemezkép törlődik a bizalmas adatok, vagy alkalmas terjesztési át harmadik félnek.

3. Típus **y** folytatásához. Hozzáadhat a `-force` paraméter a megerősítési lépés elkerülése érdekében.
4. Típus **kilépési** gombra kattintva zárja be az SSH-ügyfél.

   > [!NOTE]
   > A fennmaradó lépések azt feltételezik, hogy már [telepítve van az Azure CLI](../../../cli-install-nodejs.md) az ügyfélszámítógépen. Az alábbi lépéseket is el lehet végezni a [az Azure portal](http://portal.azure.com).

5. Az ügyfélszámítógépről nyissa meg az Azure CLI-vel, és jelentkezzen be az Azure-előfizetésében. További információkért olvassa el a [csatlakozhat az Azure-előfizetés az Azure parancssori felületen](/cli/azure/authenticate-azure-cli).

   > [!NOTE]
   > Az Azure Portalon jelentkezzen be a portálra.

6. Győződjön meg arról, hogy szolgáltatásfelügyelet módban van:

    ```azurecli
    azure config mode asm
    ```

7. Állítsa le a virtuális gép, amely már eltávolította a következő. A következő példa leállítja a virtuális gép neve `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```
   Szükség esetén megtekintheti egy lista használatával az előfizetésben létrehozott virtuális gépek `azure vm list`

   > [!NOTE]
   > Ha használja az Azure Portalon, válassza ki a virtuális Gépet, és kattintson **leállítása** a virtuális gép leállítása.

8. Ha a virtuális gép le van állítva, a lemezkép rögzítése. Az alábbi példa rögzíti a virtuális gép nevű `myVM` , és létrehoz egy általános rendszerkép nevű `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    A `-t` alárendelt parancs törli az eredeti virtuális gépet.

    > [!NOTE]
    > Az Azure Portalon, egy lemezképét rögzítheti kiválasztásával **kép** a központ menüben. Adja meg a kép a következő információkat kell: név, erőforráscsoport, hely, operációs rendszer típusa és storage-blob elérési útja.

9. Az új rendszerképet most már minden olyan új virtuális gép konfigurálásához használható rendszerképek listájának érhető el. Ez a parancs tekintheti meg:

   ```azurecli
   azure vm image list
   ```

   Az a [az Azure portal](http://portal.azure.com), megjelenik az új rendszerképet a **Virtuálisgép-lemezképek (klasszikus)** tartozik, amely a **számítási** szolgáltatások. Elérheti **Virtuálisgép-lemezképek (klasszikus)** kattintva **minden szolgáltatás** tetején található az Azure szolgáltatásra a listában, és majd jövőt láthatja a **számítási** szolgáltatások.   

   ![Rendszerkép-rögzítési sikeres](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>További lépések
A lemezkép készen áll a virtuális gépek létrehozásához használható. Az Azure CLI-parancsot is használhatja `azure vm create` , és adja meg a rendszerkép nevének hozott létre. További információkért lásd: [az Azure CLI használatával a klasszikus üzemi modellel](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

Másik megoldásként használhatja a [az Azure portal](http://portal.azure.com) hozhat létre egy egyéni virtuális Gépet a **kép** metódust, és a létrehozott lemezkép kiválasztása. További információkért lásd: [egy egyéni virtuális gép létrehozása][How to Create a Custom Virtual Machine].

**Lásd még:** [Azure Linux-ügynök használati útmutatója](../../extensions/agent-linux.md)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom-classic.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk-classic.md
[How to Create a Linux Virtual Machine]:create-custom-classic.md
