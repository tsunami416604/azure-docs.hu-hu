---
title: "Lemez csatolása a Linux virtuális gépek Azure-ban |} Microsoft Docs"
description: "Megtudhatja, hogyan adatlemezt csatolni egy Linux virtuális gép a klasszikus telepítési modellt, és végezze el a lemez inicializálását, hogy készen álljon a használatra"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4901384d-2a6f-4f46-bba0-337a348b7f87
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 017ba7197e11c2b222082833d5acabb9e542b762
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Hogyan lehet adatlemezt csatolni egy Linux virtuális gép
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Lásd: hogyan [erőforrás-kezelő telepítési modellel adatlemezzel](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Csatolhat is üres és a lemezek, amelyek tartalmazzák az Azure virtuális gépek adatokat. Mindkét típusú lemezek a .vhd fájlok találhatók az Azure storage-fiók. Mint az olyan lemezek hozzáadása a Linux rendszerű számítógép, a lemez csatlakoztatása után kell inicializálni és formázni azt, hogy készen álljon a használatra. Ez a cikk adatokat is üres és a lemezek már tartalmazó adatokat a virtuális gépeket, valamint majd inicializálni és formázni egy új lemezt csatolni.

> [!NOTE]
> Akkor célszerű egy vagy több különálló lemezek használatával egy virtuális gép adatainak tárolásához. Egy Azure virtuális gép létrehozásakor rendelkezik az operációs rendszer és egy ideiglenes lemezzel. **Ne használja az ideiglenes lemez állandó adatok tárolására.** Foglalja, csak az átmeneti tárolási biztosít. Kínál nincs redundancia vagy a biztonsági mentést, mert az nem található az Azure storage.
> Az ideiglenes lemez általában az Azure Linux ügynök által felügyelt, és automatikusan csatlakoztatva **/mnt és az erőforrások** (vagy **/mnt** Ubuntu képek). Másrészről, adatlemezt neve lehet a Linux kernel hasonlót `/dev/sdc`, és meg kell partíció formázása és csatlakoztatnia ehhez az erőforráshoz. Tekintse meg a [Azure Linux ügynök felhasználói útmutató] [ Agent] részleteiről.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>A Linux új adatlemezt inicializálása
1. SSH-kapcsolatot a virtuális Gépet. További információkért lásd: [Linuxot futtató virtuális gép bejelentkezés][Logon].
2. A következő meg kell találnia az adatok lemez inicializálása eszközazonosító. Ehhez két módja van:
   
    a) Grep SCSI-eszközök a naplófájlokban, például a következő parancsot:
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    A legutóbbi Ubuntu azokat a terjesztéseket, akkor előfordulhat, hogy kell használnia `sudo grep SCSI /var/log/syslog` mert naplózásának `/var/log/messages` alapértelmezés szerint le lehetnek tiltva.
   
    A utolsó megjelenő üzeneteket hozzáadott adatlemez azonosítóját található.
   
    ![A lemez üzeneteket](./media/attach-disk/scsidisklog.png)
   
    VAGY
   
    b) használja a `lsscsi` parancs az eszközazonosítót megállapítása. `lsscsi` telepítheti vagy `yum install lsscsi` (Red Hat a disztribúció alapján) vagy `apt-get install lsscsi` (a Debian disztribúció alapján). A lemez által keresett megtalálhatja a *lun* vagy **logikaiegység-szám**. Például a *lun* a csatolt a lemezek is könnyen látható `azure vm disk list <virtual-machine>` mint:

    ```azurecli
    azure vm disk list myVM
    ```

    A kimenet a következő példához hasonló:

    ```azurecli
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                         OS
    data:    ---  --------  --------------------------------  -----
    data:         30        myVM-2645b8030676c8f8.vhd  Linux
    data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
    info:    vm disk list command OK
    ```
   
    Hasonlítsa össze ezeket az adatokat a kimenetét `lsscsi` az azonos virtuális gép mintavételhez:
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    Az utolsó helyen található minden egyes sorban levő rekord a *lun*. Lásd: `man lsscsi` további információt.
3. A parancssorba írja be az eszköz létrehozásához a következő parancsot:
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. Amikor a rendszer kéri, írja be a  **n**  partíció létrehozásához.

    ![Eszköz létrehozása](./media/attach-disk/fdisknewpartition.png)

5. Amikor a rendszer kéri, írja be a **p** legyen a partíció az elsődleges partíció számára. Típus **1** abba, hogy az első partíció, és adja meg az alapértelmezett érték a palack fogadásához, majd írja be. Egyes rendszerek megmutatja az első és utolsó szektorok a palack alapértelmezett értékeit. Ha szeretné, fogadja el ezeket az alapértelmezett értékeket.

    ![A partíció](./media/attach-disk/fdisknewpartdetails.png)


6. Típus **p** kapcsolatos a lemez, amely alatt a részletek megtekintéséhez.

    ![Lista fizikai lemezeinek adatai](./media/attach-disk/fdiskpartitiondetails.png)


7. Típus **w** a beállításokat a lemezre írni.

    ![A lemezen változások írása](./media/attach-disk/fdiskwritedisk.png)

