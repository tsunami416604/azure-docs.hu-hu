---
title: SUSE Linux virtuális merevlemez létrehozása és feltöltése az Azure-ban
description: Ismerje meg, hogy hozzon létre és töltsön fel egy SUSE Linux operációs rendszert tartalmazó Virtuális Azure-merevlemezt.Learn to create and upload an Azure virtual hard disk (VHD) that contains a SUSE Linux operating system.
author: gbowerman
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2018
ms.author: guybo
ms.openlocfilehash: 032b49631c6adb30d4b25f8b82d35dab49ffd3a2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757675"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>SLES- vagy openSUSE-alapú virtuális gép előkészítése az Azure-beli használatra


Ez a cikk feltételezi, hogy már telepített egy SUSE vagy openSUSE Linux operációs rendszert egy virtuális merevlemezre. Több eszköz létezik .vhd fájlok létrehozásához, például egy virtualizációs megoldás, például a Hyper-V. További információt [a Hyper-V szerepkör telepítése és a Virtuális gép konfigurálása című témakörben talál.](https://technet.microsoft.com/library/hh846766.aspx)

## <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE telepítési megjegyzések
* A Linux Azure-ra való előkészítésével kapcsolatos további tippekért tekintse meg [az általános Linux-telepítési megjegyzéseket](create-upload-generic.md#general-linux-installation-notes) is.
* A VHDX formátum nem támogatott az Azure-ban, csak **a rögzített virtuális merevlemez**.  A lemezt A Hyper-V Manager vagy a convert-vhd parancsmag segítségével virtuális merevlemez-formátumra konvertálhatja.
* A Linux rendszer telepítésekor ajánlott az LVM helyett szabványos partíciókat használni (ez gyakran sok telepítés alapértelmezett). Ezzel elkerülhető, hogy az LVM-név ütközik a klónozott virtuális gépekkel, különösen akkor, ha hibaelhárítási hibaelhárításhoz egy operációsrendszer-lemezhez kell csatlakoztatni egy másik virtuális géphez. [Az LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [a RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szükség esetén adatlemezeken is használható.
* Ne konfiguráljon lapozópartíciót az operációs rendszer lemezén. A Linux-ügynök beállítható úgy, hogy hozzon létre egy cserefájlt az ideiglenes erőforráslemezen.  Erről további információt az alábbi lépésekben talál.
* Az Azure-beli virtuális hálózat minden virtuális d-jének 1 MB-hoz igazított virtuális mérettel kell rendelkeznie. Nyers lemezről virtuális merevlemezre történő konvertáláskor győződjön meg arról, hogy a nyers lemez mérete a konvertálás előtt az 1 MB többszöröse. További információt a [Linux telepítési megjegyzések című témakörben](create-upload-generic.md#general-linux-installation-notes) talál.

## <a name="use-suse-studio"></a>SUSE Studio használata
[A SUSE Studio](http://www.susestudio.com) könnyedén létrehozhatja és kezelheti SLES-lemezeit, és openSUSE-lemezképeket hozhat létre az Azure-ban és a Hyper-V-ben. Ez az ajánlott megközelítés a saját SLES és openSUSE-lemezképek testreszabásához.

A saját virtuális merevlemez létrehozásának alternatívájaként a SUSE byos (Bring Your Own Subscription) képeket is közzétesz az SLES számára a [VMDepot-on.](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf)

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>SUSE Linux Enterprise Server 11 SP4 előkészítése
1. A Hyper-V Manager középső ablaktábláján válassza ki a virtuális gépet.
2. Kattintson a **Csatlakozás gombra** a virtuális gép ablakának megnyitásához.
3. Regisztrálja a SUSE Linux Enterprise rendszert, hogy lehetővé tegye a frissítések letöltését és a csomagok telepítését.
4. Frissítse a rendszert a legújabb javításokkal:
   
        # sudo zypper update
5. Telepítse az Azure Linux-ügynököt az SLES-tárházból:
   
        # sudo zypper install python-azure-agent
6. Ellenőrizze, hogy a waagent "on" beállításra van-e állítva a chkconfig-ban, és ha nem, engedélyezze az automatikus indításhoz:
   
        # sudo chkconfig waagent on
7. Ellenőrizze, hogy fut-e a waagent szolgáltatás, és ha nem, indítsa el: 
   
        # sudo service waagent start
8. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. Ehhez nyissa meg a "/boot/grub/menu.lst" fájlt egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel a következő paramétereket tartalmazza:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Ez biztosítja, hogy az összes konzolüzenet az első soros portra kerüljön, amely segítséget nyújthat az Azure-támogatáshibakeresési problémák megoldásához.
9. Ellenőrizze, hogy a /boot/grub/menu.lst és az /etc/fstab egyaránt hivatkozik-e a lemezre az UUID (by-uuid) segítségével a lemezazonosító (by-id) helyett. 
   
    Lemez UUID-jának bekéselése
   
        # ls /dev/disk/by-uuid/
   
    Ha /dev/disk/by-id/ van használatban, frissítse mind a /boot/grub/menu.lst és /etc/fstab kapcsolót a megfelelő by-uuid értékkel.
   
    Módosítás előtt
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Változás után
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Módosítsa az udev-szabályokat, hogy ne hozzon létre statikus szabályokat az Ethernet-csatoló(k)hoz. Ezek a szabályok problémákat okozhatnak a virtuális gépek Microsoft Azure vagy Hyper-V szolgáltatásban történő klónozása során:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Javasoljuk, hogy módosítsa a fájlt "/etc/sysconfig/network/dhcp", és módosítsa a `DHCLIENT_SET_HOSTNAME` paramétert a következőre:
    
     DHCLIENT_SET_HOSTNAME="nem"
12. Az "/etc/sudoers" rovatban fűzzön megjegyzést a következő sorokhoz, ha vannak ilyenek:
    
    ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```
13. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el.  Ez általában az alapértelmezett.
14. Ne hozzon létre csereterületet az operációs rendszer lemezén.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterület a helyi erőforráslemez, amely csatlakozik a virtuális géphez kiépítése után az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy *ideiglenes* lemez, és lehet, hogy kiüríti, ha a virtuális gép megszüntetése. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) megfelelően módosítsa a következő paramétereket az /etc/waagent.conf fájlban:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resourceResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## MEGJEGYZÉS: állítsa be ezt a kívánt helyre.
15. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Kattintson **a Művelet -> Leállítás a** Hyper-V Kezelőben parancsra. A Linux virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

---
## <a name="prepare-opensuse-131"></a>OpenSUSE 13.1+ előkészítése
1. A Hyper-V Manager középső ablaktábláján válassza ki a virtuális gépet.
2. Kattintson a **Csatlakozás gombra** a virtuális gép ablakának megnyitásához.
3. A héjon futtassa`zypper lr`a parancsot ' '. Ha ez a parancs a következőhöz hasonló kimenetet ad vissza, akkor az adattárak a várt módon vannak konfigurálva – nincs szükség korrekcióra (vegye figyelembe, hogy a verziószámok változhatnak):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Ha a parancs a "Nincs definiált adattárak..." értéket adja vissza. majd a következő parancsokkal adja hozzá ezeket a repókokat:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Ezután ellenőrizheti, hogy az adattárak`zypper lr`hozzá lettek-e adva a ' ' parancs ismételt futtatásával. Abban az esetben, ha a megfelelő frissítési adattárak egyike nincs engedélyezve, engedélyezze a következő paranccsal:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Frissítse a rendszermagot a legújabb elérhető verzióra:
   
        # sudo zypper up kernel-default
   
    Vagy frissíteni a rendszert a legújabb javítások:
   
        # sudo zypper update
5. Telepítse az Azure Linux-ügynök.
   
        # sudo zypper install WALinuxAgent
6. Módosítsa a kernel rendszerindító sort a grub konfigurációban, hogy további kernel paramétereket tartalmazzon az Azure-hoz. Ehhez nyissa meg a "/boot/grub/menu.lst" fájlt egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel a következő paramétereket tartalmazza:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Ez biztosítja, hogy az összes konzolüzenet az első soros portra kerüljön, amely segítséget nyújthat az Azure-támogatáshibakeresési problémák megoldásához. Ezenkívül távolítsa el a következő paramétereket a kernel rendszerindító sorából, ha léteznek ilyenek:
   
     libata.atapi_enabled=0 tartalék=0x1f0,0x8
7. Javasoljuk, hogy módosítsa a fájlt "/etc/sysconfig/network/dhcp", és módosítsa a `DHCLIENT_SET_HOSTNAME` paramétert a következőre:
   
     DHCLIENT_SET_HOSTNAME="nem"
8. **Fontos:** Az "/etc/sudoers" rovatban fűzzön megjegyzést a következő sorokhoz, ha vannak ilyenek:
     
     ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```

9. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van beállítva, hogy indításkor induljon el.  Ez általában az alapértelmezett.
10. Ne hozzon létre csereterületet az operációs rendszer lemezén.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatja a csereterület a helyi erőforráslemez, amely csatlakozik a virtuális géphez kiépítése után az Azure-ban. Vegye figyelembe, hogy a helyi erőforráslemez egy *ideiglenes* lemez, és lehet, hogy kiüríti, ha a virtuális gép megszüntetése. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) megfelelően módosítsa a következő paramétereket az /etc/waagent.conf fájlban:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resourceResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## MEGJEGYZÉS: állítsa be ezt a kívánt helyre.
11. Futtassa a következő parancsokat a virtuális gép kiirtásához, és készítse elő az Azure-beli kiépítésre:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Győződjön meg arról, hogy az Azure Linux-ügynök indításkor fut:
    
        # sudo systemctl enable waagent.service
13. Kattintson **a Művelet -> Leállítás a** Hyper-V Kezelőben parancsra. A Linux virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="next-steps"></a>További lépések
Most már készen áll a SUSE Linux virtuális merevlemez használatára, hogy új virtuális gépeket hozzon létre az Azure-ban. Ha ez az első alkalom, hogy feltölti a .vhd fájlt az Azure-ba, olvassa el a Linux virtuális gép létrehozása egyéni lemezről című [témakört.](upload-vhd.md#option-1-upload-a-vhd)
