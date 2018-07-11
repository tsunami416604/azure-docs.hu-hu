---
title: Lemez csatolása az Azure-beli Linuxos virtuális gép |} A Microsoft Docs
description: Inicializálja a lemezt, hogy használatra kész legyen, és megtudhatja, hogyan csatlakoztathat adatlemezt egy Linux rendszerű virtuális gépre a klasszikus üzemi modell használatával
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 4901384d-2a6f-4f46-bba0-337a348b7f87
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: b5bb3a9353f83cb569988a068f3ca02da85f739c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929151"
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Adatlemez csatlakoztatása Linux rendszerű virtuális gép
> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Lásd: hogyan [adatlemezt a Resource Manager üzemi modell használatával](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Üres lemez és a lemezek, amelyek tartalmazzák az Azure-beli virtuális gépek is csatlakoztatható. Mindkét típusú lemezek az Azure storage-fiókban található .vhd fájlok. Minden lemez hozzáadása Linux rendszerű gép, a lemez csatlakoztatása után meg kell inicializálja és formázza azt, hogy használatra kész legyen. Ez a cikk részletesen is üres és a virtuális gépek, valamint majd inicializálása, és a egy új lemez formázása adatokat már tartalmazó lemezek csatolása.

> [!NOTE]
> Egy virtuális gép adatainak tárolásához egy vagy több különálló lemezek használata ajánlott. Amikor létrehoz egy Azure virtuális gépen, tartalmaz operációsrendszer-lemez és a egy ideiglenes lemezzel. **Ne használja az ideiglenes lemez a perzisztens adatok tárolására.** Ahogy a neve is mutatja, csak az ideiglenes tároló biztosít. Nincs redundancia vagy a biztonsági mentést biztosít, mivel azt az Azure storage nem található.
> Az ideiglenes lemez általában az Azure Linux-ügynök által felügyelt, és automatikusan csatlakoztatva **/mnt/erőforrás** (vagy **/mnt** az Ubuntu-rendszerképek). Másrészről, adatlemez előfordulhat, hogy lehet a Linux kernel hasonló nevű `/dev/sdc`, és meg kell particionálása, formázhatja és csatlakoztathatja az erőforrás. Tekintse meg a [Azure Linux-ügynök használati útmutatója] [ Agent] részleteiről.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>A Linux új adatlemez inicializálása
1. A virtuális gép ssh-KAPCSOLATOT. További információkért lásd: [bejelentkezés egy Linux rendszerű virtuális gép][Logon].
2. Ezután meg kell keresnie az eszköz azonosítója az adatlemez inicializálása. Ehhez két módja van:
   
    a) a Grep SCSI-eszközök a naplókban, például a következő parancsot:
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    Legutóbbi Ubuntu disztribúciók, előfordulhat, hogy kell használnia `sudo grep SCSI /var/log/syslog` mert naplózását `/var/log/messages` alapértelmezés szerint le lehetnek tiltva.
   
    Megtalálhatja az utolsó adatlemez megjelenített üzenetek hozzáadott azonosítóját.
   
    ![A lemez üzenetek beolvasása](./media/attach-disk/scsidisklog.png)
   
    VAGY
   
    (b) használata a `lsscsi` parancsot ismerje meg, hogy az eszköz azonosítója. `lsscsi` által telepíthető `yum install lsscsi` (Red hat-alapú disztribúciókon) vagy `apt-get install lsscsi` (debian-alapú disztribúciókon). A lemez által keresett annak a *lun* vagy **logikaiegység-szám**. Például a *lun* esetében a csatolt lemezek könnyen megfigyelhető a `azure vm disk list <virtual-machine>` mint:

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
   
    Hasonlítsa össze ezeket az adatokat a kimenetét `lsscsi` azonos minta virtuális gép számára:
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    A minden sorban a rekord utolsó száma a *lun*. Lásd: `man lsscsi` további információt.
3. A parancssorba írja be az eszköz létrehozásához a következő parancsot:
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. Amikor a rendszer kéri, írja be a **n** partíció létrehozásához.

    ![Eszköz létrehozása](./media/attach-disk/fdisknewpartition.png)

5. Amikor a rendszer kéri, írja be a **p** , hogy a partíció az elsődleges partíció. Típus **1** legyen az első partíció, és adja meg a 3D henger az alapértelmezett érték elfogadásához, majd írja be. Egyes rendszerek azt jeleníti meg az első és utolsó szektorok a 3D henger alapértelmezett értékeit. Választhat, alapértelmezett beállítások elfogadásához.

    ![Partíció létrehozása](./media/attach-disk/fdisknewpartdetails.png)


6. Típus **p** fájlrendszerű alaplemeznek folyamatban van a lemez részleteinek megtekintéséhez.

    ![Tárolólemez-információk listája](./media/attach-disk/fdiskpartitiondetails.png)


7. Típus **w** a beállítások a lemez írása.

    ![A lemez módosítások](./media/attach-disk/fdiskwritedisk.png)

