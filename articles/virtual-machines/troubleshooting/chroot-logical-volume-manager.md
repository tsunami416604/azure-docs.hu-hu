---
title: Linuxos virtuális gépek helyreállítása chroot használatával, ahol LVM (Logikai kötetkezelő) használatos - Azure virtuális gépek
description: Linuxos virtuális gépek helyreállítása lvm-ekkel.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 20d710f717a9dff26f46ac7a201a9b694f3fbe84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684134"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Linuxos virtuális gép hibaelhárítása, ha nincs hozzáférés az Azure soros konzoljához, és a lemezelrendezés LVM (Logikai kötetkezelő)

Ez a hibaelhárítási útmutató előnyös olyan esetekben, amikor a Linux virtuális gép nem indul, ssh nem lehetséges, és az alapul szolgáló fájlrendszer elrendezése LVM (Logikai kötetkezelő) konfigurálva van.

## <a name="take-snapshot-of-the-failing-vm"></a>Pillanatkép készítése a hibás virtuális gépről

Pillanatképet készíthet az érintett virtuális gépről. 

A pillanatkép ezután egy **mentési** virtuális géphez lesz csatolva. Kövesse az [itt](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal) található utasításokat a **pillanatkép**készítéséről.

## <a name="create-a-rescue-vm"></a>Mentési virtuális gép létrehozása
Általában egy mentési virtuális gép azonos vagy hasonló operációs rendszer verziója ajánlott. Használja az érintett virtuális gép ugyanazon **régióját** és **erőforráscsoportját**

## <a name="connect-to-the-rescue-vm"></a>Csatlakozás a mentési virtuális géphez
Csatlakozzon ssh segítségével a **mentési** virtuális gép. Magasabb szintű jogosultságokat, és vált szuper felhasználó segítségével

`sudo su -`

## <a name="attach-the-disk"></a>A lemez csatlakoztatása
Csatlakoztasson egy lemezt a **mentési** virtuális géphez, amely a korábban készített pillanatképből készült.

Az Azure Portal -> válassza ki a **mentési** virtuális gép -> **lemezek** 

