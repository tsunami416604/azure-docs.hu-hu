---
title: Az Azure-beli SAP HANA operációs rendszerének frissítése (nagyméretű példányok) | Microsoft Docs
description: Operációs rendszer verziófrissítésének elvégzése SAP HANA Azure-ban (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8485f3474da18e052bc0eab6c053be084ef884a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192416"
---
# <a name="operating-system-upgrade"></a>Operációs rendszer frissítése
Ez a dokumentum ismerteti az operációs rendszer frissítésének részleteit a HANA nagyméretű példányain.

>[!NOTE]
>Az operációs rendszer verziófrissítése az ügyfél felelőssége, a Microsoft üzemeltetési támogatási szolgálata végigvezeti Önt a frissítés során megtekinthető kulcsfontosságú területeken. A frissítés megtervezése előtt tekintse meg az operációs rendszer gyártóját is.

A HLI egység kiépítés során a Microsoft Operations csapata telepíti az operációs rendszert.
Az idő múlásával meg kell őriznie az operációs rendszert (például: javítás, hangolás, frissítés stb.) a HLI egységen.

Mielőtt megkezdené az operációs rendszer jelentős módosításait (például a SP1 verzióról SP2-re), forduljon a Microsoft Operations csapathoz egy támogatási jegy megnyitásával.

Belefoglalás a jegybe:

* A HLI-előfizetés azonosítója.
* A kiszolgáló neve.
* Az alkalmazni kívánt javítási szint.
* A módosítás megtervezésének dátuma. 

Javasoljuk, hogy ezt a jegyet legalább egy héttel a kívánt frissítési dátum előtt nyissa meg, mert a műveleti csapat ellenőrzi, hogy szükség van-e belső vezérlőprogram-frissítésre a kiszolgáló paneljén.


A különböző Linux-verziókkal rendelkező különböző SAP HANA verziók támogatási mátrixa: [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Ismert problémák

A frissítés során néhány gyakori ismert probléma a következő:
- A II. típusú SKU-ban az operációs rendszer frissítése után törlődnek a szoftver Foundation szoftverei (a strukturális alapok). Az operációs rendszer frissítése után újra kell telepítenie a kompatibilis strukturális rendszereket.
- Az Ethernet-kártya illesztőprogramjai (ENIC és FNIC) visszaállnak a régebbi verzióra. A frissítés után újra kell telepítenie az illesztőprogramok kompatibilis verzióját.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA nagyméretű példány (I) ajánlott konfiguráció

Az operációs rendszer konfigurációja a javítások, a Rendszerfrissítések és az ügyfelek által végzett módosítások miatt idővel eltérhet az ajánlott beállításoktól. A Microsoft emellett a meglévő rendszerekhez szükséges frissítéseket is azonosítja, így biztosítva, hogy optimálisan legyenek konfigurálva a legjobb teljesítményhez és rugalmassághoz. A következő utasítások a hálózati teljesítményre, a rendszer stabilitására és az optimális HANA-teljesítményre vonatkozó javaslatokat ismertetik.

### <a name="compatible-enicfnic-driver-versions"></a>Kompatibilis eNIC-/fNIC-illesztőprogram-verziók
  A megfelelő hálózati teljesítmény és a rendszer stabilitásának biztosítása érdekében javasoljuk, hogy a eNIC és a fNIC illesztőprogramok operációs rendszerre vonatkozó megfelelő verzióját a következő kompatibilitási táblázatban látható módon telepítse. A kiszolgálók a kompatibilis verziókkal rendelkező ügyfeleknek érkeznek. Vegye figyelembe, hogy bizonyos esetekben az operációs rendszer/kernel javítása során az illesztőprogramok visszaállíthatók az alapértelmezett illesztőprogram-verzióra. Győződjön meg arról, hogy az illesztőprogram megfelelő verziója az operációs rendszer/kernel javítási műveleteit futtatja.
       
      
  |  Operációs rendszer szállítója    |  Operációs rendszer csomagjának verziója     |  Belső vezérlőprogram verziója  |  eNIC-illesztőprogram |  fNIC-illesztőprogram | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3 i           |  2.3.0.47    |   2.0.0.54   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   Red Hat     |  RHEL 7,2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Az illesztőprogram verziófrissítésére és a régi rpm-csomagok tisztítására vonatkozó parancsok

#### <a name="command-to-check-existing-installed-drivers"></a>A meglévő telepített illesztőprogramok vizsgálatára szolgáló parancs
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Meglévő eNIC/fNIC rpm törlése
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>Az ajánlott eNIC-/fNIC-illesztőprogram-csomagok telepítése
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>A telepítés megerősítésére szolgáló parancsok
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB-frissítési hiba
Az Azure HANA nagyméretű példányain (I. típus) lévő SAP nem indítható állapotban lehet a frissítés után. Az alábbi eljárás javítja ezt a problémát.
#### <a name="execution-steps"></a>Végrehajtási lépések


*   Parancs `multipath -ll` végrehajtása.
*   Szerezze be a logikai egység AZONOSÍTÓját, amelynek mérete körülbelül 50G, vagy használja a parancsot:`fdisk -l | grep mapper`
*   Fájl `/etc/default/grub_installdevice` frissítése a sorral `/dev/mapper/<LUN ID>`. Példa:/dev/Mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>A LUN-azonosító a kiszolgálóról a kiszolgálóra változik.


### <a name="disable-edac"></a>EDAC letiltása 
   A hibák észlelése és javítása (EDAC) modul segítséget nyújt a memóriahiba észlelésében és kijavításában. A SAP HANA on Azure Large Instances (I. típus) mögöttes hardver azonban már ugyanazt a funkciót is végrehajtja. Ha ugyanaz a funkció engedélyezve van a hardver-és operációs rendszer (OS) szintjén, az ütközéseket okozhat, és a kiszolgáló alkalmi, nem tervezett leállásához vezethet. Ezért javasoljuk, hogy tiltsa le a modult az operációs rendszerből.

#### <a name="execution-steps"></a>Végrehajtási lépések

* Ellenőrizze, hogy a EDAC modul engedélyezve van-e. Ha az alábbi parancs kimenetet ad vissza, az azt jelenti, hogy a modul engedélyezve van. 
```
lsmod | grep -i edac 
```
* Tiltsa le a modulokat a következő sorok hozzáfűzésével a fájlhoz:`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
A módosítások érvénybe lépéséhez újraindítás szükséges. Futtassa `lsmod` a parancsot, és ellenőrizze, hogy a modul nem található-e a kimenetben.


### <a name="kernel-parameters"></a>Kernel-paraméterek
   Győződjön meg `transparent_hugepage` `numa_balancing` `processor.max_cstate`arról, hogy a, a, `ignore_ce` a `intel_idle.max_cstate` és a megfelelő beállítás van alkalmazva.

* intel_idle. max_cstate = 1
* processzor. max_cstate = 1
* transparent_hugepage = soha
* numa_balancing = letiltás
* MCE = ignore_ce


#### <a name="execution-steps"></a>Végrehajtási lépések

* Adja hozzá ezeket a paramétereket `GRB_CMDLINE_LINUX` a fájl sorához`/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Hozzon létre egy új grub-fájlt.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Rendszer újraindítása.


## <a name="next-steps"></a>További lépések
- Tekintse át a [biztonsági mentést és a visszaállítást](hana-overview-high-availability-disaster-recovery.md) az operációs rendszer biztonsági mentése I SKU osztályban.
- Tekintse át az [operációs rendszer biztonsági másolatát](os-backup-type-ii-skus.md) , amely a 2. típusú SKU osztályhoz tartozó 3. típusú bélyegzőket használja.
