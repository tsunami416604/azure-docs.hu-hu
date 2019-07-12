---
title: 'Gyors útmutató: Manuális telepítés egypéldányos SAP Hana az Azure Virtual Machinesben |} A Microsoft Docs'
description: Rövid útmutató egy példányban – SAP HANA manuális telepítéséhez az Azure Virtual machines szolgáltatásban
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 914da98359d11ff25709164d6301737404b3b011
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707670"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Gyors útmutató: Manuális telepítés egypéldányos SAP Hana az Azure Virtual machines szolgáltatásban
## <a name="introduction"></a>Bevezetés
Az útmutató segítségével állítsa be egy példányban – SAP HANA az Azure Virtual machines szolgáltatásban, az SAP NetWeaver 7,5-öt és az SAP HANA 1.0 SP12 telepítésekor manuálisan. A jelen útmutató célja az Azure-beli SAP HANA üzembe helyezése. Azt nem lecseréli az SAP dokumentációjában. 

> [!NOTE]
> Ez az útmutató Azure virtuális gépeken futó SAP Hana üzemelő. SAP HANA nagyméretű HANA-példányok üzembe helyezése információkért lásd: [használja az SAP az Azure Virtual Machinesben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy ismeri az ilyen infrastruktúra-szolgáltatás (IaaS) alapjait, mint:
 * Hogyan helyezhet üzembe virtuális gépeket (VM) vagy a virtuális hálózatok az Azure Portalon vagy a Powershellen keresztül.
 * Az Azure többplatformos parancssori felület (CLI), amely tartalmazza a sablonok a JavaScript Object Notation (JSON) lehetőséget.

Ebben az útmutatóban feltételezzük is, hogy ismeri a:
* SAP HANA és az SAP NetWeaver és hogyan telepítheti őket a helyszínen.
* Hogyan kell telepíteni, és az SAP HANA és a SAP alkalmazás példányok az Azure-ban.
* A következő fogalmak és eljárások:
   * Az Azure-ban, amely tartalmazza az Azure virtuális hálózat megtervezése és az Azure Storage használata az SAP-telepítések tervezését. Lásd: [SAP NetWeaver az Azure Virtual Machinesben – tervezési és megvalósítási útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Üzembe helyezés alapelvek és az Azure-beli virtuális gépek üzembe helyezésének módjai. Lásd: [Azure virtuális gépek üzembe helyezése, az SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Magas rendelkezésre állás az SAP NetWeaver ABAP SAP Central Services (ASCS), a SAP Central Services (SCS) és a sorba replikációs kiszolgáló (SSZON) az Azure-ban. Lásd: [magas rendelkezésre állás az SAP NetWeaver az Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Részletes információk a több biztonsági AZONOSÍTÓVAL telepítése során az Azure-ban futó ASCS/SCS hatékonyság növelése érdekében. Lásd: [hozzon létre egy SAP NetWeaver több biztonsági AZONOSÍTÓVAL konfigurációt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Alapelvek futó SAP NetWeaver Linux-alapú virtuális gépek az Azure-ban alapján. Lásd: [SAP NetWeaver futtatása Microsoft Azure SUSE Linux rendszerű virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Az útmutató az Azure-beli virtuális gépeken Linux vonatkozó beállításokat tartalmaz. Információk az Azure storage-lemez megfelelően csatlakoztatása Linux rendszerű virtuális gépek is biztosít.

A termelési forgatókönyvekhez használható Azure-beli Virtuálisgép-típusok szerepelnek a [IAAS SAP dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Éles forgatókönyvekhez natív Azure-beli Virtuálisgép-típusok választéka áll rendelkezésre.
Virtuálisgép-konfiguráció és a műveletekre vonatkozó további információkért lásd: [SAP HANA-infrastruktúra konfigurációi és a műveletek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Tekintse meg az SAP HANA magas rendelkezésre állás érdekében [SAP HANA magas rendelkezésre állás az Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Ha azt szeretné, az SAP HANA-példány vagy S/4HANA vagy BW/4HANA rendszert üzembe helyezett gyors, érdemes [SAP Cloud Appliance Library](https://cal.sap.com). Annak telepítéséről az S/4HANA rendszer SAP Cloud Appliance Library Azure-on keresztül, például a dokumentáció [Ez az útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Szüksége egy Azure-előfizetés és a egy SAP-felhasználó, aki az SAP Cloud Appliance Library regisztrálni lehet.

## <a name="additional-resources"></a>További források
### <a name="sap-hana-backup"></a>SAP HANA biztonsági mentés
Adatbázisok biztonsági mentése az SAP HANA az Azure virtuális gépekhez való további információkért lásd:
* [Azure virtuális gépeken futó SAP Hana biztonsági mentési útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [Az SAP HANA az Azure Backup a fájlok szintjére](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [SAP HANA biztonsági mentés tárolási pillanatképeken alapuló](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
Üzembe helyezése az S/4HANA, BW/4HANA vagy SAP Cloud Appliance Library használatával kapcsolatos információkért lásd: [üzembe helyezése az SAP S/4HANA vagy BW/4hana-t a Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Az SAP HANA által támogatott operációs rendszerek
SAP HANA által támogatott operációs rendszerekkel kapcsolatos információkért lásd: [SAP Megjegyzés 2235581 – SAP HANA: Támogatott operációs rendszerek](https://launchpad.support.sap.com/#/notes/2235581/E). Azure virtuális gépek ezen operációs rendszerek csak egy részhalmazát támogatjuk. Azure-beli SAP HANA üzembe helyezéséhez a következő operációs rendszerek támogatottak: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

További SAP dokumentációjában az SAP HANA és a különböző Linux operációs rendszert lásd:

* [SAP-Jegyzetnek 171356: SAP-szoftverek Linux rendszeren: Általános információk](https://launchpad.support.sap.com/#/notes/1984787).
* [SAP-Jegyzetnek 1944799: SLES operációs rendszer telepítése a SAP HANA-irányelvek](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
* [SAP Note 2205917: SAP HANA-adatbázis ajánlott az operációs rendszer beállításait a SLES 12 rendszert SAP-alkalmazások](https://launchpad.support.sap.com/#/notes/2205917/E).
* [SAP Note 1391070: Linux-UUID megoldások](https://launchpad.support.sap.com/#/notes/1391070).
* [SAP Note 2009879: SAP HANA-irányelvek Red Hat Enterprise Linux (RHEL) operációs rendszerhez](https://launchpad.support.sap.com/#/notes/2009879).
* [SAP Note 2292690: SAP HANA DB: Ajánlott az operációs rendszer beállításai RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>Az SAP az Azure-ban figyelése
Az SAP az Azure-ban figyelésével kapcsolatos információk:

* [Megjegyzés: 2191498 SAP](https://launchpad.support.sap.com/#/notes/2191498/E) továbbfejlesztett SAP használata Linux rendszerű virtuális gépek az Azure-ban ismerteti. 
* [Megjegyzés: 1102124 SAP](https://launchpad.support.sap.com/#/notes/1102124/E) linuxon SAPOSCOL kapcsolatos tudnivalókat ismerteti. 
* [Megjegyzés: 2178632 SAP](https://launchpad.support.sap.com/#/notes/2178632/E) ismerteti az alapvető figyelési metrikákat az SAP a Microsoft Azure-ban.

### <a name="azure-vm-types"></a>Az Azure virtuális gépek típusai
Az Azure Virtuálisgép-típusok és az SAP HANA használt SAP által támogatott munkaterhelés-forgatókönyvek szerepelnek [SAP-tanúsítvánnyal rendelkező IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Az SAP az SAP NetWeaver és az S/4HANA alkalmazásrétegre hitelesített Azure Virtuálisgép-típusok vannak dokumentálva [SAP Megjegyzés 1928533: SAP-alkalmazások az Azure-ban: Támogatott termékek és Azure-beli Virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533/E).

> [!NOTE]
> Integráció az SAP-Linux-Azure-hoz csak Azure Resource Manager és a klasszikus üzemi modellben nem támogatott. 

## <a name="manual-installation-of-sap-hana"></a>SAP HANA manuális telepítése

> [!IMPORTANT]
> Győződjön meg róla, hogy az operációs rendszer választja SAP-minősítéssel rendelkező SAP Hana, az adott virtuális gépek típusai, használja a. Az SAP HANA listája tanúsított Virtuálisgép-típusok és az operációs rendszer kiadja az adott virtuális gépek típusai pozíciójuk [SAP HANA-tanúsítvánnyal rendelkező IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ellenőrizze, hogy a virtuális gép típusú lekérni az SAP HANA által támogatott operációsrendszer-kiadások teljes listáját az adott VM-típus szerepel a Részletek gombra. A példában a jelen dokumentum nem támogatott az SAP az SAP HANA az M-sorozat virtuális gépei SUSE Linux Enterprise Server (SLES) operációs rendszer kiadási használtuk.
>

Ez az útmutató ismerteti, hogyan manuális telepítéséhez az SAP HANA az Azure virtuális gépek két különböző módon:

* SAP Software kiépítés Manager (SWPM) használja a "telepítés adatbázispéldány" lépésben egy elosztott NetWeaver telepítésének részeként.
* Az SAP HANA database lifecycle manager eszközzel (HDBLCM), és telepítse a NetWeaver.

Használhatja a SWPM összetevők, például SAP HANA, SAP-alkalmazáskiszolgáló és az ASCS példány egy egyetlen virtuális gép telepítése. A lépések leírását a jelen [SAP HANA bejelentést a blogon](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Ezt a beállítást a nem a jelen rövid útmutatóban ismertetett, de figyelembe kell venni a problémákat azonosak.

Egy telepítés előtt javasoljuk, hogy olvassa el a "előkészítése az Azure virtuális gépek manuális telepítéséhez az SAP HANA" Ez az útmutató későbbi szakaszában talál. Ezzel megakadályozhatja néhány alapvető hibák csak egy alapértelmezett Azure-beli Virtuálisgép-konfiguráció használatakor előforduló.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Telepítés az SAP HANA, SAP SWPM használatakor a legfontosabb lépések
Ez a szakasz felsorolja a fontos lépések az SAP HANA, Egypéldányos manuális telepítési, SAP SWPM SAP NetWeaver 7.5 elosztott telepítés használatakor. Az egyes lépéseket mutatjuk be a képernyőfelvételeken az útmutató későbbi részében részletesebben.

1. Hozzon létre egy Azure virtuális hálózat, amely két teszt virtuális gépeket tartalmazza.
2. Telepítse a két Azure virtuális gépek operációs rendszerekkel, az Azure Resource Manager-modell alapján. Ebben a példában a SUSE Linux Enterprise Server és a SLES SAP alkalmazások 12 SP1 használ. 
3. Két Azure standard vagy prémium szintű tárolólemezeket, például 75 GB-os vagy 500 GB-os lemezt, az alkalmazás-kiszolgáló virtuális Géphez csatolása.
4. Prémium szintű storage-lemez csatlakoztatható a HANA-adatbázis-kiszolgáló virtuális Géphez. További információkért lásd a "Lemez beállítása" szakaszban az útmutató későbbi részében.
5. Mérete vagy feldolgozási sebessége követelményeitől függően több lemez csatlakoztatható. Ezután hozzon létre csíkozott köteteken tárolni. Logikaikötet-kezelő (LVM) és a egy több-eszközök (mdadm) felügyeleti eszköz használata a virtuális gép operációs rendszer szintjén.
6. Hozzon létre a csatlakoztatott lemezek vagy kötetek logikai XFS fájlrendszerek.
7. Csatlakoztassa az új XFS fájlrendszer az operációs rendszer szintjén. Az SAP-szoftvereket egy fájlrendszert használja. Például a /sapmnt directory és a biztonsági másolatok, használja a fájlrendszer. Az SAP HANA-adatbázis-kiszolgálón csatlakoztassa a prémium szintű tárolólemezeket /hana és /usr/sap XFS fájlrendszerek. Ez a folyamat szükség, hogy a legfelső szintű fájlrendszer betelik. A legfelső szintű fájlrendszer nem nagyméretű Linuxos Azure virtuális gépeken. 
8. Adja meg a teszt virtuális gépek helyi IP-címét a Hosts fájlt.
9. Adja meg a **nofail** az/etc/fstab fájl paramétert.
10. Állítsa be a Linux kernel paraméterek használata a Linux operációsrendszer-kiadás alapján. További információ az SAP-megjegyzések megfigyelését, HANA, és a jelen útmutató "Kernel paraméterek" című szakaszában talál.
11. A lapozóterület bővítése.
12. Szükség esetén telepítse a teszt virtuális gépek egy grafikus asztali. Ellenkező esetben használjon távoli SAPinst telepítés.
13. Töltse le az SAP-szoftvereket az SAP Service Marketplace-ről.
14. Az SAP ASCS-példány telepíthető az alkalmazás-kiszolgáló virtuális Géphez.
15. Ossza meg a teszt virtuális gépek között a /sapmnt könyvtár NFS használatával. A virtuális gép kiszolgáló az NFS-kiszolgáló.
16. Telepítse az adatbázispéldányt, amely tartalmazza a HANA, az adatbázis-kiszolgáló virtuális gép SWPM használatával.
17. Az elsődleges (szolgáltatói CÍMEI) kiszolgáló telepíthető az alkalmazás-kiszolgáló virtuális Géphez.
18. Indítsa el az SAP felügyeleti konzolt (SAP minősítéssel, MC). Jelentkezzen be az SAP grafikus felhasználói felületének vagy a HANA Studio, például.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Fontos lépések az SAP HANA telepítése HDBLCM használatakor
Ez a szakasz felsorolja a fontos lépések az SAP HANA, Egypéldányos manuális telepítési, SAP HDBLCM SAP NetWeaver 7.5 elosztott telepítés használatakor. Az egyes lépéseket mutatjuk be részletesen ebben az útmutatóban a képernyőfelvételeken.

1. Hozzon létre egy Azure virtuális hálózat, amely két teszt virtuális gépeket tartalmazza.
2. Telepítse a két Azure virtuális gépek operációs rendszerekkel, az Azure Resource Manager-modell alapján. Ebben a példában SLES és SLES for SAP alkalmazások 12 SP1.
3. Két Azure standard vagy prémium szintű tárolólemezeket, például 75 GB-os vagy 500 GB-os lemezt, az alkalmazás-kiszolgáló virtuális Géphez csatolása.
4. Prémium szintű storage-lemez csatlakoztatható a HANA-adatbázis-kiszolgáló virtuális Géphez. További információkért lásd a "Lemez beállítása" szakaszban az útmutató későbbi részében.
5. Mérete vagy feldolgozási sebessége követelményeitől függően több lemez csatlakoztatható. Hozzon létre csíkozott kötetek vagy logikai adatmennyiség-kezelés, vagy egy mdadm eszköz a virtuális gép operációs rendszer szintjén.
6. Hozzon létre a csatlakoztatott lemezek vagy kötetek logikai XFS fájlrendszerek.
7. Csatlakoztassa az új XFS fájlrendszer az operációs rendszer szintjén. Az SAP-szoftvereket egy fájlrendszert használja. Például a /sapmnt directory és a biztonsági másolatok, használja a fájlrendszer. Az SAP HANA-adatbázis-kiszolgálón csatlakoztassa a prémium szintű tárolólemezeket /hana és /usr/sap XFS fájlrendszerek. Ez a folyamat szükség segítenek megakadályozni, hogy a legfelső szintű fájlrendszer betelőben. A legfelső szintű fájlrendszer nem nagyméretű Linuxos Azure virtuális gépeken.
8. Adja meg a teszt virtuális gépek helyi IP-címét a Hosts fájlt.
9. Adja meg a **nofail** az/etc/fstab fájl paramétert.
10. Állítsa be a kernel-paraméterek használata a Linux operációsrendszer-kiadás alapján. További információ az SAP-megjegyzések megfigyelését, HANA, és a jelen útmutató "Kernel paraméterek" című szakaszában talál.
11. A lapozóterület bővítése.
12. Szükség esetén telepítse a teszt virtuális gépek egy grafikus asztali. Ellenkező esetben használjon távoli SAPinst telepítés.
13. Töltse le az SAP-szoftvereket az SAP Service Marketplace-ről.
14. Hozzon létre egy, sapsys, a HANA-adatbázis-kiszolgáló virtuális gép azonosítója 1001, csoporttal.
15. SAP HANA HANA database lifecycle manager használatával telepítse az adatbázis-kiszolgáló virtuális Géphez.
16. Az SAP ASCS-példány telepíthető az alkalmazás-kiszolgáló virtuális Géphez.
17. Ossza meg a teszt virtuális gépek között a /sapmnt könyvtár NFS használatával. A virtuális gép kiszolgáló az NFS-kiszolgáló.
18. Telepítse az adatbázispéldányt, HANA, beleértve a HANA-adatbázis-kiszolgáló virtuális gép SWPM használatával.
19. Az elsődleges kiszolgáló telepíthető az alkalmazás-kiszolgáló virtuális Géphez.
20. Start SAP MC. SAP grafikus felhasználói felületének vagy a HANA Studio csatlakozhat.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Manuális telepítés az SAP Hana Azure virtuális gépek előkészítése
Ez a szakasz témakörei a következők:

* Operációs rendszer frissítése
* Lemez beállítása
* Kernel-paraméterek
* Fájlrendszer
* A/etc/hosts fájl
* A/etc/fstab fájl

### <a name="os-updates"></a>Operációs rendszer frissítése
Ellenőrizze a Linux operációs rendszer frissítéseket és javításokat, további szoftverek telepítése előtt. Telepítse a javítást, elkerülheti a támogatási ügyfélszolgálati hívást.

Győződjön meg arról, hogy használja:
* SUSE Linux Enterprise Server, SAP-alkalmazások számára.
* Red Hat Enterprise Linux for SAP-alkalmazások vagy a Red Hat Enterprise Linux for SAP HANA. 

Ha még nem tette meg, a Linux-gyártó a Linux előfizetését regisztrálja az operációs rendszer telepítéséhez. SUSE rendelkezik az SAP-alkalmazások, amelyek már szolgáltatásokat tartalmaznak, és automatikusan regisztrált operációsrendszer-lemezképeket.

Íme egy példa bemutatja, hogyan keressen elérhető javítások SUSE Linux segítségével a **zypper** parancsot:

 `sudo zypper list-patches`

A probléma típusától függően a javítások kategória és súlyosság szerint besorolt. A kategória gyakran használt értékek a következők: 
- Biztonság
- Ajánlott
- Optional
- Funkció
- Dokumentum
- yast

Esetében gyakran használt értékek a következők:

- Kritikus
- Fontos
- Mérsékelt
- Alacsony
- Nincs megadva

A **zypper** parancs csak megkeresi a telepített csomagokat kell a frissítéseket. Például használhatja ezt a parancsot:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

A paraméter is hozzáadhat `--dry-run` tesztelése a frissítés a rendszer ténylegesen frissítése nélkül.


### <a name="disk-setup"></a>Lemez beállítása
A legfelső szintű fájlrendszer, a Linux rendszerű virtuális gép az Azure-ban rendelkezik egy korlátozás. Tehát szüksége további lemezterületre csatlakoztatása egy Azure virtuális gép futtatása az SAP. Az SAP-alkalmazáskiszolgáló Azure virtuális gépek a standard szintű Azure storage használatát elég lehet. Az SAP HANA DBMS Azure virtuális gépek az Azure prémium szintű tárolólemezeket a termelési és tesztelési megvalósításokhoz használata kötelező.

Alapján a [SAP HANA TDI tárhellyel kapcsolatos követelmények](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), a javaslat a következő prémium szintű Azure storage-konfiguráció: 

| A VM-TERMÉKVÁLTOZATOK | RAM |  / hana/adat- és naplófájlok/hana / <br /> az LVM vagy mdadm csíkozott | / hana/megosztott | / root kötet | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

A javasolt lemez konfigurációját a HANA adatmennyiség és a naplózási kötet ugyanahhoz az adatkészlethez, a prémium szintű Azure storage-lemez LVM vagy mdadm csíkozott helyezni. Nem szükséges meghatározni bármilyen RAID tárhelyredundancia-szint, mert az Azure premium storage tartja a redundancia biztosítása érdekében a lemezek három kép. 

Győződjön meg arról, hogy konfigurálja-e elegendő tárhely, lásd: [SAP HANA TDI tárhellyel kapcsolatos követelmények](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) és [SAP HANA kiszolgáló telepítési és frissítési útmutató](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Is vegye figyelembe a különböző Azure prémium szintű tárolólemezek különböző virtuális merevlemez (VHD) átviteli mennyiségű leírtak szerint [nagy teljesítményű premium storage és a felügyelt lemezek virtuális gépekhez](../../windows/disks-types.md). 

Hozzáadhat további prémium szintű tárolólemezeket a HANA DBMS virtuális gépekhez, adatbázisban vagy tranzakciónaplóban naplóalapú biztonsági mentések tárolásához.

A csíkozást konfigurálásához használt két fő eszközökkel kapcsolatos további információkért lásd:

* [Szoftveres RAID linuxon konfigurálása](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [LVM konfigurálása az Azure-beli Linuxos virtuális gépre](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Bemutatja, hogyan csatlakoztathat lemezeket egy vendég operációs rendszer Linux futtató Azure virtuális gépekre vonatkozó további információkért lásd: [lemez hozzáadása Linux rendszerű virtuális gép](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Az Azure prémium szintű SSD-k lemez-gyorsítótárazást módok adhatja meg. A csíkozott a készlet, amely tartalmazza a /hana/data és /hana/log tiltsa le a lemezek gyorsítótárazása. A kötetek, azt jelenti, lemezek, a gyorsítótárazási mód beállítása legyen **ReadOnly**.

Példa JSON-sablonok használata virtuális gépek létrehozásához lásd: a [Azure gyorsindítási sablonok](https://github.com/Azure/azure-quickstart-templates).
A virtuálisgép-egyszerű – sles alapszintű sablon áll. Egy tároló rész alatt, egy további 100 GB-os adatlemezt tartalmaz. Ez a sablon használja alapként. A sablon az adott konfigurációhoz tesztkörnyezetéhez igazíthatja.

> [!NOTE]
> Fontos, hogy az Azure storage-lemez csatolása UUID segítségével dokumentált módon [SAP NetWeaver futtatása Microsoft Azure SUSE Linux rendszerű virtuális gépeken](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A tesztelési környezetben két Azure standard szintű storage-lemez is csatlakozik a virtuális gép, SAP-alkalmazáskiszolgáló az alábbi képernyőképen látható módon. Egy SAP szoftverek, például a NetWeaver 7,5-öt, a SAP grafikus felhasználói felület és az SAP HANA telepítés tárolják. A második lemez biztosítja, hogy elég szabad terület érhető el a további követelményekről. Például biztonsági mentés és a vizsgálati adatokat és a /sapmnt könyvtárban, azaz SAP profilok, kell az azonos SAP-rendszeren tartozó összes virtuális gép közötti megosztását.

![SAP HANA app server lemezek ablak megjelenítése a két adatlemezt, és azok méretek](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Kernel-paraméterek
Az SAP HANA megköveteli az adott Linux-kernel beállításait. A rendszermag-beállítások a standard szintű Azure-katalógus rendszerképek részét képezik, és manuálisan kell beállítani. Ha használja SUSE vagy a Red Hat a paraméterek eltérő lehet. Az SAP-megjegyzések felsorolt korábban ezeket a paramétereket kapcsolatos információkat biztosítanak. A képernyőfelvételen látható a SUSE Linux 12 SP1 lett megadva. 

Az SLES for SAP alkalmazások 12 általános rendelkezésre állás és a SLES for SAP alkalmazások 12 SP1 rendelkezik egy új eszköz **lehetőségeire adm**, amely felváltja a régi **sapconf** eszközt. Egy speciális SAP HANA-profil érhető el az **lehetőségeire adm**. A rendszer finomhangolása az SAP Hana-hoz, adja meg az ehhez a profilhoz root felhasználóként:

   `tuned-adm profile sap-hana`

További információ **lehetőségeire adm**, tekintse meg a [lehetőségeire adm SUSE dokumentációjában](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Az alábbi képernyőképen látható hogyan **lehetőségeire adm** megváltozott a `transparent_hugepage` és `numa_balancing` értékeket, a szükséges SAP HANA-beállításoknak megfelelően:

![A beállított adm eszköz módosítja a szükséges SAP HANA-beállításoknak megfelelően értékek](./media/hana-get-started/image005.jpg)

Az SAP HANA-kernel beállítások véglegesítéséhez használja **grub2** a SLES 12 rendszert. További információ **grub2**, tekintse meg a [konfigurációs fájlstruktúra](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) a SUSE-dokumentáció része.

Az alábbi képernyőfelvételen a kernel beállításokat módosítani a konfigurációs fájlban és majd lefordítják azt használatával hogyan **grub2-mkconfig**:

![Kernel beállításokat módosítani a konfigurációs fájlban, és grub2-mkconfig használatával összeállított](./media/hana-get-started/image006.jpg)

Egy másik lehetőség a beállítások módosítása YaST használatával, és a **rendszertöltő** > **Kernel paraméterek** beállítások:

![A Kernel paraméterek beállítások lapján YaST rendszertöltő](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Fájlrendszer
Az alábbi képernyőfelvételen két fájlrendszereket, a SAP alkalmazás kiszolgáló virtuális gép felett a két csatlakoztatott Azure standard szintű storage-lemez lettek létrehozva. Mindkét fájlrendszerek XFS típusú és /sapdata és /sapsoftware csatlakoztatva vannak.

Nem kötelező, hogy megtervezhessük a fájlrendszerek ezzel a módszerrel. A lemezterület strukturálhatók más lehetősége van. A legfontosabb szempont, hogy a legfelső szintű fájlrendszer tiltsa le a futását szabad lemezterületét.

![Az SAP-alkalmazáskiszolgáló virtuális Géphez létrehozott két fájlrendszerek](./media/hana-get-started/image008.jpg)

SAP HANA DB a virtuális gép, egy SAPinst rendelkező SWPM használatakor adatbázis a telepítés során, és a **tipikus** telepítésen, minden /hana és /usr/sap van telepítve. Az alapértelmezett hely a SAP HANA-naplók biztonsági mentése /usr/sap alatt áll. Újra fontos, hogy a legfelső szintű fájlrendszer kevés a tárolási hely. Győződjön meg arról, hogy elég szabad terület alatt /hana és /usr/sap SWPM az SAP HANA telepítése előtt.

Az SAP HANA standard fájlrendszer elrendezését, olvassa el a [SAP HANA kiszolgáló telepítési és frissítési útmutató](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Az SAP-alkalmazáskiszolgáló virtuális Géphez létrehozott további fájlrendszerek](./media/hana-get-started/image009.jpg)

Ha egy standard SLES/SLES for SAP alkalmazások 12 Azure image z galerie SAP NetWeaver telepíti, egy üzenet jelenik meg arról, hogy a hiba nem lapozóterület, az alábbi képernyőképen látható módon. Ez az üzenet elvetéséhez, manuálisan is hozzáadhat egy lapozófájl használatával **nn**, **mkswap**, és **swapon**. Megtudhatja, hogyan, keresse meg "Egy lapozófájl kézi hozzáadása" az a [használatával a YaST partitioner](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) a SUSE-dokumentáció része.

Egy másik lehetőség, hogy a lapozófájl-kapacitás konfigurálása a Linux rendszerű Virtuálisgép-ügynök használatával. További információk: [Azure Linux-ügynök – felhasználói útmutató](../../extensions/agent-linux.md).

![Felbukkanó üzenet tájékoztatja, hogy nincs-e elegendő lapozófájl-kapacitás](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>A/etc/hosts fájl
Mielőtt elkezdené telepíteni az SAP, győződjön meg arról, szerepeltetni kívánt állomás nevét és az SAP virtuális gépek IP-címek a Hosts fájlt. Minden az SAP virtuális gép üzembe helyezése egy Azure virtuális hálózaton belül. Ezután használja a belső IP-címek, itt látható módon:

![Gazdagép nevét és a Hosts fájlban felsorolt SAP virtuális IP-címek](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>A/etc/fstab fájl

Hasznos lehet hozzáadni a **nofail** paramétert az fstab fájlt. Így ha probléma merül fel a lemezeket, a virtuális gép nem válaszol a rendszerindítási folyamat során. De ne feledje, hogy további lemezterület nem feltétlenül érhető el, és a folyamatok előfordulhat, hogy töltse fel a legfelső szintű fájlrendszer. Ha /hana hiányzik, a SAP HANA nem indul el.

![Adja hozzá a nofail paramétert az fstab fájlt](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>A SLES 12/SLES for SAP alkalmazások 12 grafikus GNOME asztali
Ez a szakasz azt ismerteti, hogyan lehet:

* Telepíti a GNOME asztali és xrdp SLES 12/SLES for SAP alkalmazások 12.
* Futtassa SAP-MC Java-alapú Firefox segítségével SLES 12/SLES for SAP alkalmazások 12.

Lehetőségeket, például Xterminal vagy VNC, amely nem a jelen útmutatóban ismertetett is használhatja.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Telepíti a GNOME asztali és xrdp SLES 12/SLES for SAP alkalmazások 12
Ha Windows háttér, Firefox, SAPinst, SAP grafikus felhasználói Felülettel, az SAP minősítéssel, MC vagy HANA Studio futtatásához egyszerűen használhatja a grafikus asztali közvetlenül az SAP Linux rendszerű virtuális gépeken belül. Ezután csatlakozhat a virtuális géphez a távoli asztal protokoll (RDP) keresztül egy Windows-számítógépen. Attól függően, a GUI éles üzemi pontjának és költségmegtakarítási Linux-alapú rendszerekhez történő hozzáadásával kapcsolatos vállalati szabályzatok érdemes GNOME telepítése a kiszolgálón. Kövesse az alábbi lépéseket a GNOME asztali telepítése egy Azure SLES 12/SLES SAP alkalmazások 12 virtuális gép számára.

1. Telepítse a GNOME desktop a következő parancs megadásával, például PuTTY ablakban:

   `zypper in -t pattern gnome-basic`

2. Telepítse a xrdp, hogy a virtuális gép RDP-Kapcsolaton keresztül csatlakozni:

   `zypper in xrdp`

3. /Etc/sysconfig/windowmanager szerkesztheti, majd állítsa be az alapértelmezett kezelő GNOME:

   `DEFAULT_WM="gnome"`

4. Futtatás **chkconfig** , győződjön meg arról, hogy xrdp automatikusan elindul, a rendszer újraindítása után:

   `chkconfig -level 3 xrdp on`

5. Ha az RDP-kapcsolat problémáját, próbálja meg újraindítani, például a PuTTY ablakából:

   `/etc/xrdp/xrdp.sh restart`

6. Ha xrdp újra kell indítani az előző lépésben említett nem működik, ellenőrizze .pid fájlba:

   `check /var/run` 

   Keressen `xrdp.pid`. Ha megtalálta, távolítsa el, és indítsa újra.

### <a name="start-sap-mc"></a>Start SAP MC
Miután telepítette a GNOME asztali, indítsa el a grafikus Java-alapú SAP minősítéssel, MC Firefox. Fut az Azure SLES 12/SLES SAP alkalmazások 12 virtuális géphez, ha azt hiba jelenhet meg. A hiba miatt a beépülő modul hiányzó Java böngészőben jelenik meg.

Az URL-cím, az SAP minősítéssel, MC elindításához `<server>:5<instance_number>13`.

További információkért lásd: [a web-alapú SAP-felügyeleti konzol elindítása](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Az alábbi képernyőképen látható a hibaüzenet fog megjelenni, amikor a Java-böngészőbeli beépülő modul nem található:

![Hiányzó Java-böngészőbővítményt jelző hibaüzenet](./media/hana-get-started/image013.jpg)

A probléma megoldásához egyik módja, hogy telepítése a hiányzó beépülő modul használatával YaST, az alábbi képernyőképen látható módon:

![Hiányzik a beépülő modul telepítéséhez YaST használatával](./media/hana-get-started/image014.jpg)

Ha az SAP felügyeleti konzol URL-cím újbóli kéri, hogy a beépülő modul aktiválása:

![Beépülő modul aktiválási kérő párbeszédpanel](./media/hana-get-started/image015.jpg)

Akkor is megjelenik, megjelenik egy hibaüzenet, hiányzó fájl javafx.properties. Oracle Java 1.8-as SAP grafikus felhasználói Felülettel 7.4 vonatkozó követelmény vonatkozik. További információkért lásd: [SAP Megjegyzés 2059429](https://launchpad.support.sap.com/#/notes/2059424).
Az IBM-Java-verzió és a-t használó az SLES/SLES for SAP alkalmazások 12 openjdk csomagját csomag nem tartalmazza a szükséges javafx.properties fájlt. A megoldás, hogy töltse le és telepítse a Java SE 8 Oracle.

Az opensuse-alapú openjdk csomagját hasonló hibával kapcsolatos információkért tekintse meg a hozzászóláslánc [SAPGui 7.4 Java, az openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>SAP HANA manuális telepítése: SWPM
Az ebben a szakaszban szereplő képernyőképek sorozatát telepítése a SAP NetWeaver 7,5-öt és az SAP HANA SP12 SWPM rendelkező SAPinst használatakor fő lépéseit mutatja be. A NetWeaver 7.5 telepítésének részeként SWPM is telepítheti a HANA-adatbázis egy példányban.

Egy minta tesztkörnyezetben egy speciális üzleti alkalmazás programozási (ABAP) alkalmazás-kiszolgálót telepítette. Ahogy az az alábbi képernyőfelvételen is látható, használtuk a **elosztott rendszer** egy Azure-beli virtuális gépen az ascs rendszerbe fut be, és az elsődleges alkalmazáskiszolgáló-példányok telepítését. SAP HANA database rendszer egy másik Azure-beli virtuális gépen használtuk.

![Ascs rendszerbe fut be, és az elosztott rendszer lehetőség használatával telepíti elsődleges alkalmazáskiszolgáló-példányok](./media/hana-get-started/image012.jpg)

Az ASCS-példány telepítve van az app-kiszolgáló virtuális Géphez, miután az SAP-kezelőkonzolon zöld ikonnal által azonosítható. A /sapmnt könyvtár, amely magában foglalja az SAP-profil directory, az SAP HANA-adatbázis-kiszolgáló virtuális Géphez való megosztása. Az adatbázis-telepítési lépés ezt az információt hozzá kell férnie. Hozzáférés biztosítása a legjobb módja, hogy használja az NFS, amely YaST használatával konfigurálható.

![A virtuális gép, egy zöld ikonnal alkalmazás kiszolgálóra telepített ASCS példány megjelenítő SAP kezelőkonzol](./media/hana-get-started/image016.jpg)

Az alkalmazás kiszolgálón virtuális gép a /sapmnt könyvtárban megosztott NFS-n keresztül használatával a **rw** és **no_root_squash** beállítások. Az alapértelmezett érték **ro** és **root_squash**, amely vezethet problémákat, ha az adatbázis-példány telepítését.

![Az NFS-n keresztül /sapmnt könyvtár megosztása rw és no_root_squash lehetőségeinek használatával](./media/hana-get-started/image017b.jpg)

Ahogy a következő képernyőfelvételen látható, az alkalmazás-kiszolgáló virtuális Gépről a /sapmnt megosztást kell konfigurálni az SAP HANA-adatbázis-kiszolgáló virtuális gép használatával **NFS-ügyfél** és YaST:

![Az NFS-ügyfél segítségével konfigurált /sapmnt megosztás](./media/hana-get-started/image018b.jpg)

Elosztott NetWeaver 7.5 telepítés végrehajtásához, azaz egy **adatbázispéldány**, jelentkezzen be az SAP HANA-adatbázis-kiszolgáló virtuális Géphez, és indítsa el a SWPM:

![Egy adatbázis-példányt telepítése bejelentkezik, az SAP HANA-adatbázis-kiszolgáló virtuális Géphez, és SWPM indítása](./media/hana-get-started/image019.jpg)

Miután kiválasztotta **tipikus** telepítési és a telepítési adathordozó elérési útját adja meg a DB SID-AZONOSÍTÓVAL, a gazdagép nevét, a példányok számát és a DB rendszergazda jelszava:

![Az SAP HANA database rendszer rendszergazdai bejelentkezési oldal](./media/hana-get-started/image035b.jpg)

Adja meg a jelszót a DBACOCKPIT séma:

![A DBACOCKPIT séma be a jelszó-entry](./media/hana-get-started/image036b.jpg)

Adjon meg egy kérdést a SAPABAP1 séma jelszó:

![Adjon meg egy kérdést a SAPABAP1 séma jelszó](./media/hana-get-started/image037b.jpg)

Után minden feladat befejeződött, a DB telepítési folyamat egyes fázisaiban mellett egy zöld pipa jelenik meg. Az üzenet "végrehajtási... Az adatbázis példány befejeződött"jelenik meg.

![A feladat befejeződött-ablakot megerősítő üzenet](./media/hana-get-started/image023.jpg)

A sikeres telepítés után az SAP-kezelőkonzolon zöld ikonnal adatbázispéldánnyal is látható. It displays the full list of SAP HANA processes, such as hdbindexserver and hdbcompileserver.

![SAP felügyeleti konzol ablakának az SAP HANA-folyamatok listája](./media/hana-get-started/image024.jpg)

Az alábbi képernyőfelvételen a fájlstruktúra a HANA-telepítés során létrehozott SWPM /hana/shared könyvtárban részeit. Mivel nincs lehetőség egy másik elérési utat adja meg, fontos SWPM használatával további lemezterületet a SAP HANA-telepítés előtt /hana könyvtár alá csatlakoztatásához. Ebben a lépésben megakadályozza, hogy a legfelső szintű fájlrendszer szabad lemezterületét futását.

![HANA telepítése során létrehozott /hana/shared directory fájlstruktúra](./media/hana-get-started/image025.jpg)

Ezen a képernyőfelvételen látható a fájlstruktúra a /usr/sap könyvtár:

![A/usr/sap directory fájlstruktúra](./media/hana-get-started/image026.jpg)

Az utolsó lépés az elosztott ABAP-telepítés a rendszer telepíti az elsődleges alkalmazás server-példány:

![ABAP telepítési ábrázoló elsődleges application server-példány, az utolsó lépés](./media/hana-get-started/image027b.jpg)

Miután az elsődleges alkalmazás server-példány és az SAP grafikus felhasználói Felülettel van telepítve, a **DBA vezérlőpultja** tranzakció, győződjön meg arról, hogy a az SAP HANA-telepítés megfelelően fejeződött be:

![A sikeres telepítés megerősítésével DBA vezérlőpultja ablakban](./media/hana-get-started/image028b.jpg)

Utolsó lépésként érdemes, előbb telepítenie HANA Studio, a SAP app Server virtuális gép. Az SAP HANA-példány, amelyen fut az adatbázis-kiszolgáló virtuális Géphez, majd csatlakozni.

![Az SAP HANA Studio telepítése a SAP app Server virtuális gép](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>SAP HANA manuális telepítése: HDBLCM
SAP HANA telepítése elosztott telepítés részeként SWPM használatával, valamint telepítheti a HANA különálló először HDBLCM használatával. SAP NetWeaver 7.5, például Ezután telepítheti. Ebben a szakaszban a képernyőfelvételek bemutatják, hogyan működik ez a folyamat.

A HANA HDBLCM eszközzel kapcsolatos további információkért lásd:

* [Válassza ki a megfelelő SAP HANA HDBLCM a feladat](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [SAP HANA-életciklus kezelőeszközök](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [SAP HANA kiszolgáló telepítési és frissítési útmutató](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

El szeretné kerülni a csoport azonosítója beállítási problémáit a `\<HANA SID\>adm user`, amely a HDBLCM eszközzel hozza létre. Mielőtt telepíti az SAP HANA-n keresztül HDBLCM, definiálása nevű új csoport `sapsys` Csoportazonosító használatával `1001`:

![Új csoport "sapsys" használatával meghatározott csoport 1001-es azonosító](./media/hana-get-started/image030.jpg)

HDBLCM először indítja el, ha egy egyszerű start menü jeleníti meg. 1 elem kiválasztása **új rendszer telepítése**:

!["Az új rendszer telepítése" beállítást a HDBLCM indítási ablak](./media/hana-get-started/image031.jpg)

Az alábbi képernyőképen a kulcsok beállításainak korábban kiválasztott jeleníti meg.

> [!IMPORTANT]
> A HANA napló- és adatkötetek, és a telepítési útvonalat, amely ebben a mintában, és /usr/sap /hana/shared nevű könyvtárak nem lehet a legfelső szintű fájlrendszer egy részét. Ezeket a könyvtárakat az Azure-adatlemezek, amely a virtuális Géphez csatolt is tartozik. További információkért lásd a "Lemez beállítása" című szakaszt. 

Ez a megközelítés segít a legfelső szintű fájlrendszer tiltsa le a futását lemezterületét. Figyelje meg, hogy a HANA-rendszergazda rendelkezik-e a felhasználói azonosító `1005` részét képezi a `sapsys` csoport azonosítója és `1001`, a telepítés előtt definiálva lett.

![Korábban kiválasztott összes fő SAP HANA összetevők listáját](./media/hana-get-started/image032.jpg)

Ellenőrizze a `\<HANA SID\>adm user` részletei a/etc/passwd könyvtárban. Keressen `azdadm`, ahogy az alábbi képernyőfelvételen látható:

![HANA \<HANA SID\>az/etc/passwd directory felsorolt adm – felhasználó adatai](./media/hana-get-started/image033.jpg)

Miután telepítette az SAP HANA HDBLCM használatával, láthatja a fájlstruktúra a SAP HANA Studio, az alábbi képernyőképen látható módon. SAPABAP1 séma, amely tartalmazza az SAP NetWeaver-táblázatok, még nem érhető el.

![Az SAP HANA Studio SAP HANA-fájlstruktúra](./media/hana-get-started/image034.jpg)

Miután telepítette az SAP HANA, SAP NetWeaver megkönnyítése is telepítheti. Amint az alábbi képernyőképen látható, a telepítés egy elosztott telepítésben, hajtotta végre SWPM használatával. Ez a folyamat az előző szakaszban leírt. Ha az adatbázispéldányt SWPM használatával telepít, az HDBLCM használatával adja meg ugyanazokat az adatokat. Például adja meg a gazdagépnév, a HANA SID és a példányok számát. SWPM ezután használja a már meglévő HANA-telepítés, és hozzáadja a sémák.

![Egy elosztott telepítés SWPM használatával](./media/hana-get-started/image035b.jpg)

A következő képernyőképen látható a SWPM telepítési lépés, ahol a DBACOCKPIT sémára vonatkozó adatokat adja meg:

![A SWPM telepítési lépés, ahol DBACOCKPIT séma adatok](./media/hana-get-started/image036b.jpg)

Adja meg a SAPABAP1 sémára vonatkozó adatokat:

![Írja be a SAPABAP1 sémára vonatkozó adatokat](./media/hana-get-started/image037b.jpg)

Az adatbázis-példány SWPM telepítés befejezése után tekintheti meg az SAP HANA Studio SAPABAP1 séma:

![Az SAP HANA Studio SAPABAP1 séma](./media/hana-get-started/image038b.jpg)

Végül, ha az SAP-alkalmazáskiszolgáló és SAP grafikus felhasználói felületének telepítése befejeződött, ellenőrizze a a HANA-adatbázis-példány a **DBA vezérlőpultja** tranzakció:

![A HANA-adatbázis-példány a DBA vezérlőpultja tranzakcióval ellenőrzése](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Az ügyfélszoftver-letöltési SAP
Az SAP Service Marketplace-ről szoftvert letöltheti, ahogyan az az alábbi képernyőfelvételnek megfelelően.

Töltse le a NetWeaver 7.5 Linux/Hana:

 ![SAP szolgáltatás telepítési és frissítési NetWeaver 7.5 letöltéséhez ablak](./media/hana-get-started/image001.jpg)

Töltse le a HANA SP12 Platform Edition:

 ![SAP szolgáltatás telepítése és frissítése a HANA SP12 Platform kiadásának letöltésével ablak](./media/hana-get-started/image002.jpg)