![Lemez létrehozása](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Feltöltsd a mezőket. Rendeljen nevet az új lemezhez, válassza ki ugyanazt az erőforráscsoportot, mint a pillanatkép, az érintett virtuális gép és a mentési virtuális gép.

A **forrás típusa** **Snapshot** .
A **forrás pillanatkép** a korábban létrehozott **pillanatkép** neve.

![2. lemez létrehozása](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Hozzon létre egy csatlakoztatási pontot a csatlakoztatott lemezhez.

`mkdir /rescue`

Futtassa az **fdisk -l** parancsot a pillanatképlemez csatlakoztatásához, és sorolja fel az összes elérhető eszközt és partíciót

`fdisk -l`

A legtöbb esetben a csatolt pillanatkép-lemez **a /dev/sdc** könyvtárban két **partíciót /dev/sdc1** és **/dev/sdc2-t** jelenít meg.

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

A **\*** rendszerindító partíciót jelöl, mindkét partíciót csatlakoztatni kell.

Futtassa az **lsblk parancsot** az érintett virtuális gép lvm-jeinek megtekintéséhez

`lsblk`

![Lsblk futtatása](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Ellenőrizze, hogy az érintett virtuális gép lvm-jei megjelennek-e.
Ha nem, használja az alábbi parancsokat, hogy engedélyezze őket, és futtassa újra **lsblk**.
A folytatás előtt győződjön meg arról, hogy a csatlakoztatott lemezről származó lvm-ek láthatók legyenek.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Keresse meg a / (root) partíciót tartalmazó logikai kötet csatlakoztatásának elérési útját. Azt a konfigurációs fájlokat, mint a /etc/default/grub

Ebben a példában az előző **lsblk** parancs **rootvg-rootlv** kimenetének bevétele a megfelelő **gyökér** LV csatlakoztatása, és a következő parancsban használható.

A következő parancs kimenete megmutatja a **gyökér** LV csatlakoztatási útvonalát

`pvdisplay -m | grep -i rootlv`

![Gyökérlva](./media/chroot-logical-volume-manager/locate-rootlv.png)

Az eszköz csatlakoztatása a /mentési könyvtárba

`mount /dev/rootvg/rootlv /rescue`

A /rescue/boot kapcsolóval beállított **rendszerindító jelzővel** rendelkező partíció csatlakoztatása

`
mount /dev/sdc1 /rescue/boot
`

Ellenőrizze, hogy a csatlakoztatott lemez fájlrendszerei megfelelően vannak-e csatlakoztatva az **lsblk** paranccsal

![Lsblk futtatása](./media/chroot-logical-volume-manager/lsblk-output-1.png)

vagy a **df -Th** parancs

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Chroot hozzáférés megszerzése

Gain **chroot** hozzáférés, amely lehetővé teszi, hogy végre a különböző javítások, kisebb eltérések léteznek minden Linux disztribúció.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Ha olyan hibát észlel, mint például:

**chroot: nem sikerült futtatni a parancsot "/bin/bash": Nincs ilyen fájl vagy könyvtár**

kísérlet a **usr** logikai kötet csatlakoztatására

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Parancsok végrehajtásakor a **chroot** környezetben, vegye figyelembe, hogy a csatlakoztatott operációsrendszer-lemez, és nem a helyi **mentési** virtuális gép. 

A parancsok segítségével szoftverek telepíthetők, távolíthatók el és frissíthetők. A hibák kijavítása érdekében hibaelhárítást okozhat a virtuális gépek.


Hajtsa végre az lsblk parancsot, és a /rescue is now / és /rescue/boot is /boot ![Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Javítások végrehajtása

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>1. példa - a virtuális gép konfigurálása egy másik rendszermagból történő rendszerindításhoz

Gyakori forgatókönyv, hogy a virtuális gép egy korábbi rendszermagból való rendszerindításra kényszeríti, mivel az aktuálisan telepített kernel megsérülhetett, vagy a frissítés nem fejeződött be megfelelően.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*Forgatókönyv*

A **grep** parancs felsorolja azokat a magokat, amelyekről **a grub.cfg** tud.
![Kernelek](./media/chroot-logical-volume-manager/kernels.png)

**Grub2-editenv lista** megjeleníti, hogy melyik ![kernel lesz betöltve a következő boot Kernel alapértelmezett](./media/chroot-logical-volume-manager/kernel-default.png)

**Grub2-set-default-t** használnak, hogy ![módosítsa a másik kernel Grub2 set](./media/chroot-logical-volume-manager/grub2-set-default.png)

**Grub2-editenv** lista megjeleníti, hogy melyik ![kernel lesz betöltve a következő boot Új kernel](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** újjáépíti grub.cfg a ![szükséges verziók Grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>2. példa – frissítő csomagok

A sikertelen kernelfrissítés nem indítható a virtuális gép számára.
Az összes logikai kötet csatlakoztatása a csomagok eltávolításához vagy újratelepítéséhez

Futtassa az **lvs** parancsot annak ellenőrzéséhez, hogy mely **lvv-ek** érhetők el a csatlakoztatáshoz, minden virtuális gép, amely átlett telepítve, vagy egy másik felhőszolgáltatótól származik, konfigurációja eltérő lehet.

Kilépés a **chroot** környezetcsatlakoztatása a szükséges **LV**

![Speciális](./media/chroot-logical-volume-manager/advanced.png)

Most ismét hozzáférhet a **chroot** környezethez a

`chroot /rescue`

Minden LV-nek csatlakoztatott partícióként láthatónak kell lennie

![Speciális](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

A telepített **rendszermag lekérdezése**

![Speciális](./media/chroot-logical-volume-manager/rpm-kernel.png)

Ha szükséges, távolítsa el vagy frissítse a **kernel**
![Advanced](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>3. példa - soros konzol engedélyezése
Ha a hozzáférés nem sikerült az Azure soros konzolhoz, ellenőrizze a Linux virtuális gép GRUB konfigurációs paramétereit, és javítsa ki azokat. Részletes információ ebben [a dokumentumban található.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>4. példa - kernelbetöltés problémás LVM swap kötettel

Előfordulhat, hogy a virtuális gép nem indul el teljesen, és a **dracut** promptba kerül.
A hiba további részletei az Azure soros konzoljáról vagy az Azure Portal -> rendszerindítási diagnosztika -> soros naplóból találhatók.


Ehhez hasonló hiba fordulhat elő:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

A grub.cfg ebben a példában úgy van beállítva, hogy töltsön be egy LV nevet **rd.lvm.lv=VG/SwapVol,** és a virtuális gép nem találja ezt. Ez a sor azt mutatja, hogy a rendszermag betöltése az LV SwapVol-ra hivatkozva

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Távolítsa el a jogsértő LV-t az /etc/default/grub konfigurációból, és építse újra a grub2.cfg-t


## <a name="exit-chroot-and-swap-the-os-disk"></a>Kilépés a chroot-ból és az operációs rendszer lemezének cseréje

A probléma javítása után folytassa a lemez leválasztását, és válassza le a lemezt a mentési virtuális gépről, lehetővé téve, hogy az érintett virtuálisgép-operációs rendszer lemezével felcserélőlegyen.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Válassza le a lemezt a mentési virtuális gépről, és hajtson végre egy lemezcsere.

Válassza ki a virtuális gép a **portállemezek** és válassza **leválasztás**
![lemez](./media/chroot-logical-volume-manager/detach-disk.png) 

A módosítások ![mentése Mentés leválasztás](./media/chroot-logical-volume-manager/save-detach.png) 

A lemez most elérhetővé válik, amely lehetővé teszi, hogy az érintett virtuális gép eredeti operációsrendszer-lemezével felcserélődjön.

Navigálás az Azure Portalon a hibás virtuális géphez, és válassza **a Disks** -> **Swap OS Disk**
![Swap lemez](./media/chroot-logical-volume-manager/swap-disk.png) 

Töltse ki a mezőket a **Lemez kiválasztása** a pillanatkép lemez csak leválasztott az előző lépésben. Az érintett virtuális gép virtuális gépének neve is szükséges, majd válassza az **OK gombot**

![Új operációs rendszer lemeze](./media/chroot-logical-volume-manager/new-osdisk.png) 

Ha a virtuális gép fut a lemezcsere leállítja, indítsa újra a virtuális gépet, miután a lemezcsere művelet befejeződött.


## <a name="next-steps"></a>További lépések
További információ

 [Azure soros konzol]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[Egyfelhasználós mód](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
