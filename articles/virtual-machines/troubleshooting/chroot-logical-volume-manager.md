---
title: Linux rendszerű virtuális gépek helyreállítása a kromát használatával, ahol az LVM (logikai kötet-kezelő) használatos – Azure-beli virtuális gépek
description: Linux rendszerű virtuális gépek helyreállítása a LVMs-mel.
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
ms.openlocfilehash: 9c3f054a1bae745e4ee7ce9e3bddca3c9bf31083
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536002"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Linux rendszerű virtuális gép hibaelhárítása, ha nincs hozzáférés az Azure soros konzolhoz, és a lemez elrendezése az LVM (logikai kötet kezelője) használatával történik.

Ez a hibaelhárítási útmutató olyan forgatókönyvek esetén hasznos, amikor a Linux rendszerű virtuális gép nem indul el, az SSH nem lehetséges, és a mögöttes fájlrendszer elrendezése az LVM (logikai kötet kezelője) beállítással van konfigurálva.

## <a name="take-snapshot-of-the-failing-vm"></a>Pillanatkép készítése a hibás virtuális gépről

Készítsen pillanatképet az érintett virtuális gépről. 

A pillanatkép ezután csatolva lesz egy **mentési** virtuális géphez. A **Pillanatképek**elvégzéséhez kövesse az [alábbi utasításokat.](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal)

## <a name="create-a-rescue-vm"></a>Mentési virtuális gép létrehozása
Általában egy azonos vagy hasonló operációsrendszer-verzióval rendelkező mentési virtuális gép ajánlott. Az érintett virtuális gép azonos **régiójának** és **erőforráscsoportának** használata

## <a name="connect-to-the-rescue-vm"></a>Kapcsolódás a mentési virtuális géphez
Az SSH használatával kapcsolódhat a **mentési** virtuális géphez. Jogosultságszint-emelési jogosultságok és a felügyelővé válás

`sudo su -`

## <a name="attach-the-disk"></a>A lemez csatolása
Csatoljon egy lemezt a korábban készített pillanatképből származó **mentési** virtuális géphez.

Azure Portal – > a **mentési** virtuális gép kiválasztása – > **lemezek** 

