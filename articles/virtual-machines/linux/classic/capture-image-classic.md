---
title: "Rögzítsen egy rendszerképet a Linux virtuális gépek |} Microsoft Docs"
description: "Útmutató a Linux-alapú Azure virtuális gépek (VM) a klasszikus üzembe helyezési modellel létrehozott lemezkép rögzítése."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
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
ms.author: iainfou
ms.openlocfilehash: 411eba925958a67a40e044a9ee0eb9cd56cd04db
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Klasszikus linuxos virtuális gép rögzítése lemezképként
> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. [ Ismerje meg, hogyan ](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ez a cikk bemutatja, hogyan a klasszikus Azure virtuális gép (VM) Linux futtató más virtuális gépek létrehozása lemezkép rögzítése. Ez a rendszerkép tartalmazza az operációsrendszer-lemez és adatlemezek a virtuális Géphez csatlakozik. Nem tartoznak bele, olyan hálózati beállításokat úgy kell konfigurálni, hogy a virtuális gép létrehozásakor a lemezképből.

Azure tárolja a **képek**, feltöltött képeket együtt. Lemezképek kapcsolatos további információkért lásd: [kapcsolatos virtuálisgép-lemezképeket az Azure-ban][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Előkészületek
Ezek a lépések feltételezik, hogy korábban már létrehozott egy Azure virtuális Gépen, klasszikus telepítési modellel és konfigurálva az operációs rendszer, beleértve a bármely adatlemezt csatolni. Ha szeretne virtuális gép létrehozása, olvasása [létrehozása Linux virtuális gépek][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>A virtuális gép rögzítése
1. [Csatlakoztassa a virtuális Gépet](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) egy SSH-ügyfél az Ön által választott használatával.
2. Az SSH ablakban írja be a következő parancsot. A kimenet `waagent` függően eltérőek lehetnek attól függően, hogy ezt a segédprogramot verziója:

    ```bash
    sudo waagent -deprovision+user
    ```

    A fenti paranccsal próbál tisztítsa meg a rendszer, és lehetővé teszi a megfelelő reprovisioning. Ez a művelet a következő feladatokat hajtja végre:

   * SSH-állomások kulcsait eltávolítja (ha Provisioning.RegenerateSshHostKeyPair "y", a konfigurációs fájlban)
   * Törli a /etc/resolv.conf névkiszolgáló-konfiguráció
   * Eltávolítja a `root` a felhasználó jelszava a/etc/árnyékmásolat (ha Provisioning.DeleteRootPassword "y" konfigurációs fájlban)
   * A gyorsítótárazott DHCP-ügyfélbérletek eltávolítása
   * A localhost.localdomain állomásnév visszaállítása
   * Törli a legutóbbi kiépített felhasználói fiókot (/var/lib/waagent nyert) **és az adatok**.

     > [!NOTE]
     > Megszüntetés törli a fájlokat és adatokat a lemezkép "általánosítja". Ez a parancs csak egy virtuális Gépet, amelyet lemezképfájlforrásként kíván új sablonként lemezkép rögzítése futtatható. Nem biztos, hogy a lemezkép minden a bizalmas adatok megszűnik, vagy alkalmas terjesztési át harmadik félnek.

3. Típus **y** folytatja. Hozzáadhat a `-force` paraméter a megerősítési lépés elkerülése érdekében.
4. Típus **kilépési** az SSH-ügyfél bezárásához.

   > [!NOTE]
   > A fennmaradó lépések azt feltételezik, hogy már rendelkezik [az Azure parancssori felület telepítve](../../../cli-install-nodejs.md) az ügyfélszámítógépen. Az alábbi lépéseket is teheti a [Azure-portálon](http://portal.azure.com).

5. Az ügyfélszámítógépen nyissa meg az Azure parancssori felület, és jelentkezzen be az Azure-előfizetéshez. További információkért olvassa el a [csatlakozás Azure-előfizetéshez az Azure parancssori felületen](/cli/azure/authenticate-azure-cli).

   > [!NOTE]
   > Az Azure portálon jelentkezzen be a portálra.

6. Ellenőrizze, hogy a szolgáltatás felügyeleti mód:

    ```azurecli
    azure config mode asm
    ```

7. Állítsa le a virtuális Gépet, amely már platformelőfizetés. Az alábbi példa nevű virtuális gép leáll `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```
   Ha szükséges, megtekintheti az előfizetésében használatával létrehozott virtuális gépek listája`azure vm list`

   > [!NOTE]
   > Az Azure portál használata, jelölje ki a virtuális Gépet, és kattintson **leállítása** a virtuális gép leállítása.

8. Ha a virtuális gép le van állítva, a lemezképének rögzítése. Az alábbi példa rögzíti a virtuális gép nevű `myVM` , és létrehoz egy általánosított nevű lemezkép `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    A `-t` alparancs törli az eredeti virtuális gépet.

    > [!NOTE]
    > Az Azure-portálon rögzítheti képként kiválasztásával **kép** a központ menüben. Kell megadnia a kép a következő információkat: neve, a erőforráscsoport, a helye, a operációs rendszer típusa és a tárolási blob elérési útja.

9. Az új lemezképet a listában minden új virtuális gép konfigurálásához használható képek most érhető el. Ez a parancs tekintheti meg:

   ```azurecli
   azure vm image list
   ```

   Az a [Azure-portálon](http://portal.azure.com), megjelenik az új lemezképet a **Virtuálisgép-rendszerképek (klasszikus)** , amely a **számítási** szolgáltatások. Van-e hozzáférési **Virtuálisgép-rendszerképek (klasszikus)** kattintva _további szolgáltatások_ alján található az Azure szolgáltatás a lista, majd a **számítási** szolgáltatások.   

   ![Lemezkép-rögzítési sikeres](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>További lépések
A lemezkép készen áll a virtuális gépek létrehozásához használható. Az Azure CLI paranccsal `azure vm create` és adja meg a létrehozott lemezkép nevét. További információkért lásd: [klasszikus telepítési modellt az Azure parancssori felület használatával](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

Másik megoldásként használhatja a [Azure-portálon](http://portal.azure.com) használatával egyéni virtuális gép létrehozása a **kép** metódus és a létrehozott lemezkép kiválasztása. További információkért lásd: [egyéni virtuális gép létrehozása][How to Create a Custom Virtual Machine].

**További információ:** [Azure Linux ügynök felhasználói útmutatója](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk.md
[How to Create a Linux Virtual Machine]:create-custom.md
