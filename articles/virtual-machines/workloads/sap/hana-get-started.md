---
title: 'Gyors üzembe helyezés: Az Egypéldányos SAP HANA Azure virtuális gépek manuális telepítésére |} Microsoft Docs'
description: Gyors üzembe helyezési útmutató az Egypéldányos SAP HANA manuális telepítése Azure virtuális gépeken
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 14cdb2d3e433da38913ffa29b3b150bdb264278b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658706"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Gyors üzembe helyezés: Egypéldányos SAP HANA Azure virtuális gépeken manuális telepítése
## <a name="introduction"></a>Bevezetés
Ez az útmutató segítséget nyújt a SAP NetWeaver 7.5 és SAP HANA 1.0 SP12 telepítésekor manuálisan egy egypéldányos SAP HANA az Azure virtuális gépek (VM) beállítása. Ez az útmutató elsősorban SAP HANA Azure üzembe helyezni. SAP dokumentáció nem helyettesíti. 

>[!Note]
>Ez az útmutató ismerteti a SAP HANA Azure virtuális gépeken történő központi telepítéséhez. SAP HANA HANA nagy példányok történő telepítésével kapcsolatos információkért lásd: [SAP használata az Azure virtuális gépek (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy Ön ismeri a szolgáltató (IaaS) alapjait, az ilyen infrastruktúra:
 * Ügyfélszoftverek központi telepítése a virtuális gépek vagy virtuális hálózatok az Azure portálon vagy a PowerShell segítségével.
 * Az Azure platformfüggetlen parancssori felület (CLI), többek között a sablonok JavaScript Object Notation (JSON) lehetőséget.

Ez az útmutató feltételezi, hogy jártas:
* SAP HANA és az SAP NetWeaver és a helyszíni telepítéséről.
* Telepítése, és működési SAP HANA és az Azure-on az SAP alkalmazáspéldányok.
* A következő fogalmak és eljárások:
   * SAP központi telepítésének tervezésében az Azure, beleértve az Azure virtuális hálózat tervezése és az Azure Storage használatáért. Lásd: [SAP NetWeaver az Azure virtuális gépek (VM) - tervezési és megvalósítási útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Központi telepítési alapelvei és az Azure virtuális gépek telepítésének módja. Lásd: [Azure virtuális gépek telepítése az SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Magas rendelkezésre állású SAP NetWeaver Asc (ABAP SAP központi szolgáltatások), a SCS (SAP központi szolgáltatások) és az Azure-on (kiértékelése fogadását rendezésének) felhasználók. Lásd: [magas rendelkezésre állás a SAP NetWeaver Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Megtudhatja, hogyan egy ASC/SCS Azure multi-SID telepítését, ami a hatékonyság növelése érdekében. Lásd: [SAP NetWeaver multi-SID-konfiguráció létrehozása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * SAP NetWeaver futó alapelvek alapján Linux-alapú virtuális gépek Azure-ban. Lásd: [SAP NetWeaver Microsoft Azure SUSE Linux virtuális gépeken futó](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Ez az útmutató az Azure virtuális gépek és a részletek a Linux beállításait ismerteti a Linux virtuális gépek az Azure storage-lemezek megfelelően csatlakoztatni.

Most Azure virtuális gépek hitelesített SAP által SAP HANA méretezett konfigurációkat. Az SAP HANA-munkaterhelések kibővített konfigurációkat még nem támogatottak. SAP HANA magas rendelkezésre állású azokban az esetekben a méretezett konfigurációk, lásd: [SAP HANA Azure virtuális gépek (VM) a magas rendelkezésre állású](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

Ha egy SAP HANA-példány vagy S/4HANA vagy BW/4HANA rendszert telepíteni nagyon gyorsan időben történő-lekérésére törekszik, fontolja meg a használatát [felhő készülék teszteléshez](http://cal.sap.com). Találhat, például SAP-CAL az Azure-on keresztül az S/4HANA rendszer telepítésével kapcsolatos [Ez az útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Szeretné, hogy szüksége az Azure-előfizetés és az egy SAP felhasználók felhő készülék teszteléshez regisztrálhatók.

## <a name="additional-resources"></a>További források
### <a name="sap-hana-backup"></a>SAP HANA biztonsági mentése
Azure virtuális gépeken SAP HANA-adatbázisok biztonsági mentésével információkért lásd:
* [Biztonsági mentési útmutató SAP Hana Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [A fájl szintjén SAP HANA Azure biztonsági mentés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [A storage-pillanatfelvételekkel alapján SAP HANA biztonsági mentése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>SAP felhő készülék könyvtára
S/4HANA és BW/4HANA telepítési felhő készülék teszteléshez használatával további információkért lásd: [telepítése SAP S/4HANA vagy a Microsoft Azure BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA-támogatott operációs rendszerek
SAP HANA-támogatott operációs rendszereken információkért lásd: [SAP támogatási Megjegyzés #2235581 - SAP HANA: támogatott operációs rendszerek](https://launchpad.support.sap.com/#/notes/2235581/E). Azure virtuális gépek támogatják az említett operációs rendszerektől csak egy részét. A következő operációs rendszerek központi telepítése az Azure-on SAP HANA támogatottak: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

SAP termékkel kapcsolatos további dokumentáció SAP HANA és a különböző Linux operációs rendszert lásd:

* [SAP támogatási Megjegyzés #171356 - SAP szoftverek Linux: általános információk](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP támogatási Megjegyzés #1944799 - SAP HANA-irányelvek SLES operációs rendszer telepítéséhez](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [SAP támogatási Megjegyzés #2205917 - SAP HANA DB ajánlott az operációs rendszer beállításait a SLES 12 SAP-alkalmazásokból](https://launchpad.support.sap.com/#/notes/2205917/E)
* [SAP támogatási Megjegyzés #1984787 - SUSE Linux Enterprise Server 12: Telepítési jegyzetek](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP támogatási Megjegyzés #1391070 - Linux UUID megoldások](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP támogatási Megjegyzés #2009879 - SAP HANA-irányelvek Red Hat Enterprise Linux (RHEL) operációs rendszerhez](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - SAP HANA DB: RHEL 7 operációs rendszer ajánlott beállításai](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>SAP figyelése az Azure-ban
Az Azure-ban figyelési SAP kapcsolatos információkért lásd:

* [SAP Megjegyzés 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Ez a Megjegyzés ismerteti, amelyek SAP "bővített figyelés" Linux virtuális gépek Azure-on. 
* [SAP Megjegyzés 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Megjegyzés vehető fel Linux SAPOSCOL kapcsolatos tudnivalókat ismerteti. 
* [SAP Megjegyzés 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Ez a Megjegyzés SAP alapvető figyelési metrikákat a Microsoft Azure ismerteti.

### <a name="azure-vm-types"></a>Az Azure Virtuálisgép-típusokon
Az Azure Virtuálisgép-típusokon és munkaterhelés SAP-támogatott forgatókönyvek SAP HANA használt ismertetett [SAP hitelesített IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

SAP NetWeaver vagy az S/4HANA alkalmazásréteg SAP által hitelesített Azure Virtuálisgép-típusokon ismertetett [SAP Megjegyzés 1928533 - Azure SAP-alkalmazásokból: támogatott termékek és az Azure virtuális gép típusok](https://launchpad.support.sap.com/#/notes/1928533/E).

>[!Note]
>SAP-Linux-Azure integrációs csak Azure Resource Manager és a klasszikus telepítési modell nem támogatott. 

## <a name="manual-installation-of-sap-hana"></a>SAP HANA manuális telepítése
Ez az útmutató ismerteti, hogyan történő manuális telepítés SAP HANA Azure virtuális gépek két különböző módon:

* A "telepítés adatbázispéldány" lépésben elosztott NetWeaver telepítés részeként SAP szoftver kiépítés Manager (SWPM) használatával
* Az SAP HANA használatával adatbázis-életciklus manager eszközt, HDBLCM és majd a NetWeaver telepítése

Is segítségével SWPM minden összetevő (SAP HANA, a SAP-kiszolgáló és az ASC példány) telepítése egy egyetlen virtuális gépre, ez a [SAP HANA blog közlemény](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Ez a beállítás nem gyors üzembe helyezési útmutatóban ismertetett, de figyelembe kell venni a problémákat változatlan marad.

Telepítés megkezdése előtt javasoljuk, hogy olvassa el a "előkészítése Azure virtuális gépek SAP HANA manuális telepítéséhez" szakasz az útmutató későbbi részében. Ezzel megakadályozhatja több alapvető, csak egy alapértelmezett Azure Virtuálisgép-konfiguráció használatakor előforduló hibákat.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Fontos lépések az SAP SWPM használatakor SAP HANA-telepítés
Ez a szakasz a manuális, Egypéldányos SAP HANA-telepítés legfontosabb lépéseit sorolja fel, SAP SWPM SAP NetWeaver 7.5 elosztott telepítés használatakor. Az egyes lépéseket képernyőképeket az útmutató részletesen ismerteti.

1. Hozzon létre egy Azure virtuális hálózat, amely két teszt virtuális gépek tartalmazza.
2. A két Azure virtuális gépeken futó operációs rendszerek (a példánkban SUSE Linux Enterprise Server (SLES) és az SAP alkalmazások 12 SP1 SLES), az Azure Resource Manager modell szerint telepítheti.
3. Két Azure standard vagy prémium szintű storage lemezek (például 75 GB-os vagy 500 GB-os lemezt) csatlakoztatni a virtuális gép kiszolgáló.
4. Prémium szintű storage lemezeket a HANA-adatbázis-kiszolgálóhoz csatlakoztatni virtuális gép. További információkért lásd: a "Lemez beállítása" című szakaszban található útmutatóban.
5. Méret vagy átviteli követelményeitől függően több lemezt csatolni, és hozza létre a csíkozott kötetek vagy logikai kötetkezelés, vagy egy több-eszközök felügyeleti eszköz (MDADM) használatával az operációs rendszer szintjén, a virtuális Gépen belül.
6. A csatlakoztatott lemezek vagy logikai kötetek XFS fájlrendszerek hozható létre.
7. Csatlakoztassa az új XFS fájlrendszer az operációs rendszer szintjén. A SAP szoftver egy fájlrendszert használjon. Például a /sapmnt directory és a biztonsági mentések, használja a többi fájlrendszer. Az SAP HANA-adatbázis-kiszolgálón csatlakoztassa a XFS fájlrendszerek /hana és /usr/sap a prémium szintű storage lemezek. Ez a folyamat szükséges a legfelső szintű fájlrendszer, amely nem nagy Linux Azure virtuális gépeken, nehogy betelőben.
8. Adja meg a helyi IP-címek a teszt virtuális gépek a/Etc/Hosts fájlt.
9. Adja meg a **nofail** a/etc/fstab fájl paramétere.
10. Linux kernel paraméterek használata a Linux operációsrendszer-kiadásnak megfelelően beállítani. További információkért lásd: a megfelelő SAP tudomásul veszi, hogy milyen HANA és a "Kernel paraméterek" szakasz az útmutatóban.
11. A lapozóterület bővítése.
12. Szükség esetén telepíti egy grafikus asztal a teszteléshez használt virtuális gépek. Ellenkező esetben használja a távoli SAPinst telepítés.
13. Töltse le az SAP szoftvert az SAP szolgáltatás piactérről.
14. Telepítse a SAP ASC példányát a virtuális gép alkalmazáskiszolgálóra.
15. Az NFS ossza meg a /sapmnt directory, a teszt virtuális gépek között. A virtuális gép kiszolgáló, az NFS-kiszolgáló.
16. Telepítse az adatbázispéldány fölött, HANA, beleértve a virtuális gép DB kiszolgálón SWPM használatával.
17. A virtuális gép kiszolgáló az elsődleges alkalmazáskiszolgálón (szolgáltatói CÍMEKET) telepíthető.
18. Indítsa el az SAP Management Console (SAP MC). Csatlakozzon például SAP grafikus felhasználói felületen vagy HANA Studio.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Fontos lépések az SAP HANA-telepítés HDBLCM használatakor
Ez a szakasz a manuális, Egypéldányos SAP HANA-telepítés legfontosabb lépéseit sorolja fel, SAP HDBLCM SAP NetWeaver 7.5 elosztott telepítés használatakor. Az egyes lépéseket részletesen további képernyőfelvételeken ebben az útmutatóban.

1. Hozzon létre egy Azure virtuális hálózat, amely két teszt virtuális gépek tartalmazza.
2. Operációs rendszerek (a példánkban SLES és SLES SAP alkalmazások 12 SP1 rendszerhez) az Azure Resource Manager modellt megfelelően két Azure virtuális gépek telepítéséhez.
3. Két Azure standard vagy prémium szintű storage lemezek (például 75 GB-os vagy 500 GB-os lemezeken) csatolása a virtuális gép alkalmazáskiszolgálóra.
4. Prémium szintű storage lemezeket a HANA-adatbázis-kiszolgálóhoz csatlakoztatni virtuális gép. További információkért lásd: a "Lemez beállítása" című szakaszban található útmutatóban.
5. Méret vagy átviteli követelményeitől függően több lemezt csatolni, és hozzon létre csíkozott kötetek vagy logikai kötetkezelés, vagy egy több-eszközök felügyeleti eszköz (MDADM) használatával az operációs rendszer szintjén, a virtuális Gépen belül.
6. A csatlakoztatott lemezek vagy logikai kötetek XFS fájlrendszerek hozható létre.
7. Csatlakoztassa az új XFS fájlrendszer az operációs rendszer szintjén. A SAP szoftver egy fájlrendszer használja, és a /sapmnt directory és a biztonsági mentést, a másik egy például használja. Az SAP HANA-adatbázis-kiszolgálón csatlakoztassa a XFS fájlrendszerek /hana és /usr/sap a prémium szintű storage lemezek. Ez a folyamat szükség, a legfelső szintű fájlrendszer, amely nem nagy Linux Azure virtuális gépeken, kitöltse megelőzése érdekében.
8. Adja meg a helyi IP-címek a teszt virtuális gépek a/Etc/Hosts fájlt.
9. Adja meg a **nofail** a/etc/fstab fájl paramétere.
10. Használja a Linux operációsrendszer-kiadásnak megfelelő kernel paraméterek beállítása. További információkért lásd: a megfelelő SAP tudomásul veszi, hogy milyen HANA és a "Kernel paraméterek" szakasz az útmutatóban.
11. A lapozóterület bővítése.
12. Szükség esetén telepíti egy grafikus asztal a teszteléshez használt virtuális gépek. Ellenkező esetben használja a távoli SAPinst telepítés.
13. Töltse le az SAP szoftvert az SAP szolgáltatás piactérről.
14. Hozzon létre egy csoportot, sapsys, a virtuális gép HANA DB kiszolgálón azonosító 1001 csoporttal.
15. Az adatbázis-kiszolgálót VM SAP HANA telepítése HANA adatbázis életciklusát kezelő (HDBLCM) segítségével.
16. Telepítse a SAP ASC példányát a virtuális gép alkalmazáskiszolgálóra.
17. Az NFS ossza meg a /sapmnt directory, a teszt virtuális gépek között. A virtuális gép kiszolgáló, az NFS-kiszolgáló.
18. Telepítse az adatbázispéldány fölött, HANA, beleértve a virtuális gép HANA DB kiszolgálón SWPM használatával.
19. A virtuális gép kiszolgáló az elsődleges alkalmazáskiszolgálón (szolgáltatói CÍMEKET) telepíthető.
20. Indítsa el a SAP MC. SAP grafikus felhasználói felületének vagy HANA Studio keresztül csatlakozni.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Azure virtuális gépek SAP HANA kézi telepítésének előkészítése
Ez a szakasz témakörei a következők:

* Operációs rendszer frissítése érdekében
* Lemez beállítása
* Kernel-paraméterek
* fájlrendszer
* A/etc/hosts fájlt
* A/etc/fstab fájl

### <a name="os-updates"></a>Operációs rendszer frissítése érdekében
Ellenőrizze a Linux operációs rendszert futtató frissítések és javítások, további szoftverek telepítése előtt. A javítás telepítésével, valószínűleg létre tudja a támogatási szolgálat hívása elkerülése érdekében.

Győződjön meg arról, hogy használ:
* SUSE Linux Enterprise Server SAP-alkalmazásokból.
* Red Hat Enterprise Linux az SAP-alkalmazásokból vagy a Red Hat Enterprise Linux SAP Hana. 

Ha még nem tette meg, regisztráljon az operációs rendszer telepítése a Linux-előfizetés a Linux szállítótól. Vegye figyelembe, hogy SUSE OS képek SAP-alkalmazások, amelyek már tartalmaznak, és amelynek automatikusan regisztrálva.

Íme egy példa segítségével ellenőrzése a rendelkezésre álló javítások SUSE Linux a **zypper** parancs:

 `sudo zypper list-patches`

Attól függően, hogy milyen típusú a probléma kategória és súlyosság szerint besorolt javítások. A gyakran használt kategória értékei: **biztonsági**, **ajánlott**, **választható**, **szolgáltatás**, **dokumentum**, vagy **yast**.
A gyakran használt súlyossági értékei: **kritikus**, **fontos**, **mérsékelt**, **alacsony**, vagy **Meghatározatlan**.

A **zypper** parancs csak megkeresi a telepített csomagokat a frissítéseket. Például használhatja ezt a parancsot:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

A paraméter is hozzáadhat `--dry-run` tesztelése a frissítés a rendszer ténylegesen frissítése nélkül.


### <a name="disk-setup"></a>Lemez beállítása
A legfelső szintű egy Linux virtuális Gépet az Azure rendszerben méret korlátozás van érvényben. Ezért fontos további lemezterületet csatlakoztatása az Azure virtuális gép SAP futtatásához. A SAP application server Azure virtuális gépeken az Azure standard szintű tárolást lemezek használatát is elegendő lehet. Azonban SAP HANA DBMS Azure virtuális gépek, a prémium szintű Azure Storage-lemezek üzemi és tesztkörnyezetben megvalósítások használata kötelező.

Alapján a [SAP HANA TDI tárhellyel kapcsolatos követelmények](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), a javaslat a következő prémium szintű Azure Storage-konfiguráció: 

| VIRTUÁLIS GÉP TERMÉKVÁLTOZAT | RAM |  / hana/adatainak és naplókönyvtárainak/hana / <br /> csíkozott LVM vagy MDADM | / hana/megosztott | / root kötet | / usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

A javasolt lemez konfigurációját a HANA adatmennyiség és a naplózási kötet kerülnek, ugyanazokat a prémium szintű Azure storage lemezek LVM vagy MDADM csíkozott. Nincs szükség minden RAID redundancia szint meghatározása, mert prémium szintű Azure Storage tartja a három képet a lemezek a redundancia érdekében. Győződjön meg arról, hogy konfigurálja-e elegendő szabad lemezterület, tekintse meg a [SAP HANA TDI tárhellyel kapcsolatos követelmények](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) és [SAP HANA-kiszolgáló telepítési és frissítési útmutató](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). A különböző virtuális merevlemez (VHD) átviteli kötetek különböző a prémium szintű Azure storage lemezt is megfontolnia részletes ismertetését lásd: [prémium szintű Storage nagy teljesítményű és a virtuális gépek felügyelt lemezek](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

További premium tárolólemezek adhat hozzá a HANA adatbázis-kezelő virtuális gépeket, adatbázis vagy a tranzakciós napló biztonsági mentések tárolására.

A csíkozást konfigurálásához használt két fő eszközökkel kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Szoftveres RAID Linux konfigurálása](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [A Linux virtuális gép az Azure-ban LVM konfigurálása](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

A lemez csatolása a vendég operációs rendszer Linux futtató Azure virtuális gépek további információkért lásd: [vehet fel lemezt a Linux virtuális gép](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Prémium szintű Storage megadhatja módok gyorsítótárazás lemezre. Csíkozott állítsa az /hana/data és /hana/log lemez gyorsítótárazás le kell tiltani. A többi kötet (lemezeket), a gyorsítótár üzemmódban kell megadni **ReadOnly**.

További információkért lásd: [prémium szintű Storage: nagy teljesítményű tárolást Azure virtuális gépek terheléseihez](../../windows/premium-storage.md).

A minta JSON sablonok találja a virtuális gépek létrehozásához, [Azure gyors üzembe helyezési sablonokat](https://github.com/Azure/azure-quickstart-templates).
A virtuálisgép-egyszerű-sles sablon egy sablont. Ez magában foglalja a tárolás részén, az adatok 100 GB-os-lemezzel. Ez a sablon alapjául használható. A sablon a megadott konfiguráció módosíthatja.

>[!Note]
>Fontos, hogy UUID használatával, ahogy az az Azure storage lemez csatolása [SAP NetWeaver futtató Azure virtuális gépeken Microsoft SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A tesztkörnyezetben két Azure standard tárolólemezek lenne csatlakoztatva SAP alkalmazáskiszolgálóra VM, az alábbi képernyőfelvételen látható módon. Egy olyan lemezt telepítését (beleértve a NetWeaver 7.5, a SAP grafikus felhasználói felület és az SAP HANA) SAP szoftver tárolódnak. A második lemezről biztosítani, hogy elég szabad terület áll rendelkezésre további követelményeket (például a biztonsági mentés és a vizsgálati adatokat), és a /sapmnt könyvtár (Ez azt jelenti, hogy SAP-profilok) az azonos SAP fekvő tartozó összes virtuális gép közötti megosztását.

![SAP HANA app kiszolgáló lemezek ablakot, két adatlemezek és azok mérete](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Kernel-paraméterek
SAP HANA szükséges adott Linux kernel beállítások, amelyek nem részei a normál Azure katalógusában lemezképeket, és manuálisan kell beállítani. Attól függően, hogy a hogy SUSE vagy a Red Hat a paraméterek eltérő lehet. A korábban felsorolt SAP megjegyzéseket adhat paraméterek kapcsolatos információkat. A képernyőfelvételeken látható a SUSE Linux 12 SP1 lett megadva. 

Az SAP alkalmazások 12 GA SLES és az SAP alkalmazások 12 SP1 SLES rendelkezik egy új eszköz **beállított adm**, amely lecseréli a régi **sapconf** eszköz. A speciális SAP HANA-profil érhető el **beállított adm**. A rendszer hangolására SAP Hana, legfelső szintű felhasználóként írja be a következő:

   `tuned-adm profile sap-hana`

További információ **beállított adm**, tekintse meg a [SUSE dokumentációjában az beállított adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Az alábbi képernyőfelvételen látható hogyan **beállított adm** megváltozott a `transparent_hugepage` és `numa_balancing` értékek, a szükséges SAP HANA-beállítások szerint.

![A beállított adm eszköz megváltoztatja az értékeket szükség SAP HANA-beállítások szerint](./media/hana-get-started/image005.jpg)

Az SAP HANA kernel beállítások véglegesítéséhez használja **grub2** a SLES 12 rendszert. További információ **grub2**, navigáljon a [konfigurációs fájlstruktúra](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) a SUSE dokumentáció része.

Az alábbi képernyőfelvételen látható, hogyan a kernel beállítások volt módosítani a konfigurációs fájlban, és majd lefordítják azt használó **grub2-mkconfig**:

![Kernel beállításait módosítani a konfigurációs fájlban, és fordítva grub2-mkconfig használatával](./media/hana-get-started/image006.jpg)

Egy másik lehetőség egy YaST használatával módosíthatja a beállításokat és a **Rendszertöltőt** > **Kernel paraméterek** beállítások:

![A Kernel paraméterek beállítások lapján YaST Rendszertöltőt](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>fájlrendszer
Az alábbi képernyőfelvételen látható két fájlrendszereket, a két csatolt Azure standard tárolólemezeket fölött SAP alkalmazáskiszolgálón VM létrehozott. Mindkét fájlrendszerek XFS típusú, és /sapdata és /sapsoftware csatlakoztatva vannak.

Nincs kötelező, így a fájlrendszerek szerkezetének. A lemezterület rendszerezésére szolgál más lehetőségek állnak rendelkezésre. A legfontosabb szempont, hogy a legfelső szintű fájlrendszer megakadályozása fut, nincs elég szabad lemezterület.

![A virtuális gép SAP alkalmazáskiszolgálón létrehozott két fájlrendszerek](./media/hana-get-started/image008.jpg)

A SAP HANA DB VM vonatkozó adatbázis telepítés esetén, SAPinst (SWPM) használatakor és a **tipikus** telepítésen, minden /hana és /usr/sap telepítve van. Az alapértelmezett helye a SAP HANA-napló biztonsági mentését /usr/sap alatt áll. Újra mivel fontos, hogy kevés a tárolási hely a legfelső szintű fájlrendszer megakadályozása, ellenőrizze, hogy van elég szabad hely a /hana és /usr/sap SAP HANA SWPM használatával telepítése előtt.

Az SAP HANA szabványos fájlrendszer elrendezését, olvassa el a [SAP HANA-kiszolgáló telepítési és frissítési útmutató](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![A virtuális gép SAP alkalmazáskiszolgálón létre további fájlrendszerek](./media/hana-get-started/image009.jpg)

Amikor SAP NetWeaver telepít egy szabványos SLES/SLES SAP alkalmazások 12 Azure gyűjtemény lemezkép, egy üzenet jelenik meg, amely szerint a nincs swap terület, az alábbi képernyőfelvételen látható módon. Ez az üzenet bezárásához, kézzel is hozzáadhatja a lapozófájl használatával **nn**, **mkswap**, és **swapon**. Megtudhatja, hogyan keresse meg "Lapozófájl kézi hozzáadása" a a [használatával a YaST particionáló](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) a SUSE dokumentáció része.

Egy másik lehetőség egy lapozóterület konfigurálása a Linux Virtuálisgép-ügynök használatával. További információkért lásd: a [Azure Linux ügynök felhasználói útmutató](../../extensions/agent-linux.md).

![Előugró üzenet tájékoztatja, hogy nincs-e elegendő lapozóterület](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>A/etc/hosts fájlt
SAP telepítés megkezdése előtt győződjön meg arról az állomásnevet és IP-címek SAP virtuális gépek szerepel a/Etc/Hosts fájlt. Egy Azure virtuális hálózaton belüli összes SAP virtuális telepíthet, és a belső IP-címek, majd használja, ahogy az itt látható:

![Állomásnevet és IP-címeket a listában szereplő/etc/hosts SAP virtuális gépek](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>A/etc/fstab fájl

Hasznos lehet hozzáadni a **nofail** fstab fájl paraméter. Így ha probléma merül fel a lemezeket, a virtuális gép nem lefagy a rendszerindítási folyamat során. De ne feledje, hogy a megfelelő mennyiségű lemezterületet nem feltétlenül érhető el, és a folyamatok előfordulhat, hogy a legfelső szintű fájlrendszer felfelé kitölti a rendszer. Ha /hana hiányzik, a SAP HANA nem indul el.

![Adja hozzá a nofail paraméter az fstab fájlhoz](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Grafikus GNOME asztalt a SLES 12/SLES az SAP alkalmazások 12
Ez a szakasz témakörei a következők:

* A GNOME asztali és xrdp telepítése a SLES 12/SLES SAP alkalmazások 12
* Java-alapú SAP MC Firefox segítségével SLES 12/SLES az SAP alkalmazások 12 futnak

Alternatív eszközökbe, például Xterminal vagy VNC (a jelen útmutató nem ismerteti) is használhatja.

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>A GNOME asztali és xrdp telepítése a SLES 12/SLES SAP alkalmazások 12
Ha Windows háttér, könnyen használhatja az SAP Linux virtuális gépeken belül közvetlenül egy grafikus asztali Firefox, SAPinst, SAP grafikus felhasználói Felülettel, SAP MC vagy HANA Studio futtatásához, és csatlakoztassa a virtuális Gépet a távoli asztal protokoll (RDP) keresztül Windows rendszerű számítógépről. A vállalati házirendeknek grafikus felhasználói felületeket üzemi és tesztkörnyezetben Linux történő hozzáadásával kapcsolatos függő alapú rendszereken, megfontolhatja a GNOME telepítése a kiszolgálón. A GNOME asztali telepítése egy Azure SLES 12/SLES SAP alkalmazások 12 virtuális gép számára:

1. Telepítse a GNOME asztali (például a PuTTY ablak) a következő parancs beírásával:

   `zypper in -t pattern gnome-basic`

2. A kapcsolat a virtuális gép RDP keresztül engedélyezésére xrdp telepítése:

   `zypper in xrdp`

3. /Etc/sysconfig/windowmanager szerkesztése, majd állítsa be az alapértelmezett kezelő GNOME:

   `DEFAULT_WM="gnome"`

4. Futtatás **chkconfig** győződjön meg arról, hogy xrdp automatikusan elindul a rendszer újraindítása után:

   `chkconfig -level 3 xrdp on`

5. Ha a távoli ASZTAL kapcsolaton keresztül problémát, próbálja meg újraindítani az (ablakból PuTTY, például):

   `/etc/xrdp/xrdp.sh restart`

6. Ha xrdp újra kell indítani az előző lépésben említett sem jár sikerrel, ellenőrizze .pid fájl:

   `check /var/run` 

   Keressen `xrdp.pid`. Ha található, távolítsa el, és próbálja meg újraindítani az újra.

### <a name="starting-sap-mc"></a>SAP MC indítása
Telepítése után a GNOME asztali, Firefox Azure SLES 12/SLES az SAP alkalmazások 12 virtuális gép futtatása közben a grafikus Java-alapú SAP MC kiindulva megjelenítheti hiba miatt a hiányzó Java-böngésző beépülő modult.

Az URL-címe, a SAP MC elindításához `<server>:5<instance_number>13`.

További információkért lásd: [a Web-alapú SAP felügyeleti konzol elindítása](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Az alábbi képernyőfelvételen látható a hibaüzenet jelenik meg, ha a Java-böngésző beépülő modul nem található:

![Hiányzó Java-böngésző beépülő modul utaló hibaüzenetet jelenít meg](./media/hana-get-started/image013.jpg)

A probléma megoldásához egyike a telepítése a hiányzó beépülő modul használatával YaST, az alábbi képernyőfelvételen látható módon:

![YaST telepítése a hiányzó beépülő modul használatával](./media/hana-get-started/image014.jpg)

Ismételt megadását az SAP felügyeleti konzol URL-cím, egy üzenet jelenik meg, hogy aktiválja a beépülő modul kéri:

![Beépülő modul aktiválást párbeszédpanel](./media/hana-get-started/image015.jpg)

Akkor is megjelenik egy hibaüzenet, a hiányzó fájlok javafx.properties. Oracle Java 1.8 szükséglete SAP grafikus felhasználói Felülettel 7.4 kapcsolódik. (Lásd: [SAP Megjegyzés 2059429](https://launchpad.support.sap.com/#/notes/2059424).) Az IBM Java-verziót és a érkezik a SLES/SLES az SAP alkalmazások 12 openjdk csomag nem tartalmazza a szükséges javafx.properties fájlt. A megoldás letöltése és telepítése a Java használata 8 az Oracle-t.

OpenSUSE openjdk hasonló hibával kapcsolatos információkért tekintse meg a vitafórum szál [SAPGui 7.4 Java a openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Az SAP HANA manuális telepítésére: SWPM
Ebben a szakaszban példaként bemutató képernyőképeket láthat sorozatát SAP NetWeaver 7.5-ös és az SAP HANA SP12 telepítése SWPM (SAPinst) használatakor főbb lépéseit mutatja be. A NetWeaver 7.5 telepítésének részeként SWPM is telepíthet a HANA-adatbázisból egyetlen példányt.

Egy minta tesztkörnyezetben telepítettük a csak egy speciális üzleti alkalmazás programozási (ABAP) alkalmazások kiszolgálói. Ahogy az az alábbi képernyőfelvételen, használhatja a **rendszer elosztott** ASC és elsődleges alkalmazáskiszolgáló-példányok telepítése egy Azure virtuális gép és SAP HANA adatbázis rendszert, a másik Azure virtuális gép lehetőséget.

![Asc és telepítik a rendszer elosztott lehetőséget elsődleges alkalmazáskiszolgáló-példányok](./media/hana-get-started/image012.jpg)

Miután a ASC példány telepítve van az alkalmazás server rendszerű virtuális Géphez, és az SAP-kezelőkonzolon "zöld" értékre állítva (jelenik meg az alábbi képernyőfelvételen), a /sapmnt könyvtár (beleértve a SAP profil könyvtár) meg kell osztani VM SAP HANA-adatbázis-kiszolgálóval. Az adatbázis-telepítési lépés információk engedéllyel kell rendelkeznie. A legjobb hozzáférést módja használja az NFS-SZEL YaST használatával állítható be.

![A ASC példány megjelenítő SAP felügyeleti konzol a virtuális gép alkalmazáskiszolgálón telepítve, és "zöld" beállítása](./media/hana-get-started/image016.jpg)

A kiszolgálón app virtuális gép, a /sapmnt directory közösen kell keresztül NFS használata a **rw** és **no_root_squash** beállítások. Az alapértelmezett érték **ro** és **root_squash**, amely vezethet problémákat, ha az adatbázis-példány telepítése.

![Megosztott NFS keresztül /sapmnt könyvtár rw és no_root_squash beállításokkal](./media/hana-get-started/image017b.jpg)

A következő képernyőfelvételen látható, mert a virtuális gép alkalmazáskiszolgálóra /sapmnt megosztás be kell állítani a SAP HANA-adatbázis-kiszolgáló virtuális gép használatával **NFS-ügyfél** (és YaST).

![Az NFS-ügyfél segítségével konfigurált /sapmnt megosztás](./media/hana-get-started/image018b.jpg)

Elosztott telepítés NetWeaver 7.5 (**adatbázispéldány**), az alábbi képernyőfelvételen látható, jelentkezzen be a SAP HANA-adatbázis-kiszolgáló virtuális gép, és indítsa el a SWPM.

![Az SAP HANA DB server rendszerű virtuális Géphez való bejelentkezéskor, majd SWPM elindításával adatbázispéldány telepítése](./media/hana-get-started/image019.jpg)

Miután kiválasztotta a **tipikus** telepítési és a telepítési adathordozó elérési útvonalát adja meg, DB SID AZONOSÍTÓVAL, az állomás neve, a példányszámának és a DB rendszergazda jelszava.

![Az SAP HANA adatbázis rendszer rendszergazdai bejelentkezési lap](./media/hana-get-started/image035b.jpg)

Adja meg a jelszót a DBACOCKPIT séma:

![A jelszó-beviteli mezője az DBACOCKPIT séma](./media/hana-get-started/image036b.jpg)

Adjon meg egy kérdést, a SAPABAP1 séma jelszót:

![Adjon meg egy kérdést a SAPABAP1 séma jelszó](./media/hana-get-started/image037b.jpg)

Minden tevékenység befejezése után egy zöld pipa jelzi a DB telepítési folyamat egyes fázisait mellett megjelenik. Az üzenet "végrehajtási a... Az adatbázis-példány befejeződött"jelenik meg.

![A feladat befejeződött ablak megerősítést kérő üzenet](./media/hana-get-started/image023.jpg)

A sikeres telepítés után az SAP-kezelőkonzolon kell is megjelenítése a DB példányához, "zöld", és az SAP HANA folyamatokat (hdbindexserver, hdbcompileserver, és így tovább) teljes listájának megjelenítéséhez.

![SAP-felügyeleti konzol ablakot SAP HANA-folyamatok listája](./media/hana-get-started/image024.jpg)

Az alábbi képernyőfelvételen látható a fájlstruktúra SWPM HANA telepítése során létrehozott /hana/shared könyvtár alá részeit. Mivel nincs lehetőség egy másik elérési utat adjon meg, fontos további lemezterületet a /hana könyvtárban, a SAP HANA-telepítés előtt csatlakoztassa SWPM használatával. Ez megakadályozza, hogy a legfelső szintű fájlrendszer kevés a szabad hely.

![HANA telepítés során létrehozott /hana/shared directory fájlstruktúra](./media/hana-get-started/image025.jpg)

Ezen a képernyőfelvételen látható a /usr/sap directory fájl szerkezetét mutatja:

![A/usr/sap directory fájlstruktúra](./media/hana-get-started/image026.jpg)

Az elosztott ABAP telepítés utolsó lépését az elsődleges kiszolgáló alkalmazáspéldány telepíteni fogja:

![ABAP telepítési ábrázoló elsődleges kiszolgáló alkalmazáspéldány utolsó lépése](./media/hana-get-started/image027b.jpg)

Miután az elsődleges kiszolgáló alkalmazáspéldány és az SAP grafikus felhasználói Felülettel van telepítve, a **DBA Vezérlőpult** tranzakció annak ellenőrzéséhez, hogy a SAP HANA-telepítés befejezése megfelelően:

![Erősítse meg a sikeres telepítés DBA vezérlőpultja ablakban](./media/hana-get-started/image028b.jpg)

Utolsó lépésként előfordulhat, hogy szeretné előbb telepítenie HANA Studio az SAP-alkalmazások kiszolgálói virtuális gép, és csatlakozzon az SAP HANA-példányához, hogy a virtuális gép DB kiszolgálón fut:

![Az SAP-alkalmazások kiszolgálói virtuális gép SAP HANA Studio telepítése](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Az SAP HANA manuális telepítésére: HDBLCM
Mellett SWPM használatával történő telepítése SAP HANA egy elosztott telepítés részeként, telepítheti a HANA önálló először HDBLCM használatával. SAP NetWeaver 7.5, például Ezután telepítheti. Ebben a szakaszban a képernyőképek jelenjen meg ez a folyamat működéséről.

A HANA HDBLCM eszközzel kapcsolatos további információkért lásd:

* [A megfelelő SAP HANA HDBLCM a tevékenység kiválasztása](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA életciklus felügyeleti eszközök](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA-kiszolgáló telepítési és frissítési útmutató](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

A csoport azonosító beállítási problémák elkerülése érdekében a `\<HANA SID\>adm user` (a HDBLCM eszköz által létrehozott), adjon meg egy új csoportot nevű `sapsys` Csoportazonosító használatával `1001` SAP HANA keresztül HDBLCM telepítése előtt:

![Új csoport "sapsys" segítségével meghatározott csoport azonosítója 1001](./media/hana-get-started/image030.jpg)

HDBLCM indításakor először egy egyszerű start menüben jelenik meg. Válassza ki a cikket 1, **telepítése új rendszer**, az alábbi képernyőfelvételen látható módon:

!["Új rendszer telepítése" beállítást a HDBLCM start ablakban](./media/hana-get-started/image031.jpg)

Az alábbi képernyőfelvételen megjeleníti a korábban kiválasztott összes kulcs beállításokat.

> [!IMPORTANT]
> Könyvtárak nevű HANA napló és a adatköteteken, valamint a telepítési útvonalat (/ hana/Ez a példa megosztott) és a /usr/sap, nem a legfelső szintű fájlrendszer részének kell lennie. Ezeket a könyvtárakat volt a virtuális Géphez csatlakozik, (a "Lemez setup" szakaszban leírt) az Azure data lemezeket tartozik. Ez a megközelítés megakadályozza, hogy a legfelső szintű fájlrendszer futtatásának nincs elég lemezterület. Az alábbi képernyőképen látható, hogy a HANA rendszergazda felhasználói Azonosítóval rendelkezik-e `1005` és része a `sapsys` csoport (azonosító `1001`), amely a telepítés előtt lett definiálva.

![Korábban kiválasztott összes fő SAP HANA összetevők listáját](./media/hana-get-started/image032.jpg)

Ellenőrizheti a `\<HANA SID\>adm user` (`azdadm` az alábbi képernyőfelvételen a) a címtárban/etc/passwd részletek:

![HANA \<HANA SID\>adm felhasználó adatait, a/etc/passwd directory szerepel](./media/hana-get-started/image033.jpg)

Miután SAP HANA HDBLCM használatával telepítette, a fájl szerkezet SAP HANA Studio, láthatja, az alábbi képernyőfelvételen látható módon. A SAPABAP1 séma, amely tartalmazza a SAP NetWeaver táblák, még nem érhető el.

![Az SAP HANA Studio SAP HANA-fájlstruktúra](./media/hana-get-started/image034.jpg)

Miután telepítette a SAP HANA, a SAP NetWeaver utasítást is telepítheti. Az alábbi képernyőképen látható, a telepítés elvégezték elosztott telepítése SWPM (az előző szakaszban leírtak) használatával. Ha az adatbázispéldányt SWPM használatával telepíti, adja meg ugyanazokat az adatokat HDBLCM (például állomásnév HANA SID és példányszámának) használatával. SWPM majd a meglévő HANA telepítését használja, és hozzáadja a sémák.

![Egy elosztott telepítés SWPM használatával történik](./media/hana-get-started/image035b.jpg)

Az alábbi képernyőfelvételen látható a SWPM telepítési lépés, ahol a DBACOCKPIT séma adatait adja meg:

![A SWPM telepítési lépés, ahol DBACOCKPIT séma adatok](./media/hana-get-started/image036b.jpg)

Adja meg a SAPABAP1 séma adatait:

![A SAPABAP1 séma kapcsolatos adatok megadása](./media/hana-get-started/image037b.jpg)

A SWPM adatbázis-példány telepítése után megtekintheti a SAPABAP1 séma SAP HANA Studio:

![Az SAP HANA Studio SAPABAP1 séma](./media/hana-get-started/image038b.jpg)

Végezetül az SAP-alkalmazások kiszolgálói és SAP grafikus felhasználói felülettel módú telepítés befejezése után ellenőrizheti a HANA DB-példány használatával a **DBA Vezérlőpult** tranzakció:

![A HANA DB-példány a DBA Vezérlőpult tranzakcióhoz ellenőrzése](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP szoftver letöltése
Szoftver letöltheti az SAP szolgáltatás piactérről, ahogy az az alábbi képek.

Töltse le a NetWeaver 7.5 Linux/Hana:

 ![SAP-szolgáltatás telepítése és frissítése ablakának NetWeaver 7.5 letöltése](./media/hana-get-started/image001.jpg)

Töltse le a HANA SP12 Platform Edition:

 ![SAP-szolgáltatás telepítése és frissítése ablakának HANA SP12 Platform Edition letöltése](./media/hana-get-started/image002.jpg)

