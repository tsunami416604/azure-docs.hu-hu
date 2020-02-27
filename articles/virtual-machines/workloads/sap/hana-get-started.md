---
title: 'Gyors útmutató: egypéldányos SAP HANA manuális telepítése az Azure Virtual Machines-on | Microsoft Docs'
description: Gyors útmutató az Egypéldányos SAP HANA Azure-beli telepítésének manuális telepítéséhez Virtual Machines
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 0090ffe977dee3e493d726c9eb4d151bcbeb503f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617248"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Gyors útmutató: egypéldányos SAP HANA manuális telepítése az Azure-on Virtual Machines
## <a name="introduction"></a>Introduction (Bevezetés)
Ez az útmutató segítséget nyújt az Azure Virtual Machines egypéldányos SAP HANA beállításához az SAP NetWeaver 7,5 és SAP HANA 1,0 SP12 manuális telepítésekor. Ennek az útmutatónak a témája a SAP HANA Azure-beli üzembe helyezése. Nem helyettesíti az SAP-dokumentációt. 

> [!NOTE]
> Ez az útmutató ismerteti SAP HANA üzembe helyezését az Azure-beli virtuális gépeken. További információ a SAP HANA a HANA Large-példányokban való üzembe helyezéséről: [az SAP használata az Azure-on Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy már ismeri az alábbi infrastruktúra-(IaaS-) alapismereteket:
 * Virtuális gépek (VM-EK) vagy virtuális hálózatok üzembe helyezése a Azure Portal vagy a PowerShell használatával.
 * Az Azure platformfüggetlen parancssori felülete (CLI), amely a JavaScript Object Notation-(JSON-) Sablonok használatát is magában foglalja.

Az útmutató emellett azt is feltételezi, hogy már ismeri a következőket:
* SAP HANA és SAP NetWeaver, valamint a helyszíni telepítésének módja.
* SAP HANA-és SAP-alkalmazási példányok telepítése és üzemeltetése az Azure-ban.
* A következő fogalmak és eljárások:
   * Az Azure-beli SAP üzembe helyezésének megtervezése, amely magában foglalja az Azure Virtual Network tervezését és az Azure Storage használatát. Lásd: [SAP NetWeaver az Azure Virtual Machines – tervezési és megvalósítási útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Üzembe helyezési alapelvek és a virtuális gépek üzembe helyezésének módjai az Azure-ban. Lásd: [Azure Virtual Machines-telepítés az SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)-hoz.
   * Magas rendelkezésre állás az SAP NetWeaver ABAP SAP Central Services (ASCS), az SAP Central Services (SCS) és a sorba helyezni Replication Server (ERS) számára az Azure-ban. Tekintse [meg az Azure-beli virtuális gépeken elérhető SAP NetWeaver magas rendelkezésre állását](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Részletes információk a ASCS/SCS Azure-beli többszörös SID-telepítésének hatékonyságáról. Lásd: [SAP NetWeaver multi-SID konfiguráció létrehozása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Az SAP NetWeaver futtatásának alapelvei a Linux-alapú virtuális gépeken az Azure-ban. Lásd: [az SAP NetWeaver futtatása Microsoft Azure SUSE Linux rendszerű virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Ez az útmutató a Linux Azure-beli virtuális gépeken való telepítésének speciális beállításait tartalmazza. Emellett tájékoztatást nyújt arról is, hogyan csatolhatja megfelelően az Azure Storage-lemezeket a Linux rendszerű virtuális gépekhez.

Az éles környezetekben használható Azure-beli virtuálisgép-típusok listáját a [IAAS SAP-dokumentációja](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)tartalmazza. A nem termék jellegű forgatókönyvek esetében a natív Azure-beli virtuálisgép-típusok szélesebb választéka érhető el.
További információ a virtuális gépek konfigurálásáról és működéséről: [SAP HANA infrastruktúra-konfigurációk és-műveletek az Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)-ban.
A magas rendelkezésre állás SAP HANA tekintse meg az [Azure Virtual Machines SAP HANA magas rendelkezésre állását](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)ismertető témakört.

Ha SAP HANA példányt vagy az S/4HANA vagy a BW/4HANA rendszer gyors telepítését kívánja telepíteni, érdemes lehet az [SAP Cloud Appliance Library](https://cal.sap.com)-t használni. Az S/4HANA rendszer az Azure-beli SAP Cloud Appliance library-n keresztül történő üzembe helyezésével kapcsolatos dokumentációt találhat, például ebben az [útmutatóban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Minden, amire szüksége van egy Azure-előfizetés és egy SAP-felhasználó, aki regisztrálható az SAP Cloud Appliance Library használatával.

## <a name="additional-resources"></a>További források
### <a name="sap-hana-backup"></a>SAP HANA biztonsági mentés
Az Azure-beli virtuális gépeken SAP HANA adatbázisok biztonsági mentésével kapcsolatos információkért lásd:
* [Biztonsági mentési útmutató az Azure Virtual Machines SAP HANAához](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [SAP HANA Azure Backup a fájl szintjén](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [SAP HANA biztonsági mentés tárolási Pillanatképek alapján](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
Az SAP Cloud Appliance Library az S/4HANA vagy a BW/4HANA üzembe helyezésével kapcsolatos információkért lásd: [az SAP S/4HANA vagy a BW/4HANA üzembe helyezése Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA – támogatott operációs rendszerek
SAP HANA által támogatott operációs rendszerekkel kapcsolatos információkért lásd [: SAP Note 2235581-SAP HANA: támogatott operációs rendszerek](https://launchpad.support.sap.com/#/notes/2235581/E). Az Azure-beli virtuális gépek csak ezen operációs rendszerek egy részhalmazát támogatják. A következő operációs rendszerek támogatottak a SAP HANA Azure-beli üzembe helyezéséhez: 

* SUSE Linux Enterprise Server 12. x
* Red Hat Enterprise Linux 7.2

A SAP HANA és a különböző Linux operációs rendszerekkel kapcsolatos további SAP-dokumentációért lásd:

* [SAP-megjegyzés 171356: SAP-szoftverek Linux rendszeren: általános információk](https://launchpad.support.sap.com/#/notes/1984787).
* [1944799-es SAP-Megjegyzés: SAP HANA útmutató a SLES operációs rendszer telepítéséhez](http://service.sap.com/sap/support/notes/1944799).
* [2205917-es SAP-Megjegyzés: SAP HANA db ajánlott operációsrendszer-beállítások az SLES 12 for SAP-alkalmazásokhoz](https://launchpad.support.sap.com/#/notes/2205917/E).
* [SAP-megjegyzés 1391070: Linux UUID-megoldások](https://launchpad.support.sap.com/#/notes/1391070).
* [SAP-megjegyzés 2009879: SAP HANA Red Hat Enterprise Linux (RHEL) operációs rendszerre vonatkozó irányelvek](https://launchpad.support.sap.com/#/notes/2009879).
* [SAP-megjegyzés 2292690: SAP HANA db: ajánlott operációsrendszer-beállítások a RHEL 7 rendszerhez](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>SAP-monitorozás az Azure-ban
További információ az Azure-beli SAP-figyelésről:

* A 2191498-es [SAP-Megjegyzés](https://launchpad.support.sap.com/#/notes/2191498/E) a Linux rendszerű Azure-beli virtuális gépekkel bővített SAP-figyelést ismerteti. 
* A 1102124-es [SAP-Megjegyzés](https://launchpad.support.sap.com/#/notes/1102124/E) a Linux SAPOSCOL kapcsolatos információkat tárgyalja. 
* A 2178632-es [SAP-Megjegyzés](https://launchpad.support.sap.com/#/notes/2178632/E) az SAP-hez Microsoft Azure legfontosabb monitorozási mérőszámait ismerteti.

### <a name="azure-vm-types"></a>Azure-beli virtuális gépek típusai
Az SAP HANA által használt Azure-beli virtuálisgép-típusok és SAP által támogatott számítási feladatok dokumentálva vannak az [SAP Certified IaaS platformokban](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Az SAP NetWeaver vagy az S/4HANA által hitelesített Azure-beli virtuálisgép-típusok dokumentálva vannak az [SAP Note 1928533: SAP-alkalmazások az Azure-ban: támogatott termékek és Azure VM-típusok](https://launchpad.support.sap.com/#/notes/1928533/E).

> [!NOTE]
> SAP-Linux – az Azure-integráció csak Azure Resource Manageron és nem a klasszikus üzemi modellen támogatott. 

## <a name="manual-installation-of-sap-hana"></a>SAP HANA manuális telepítése

> [!IMPORTANT]
> Győződjön meg arról, hogy a kiválasztott operációs rendszer SAP-tanúsítvánnyal rendelkezik SAP HANA a megadott virtuálisgép-típusoknál. A virtuálisgép-típusok SAP HANA minősített virtuálisgép-típusainak és operációsrendszer-kiadásainak listája [SAP HANA tanúsított IaaS platformokon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)kereshető. Ügyeljen rá, hogy a felsorolt virtuálisgép-típus részleteire kattintva beolvassa a SAP HANA által támogatott operációsrendszer-kiadásokat az adott virtuálisgép-típushoz. A jelen dokumentumban szereplő példában egy SUSE Linux Enterprise Server (SLES) operációsrendszer-kiadást használtunk, amelyet az SAP nem támogat az M-sorozatú virtuális gépeken SAP HANA.
>

Ez az útmutató a SAP HANA Azure-beli virtuális gépeken történő manuális telepítésének módját ismerteti két különböző módon:

* Használja az SAP Software kiépítési kezelőjét (SWPM) az elosztott NetWeaver telepítésének részeként az "adatbázis-példány telepítése" lépésben.
* Használja a SAP HANA adatbázis-életciklus-kezelő (HDBLCM) eszközt, majd telepítse a NetWeaver alkalmazást.

A SWPM segítségével az összes összetevőt, például az SAP HANA, az SAP Application Servert és a ASCS-példányt is telepítheti egyetlen virtuális gépen. A lépéseket ebben a [SAP HANA blogbejegyzésben](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)ismertetjük. Ez a beállítás nincs ismertetve ebben a rövid útmutatóban, de a figyelembe venni kívánt problémák megegyeznek.

A telepítés megkezdése előtt javasoljuk, hogy olvassa el a jelen útmutató későbbi, "Azure-beli virtuális gépek előkészítése SAP HANA manuális telepítésére" című szakaszát. Ez segít megakadályozni, hogy a rendszer csak az alapértelmezett Azure-beli virtuális gép konfigurációját használja.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>A SAP HANA telepítésének főbb lépései az SAP-SWPM használatakor
Ez a szakasz a manuális, egypéldányos SAP HANA telepítésének főbb lépéseit sorolja fel, ha az SAP SWPM használatával hajtja végre az elosztott SAP NetWeaver 7,5 telepítését. Az egyes lépéseket részletesebben ismertetjük a képernyőképen az útmutató későbbi részében.

1. Hozzon létre egy Azure-beli virtuális hálózatot, amely két teszt virtuális gépet tartalmaz.
2. A Azure Resource Manager modellnek megfelelően telepítse a két Azure-beli virtuális gépet operációs rendszerekkel. Ez a példa SUSE Linux Enterprise Server és SLES használ a 12 SP1 rendszerű SAP-alkalmazásokhoz. 
3. Két Azure standard vagy Premium Storage-lemezt csatlakoztathat, például 75 GB-os vagy 500 GB-os lemezeket az Application Server virtuális géphez.
4. Csatlakoztassa a Premium Storage-lemezeket a HANA DB-kiszolgáló virtuális géphez. További információt az útmutató későbbi, "a lemez telepítése" című szakaszában talál.
5. A mérettől vagy az átviteli sebességtől függően több lemez csatolása szükséges. Ezután hozzon létre csíkozott köteteket. Használja a logikai kötetek felügyeletét (LVM) vagy egy többeszközes felügyeleti (mdadm) eszközt a virtuális gépen belüli operációs rendszer szintjén.
6. Hozzon létre XFS-fájlrendszereket a csatlakoztatott lemezeken vagy logikai köteteken.
7. Csatlakoztassa az új XFS fájlrendszert az operációs rendszer szintjén. Használjon egy fájlrendszert az összes SAP-szoftverhez. Használja a másik fájlrendszert a/sapmnt könyvtárához és a biztonsági mentésekhez, például:. A SAP HANA adatbázis-kiszolgálón csatlakoztassa a XFS fájlrendszert a Premium Storage-lemezeken a/Hana és a/usr/SAP. Ez a folyamat azért szükséges, hogy megakadályozza a gyökér fájlrendszer kitöltését. A gyökérszintű fájlrendszer nem nagyméretű a Linux Azure-beli virtuális gépeken. 
8. Adja meg a/etc/hosts-fájlban a teszt virtuális gépek helyi IP-címeit.
9. Adja meg a **nem hibás** paramétert az/etc/fstab fájlban.
10. A Linux kernel paramétereinek beállítása a használt Linux operációs rendszer kiadásának megfelelően. További információ: a HANA és a "kernel parameters" című szakaszban szereplő SAP-megjegyzések című rész a jelen útmutatóban.
11. A lapozóterület bővítése.
12. Szükség esetén a tesztelési virtuális gépeken is telepíthet grafikus asztali gépeket. Ellenkező esetben használjon távoli SAPinst-telepítést.
13. Töltse le az SAP-szoftvert az SAP szolgáltatás Piactérről.
14. Telepítse az SAP ASCS-példányt az App Server virtuális gépre.
15. Ossza meg a/sapmnt könyvtárat a teszt virtuális gépek között az NFS használatával. Az alkalmazáskiszolgáló virtuális gép az NFS-kiszolgáló.
16. Telepítse az adatbázis-példányt, amely tartalmazza a HANA-t, az DB-kiszolgáló virtuális gép SWPM használatával.
17. Telepítse az elsődleges alkalmazáskiszolgáló (PAS) szolgáltatást az alkalmazáskiszolgáló virtuális gépre.
18. Indítsa el az SAP felügyeleti konzolt (SAP MC). Kapcsolódjon az SAP GUI vagy a HANA Studio használatával, például:.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>A SAP HANA telepítésének főbb lépései a HDBLCM használatakor
Ez a szakasz a manuális, egypéldányos SAP HANA telepítésének főbb lépéseit sorolja fel, ha az SAP HDBLCM használatával hajtja végre az elosztott SAP NetWeaver 7,5 telepítését. Az egyes lépéseket részletesen ismertetjük a képernyőképen az útmutató során.

1. Hozzon létre egy Azure-beli virtuális hálózatot, amely két teszt virtuális gépet tartalmaz.
2. A Azure Resource Manager modellnek megfelelően telepítsen két Azure-beli virtuális gépet operációs rendszerrel. Ebben a példában a SLES és a SLES for SAP Applications for 12 SP1 verziót használja.
3. Két Azure standard vagy Premium Storage-lemezt csatlakoztathat, például 75-GB vagy 500-GB lemezeket az App Server virtuális géphez.
4. Csatlakoztassa a Premium Storage-lemezeket a HANA DB-kiszolgáló virtuális géphez. További információt az útmutató későbbi, "a lemez telepítése" című szakaszában talál.
5. A mérettől vagy az átviteli sebességtől függően több lemez csatolása szükséges. Csíkozott köteteket hozhat létre a logikai kötetek kezelése vagy a mdadm eszköz használatával az operációs rendszer szintjén a virtuális gépen belül.
6. Hozzon létre XFS-fájlrendszereket a csatlakoztatott lemezeken vagy logikai köteteken.
7. Csatlakoztassa az új XFS fájlrendszert az operációs rendszer szintjén. Használjon egy fájlrendszert az összes SAP-szoftverhez. Használja a másik fájlrendszert a/sapmnt könyvtárához és a biztonsági mentésekhez, például:. A SAP HANA adatbázis-kiszolgálón csatlakoztassa a XFS fájlrendszert a Premium Storage-lemezeken a/Hana és a/usr/SAP. Ez a folyamat azért szükséges, hogy megakadályozza a gyökér fájlrendszer kitöltését. A gyökérszintű fájlrendszer nem nagyméretű a Linux Azure-beli virtuális gépeken.
8. Adja meg a/etc/hosts-fájlban a teszt virtuális gépek helyi IP-címeit.
9. Adja meg a **nem hibás** paramétert az/etc/fstab fájlban.
10. Állítsa be a rendszermag paramétereit a használt Linux operációs rendszer kiadásának megfelelően. További információ: a HANA és a "kernel parameters" című szakaszban szereplő SAP-megjegyzések című rész a jelen útmutatóban.
11. A lapozóterület bővítése.
12. Szükség esetén a tesztelési virtuális gépeken is telepíthet grafikus asztali gépeket. Ellenkező esetben használjon távoli SAPinst-telepítést.
13. Töltse le az SAP-szoftvert az SAP szolgáltatás Piactérről.
14. Hozzon létre egy sapsys a 1001 AZONOSÍTÓJÚ csoporttal a HANA DB kiszolgálói virtuális gépen.
15. Telepítse a SAP HANAt az adatbázis-kiszolgáló virtuális gépen a HANA Database Lifecycle Manager használatával.
16. Telepítse az SAP ASCS-példányt az App Server virtuális gépre.
17. Ossza meg a/sapmnt könyvtárat a teszt virtuális gépek között az NFS használatával. Az alkalmazáskiszolgáló virtuális gép az NFS-kiszolgáló.
18. Telepítse a HANA-t tartalmazó adatbázis-példányt a HANA DB Server virtuális gépen a SWPM használatával.
19. Telepítse az elsődleges alkalmazáskiszolgáló alkalmazást az alkalmazáskiszolgáló virtuális gépre.
20. Start SAP MC. Az SAP grafikus felhasználói felületén vagy a HANA studión keresztül kapcsolódhat.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Azure-beli virtuális gépek előkészítése SAP HANA manuális telepítésére
Ez a szakasz a következő témaköröket tartalmazza:

* Operációs rendszer frissítései
* Lemez beállítása
* Kernel-paraméterek
* Fájlrendszerek
* A/etc/hosts fájl
* Az/etc/fstab fájl

### <a name="os-updates"></a>Operációs rendszer frissítései
További szoftverek telepítése előtt keresse meg a Linux operációs rendszer frissítéseit és javításait. Egy javítás telepítésével elkerülheti a támogatási szolgálat hívását.

Győződjön meg arról, hogy a következőket használja:
* SUSE Linux Enterprise Server SAP-alkalmazásokhoz.
* Red Hat Enterprise Linux az SAP-alkalmazásokhoz vagy a SAP HANA Red Hat Enterprise Linuxhoz. 

Ha még nem tette meg, regisztrálja az operációs rendszer központi telepítését a linuxos gyártótól származó Linux-előfizetéssel. A SUSE operációs rendszer lemezképei olyan SAP-alkalmazásokhoz, amelyek már tartalmazzák a szolgáltatásokat, és amelyek automatikusan regisztrálva vannak.

Az alábbi példa bemutatja, hogyan ellenőrizhetők a SUSE Linux rendelkezésre álló javításai a **Zypper** parancs használatával:

 `sudo zypper list-patches`

A probléma típusától függően a javítások kategória és súlyosság szerint vannak osztályozva. A kategória leggyakrabban használt értékei a következők: 
- Biztonság
- Ajánlott
- Optional
- Funkció
- Dokumentum
- YaST

A súlyosság leggyakrabban használt értékei a következők:

- Kritikus
- Fontos
- Mérsékelt
- Alacsony
- Meghatározatlan

A **Zypper** parancs csak azokat a frissítéseket keresi, amelyekre a telepített csomagok szükségesek. Ezt a parancsot használhatja például:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

A `--dry-run` paraméter hozzáadásával tesztelheti a frissítést anélkül, hogy ténylegesen frissítené a rendszerkonfigurációt.


### <a name="disk-setup"></a>Lemez beállítása
Az Azure-beli Linux rendszerű virtuális gépen a gyökér fájlrendszer mérete korlátozott. Ezért további lemezterületet kell csatlakoztatnia egy Azure-beli virtuális géphez az SAP futtatásához. Az SAP Application Server Azure-beli virtuális gépek esetében elég lehet az Azure standard Storage-lemezek használata. SAP HANA adatbázis-kezelői Azure-beli virtuális gépek esetében kötelező az Azure Premium Storage-lemezek használata a termelési és a nem termékbeli megvalósításokhoz.

A [SAP HANA TDI tárolási követelményei](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)alapján a következő Azure Premium Storage-konfiguráció javasolt: 

| Virtuális gép termékváltozata | RAM |  /Hana/Data és/Hana/log <br /> az LVM vagy a mdadm szalagos | /hana/shared | /root-kötet | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

A javasolt lemez-konfigurációban a HANA-adatmennyiség és a naplózási kötet ugyanarra az Azure Premium Storage-lemezre kerül, amely az LVM vagy a mdadm szalagos. Nincs szükség a RAID-redundancia megadására, mert az Azure Premium Storage a lemezek három rendszerképét tárolja a redundancia érdekében. 

A megfelelő tárterület konfigurálásához lásd: [SAP HANA a TDI tárolási követelményei](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) és a [SAP HANA Server telepítési és frissítési útmutatója](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Vegye figyelembe a különböző Azure Premium Storage-lemezek különböző virtuális merevlemez-átviteli sebességét is, ahogy azt a [nagy teljesítményű Premium Storage-ban és a virtuális gépek felügyelt lemezein](../../windows/disks-types.md)dokumentálja. 

A HANA adatbázis-kezelő virtuális gépekhez további Premium Storage-lemezeket adhat hozzá az adatbázisok vagy tranzakciónaplók biztonsági másolatainak tárolásához.

A csíkozás konfigurálásához használt két fő eszközről a következő témakörben talál további információt:

* [A szoftveres RAID konfigurálása Linux rendszeren](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Az LVM konfigurálása Linux rendszerű virtuális gépen az Azure-ban](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

További információ arról, hogyan lehet lemezeket csatlakoztatni a Linux rendszerű Azure-beli virtuális gépekhez vendég operációs rendszerként: [lemez hozzáadása Linux rendszerű virtuális géphez](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Az Azure Premium SSD-k segítségével megadhatja a lemezes gyorsítótárazási módokat. A/Hana/Data és/Hana/log tároló csíkozott készlet esetében tiltsa le a lemezes gyorsítótárazást. A többi kötet esetében, azaz a lemezeken a gyorsítótárazási mód **írásvédett**értékre van állítva.

A virtuális gépek létrehozásához használt JSON-sablonok megkereséséhez tekintse meg az [Azure Gyorsindítás sablonjait](https://github.com/Azure/azure-quickstart-templates).
A VM-Simple-SLES sablon egy alapszintű sablon. Tartalmaz egy tárolási szakaszt, amely egy további 100 GB-os adatlemezzel rendelkezik. Ezt a sablont használja alapként. A sablont átalakíthatja a megadott konfigurációra.

> [!NOTE]
> Fontos, hogy az Azure Storage-lemezt az [Microsoft Azure SUSE Linux rendszerű virtuális gépeken az SAP NetWeaver futtatása](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című dokumentumban leírtak szerint egy UUID-vel csatlakoztassa.

A tesztkörnyezetben két Azure standard Storage-lemez van csatlakoztatva az SAP app Server virtuális géphez, ahogy az alábbi képernyőképen is látható. Az egyik lemez tárolja az összes SAP-szoftvert, például a NetWeaver 7,5, az SAP GUI és a SAP HANAt a telepítéshez. A második lemez gondoskodik arról, hogy a további követelményekhez elegendő szabad terület álljon rendelkezésre. Például a biztonsági mentési és tesztelési, valamint a/sapmnt könyvtárat, azaz az SAP-profilokat az azonos SAP-környezethez tartozó összes virtuális gép között meg kell osztani.

![SAP HANA az App Server-lemezek ablak két adatlemezt és azok méreteit jeleníti meg](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Kernel-paraméterek
SAP HANA speciális Linux kernel-beállításokat igényel. Ezek a kernel-beállítások nem a szabványos Azure Gallery-lemezképek részét képezik, és manuálisan kell beállítani őket. Attól függően, hogy a SUSE vagy a Red Hat szolgáltatást használja-e, a paraméterek eltérőek lehetnek. A korábban felsorolt SAP-megjegyzések információt nyújtanak ezekről a paraméterekről. A képernyőképen a SUSE Linux 12 SP1 volt használatban. 

SLES for SAP Applications 12 az SAP-alkalmazások 12 SP1 verziójának általános rendelkezésre állása és SLES: a régi **sapconf** eszközt felváltó új eszköz, **tuned-adm**. A **hangolt-adm-** hez speciális SAP HANA profil érhető el. A SAP HANA rendszerének hangolásához adja meg a következő profilt root felhasználóként:

   `tuned-adm profile sap-hana`

A **tuned-adm-** vel kapcsolatos további információkért tekintse meg a [hangolt-adm-vel kapcsolatos SUSE dokumentációt](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

A következő képernyőképen megtudhatja, hogyan változtatta meg a **tuned-adm** a `transparent_hugepage` és `numa_balancing` értékeket a szükséges SAP HANA beállításoknak megfelelően:

![A hangolt-adm eszköz a szükséges SAP HANA beállításoknak megfelelően módosítja az értékeket](./media/hana-get-started/image005.jpg)

A SAP HANA kernel beállításainak végleges elvégzéséhez használja a **GRUB2** -t a SLES 12-én. A **GRUB2**kapcsolatos további információkért tekintse meg a SUSE dokumentációjának [konfigurációs fájl szerkezete](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) című szakaszát.

A következő képernyőképen látható, hogyan módosultak a rendszermag beállításai a konfigurációs fájlban, majd az **GRUB2-mkconfig**használatával lettek lefordítva:

![A rendszermag beállításai megváltoztak a konfigurációs fájlban, és a GRUB2-mkconfig használatával lefordították](./media/hana-get-started/image006.jpg)

Egy másik lehetőség a beállítások módosítása a YaST és a **rendszerindító betöltő** > **kernel paraméterek** beállításaival:

![A YaST rendszerindítási betöltő kernel-paraméterek beállításai lapja](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Fájlrendszerek
Az alábbi képernyőfelvételen két olyan fájlrendszer látható, amely az SAP app Server virtuális gépen lett létrehozva a két csatolt Azure standard Storage-lemez felett. Mindkét fájlrendszer XFS típusú, és/sapdata és/sapsoftware. van csatlakoztatva

Így nem kötelező a fájlrendszereket felstrukturálni. Más lehetőségek is rendelkezésre állnak a lemezterület strukturálása érdekében. A legfontosabb szempont, hogy megakadályozza, hogy a gyökér fájlrendszer kifogyjon a szabad területről.

![Két, az SAP app Server-alapú virtuális gépen létrehozott fájlrendszerek](./media/hana-get-started/image008.jpg)

A SAP HANA DB virtuális gép esetében az adatbázis telepítése során, amikor a SAPinst és a **szokásos** telepítési lehetőséggel használja a SWPM-t, a/Hana és a/usr/SAP. alatt minden telepítve van A SAP HANA napló biztonsági mentésének alapértelmezett helye a/usr/SAP. alatt található. Újra meg kell akadályozni, hogy a gyökér fájlrendszer kifogyjon a tárterületen. Győződjön meg arról, hogy elegendő szabad terület van a/Hana és a/usr/SAP területen, mielőtt telepítené a SWPM-t a SAP HANA telepítése előtt.

A SAP HANA szabványos fájlrendszer-elrendezésének leírását a [SAP HANA Server telepítési és frissítési útmutatójában](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)találja.

![Az SAP app Server virtuális gépen létrehozott további fájlrendszerek](./media/hana-get-started/image009.jpg)

Ha az SAP NetWeaver standard SLES/SLES telepíti az SAP-alkalmazások 12 Azure Gallery-rendszerképét, egy üzenet jelenik meg, amely szerint nincs lapozófájl, ahogy az alábbi képernyőfelvételen is látható. Az üzenet elvetéséhez manuálisan is hozzáadhat egy swap-fájlt a **dd**, a **mkswap**és a **swapon**használatával. Ebből a cikkből megtudhatja, hogyan keresheti meg a "swap-fájl hozzáadása manuálisan" kifejezést a SUSE dokumentációjának [YaST particionáló](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) szakaszának használatával.

Egy másik lehetőség a swap-terület konfigurálása a linuxos virtuálisgép-ügynök használatával. További információk: [Azure Linux-ügynök – felhasználói útmutató](../../extensions/agent-linux.md).

![Előugró üzenet, amely azt tanácsolja, hogy nincs elegendő lapozófájl](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>A/etc/hosts fájl
Mielőtt telepítené az SAP-t, győződjön meg arról, hogy a/etc/hosts fájlban szerepelnek az SAP virtuális gépek állomásneve és IP-címei. Az összes SAP virtuális gép üzembe helyezése egy Azure-beli virtuális hálózaton belül. Ezután használja a belső IP-címeket, ahogy az itt látható:

![A/etc/hosts fájlban felsorolt SAP virtuális gépek állomásneve és IP-címei](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Az/etc/fstab fájl

Hasznos lehet a **hiba** paraméter hozzáadása az fstab-fájlhoz. Így ha valami nem stimmel a lemezekkel, a virtuális gép nem válaszol a rendszerindítási folyamatban. Ne feledje azonban, hogy előfordulhat, hogy a további lemezterület nem érhető el, és a folyamatok kitölthetik a gyökérszintű fájlrendszert. Ha a/Hana hiányzik, SAP HANA nem indul el.

![A hiba paraméter hozzáadása az fstab-fájlhoz](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Grafikus GNOME Desktop on SLES 12/SLES SAP-alkalmazásokhoz 12
Ez a szakasz a következőket ismerteti:

* Telepítse a GNOME Desktopot és a xrdp-t a SLES 12/SLES for SAP Applications for 12.
* Futtassa a Java-alapú SAP MC-t a Firefox használatával a SLES 12/SLES a következő SAP-alkalmazásokhoz: 12.

Használhatja a jelen útmutatóban nem ismertetett alternatívákat is, például a Xterminal vagy a VNC-t.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>A GNOME asztali és xrdp telepítése a SLES 12/SLES for SAP-alkalmazásokhoz 12
Ha rendelkezik Windows-háttérrel, egyszerűen használhatja az SAP Linux virtuális gépeken található grafikus asztalt a Firefox, a SAPinst, az SAP GUI, az SAP MC vagy a HANA Studio futtatásához. Ezután csatlakozhat a virtuális géphez a RDP protokoll (RDP) használatával egy Windows rendszerű számítógépről. Attól függően, hogy a vállalati szabályzatok hogyan adhatók hozzá az éles környezethez és a nem termékhez tartozó Linux-alapú rendszerekhez, érdemes lehet a GNOME-ot telepíteni a kiszolgálóra. A következő lépésekkel telepítheti a GNOME Desktopot egy Azure SLES 12/SLES for SAP Applications 12 virtuális gépre.

1. Telepítse a GNOME Desktopot úgy, hogy beírja a következő parancsot, például egy Putty ablakban:

   `zypper in -t pattern gnome-basic`

2. Telepítse a xrdp-t a virtuális gép RDP-kapcsolaton keresztüli elérésének engedélyezéséhez:

   `zypper in xrdp`

3. /Etc/sysconfig/windowmanager szerkesztése és az alapértelmezett ablakkezelő beállítása a GNOME-ra:

   `DEFAULT_WM="gnome"`

4. Futtassa a **Chkconfig** , és győződjön meg arról, hogy a xrdp automatikusan elindul újraindítás után:

   `chkconfig -level 3 xrdp on`

5. Ha problémája van az RDP-kapcsolattal, próbálja meg újraindítani például egy Putty-ablakban:

   `/etc/xrdp/xrdp.sh restart`

6. Ha az előző lépésben megemlített xrdp-újraindítás nem működik, ellenőrizze a. PID fájlt:

   `check /var/run` 

   Keresse meg a `xrdp.pid`. Ha megtalálta, távolítsa el, majd próbálja meg újra az újraindítást.

### <a name="start-sap-mc"></a>SAP MC elindítása
A GNOME Desktop telepítése után indítsa el a grafikus Java-alapú SAP MC-t a Firefoxban. Ha egy Azure SLES 12/SLES fut az SAP-alkalmazásokhoz 12 virtuális gépen, akkor hiba jelenhet meg. A hiba a Java böngésző hiányzó beépülő modulja miatt fordul elő.

Az SAP MC elindításához szükséges URL-cím `<server>:5<instance_number>13`.

További információ: [a webalapú SAP Management konzol elindítása](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Az alábbi képernyőfelvételen a Java-böngésző beépülő moduljának hiányában megjelenő hibaüzenet látható:

![Hibaüzenet, amely hiányzó java-böngésző beépülő modult jelez](./media/hana-get-started/image013.jpg)

A probléma megoldásának egyik módja, ha a hiányzó beépülő modult a YaST használatával telepíti, ahogy az a következő képernyőképen látható:

![A YaST használata a hiányzó beépülő modul telepítéséhez](./media/hana-get-started/image014.jpg)

Ha újra megadja az SAP Management Console URL-címét, a rendszer felszólítja a beépülő modul aktiválásához:

![Beépülő modul aktiválását kérő párbeszédpanel](./media/hana-get-started/image015.jpg)

Előfordulhat, hogy a hiányzó fájlról (JavaFX. properties) hibaüzenetet is kap. Az SAP GUI 7,4-hez készült Oracle Java 1,8 követelményéhez kapcsolódik. További információ: [SAP Note 2059429](https://launchpad.support.sap.com/#/notes/2059424).
Az IBM Java verziója és a SLES/SLES for SAP Applications OpenJDK-csomagja nem tartalmazza a szükséges JavaFX. properties fájlt. A megoldás a Java SE 8 letöltése és telepítése az Oracle-ből.

Az openSUSE OpenJDK hasonló problémával kapcsolatos információkért tekintse meg a vitafórum szál [SAPGui 7,4 Java for opensuse 42,1 LEAP](https://scn.sap.com/thread/3908306)című témakört.

## <a name="manual-installation-of-sap-hana-swpm"></a>SAP HANA manuális telepítése: SWPM
Az ebben a szakaszban található képernyőképek sorozata az SAP NetWeaver 7,5 és a SAP HANA SP12 telepítésének legfontosabb lépéseit mutatja be, amikor a SWPM-t használja a SAPinst használatával. A NetWeaver 7,5 telepítésének részeként a SWPM egyetlen példányként is telepítheti a HANA-adatbázist.

A tesztkörnyezetben egy speciális Business Application Programming (ABAP) App Servert telepítettünk. Ahogy az a következő képernyőképen is látható, a ASCS és az elsődleges Application Server-példányok egy Azure-beli virtuális gépen való telepítéséhez az **elosztott rendszer** lehetőséget használtuk. Egy másik Azure-beli virtuális gép adatbázis-rendszerének SAP HANA használjuk.

![Az elosztott rendszer lehetőséggel telepített ASCS és elsődleges Application Server-példányok](./media/hana-get-started/image012.jpg)

Miután telepítette az ASCS-példányt az App Server-alapú virtuális gépre, az SAP kezelési konzolján egy zöld ikon azonosítja. A/sapmnt könyvtárat, amely tartalmazza az SAP-profil könyvtárat, meg kell osztani a SAP HANA adatbázis-kiszolgáló virtuális géppel. Az adatbázis-telepítési lépésnek hozzá kell férnie ehhez az információhoz. A hozzáférés biztosításának legjobb módja az NFS használata, amelyet a YaST használatával lehet konfigurálni.

![SAP felügyeleti konzol, amely az App Server virtuális gépen telepített ASCS-példányt zöld ikon használatával mutatja be](./media/hana-get-started/image016.jpg)

Az App Server-alapú virtuális gépen a/sapmnt könyvtárat az **RW** és a **no_root_squash** kapcsolók használatával osztja meg az NFS-en keresztül. Az alapértelmezett értékek a **ro** és a **root_squash**, ami problémákat okozhat az adatbázis-példány telepítésekor.

![A/sapmnt könyvtár megosztása az NFS-en keresztül az RW és a no_root_squash beállítások használatával](./media/hana-get-started/image017b.jpg)

A következő képernyőképen látható, hogy az/sapmnt-megosztást az App Server virtuális gépről az **NFS-ügyfél** és a YaST használatával kell konfigurálni a SAP HANA adatbázis-kiszolgáló virtuális gépen:

![Az NFS-ügyfél használatával konfigurált/sapmnt-megosztás](./media/hana-get-started/image018b.jpg)

Ha elosztott NetWeaver 7,5-telepítést kíván végrehajtani, azaz egy **adatbázis-példányt**, jelentkezzen be az SAP HANA db-kiszolgáló virtuális gépre, és indítsa el a SWPM:

![Adatbázis-példány telepítése a SAP HANA adatbázis-kiszolgáló virtuális gépre való bejelentkezéssel és a SWPM elindításával](./media/hana-get-started/image019.jpg)

Miután kiválasztotta a **tipikus** telepítést és a telepítési adathordozó elérési útját, adja meg az adatbázis biztonsági azonosítóját, a gazdagép nevét, a példány számát és az adatbázis rendszergazda jelszavát:

![A SAP HANA adatbázis-rendszergazda bejelentkezési lapja](./media/hana-get-started/image035b.jpg)

Adja meg a DBACOCKPIT séma jelszavát:

![A DBACOCKPIT séma jelszavas belépési mezője](./media/hana-get-started/image036b.jpg)

Adja meg a SAPABAP1 séma jelszavának kérdését:

![Adja meg a SAPABAP1-séma jelszavának kérdését](./media/hana-get-started/image037b.jpg)

Az egyes feladatok befejezését követően zöld pipa jelenik meg az adatbázis-telepítési folyamat minden fázisa mellett. Az üzenet végrehajtása... Az adatbázis-példány befejeződött "üzenet jelenik meg.

![Feladat – befejezett ablak, megerősítő üzenet](./media/hana-get-started/image023.jpg)

A sikeres telepítés után az SAP felügyeleti konzolja az adatbázis-példányt is megjeleníti zöld ikonnal. Megjeleníti a SAP HANA folyamatok teljes listáját, például a hdbindexserver és a hdbcompileserver.

![SAP Management Console ablak SAP HANA folyamatok listájával](./media/hana-get-started/image024.jpg)

Az alábbi képernyőfelvételen a/Hana/Shared könyvtár azon részei láthatók, amelyeket a SWPM a HANA telepítésekor hozott létre. Mivel nincs lehetőség más elérési út megadására, fontos, hogy a SAP HANA telepítése előtt csatlakoztassa a további lemezterületet a/Hana könyvtárához a SWPM használatával. Ez a lépés megakadályozza, hogy a gyökérszintű fájlrendszer ne fusson el szabad területtel.

![A HANA-telepítés során létrehozott/Hana/Shared-fájl szerkezete](./media/hana-get-started/image025.jpg)

Ez a képernyőkép a/usr/SAP könyvtárának a fájljának szerkezetét mutatja:

![A/usr/SAP-könyvtár szerkezete](./media/hana-get-started/image026.jpg)

Az elosztott ABAP telepítésének utolsó lépése az elsődleges Application Server-példány telepítése:

![ABAP-telepítés, amely az elsődleges Application Server-példányt mutatja Utolsó lépésként](./media/hana-get-started/image027b.jpg)

Miután telepítette az elsődleges Application Server-példányt és az SAP GUI-t, a **DBA pilótafülke** -tranzakció segítségével ellenőrizze, hogy a SAP HANA telepítés helyesen fejeződött-e be:

![A DBA pilótafülke ablakának sikeres telepítésének megerősítése](./media/hana-get-started/image028b.jpg)

Utolsó lépésként érdemes először telepíteni a HANA studiót az SAP app Server VM-ben. Ezután kapcsolódjon az adatbázis-kiszolgáló virtuális gépen futó SAP HANA-példányhoz.

![SAP HANA Studio telepítése az SAP app Server VM-ben](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>SAP HANA manuális telepítése: HDBLCM
A SAP HANA a SWPM használatával történő elosztott telepítés részeként történő telepítése mellett a HDBLCM használatával is telepítheti a HANA önálló verzióját. Ezután telepítheti például az SAP NetWeaver 7,5-et. Az ebben a szakaszban található képernyőképek bemutatják, hogyan működik ez a folyamat.

A HANA HDBLCM eszközzel kapcsolatos további információkért lásd:

* [Válassza ki a feladat helyes SAP HANA HDBLCM](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [SAP HANA életciklus-felügyeleti eszközök](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [SAP HANA kiszolgáló telepítési és frissítési útmutatója](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

El szeretné kerülni a HDBLCM eszköz által létrehozott `\<HANA SID\>adm user`alapértelmezett csoportazonosító-beállításával kapcsolatos problémákat. Mielőtt telepítené a SAP HANAt a HDBLCM-on keresztül, Definiáljon egy `sapsys` nevű új csoportot a Group ID `1001`használatával:

![Új "sapsys" csoport, amelyet a 1001-es AZONOSÍTÓJÚ csoport határoz meg](./media/hana-get-started/image030.jpg)

Amikor első alkalommal indítja el a HDBLCM, egy egyszerű Start menü jelenik meg. Válassza az 1. elemet, és **telepítse az új rendszerszolgáltatást**:

!["Az új System telepítése" lehetőség a HDBLCM Start ablakban](./media/hana-get-started/image031.jpg)

Az alábbi képernyőfelvételen a korábban kiválasztott összes beállítás látható.

> [!IMPORTANT]
> A HANA-napló és az adatkötetek elnevezésű könyvtárai, valamint a telepítési útvonal, amely ebben a mintában/Hana/Shared, és a/usr/SAP nem lehet a gyökérszintű fájlrendszer része. Ezek a könyvtárak a virtuális géphez csatolt Azure-adatlemezekhez tartoznak. További információt a "lemez beállítása" című szakaszban talál. 

Ez a módszer segít megakadályozni, hogy a gyökér fájlrendszer kifogyjon a szabad területről. Figyelje meg, hogy a HANA-rendszergazda rendelkezik `1005` felhasználói AZONOSÍTÓval, és a `1001``sapsys` csoport része, amely a telepítés előtt lett meghatározva.

![A korábban kiválasztott összes kulcsfontosságú SAP HANA-összetevő listája](./media/hana-get-started/image032.jpg)

A/etc/passwd könyvtárban keresse meg a `\<HANA SID\>adm user` részleteit. Keresse meg a `azdadm`, ahogy az a következő képernyőképen látható:

![HANA \<HANA SID\>az ADM felhasználói adatai a/etc/passwd könyvtárban találhatók](./media/hana-get-started/image033.jpg)

Miután telepítette SAP HANA a HDBLCM használatával, a SAP HANA Studióban láthatja a fájl struktúráját, ahogy az alábbi képernyőfelvételen is látható. A SAPABAP1 séma, amely tartalmazza az összes SAP NetWeaver-táblázatot, még nem érhető el.

![SAP HANA a fájl szerkezete a SAP HANA Studióban](./media/hana-get-started/image034.jpg)

A SAP HANA telepítését követően telepítheti az SAP NetWeaver-t is. Ahogy az a következő képernyőképen is látható, a telepítést elosztott telepítésként hajtották végre a SWPM használatával. Ezt a folyamatot az előző szakaszban ismertetjük. Amikor az SWPM használatával telepíti az adatbázis-példányt, ugyanazokat az adatfájlokat adja meg a HDBLCM használatával. Megadhatja például a gazdagép nevét, a HANA SID-t és a példány számát. A SWPM ezután a meglévő HANA-telepítést használja, és további sémákat bővít.

![SWPM használatával végrehajtott elosztott telepítés](./media/hana-get-started/image035b.jpg)

Az alábbi képernyőfelvételen a SWPM telepítési lépése látható, ahol megadhatja az DBACOCKPIT séma adatait:

![A SWPM telepítési lépése, ahol a DBACOCKPIT-séma adatbevitele történik](./media/hana-get-started/image036b.jpg)

Adja meg a SAPABAP1 séma adatait:

![A SAPABAP1 séma adatainak megadása](./media/hana-get-started/image037b.jpg)

A SWPM-adatbázis példányának telepítése után a SAP HANA Studióban láthatja a SAPABAP1 sémát:

![A SAPABAP1 sémája SAP HANA Studióban](./media/hana-get-started/image038b.jpg)

Végül, miután az SAP app Server és az SAP GUI telepítése elkészült, ellenőrizze a HANA DB-példányt a **DBA pilótafülke** -tranzakció használatával:

![A HANA-adatbázis példánya ellenőrizve a DBA pilótafülke tranzakcióval](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP-szoftverek letöltése
A szoftvereket az SAP szolgáltatás piactérről töltheti le, ahogy az az alábbi képernyőképeken is látható.

Az NetWeaver 7,5 for Linux/HANA letöltése:

 ![Az SAP szolgáltatás telepítési és frissítési ablaka a NetWeaver 7,5 letöltéséhez](./media/hana-get-started/image001.jpg)

A HANA SP12 platform kiadásának letöltése:

 ![Az SAP szolgáltatás telepítési és frissítési ablaka a HANA SP12 platform kiadásának letöltéséhez](./media/hana-get-started/image002.jpg)