![Lemez létrehozása](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Töltse fel a mezőket. Rendeljen egy nevet az új lemezhez, válassza ki ugyanazt az erőforráscsoportot, mint a pillanatkép, az érintett virtuális gép és a mentési virtuális gép.

A **forrás típusa** **Pillanatkép** .
A **forrás-pillanatkép** a korábban létrehozott **Pillanatkép** neve.

![2\. lemez létrehozása](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Csatlakoztatási pont létrehozása a csatlakoztatott lemezhez.

`mkdir /rescue`

Futtassa az **fdisk-l** parancsot a pillanatkép-lemez csatlakoztatásának ellenőrzéséhez, és az összes elérhető eszköz és partíció listázása

`fdisk -l`

A legtöbb esetben a csatolt pillanatkép-lemez a **/dev/SDC** két partíció **/dev/sdc1** és **/dev/sdc2** jelenik meg.

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

A **\*** egy rendszerindító partíciót jelöl, mindkét partíciót csatlakoztatni kell.

A **lsblk** parancs futtatásával tekintse meg az érintett virtuális gép LVMs

`lsblk`

![Lsblk futtatása](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Ellenőrizze, hogy megjelenik-e a LVMs az érintett virtuális gépről.
Ha nem, az alábbi parancsokkal engedélyezheti őket, és újra futtathatja a **lsblk**.
A folytatás előtt győződjön meg arról, hogy az LVMs a csatlakoztatott lemezről látható.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Keresse meg a/(root) partíciót tartalmazó logikai kötet csatlakoztatási útvonalát. A konfigurációs fájlok, például a/etc/default/grub

Ebben a példában a **rootvg-rootlv** előző **lsblk** parancs kimenetét a következő parancsban lehet használni , és a következő parancsban használható.

A következő parancs kimenete megmutatja, hogy milyen elérési utat kell csatlakoztatni a **gyökér** lv-hoz

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Az eszköz csatlakoztatása a címtár/Rescue

`mount /dev/rootvg/rootlv /rescue`

A/Rescue/boot beállított **rendszerindítási jelzővel** rendelkező partíció csatlakoztatása

`
mount /dev/sdc1 /rescue/boot
`

Ellenőrizze, hogy a csatlakoztatott lemez fájlrendszerei megfelelően vannak-e csatlakoztatva a **lsblk** parancs használatával.

![Lsblk futtatása](./media/chroot-logical-volume-manager/lsblk-output-1.png)

vagy a **DF-th** parancs

![DF](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>A kromát elérésének megszerzése

A **kromát** hozzáférésének biztosítása, amely lehetővé teszi a különböző javítások elvégzését, az egyes Linux-disztribúciók esetében azonban kisebb eltérések is léteznek.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Ha egy hiba tapasztalható, például:

**kromát: nem sikerült futtatni a (z) "/bin/bash" parancsot: nincs ilyen fájl vagy könyvtár.**

kísérlet a **usr** logikai kötet csatlakoztatására

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Ha egy **kromát** -környezetben hajtja végre a parancsokat, vegye figyelembe, hogy azok a csatlakoztatott operációsrendszer-lemezre futnak, nem a helyi **mentési** virtuális gépre. 

A parancsok segítségével telepítheti, eltávolíthatja és frissítheti a szoftvereket. A virtuális gépek hibaelhárítása a hibák elhárítása érdekében.


Hajtsa végre a lsblk parancsot, és a/Rescue most/és/Rescue/boot/boot ![a be](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Javítások végrehajtása

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>1\. példa – a virtuális gép beállítása egy másik kernelből való rendszerindításra

Gyakori eset, ha egy virtuális gépet egy korábbi kernelről indít el, mert az aktuálisan telepített kernel sérült, vagy a frissítés nem fejeződött be megfelelően.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*walkthrough*

A **grep** -parancs felsorolja azokat a kerneleket, amelyekkel a **grub. cfg** tisztában van.
![kernelek](./media/chroot-logical-volume-manager/kernels.png)

**GRUB2 – a editenv listában** látható, hogy melyik kernel lesz betöltve a következő rendszerindítási ![kernel alapértelmezett](./media/chroot-logical-volume-manager/kernel-default.png)

**GRUB2 – az alapértelmezett** érték a másik kernel ![GRUB2-készletre való váltásra szolgál](./media/chroot-logical-volume-manager/grub2-set-default.png)

**GRUB2 – a editenv** listában látható, hogy melyik kernel lesz betöltve a következő rendszerindításkor ![új kernel](./media/chroot-logical-volume-manager/kernel-new.png)

**GRUB2-mkconfig** újraépíti a grub. cfg fájlt a szükséges verziók használatával ![grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>2\. példa – csomagok frissítése

A sikertelen kernel-frissítés a virtuális gép nem rendszerindítását teszi lehetővé.
Az összes logikai kötet csatlakoztatása a csomagok eltávolításának vagy újratelepítésének engedélyezéséhez

Futtassa a **LVS** parancsot annak ellenőrzéséhez, hogy mely **LVS** érhetők el a CSATLAKOZTATÁSHOZ, minden virtuális gép, amely át lett telepítve, vagy egy másik felhőalapú szolgáltatótól származik, változhat a konfigurációban.

Kilépés a **kromát** -környezetből a szükséges **lv** -vel

![Speciális](./media/chroot-logical-volume-manager/advanced.png)

Most futtassa ismét a **kromát** -környezetet

`chroot /rescue`

Minden LVs csatlakoztatott partícióként kell látni

![Speciális](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

A telepített **kernel** lekérdezése

![Speciális](./media/chroot-logical-volume-manager/rpm-kernel.png)

Ha szükséges, frissítse a **kernel**
![Advanced](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>3\. példa – soros konzol engedélyezése
Ha a hozzáférés nem volt lehetséges az Azure soros konzolon, ellenőrizze a GRUB-konfigurációs paramétereket a linuxos virtuális gépen, és javítsa ki azokat. Részletes információkat [ebben a dokumentumban](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration) találhat


## <a name="exit-chroot-and-swap-the-os-disk"></a>Kilépés a kromátból és az operációsrendszer-lemez cseréje

A probléma kijavítása után folytassa a leválasztást, és válassza le a lemezt a mentési virtuális gépről, hogy az az érintett VM operációsrendszer-lemezzel legyen felcserélve.

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

Válassza le a lemezt a mentési virtuális gépről, és hajtson végre egy lapozófájlt.

Válassza ki a virtuális gépet a portál **lemezek** közül, és válassza a **leválasztás**
![lemez leválasztása](./media/chroot-logical-volume-manager/detach-disk.png) 

Mentse a módosításokat ![válassza a leválasztási](./media/chroot-logical-volume-manager/save-detach.png) 

A lemez mostantól elérhetővé válik, amely lehetővé teszi, hogy a rendszer megcserélje az érintett virtuális gép eredeti operációsrendszer-lemezét.

Navigáljon a Azure Portal a hibás virtuális géphez, és válassza a **lemezek** -> az **operációsrendszer-lemez felcserélése**
![a lemez cseréje](./media/chroot-logical-volume-manager/swap-disk.png) 

Hajtsa végre a mezőket a **kiválasztott lemez** az előző lépésben leválasztott pillanatkép-lemez. Az érintett virtuális gép virtuálisgép-nevét is meg kell adni, majd kattintson az **OK gombra** .

![Új operációsrendszer-lemez](./media/chroot-logical-volume-manager/new-osdisk.png) 

Ha a virtuális gép futtatja a lemezes cserét, állítsa le, indítsa újra a virtuális gépet a lemezes swap művelet befejeződése után.


## <a name="next-steps"></a>Következő lépések
További információ az [Azure soros konzolról]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
