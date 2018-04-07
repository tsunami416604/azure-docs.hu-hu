---
title: Létrehozása és feltöltése az Azure-ban SUSE Linux virtuális merevlemez
description: Ismerje meg, létrehozása és feltöltése az Azure virtuális merevlemez (VHD), amely tartalmazza a SUSE Linux operációs rendszert.
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
ms.openlocfilehash: 2372550548f40ad07b4f76c19bc3bc1cb8380830
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>SLES- vagy openSUSE-alapú virtuális gép előkészítése Azure-beli használatra
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már telepítve van egy SUSE vagy openSUSE Linux operációs rendszer egy virtuális merevlemez. Több különféle eszköz található .vhd fájlok, például egy például a Hyper-V virtualizálási megoldás létrehozása. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gépek](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE telepítési megjegyzések
* Is lásd: [általános Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) kapcsolatos további információkat az Azure Linux előkészítése.
* A VHDX formátum nem támogatott az Azure csak **rögzített VHD**.  Átválthat a lemez VHD formátumú Hyper-V kezelője vagy a convert-vhd-parancsmag segítségével.
* A Linux rendszer telepítésekor LVM (gyakran sok telepítés alapértelmezett), hanem szabványos partíciók használata ajánlott. LVM neve ütközik a klónozott virtuális gépek, így elkerülhető, különösen akkor, ha egy operációsrendszer-lemez legalább egyszer meg kell hibaelhárítási egy másik virtuális géphez csatlakoztatható. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Ha előnyben részesített adatlemezek használható.
* Ne konfiguráljon egy swap partíciót az operációsrendszer-lemezképet. A Linux-ügynök beállítható úgy, hogy az ideiglenes erőforrás lemezen a lapozófájl létrehozásához.  További információk a megtalálhatók az alábbi lépéseket.
* Minden, az Azure virtuális merevlemez rendelkeznie kell egy virtuális mérete 1MB igazodik. Virtuális merevlemez egy nyers lemezen történő átalakítása meg kell győződnie arról, hogy a nyers lemez mérete 1MB átalakítás előtti többszöröse. Lásd: [Linux telepítési jegyzetek](create-upload-generic.md#general-linux-installation-notes) további információt.

## <a name="use-suse-studio"></a>SUSE studióval
[SUSE Studio](http://www.susestudio.com) könnyen létrehozása és a SLES és openSUSE lemezképek kezelése az Azure és a Hyper-V. Ez az az ajánlott módszer a saját SLES és openSUSE lemezképek testreszabásához.

A saját virtuális merevlemez létrehozása helyett, SUSE is közzéteszi (Bring Your saját előfizetés) saját lemezképek a következő SLES [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>SUSE Linux Enterprise Server 11 SP4 előkészítése
1. A középső ablaktáblán a Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Connect** a virtuális gép ablak megnyitásához.
3. Regisztrálja a SUSE Linux Enterprise rendszer engedélyezze a frissítések letöltése és telepítése a csomagokat.
4. Frissítés a rendszer, amely a legújabb javítások:
   
        # sudo zypper update
5. Az Azure Linux ügynök telepítése a SLES tárházból:
   
        # sudo zypper install WALinuxAgent
6. Ha waagent van-e állítva az "on" beadása chkconfig, és ha nem, engedélyezze az automatikus indítás:
   
        # sudo chkconfig waagent on
7. Ellenőrizze, hogy a waagent-szolgáltatás fut, és ha nem, indítsa el: 
   
        # sudo service waagent start
8. Módosítsa a kernel rendszerindító sor lárvajárat konfigurációs kiegészítő rendszermag paraméterek az Azure-bA felvenni. Ehhez a Megnyitás "/ boot/grub/menu.lst" egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel az alábbi paramétereket tartalmazza:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Ezzel biztosíthatja, hogy minden konzol küldi el az első soros port, amely segít az Azure által támogatott hibáinak feltárására.
9. Győződjön meg arról, hogy /boot/grub/menu.lst és /etc/fstab is hivatkoznak a lemez az UUID (által-uuid) helyett a Lemezazonosítót (-azonosító szerint). 
   
    Lemezek UUID azonosítója beolvasása
   
        # ls /dev/disk/by-uuid/
   
    Ha /dev/disk/by-id / /boot/grub/menu.lst mind a/etc/fstab használ, frissítse a megfelelő által-uuid-értékkel rendelkező
   
    Változás előtt
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Módosítás után
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Az Ethernet-adaptert statikus szabályainak elkerülése érdekében udev szabályok módosítása. Ezek a szabályok problémákat okozhat, ha a Microsoft Azure- vagy Hyper-v virtuális gép klónozása
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. A fájl szerkesztése ajánlott "/ etc/sysconfig/hálózati/dhcp", és módosítsa a `DHCLIENT_SET_HOSTNAME` a következő paramétert:
    
     DHCLIENT_SET_HOSTNAME="no"
12. A "/ etc/sudoers" megjegyzéssé, vagy ha vannak ilyenek, távolítsa el a következő sorokat:
    
     Alapértelmezés szerint targetpw # kérje meg a jelszót, azaz legfelső szintű összes ALL=(ALL) minden célként megadott felhasználó # figyelmeztetés! Csak ezzel együtt a 'Alapértelmezett targetpw'!
13. Győződjön meg arról, hogy az SSH-kiszolgálót telepítse és konfigurálja a rendszerindítás elindításához.  Ez általában az alapértelmezett beállítás.
14. Ne hozzon létre az operációsrendszer-lemezképet a lapozóterület.
    
    Az Azure Linux ügynök automatikusan konfigurálhatók a lapozóterület használata a helyi erőforrás lemezt a virtuális Géphez csatolt Azure kiépítése után. Vegye figyelembe, hogy a helyi erőforrás lemez egy *ideiglenes* lemezre, és előfordulhat, hogy szerepelnek, ha a virtuális gép van platformelőfizetés. Az Azure Linux ügynök telepítése után (lásd az előző lépésben), annak megfelelően módosítsa a /etc/waagent.conf a következő paraméterekkel:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Megjegyzés: állítsa függetlenül esetleg szükség lenne rá kell lennie.
15. A virtuális gép kiosztásának megszüntetése, és előkészíti az Azure-on történő üzembe helyezéséhez a következő parancsok futtatásával:
    
    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-force - deprovision
    # <a name="export-histsize0"></a>exportálja a HISTSIZE = 0
    # <a name="logout"></a>kijelentkezés
16. Kattintson a **művelet le -> leállítási** a Hyper-V kezelőjében. A Linux virtuális merevlemez az Azure-bA feltölteni kívánt készen áll.

- - -
## <a name="prepare-opensuse-131"></a>OpenSUSE 13.1 + előkészítése
1. A középső ablaktáblán a Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Connect** a virtuális gép ablak megnyitásához.
3. Futtassa a parancsot a rendszerhéj "`zypper lr`". Ha ez a parancs visszaadja kimenet az alábbihoz hasonló, akkor a tárolóhelyekkel konfigurált elvárt – módosítás nélkül szükség (vegye figyelembe, hogy a verziószáma változhat):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Ha a parancs visszaadja a "Nincs definiálva... adattárak" használja az alábbi parancsokat a repók hozzáadása:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Ezután ellenőrizheti a tárolóhelyekkel lettek hozzáadva a parancs futtatásával "`zypper lr`" újra. Abban az esetben, ha egy, a megfelelő frissítési adattárak nincs engedélyezve, engedélyezze a következő paranccsal:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Frissítés a legújabb elérhető verziójára kernel:
   
        # sudo zypper up kernel-default
   
    Vagy a rendszer frissítése a legújabb javítások:
   
        # sudo zypper update
5. Az Azure Linux ügynök telepítése.
   
   # <a name="sudo-zypper-install-walinuxagent"></a>sudo zypper telepítés WALinuxAgent
6. Módosítsa a kernel rendszerindító sor lárvajárat konfigurációs kiegészítő rendszermag paraméterek az Azure-bA felvenni. Ehhez nyissa meg a "/ boot/grub/menu.lst" egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel az alábbi paramétereket tartalmazza:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Ezzel biztosíthatja, hogy minden konzol küldi el az első soros port, amely segít az Azure által támogatott hibáinak feltárására. A következő paraméterek ezenkívül eltávolítása a kernel rendszerindító sor, ha vannak ilyenek:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. A fájl szerkesztése ajánlott "/ etc/sysconfig/hálózati/dhcp", és módosítsa a `DHCLIENT_SET_HOSTNAME` a következő paramétert:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Fontos:** "/ etc/sudoers", a megjegyzéssé, vagy ha vannak ilyenek, távolítsa el a következő sorokat:
   
     Alapértelmezés szerint targetpw # kérje meg a jelszót, azaz legfelső szintű összes ALL=(ALL) minden célként megadott felhasználó # figyelmeztetés! Csak ezzel együtt a 'Alapértelmezett targetpw'!
9. Győződjön meg arról, hogy az SSH-kiszolgálót telepítse és konfigurálja a rendszerindítás elindításához.  Ez általában az alapértelmezett beállítás.
10. Ne hozzon létre az operációsrendszer-lemezképet a lapozóterület.
    
    Az Azure Linux ügynök automatikusan konfigurálhatók a lapozóterület használata a helyi erőforrás lemezt a virtuális Géphez csatolt Azure kiépítése után. Vegye figyelembe, hogy a helyi erőforrás lemez egy *ideiglenes* lemezre, és előfordulhat, hogy szerepelnek, ha a virtuális gép van platformelőfizetés. Az Azure Linux ügynök telepítése után (lásd az előző lépésben), annak megfelelően módosítsa a /etc/waagent.conf a következő paraméterekkel:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Megjegyzés: állítsa függetlenül esetleg szükség lenne rá kell lennie.
11. A virtuális gép kiosztásának megszüntetése, és előkészíti az Azure-on történő üzembe helyezéséhez a következő parancsok futtatásával:
    
    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-force - deprovision
    # <a name="export-histsize0"></a>exportálja a HISTSIZE = 0
    # <a name="logout"></a>kijelentkezés
12. Győződjön meg arról, az Azure Linux ügynök a indításakor fut:
    
        # sudo systemctl enable waagent.service
13. Kattintson a **művelet le -> leállítási** a Hyper-V kezelőjében. A Linux virtuális merevlemez az Azure-bA feltölteni kívánt készen áll.

## <a name="next-steps"></a>További lépések
Most már készen áll a SUSE Linux virtuális merevlemez segítségével új virtuális gépek létrehozása az Azure-ban. Ha az első alkalommal, hogy van-e a .vhd fájl feltöltése az Azure, lásd: [Linux virtuális gép létrehozása az egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd).
