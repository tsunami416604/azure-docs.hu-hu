---
title: Az SAP HANA operációs rendszerfrissítése az Azure-ban (nagy példányok)| Microsoft dokumentumok
description: Operációs rendszer-frissítés végrehajtása az SAP HANA számára az Azure-ban (nagy példányok)
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
ms.openlocfilehash: 3a0a5d39a7cb2162186291ea534a623ef45c40d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675626"
---
# <a name="operating-system-upgrade"></a>Operációs rendszer frissítése
Ez a dokumentum ismerteti az operációs rendszer frissítései a HANA nagy példányok részleteit.

>[!NOTE]
>Az operációs rendszer frissítése az ügyfél felelőssége, a Microsoft műveleti támogatása elvezetheti Önt a frissítés során figyelni kívánt kulcsfontosságú területekre. A frissítés megtervezése előtt forduljon az operációs rendszer forgalmazójához is.

A HLI-egység kiépítése során a Microsoft műveleti csapata telepíti az operációs rendszert.
Az idő múlásával fenn kell tartania az operációs rendszert (példa: Javítás, hangolás, frissítés stb.) a HLI egységen.

Mielőtt jelentős módosításokat végezne az operációs rendszeren (például az SP1 sp1-es verziót az SP2 szervizcsomagra), a microsoftos műveleti csapathoz kell fordulnia egy támogatási jegy megnyitásával, hogy megtekinthes dista programot.

Tartalmazza a jegyet:

* A HLI-előfizetés azonosítója.
* A kiszolgáló neve.
* A alkalmazni kívánt javítási szint.
* A módosítás megtervezésének dátuma. 

Javasoljuk, hogy legalább egy héttel a kívánt frissítési dátum előtt nyissa meg ezt a jegyet, mivel az Operations csapata ellenőrzi, hogy szükség lesz-e firmware-frissítésre a kiszolgálópanelen.


A különböző SAP HANA-verziók különböző Linux-verziókkal rendelkező támogatási mátrixáról az [SAP Note #2235581.](https://launchpad.support.sap.com/#/notes/2235581)


## <a name="known-issues"></a>Ismert problémák

A frissítés során az alábbi néhány gyakori probléma található:
- A Termékváltozat II típusú termékváltozatának termékváltozatán a szoftveralapszoftver (SFS) az operációs rendszer frissítése után törlődik. Az operációs rendszer frissítése után újra kell telepítenie a kompatibilis SFS-t.
- Az Ethernet-kártya-illesztőprogramok (ENIC és FNIC) visszaálltak a régebbi verzióra. A frissítés után újra kell telepítenie az illesztőprogramok kompatibilis verzióját.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA nagy példány (I. típus) ajánlott konfigurációja

Az operációs rendszer konfigurációja idővel elsodródhat az ajánlott beállításoktól a javítás, a rendszerfrissítések és az ügyfelek által végrehajtott módosítások miatt. Emellett a Microsoft azonosítja a meglévő rendszerekhez szükséges frissítéseket annak érdekében, hogy optimálisan legyenek konfigurálva a legjobb teljesítmény és rugalmasság érdekében. Az alábbi utasítások ismertetik a hálózati teljesítményt, a rendszer stabilitását és az optimális HANA-teljesítményt kezelő ajánlásokat.

### <a name="compatible-enicfnic-driver-versions"></a>Kompatibilis eNIC/fNIC illesztőprogram-verziók
  A megfelelő hálózati teljesítmény és a rendszer stabilitása érdekében ajánlott biztosítani, hogy az eNIC és fNIC-illesztőprogramok operációs rendszerspecifikus megfelelő verziója a következő kompatibilitási táblázatban látható módon legyen telepítve. A kiszolgálókat kompatibilis verziókkal szállítjuk az ügyfeleknek. Vegye figyelembe, hogy bizonyos esetekben az operációs rendszer/kernel javítása során az illesztőprogramok visszaállíthatók az alapértelmezett illesztőprogram-verziókra. Győződjön meg arról, hogy a megfelelő illesztőprogram-verzió az operációs rendszer/kernel javítás idoszaka után fut.
       
      
  |  Operációs rendszer szállítója    |  Operációs rendszer csomagjának verziója     |  Belső vezérlőprogram verziója  |  eNIC illesztőprogram |  fNIC illesztőprogram | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   Suse        |  SLES 12 SP2            |   3.1.3h.           |  2.3.0.40    |   1.6.0.34   |
  |   Suse        |  SLES 12 SP3            |   3.1.3h.           |  2.3.0.44    |   1.6.0.36   |
  |   Red Hat     |  RHEL 7,2               |   3.1.3h.           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Parancsok az illesztőprogram frissítéséhez és a régi fordulatszám-csomagok tisztításához
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>Megerősítandó parancsok
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB frissítési hiba
SAP az Azure HANA nagy példányok (I. típusú) lehet egy nem indítható állapotban frissítés után. Az alábbi eljárás megoldja ezt a problémát.
#### <a name="execution-steps"></a>Végrehajtási lépések


*   Végrehajtás `multipath -ll` parancs.
*   A körülbelül 50 G méretű LUN-azonosító beszerezni, vagy használja a parancsot:`fdisk -l | grep mapper`
*   A `/etc/default/grub_installdevice` fájl `/dev/mapper/<LUN ID>`frissítése a sorával. Példa: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>A lun-azonosító kiszolgálónként változik.


### <a name="disable-edac"></a>Edac letiltása 
   A Hibaészlelés és -javítás (EDAC) modul segít a memóriahibák észlelésében és javításában. Azonban az sap HANA alapjául szolgáló hardver az Azure nagy példányok (I. típus) már ugyanazt a funkciót. Ha ugyanaz a szolgáltatás engedélyezve van a hardver és az operációs rendszer (OS) szintjén, az ütközéseket okozhat, és a kiszolgáló alkalmi, nem tervezett leállásához vezethet. Ezért ajánlott letiltani a modult az operációs rendszerből.

#### <a name="execution-steps"></a>Végrehajtási lépések

* Ellenőrizze, hogy az EDAC modul engedélyezve van-e. Ha egy kimenet az alábbi parancsban ad vissza, az azt jelenti, hogy a modul engedélyezve van. 
```
lsmod | grep -i edac 
```
* A modulok letiltása a következő sorok fájlhoz fűzésével`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
A módosítások életbe lépéséhez újraindításszükséges. Végrehajtás `lsmod` parancs és ellenőrizze, hogy a modul nem jelenik meg a kimenetben.


### <a name="kernel-parameters"></a>Kernel paraméterei
   Ellenőrizze, hogy a `transparent_hugepage` `numa_balancing`program `processor.max_cstate` `ignore_ce` megfelelően adja-e meg a , a , és `intel_idle.max_cstate` alkalmazza a beállításokat.

* intel_idle,max_cstate=1
* processzor.max_cstate=1
* transparent_hugepage=soha
* numa_balancing=letiltás
* mce=ignore_ce


#### <a name="execution-steps"></a>Végrehajtási lépések

* Adja hozzá ezeket `GRB_CMDLINE_LINUX` a paramétereket a fájl sorához`/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Hozzon létre egy új grub fájlt.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Indítsa újra a rendszert.


## <a name="next-steps"></a>További lépések
- Tekintse át a [biztonsági mentést és](hana-overview-high-availability-disaster-recovery.md) a visszaállítást az I. típusú termékváltozat-osztály biztonsági mentéséhez.
- Lásd [az operációs rendszer biztonsági mentését a 3.](os-backup-type-ii-skus.md)
