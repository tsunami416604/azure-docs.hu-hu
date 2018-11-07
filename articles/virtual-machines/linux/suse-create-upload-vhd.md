---
title: Hozzon létre, és az Azure-beli SUSE Linux VHD feltöltése
description: Ismerje meg, hozhat létre és töltse fel az Azure virtuális merevlemez (VHD), amely tartalmazza a SUSE Linux operációs rendszer.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 5aa998ef7af157f84a3985fdb458c2800f2575f4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249370"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>SLES- vagy openSUSE-alapú virtuális gép előkészítése Azure-beli használatra
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már telepítette a SUSE- vagy openSUSE Linux operációs rendszer virtuális merevlemezre. Több eszköz létezik a .vhd fájlokat, például például a Hyper-V virtualizálási megoldás létrehozása. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gép](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES vagy opensuse-alapú telepítéssel kapcsolatos megjegyzések
* Tekintse meg a is [általános Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további tippek Linux előkészítése az Azure-hoz.
* A VHDX formátum nem támogatott az Azure-ban, csak **rögzített VHD**.  Átválthat a lemez VHD formátumú Hyper-V kezelőjével vagy a convert-vhd-parancsmag használatával.
* A Linux rendszer telepítésekor LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciók használata ajánlott. LVM neve nem felel meg a klónozott virtuális gépeket, így elkerülhető, különösen akkor, ha minden eddiginél kell operációsrendszer-lemezt egy másik virtuális Géphez van csatlakoztatva a hibaelhárításhoz. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezekre is használható, ha az előnyben részesített.
* Az operációsrendszer-lemez nem konfigurál egy lapozó partíciót. A Linux-ügynök beállítható úgy, hogy hozzon létre egy ideiglenes erőforrás lemezen a lapozófájl.  További információ található a következő lépéseket.
* Az Azure-ban minden virtuális merevlemezek rendelkeznie kell egy virtuális méret 1 MB igazítva. A virtuális merevlemez nyers lemezről történő konvertálása során biztosítania kell, hogy a nyers lemez mérete nagyobb-e az átalakítás előtt 1MB többszöröse. Lásd: [Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) további információt.

## <a name="use-suse-studio"></a>SUSE Studio használata
[SUSE Studio](http://www.susestudio.com) egyszerűen hozzon létre és a SLES és openSUSE lemezképek kezelése az Azure és Hyper-V. Ez az az ajánlott módszer a saját SLES és openSUSE rendszerképek testreszabásához.

Ahelyett, hogy a saját virtuális merevlemez létrehozásához, mint SUSE is közzéteszi (Bring Your saját előfizetés) saját lemezképek, a SLES-hez [gyakorlatilag korlátlanná teszik](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>SUSE Linux Enterprise Server 11 SP4 előkészítése
1. A középső ablaktáblán a Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Connect** a virtuális gép ablak megnyitásához.
3. Regisztrálja a SUSE Linux Enterprise rendszer, hogy a csomagok frissítések letöltése és telepítése.
4. Frissítse a rendszer a legújabb javításokat:
   
        # sudo zypper update
5. Az Azure Linux-ügynök telepítése a SLES adattárából:
   
        # sudo zypper install WALinuxAgent
6. Ha waagent értéke "on" felvétel chkconfig, és ha nem, akkor engedélyezze a autostart:
   
        # sudo chkconfig waagent on
7. Ellenőrizze, hogy a waagent-szolgáltatás fut, és ha nem, indítsa el: 
   
        # sudo service waagent start
8. Módosítsa a rendszermag rendszerindítási sorához további kernel paramétereket tartalmazza az Azure-hoz a grub-hibát konfigurációjában. Ehhez a nyílt "/ boot/grub/menu.lst" egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel tartalmazza a következő paraméterekkel:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Ez biztosítja az összes konzol üzenetet küld az első soros port, amely segítheti az Azure támogatási problémák hibakeresésében.
9. Győződjön meg arról, hogy /boot/grub/menu.lst és /etc/fstab is hivatkozhatnak a lemezt az UUID-azonosítója (amelyet-uuid) helyett a Lemezazonosítót (-azonosító szerint). 
   
    A lemez UUID azonosító lekérése
   
        # ls /dev/disk/by-uuid/
   
    Ha /dev/disk/by-id van /boot/grub/menu.lst mind a/etc/fstab használt, frissítse a megfelelő által-uuid értékkel
   
    Változás előtt
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Váltás után
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Az Ethernet-adaptert statikus szabályainak elkerülése érdekében, udev szabályok módosítása. Ezek a szabályok problémákat okozhat, ha a Microsoft Azure vagy Hyper-v virtuális gépek klónozásának
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Ajánlott a fájl szerkesztése "/ etc/sysconfig/hálózati/dhcp", és módosítsa a `DHCLIENT_SET_HOSTNAME` a következő paramétert:
    
     DHCLIENT_SET_HOSTNAME="no"
12. A "/ etc/sudoers" tegye megjegyzésbe, vagy ha vannak ilyenek, távolítsa el a következő sorokat:
    
     Alapértelmezés szerint targetpw # kérje meg a jelszót a céloldali felhasználó pl. legfelső szintű összes ALL=(ALL) összes # figyelmeztetés! Csak ezzel együtt az 'Alapértelmezett targetpw'!
13. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve és konfigurálva van rendszerindítás elindításához.  Ez általában az alapértelmezett érték.
14. Nem hozható létre lapozófájl-kapacitás az operációsrendszer-lemez.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatják a lapozóterület használata a helyi erőforrás-lemez, amely az Azure-ban üzembe helyezés után a virtuális Géphez van csatlakoztatva. Vegye figyelembe, hogy a helyi erőforrás-lemez egy *ideiglenes* lemezre, és előfordulhat, hogy ki kell üríteni, ha a virtuális gép. Az Azure Linux-ügynök telepítése után (lásd az előző lépésben), annak megfelelően módosítsa a következő paramétereket lévő /etc/waagent.conf:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Megjegyzés: állítsa ezt a beállítást függetlenül kell legyen.
15. Futtassa az alábbi parancsokat a virtuális gép megszüntetése és kiépítése az Azure előkészítése:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Kattintson a **művelet le -> Leállítás** a Hyper-V kezelőjében. A Linux rendszerű VHD-t most már készen áll a tölthető fel az Azure-bA.

- - -
## <a name="prepare-opensuse-131"></a>OpenSUSE 13.1 + előkészítése
1. A középső ablaktáblán a Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Connect** a virtuális gép ablak megnyitásához.
3. Futtassa a parancsot a rendszerhéj "`zypper lr`". Ez a parancs kimenete az alábbihoz hasonló, akkor a várt a módosítás nélkül szükség a tárházak vannak konfigurálva (vegye figyelembe, hogy a verziószámok eltérhetnek):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Ha a parancs visszaadja a "Nincs definiálva... tárházak" majd a következő parancsokat használja, ezek a kódtárak hozzáadása:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Ezután ellenőrizheti a tárházak lettek hozzáadva a parancs futtatásával "`zypper lr`" újra. Abban az esetben a megfelelő frissítési tárházak egyik nincs engedélyezve, engedélyezze a következő paranccsal:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Az elérhető legújabb verzióra frissíteni a kernel:
   
        # sudo zypper up kernel-default
   
    Vagy frissítse a rendszer a legújabb javításokat:
   
        # sudo zypper update
5. Az Azure Linux-ügynök telepítése.
   
        # sudo zypper install WALinuxAgent
6. Módosítsa a rendszermag rendszerindítási sorához további kernel paramétereket tartalmazza az Azure-hoz a grub-hibát konfigurációjában. Ehhez nyissa meg a "/ boot/grub/menu.lst" egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel tartalmazza a következő paraméterekkel:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Ez biztosítja az összes konzol üzenetet küld az első soros port, amely segítheti az Azure támogatási problémák hibakeresésében. A következő paraméterek ezenkívül eltávolítása a rendszermag rendszerindítási sorához, ha vannak ilyenek:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. Ajánlott a fájl szerkesztése "/ etc/sysconfig/hálózati/dhcp", és módosítsa a `DHCLIENT_SET_HOSTNAME` a következő paramétert:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Fontos:** "/ etc/sudoers", tegye megjegyzésbe, vagy ha vannak ilyenek, távolítsa el a következő sorokat:
   
     Alapértelmezés szerint targetpw # kérje meg a jelszót a céloldali felhasználó pl. legfelső szintű összes ALL=(ALL) összes # figyelmeztetés! Csak ezzel együtt az 'Alapértelmezett targetpw'!
9. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve és konfigurálva van rendszerindítás elindításához.  Ez általában az alapértelmezett érték.
10. Nem hozható létre lapozófájl-kapacitás az operációsrendszer-lemez.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatják a lapozóterület használata a helyi erőforrás-lemez, amely az Azure-ban üzembe helyezés után a virtuális Géphez van csatlakoztatva. Vegye figyelembe, hogy a helyi erőforrás-lemez egy *ideiglenes* lemezre, és előfordulhat, hogy ki kell üríteni, ha a virtuális gép. Az Azure Linux-ügynök telepítése után (lásd az előző lépésben), annak megfelelően módosítsa a következő paramétereket lévő /etc/waagent.conf:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Megjegyzés: állítsa ezt a beállítást függetlenül kell legyen.
11. Futtassa az alábbi parancsokat a virtuális gép megszüntetése és kiépítése az Azure előkészítése:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Ellenőrizze, hogy az Azure Linux-ügynök rendszerindításkor futó:
    
        # sudo systemctl enable waagent.service
13. Kattintson a **művelet le -> Leállítás** a Hyper-V kezelőjében. A Linux rendszerű VHD-t most már készen áll a tölthető fel az Azure-bA.

## <a name="next-steps"></a>További lépések
Most már készen áll a SUSE Linux rendszerű virtuális merevlemez használatával hozzon létre új virtuális gépek az Azure-ban. Ha ez az első alkalommal, hogy a .vhd fájlt videófájl az Azure-ba, tekintse meg a [egy Linux virtuális gép létrehozása egy egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd).