8. A fájlrendszer most már az új partícióra hozhat létre. A partíciószám hozzáfűzése az Eszközazonosítót (az alábbi példában `/dev/sdc1`). Az alábbi példa a /dev/sdc1 ext4 partíciót hoz létre:
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![Fájlrendszer létrehozása](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > SuSE Linux Enterprise 11 rendszerek ext4 fájlrendszerek csak támogatása csak olvasási hozzáféréssel. Ezekben a rendszerekben ajánlott formázza az új fájlrendszer ext3 ext4 helyett.

9. Győződjön meg az új fájlrendszer csatlakoztatása a következő könyvtár:
   
    ```bash
    sudo mkdir /datadrive
    ```

10. Végül pedig akkor is csatlakoztathatja a meghajtót, a következő:
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    Az adatlemez most már készen áll a használatra **/datadrive**.
   
    ![A következő könyvtár létrehozásakor, és csatlakoztassa a lemezt](./media/attach-disk/mkdirandmount.png)

11. Az új meghajtó felvétele /etc/fstab:
   
    Annak érdekében, hogy a meghajtó újraindítás után automatikusan csatlakoztatni, hozzá kell adni az/etc/fstab fájlt a. Emellett azt javasoljuk, hogy az UUID (univerzálisan egyedi azonosító) használt /etc/fstab a csak az eszköz nevét (pl. /dev/sdc1) helyett. Folyamatban van csatlakoztatva egy adott helyre, ha az operációs rendszer észleli a lemezhiba során minden fennmaradó adatlemezeket, majd hozzárendeli azokat az eszközazonosítókat és rendszerindító a hibás lemez UUID használatával elkerülhető. Az új meghajtó UUID megkereséséhez használja a **blkid** segédprogrammal:
   
    ```bash
    sudo -i blkid
    ```
   
    A kimenet a következő példához hasonlóan néz ki:
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > Nem megfelelően szerkesztése a **/etc/fstab** fájl meghiúsulását eredményezheti. Ha nem tudja biztosan, tekintse meg a telepítési dokumentációban talál információkat megfelelően szerkesztése ezt a fájlt. Emellett javasoljuk, hogy a /etc/fstab fájl biztonsági másolata jön létre szerkesztése előtt.

    Ezután nyissa meg a **/etc/fstab** fájlt egy szövegszerkesztőben:

    ```bash
    sudo vi /etc/fstab
    ```

    Ebben a példában az új használjuk az UUID értéknek **/dev/sdc1** eszközt, és a csatlakoztatási pont az előző lépésekben létrehozott **/datadrive**. Adja hozzá a következő sor végére a **/etc/fstab** fájlt:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    Vagy, a SuSE Linux-alapú rendszereken szükség lehet egy némileg eltérő formátumot használja:

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > A `nofail` beállítással biztosíthatja, hogy a virtuális gép elindul még akkor is, ha a fájlrendszer sérült, vagy a lemez nem létezik a rendszerindítás közben. Ez a beállítás nélkül felmerülhet viselkedés leírtak szerint [nem SSH a Linux rendszerű virtuális gépek FSTAB-hibák miatt](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Most tesztelheti, hogy csatlakoztatva van-e a fájlrendszer megfelelően shellről, és ezután szolgáltatással a fájlrendszer, azaz a példa használatával csatlakoztatási pont `/datadrive` a korábbi lépésekben létrehozott:

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    Ha a `mount` parancs hibát ad vissza, akkor ellenőrizze a helyes szintaxis/etc/fstab fájl. Ha további meghajtók és partíciók jönnek létre, adja meg azokat az/etc/fstab külön-külön is.

    Győződjön meg arról, a meghajtó írható a következő paranccsal:

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > Ezt követően az adatlemez eltávolítása fstab szerkesztése nélkül a virtuális gép rendszerindítás okozhatnak. Ha ez gyakran előfordul, a legtöbb disztribúciók adja meg vagy a `nofail` és/vagy `nobootwait` fstab beállítások, amelyek lehetővé teszik a rendszert a akkor is, ha a lemezhiba csatlakoztathatja indítása során. További információ ezekről a paraméterekről a disztribúció dokumentációjában.

### <a name="trimunmap-support-for-linux-in-azure"></a>Az Azure-ban Linux TRIM/UNMAP támogatása
Egyes Linux-kernelek vannak a elveti a nem használt blokkolja a lemez TRIM/UNMAP műveletek támogatásához. Ezek a műveletek elsősorban hasznosak tájékoztatja, hogy az oldalak törlése az Azure már nem érvényesek, és lehet elvetni a standard szintű tárolóban. Oldalak elvetése mentheti a költség, ha nagy méretű fájlokat hoz létre, és törölje őket.

Nincsenek a TRIM engedélyezéséhez kétféleképpen támogatja a Linux rendszerű virtuális gépen. A szokásos módon tekintse meg a terjesztési az ajánlott módszer:

* Használja a `discard` lehetőség a csatlakoztatási `/etc/fstab`, például:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* Bizonyos esetekben a `discard` lehetőség is van a teljesítményre. Másik megoldásként futtathatja a `fstrim` manuálisan parancsot a parancssorból, vagy adja hozzá a crontab rendszeresen futtatásához:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**
  
    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [virtual-machines-linux-lunzero](../../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>További lépések
Tudjon meg többet a Linux rendszerű virtuális gép használatáról az alábbi cikkeket:

* [Bejelentkezés egy Linux rendszerű virtuális géphez][Logon]
* [Linux rendszerű virtuális gép lemez leválasztása](detach-disk-classic.md)
* [Az Azure CLI használatával a klasszikus üzemi modellel](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Az Azure-ban Linux rendszerű virtuális gép RAID konfigurálása](../configure-raid.md)
* [LVM konfigurálása az Azure-beli Linuxos virtuális gépre](../configure-lvm.md)

<!--Link references-->
[Agent]:../../extensions/agent-linux.md
[Logon]:../mac-create-ssh-keys.md