8. A fájlrendszer most már az új partícióra hozhat létre. Az eszköz azonosítója a partíciószám hozzáfűzése (a következő példa `/dev/sdc1`). A következő példa a /dev/sdc1 ext4 partíciót hoz létre:
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![-Fájl létrehozása](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > SuSE Linux Enterprise 11 rendszerek csak ext4 fájlrendszerekhez támogatja a csak olvasási hozzáféréssel. Ezek a rendszerek javasoljuk, hogy az új fájlrendszer formátuma ext4 helyett ext3.

9. Ellenőrizze azt, hogy az új fájlrendszer, csatlakoztassa az alábbiak szerint:
   
    ```bash
    sudo mkdir /datadrive
    ```

10. Végül akkor is csatlakoztathatja a meghajtót, az alábbiak szerint:
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    Az adatlemez készen áll a használják **/datadrive**.
   
    ![A következő könyvtár létrehozásakor, és csatlakoztassa a lemezt](./media/attach-disk/mkdirandmount.png)

11. Vegye fel az új meghajtó /etc/fstab:
   
    Annak érdekében, hogy a meghajtó a rendszer újraindítása után automatikusan csatlakoztatni, hozzá kell adni a/etc/fstab fájlba. Ajánlott továbbá szolgál, hogy a UUID (univerzálisan egyedi azonosítója) /etc/fstab utal, csak az eszköz nevét (pl. /dev/sdc1) helyett. A UUID használatával elkerülhető az éppen csatlakoztatva egy adott helyre, ha az operációs rendszer rendszerindítási és minden fennmaradó adatlemezt, majd hozzárendeli ezeket eszközazonosítók során észleli a lemezhiba helytelen lemezkonfigurációt. Az új meghajtó UUID megkereséséhez használja a **blkid** segédprogram:
   
    ```bash
    sudo -i blkid
    ```
   
    A kimeneti az alábbihoz hasonlít:
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > Nem megfelelő módosítása a **/etc/fstab** fájl meghiúsulását eredményezheti. Ha nem ismeri, tekintse meg a telepítési dokumentációban talál információkat arról a fájl megfelelően szerkesztése. Ajánlott továbbá, hogy létrejött-e a fájl biztonsági másolata /etc/fstab szerkesztése előtt.

    Ezután nyissa meg a **/etc/fstab** fájlt egy szövegszerkesztőben:

    ```bash
    sudo vi /etc/fstab
    ```

    A jelen példában használjuk a UUID az új **/dev/sdc1** eszköz, amely az előző lépést, és a csatlakoztatási pont jött létre **/datadrive**. Adja hozzá az alábbi végének a **/etc/fstab** fájlt:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    Vagy a SuSE Linux-alapú rendszereken esetleg némileg eltérő formátumának a használatára:

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > A `nofail` lehetőség biztosítja, hogy a virtuális gép elindul, még akkor is, ha a fájlrendszer sérült, vagy a lemez nem létezik a következő rendszerindításkor. Ez a beállítás nélkül felmerülhet viselkedés leírtak [nem SSH Linux virtuális gép között az FSTAB hibák miatt](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Most tesztelheti, hogy a fájlrendszer csatlakoztatva van-e megfelelően leválasztás és a fájlrendszerben majd tudják, azaz a példánál csatlakoztatási pontot `/datadrive` a korábbi lépésekben létrehozott:

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    Ha a `mount` hibát hoz létre, jelölje be a helyes szintaxis/etc/fstab fájlt. Ha további meghajtók és partíciók jönnek létre, adja meg azokat az/etc/fstab külön-külön is.

    A meghajtó írható ellenőrizze a parancs segítségével:

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > Ezt követően eltávolítása adatlemezt fstab szerkesztése nélkül, hogy a virtuális gépek a rendszerindítás. Ha ez gyakran előfordul, a legtöbb terjesztéseket biztosítanak, vagy a `nofail` és/vagy `nobootwait` fstab beállítások, amelyek lehetővé teszik a rendszert még akkor is, ha a lemez nem tudja csatlakoztatni rendszerindító idő. A terjesztési dokumentációjában talál további információt ezekről a paraméterekről.

### <a name="trimunmap-support-for-linux-in-azure"></a>Az Azure Linux vágást/UNMAP támogatása
Egyes Linux kernelek támogatja a vágás/UNMAP műveleteket elveti a nem használt blokkok a lemezen. Ezek a műveletek elsősorban hasznosak standard tárolási tájékoztatja Azure törölt lapok már nem érvényesek, és is elvesznek. Lapok elvetése költség mentheti, ha nagy fájlok létrehozása, majd törli őket.

Két módon vágást engedélyezése támogatja a Linux virtuális gép van. A szokásos módon olvassa el a terjesztési az ajánlott módszer:

* Használja a `discard` lehetőség a csatlakoztatási `/etc/fstab`, például:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* Bizonyos esetekben a `discard` lehetőség is van a teljesítményre. Alternatív megoldásként futtathatja a `fstrim` manuálisan parancsot a parancssorból, vagy adja hozzá a crontab rendszeresen futtatásához:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL vagy CentOS**
  
    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [virtual-machines-linux-lunzero](../../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Következő lépések
További információk a Linux virtuális gép használata a következő cikkekben:

* [Bejelentkezés a Linux rendszerű virtuális gép][Logon]
* [Útmutató a lemezt leválasztani a Linux virtuális gépek](detach-disk.md)
* [A klasszikus telepítési modellt az Azure parancssori felület használatával](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [A Linux virtuális gép RAID konfigurálása az Azure-ban](../configure-raid.md)
* [A Linux virtuális gép az Azure-ban LVM konfigurálása](../configure-lvm.md)

<!--Link references-->
[Agent]:../agent-user-guide.md
[Logon]:../mac-create-ssh-keys.md
