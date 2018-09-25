---
title: 'Gyors útmutató: Manuális telepítés egypéldányos SAP Hana az Azure Virtual Machinesben |} A Microsoft Docs'
description: Rövid útmutató egy példányban – SAP HANA manuális telepítéséhez az Azure Virtual machines szolgáltatásban
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
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 6355a7ce203f2bf75b5c93d225502f961deeee43
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032081"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Gyors útmutató: Egy példányban – SAP HANA az Azure virtuális gépek manuális telepítése
## <a name="introduction"></a>Bevezetés
Ez az útmutató segít egy példányban – SAP HANA az Azure-beli virtuális gépek (VM) beállítása, az SAP NetWeaver 7,5-öt és az SAP HANA 1.0 SP12 telepítésekor manuálisan. A jelen útmutató célja az Azure-beli SAP HANA üzembe helyezése. Nem helyettesíti az SAP dokumentációjában. 

>[!Note]
>Ez az útmutató Azure virtuális gépeken futó SAP Hana üzemelő. Információ az SAP HANA nagyméretű HANA-példányokhoz környezetbe való üzembe helyezés: [az SAP használata Azure-beli virtuális gépek (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy ismeri az ilyen infrastruktúra-szolgáltatás (IaaS) alapjait, mint:
 * Hogyan helyezhet üzembe virtuális gépeket vagy virtuális hálózatok az Azure Portalon vagy a Powershellen keresztül.
 * Az Azure többplatformos parancssori felület (CLI), valamint a sablonok a JavaScript Object Notation (JSON) lehetőséget.

Ez az útmutató feltételezi, hogy Ön ismeri a:
* SAP HANA és az SAP NetWeaver és hogyan telepítheti őket a helyszínen.
* Telepítése, és az SAP HANA és a SAP alkalmazás példányok az Azure-ban működik.
* A következő fogalmak és eljárások:
   * Az SAP üzembe helyezésének megtervezése az Azure-ban, beleértve az Azure virtuális hálózat megtervezése és Azure Storage használatát. Lásd: [SAP NetWeaver az Azure Virtual Machines (VM) – tervezési és megvalósítási útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Üzembe helyezés alapelvek és az Azure-beli virtuális gépek üzembe helyezésének módjai. Lásd: [Azure virtuális gépek üzembe helyezése, az SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Magas rendelkezésre állás az SAP NetWeaver ASCS (ABAP SAP Central Services), SCS (SAP Central Services) és SSZON (sorba replikációs kiszolgáló) az Azure-ban. Lásd: [magas rendelkezésre állás az SAP NetWeaver az Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Részletes információk a egy több SID-vel telepített Azure-on futó ASCS/SCS kihasználva a hatékonyság növelése érdekében. Lásd: [hozzon létre egy SAP NetWeaver több biztonsági AZONOSÍTÓVAL konfigurációt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Alapelvek futó SAP NetWeaver Linux-alapú virtuális gépek az Azure-ban alapján. Lásd: [Microsoft Azure SUSE Linux rendszerű virtuális gépeken futó SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Az útmutató Linux az Azure virtuális gépek és a részletek vonatkozó beállításokat tartalmaz a megfelelően az Azure storage-lemez csatlakoztatása Linux rendszerű virtuális gépekhez.

A termelési forgatókönyvekhez használható Azure-beli Virtuálisgép-típusok szerepelnek a [IAAS SAP dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Nem éles környezethez natív Azure-beli Virtuálisgép-típusok választéka áll rendelkezésére.
További részletekért a virtuális gép konfigurációs és műveletek tekintse meg a dokumentum [SAP HANA-infrastruktúra konfigurációi és a műveletek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Tekintse meg az SAP HANA magas rendelkezésre állás érdekében [SAP HANA magas rendelkezésre állás az Azure-beli virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Ha egy SAP HANA-példány vagy S/4HANA vagy BW/4HANA rendszert üzembe helyezett nagyon gyors piacra tudásbázison, fontolja meg a használatát [SAP Cloud Appliance Library](http://cal.sap.com). Központi telepítésével kapcsolatos, például SAP CAL az Azure-on keresztül az S/4HANA rendszer dokumentációjában talál [Ez az útmutató](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Szeretné, hogy szüksége egy Azure-előfizetés és a egy SAP-felhasználó, az SAP Cloud Appliance Library regisztrált.

## <a name="additional-resources"></a>További források
### <a name="sap-hana-backup"></a>SAP HANA biztonsági mentés
Azure virtuális gépeken SAP HANA-adatbázisok biztonsági mentésén kapcsolatos információkért lásd:
* [Biztonsági mentési útmutató az SAP Hana az Azure Virtual machines szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Az SAP HANA az Azure Backup a fájlok szintjén](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [Tárolási pillanatképeken alapuló SAP HANA biztonsági mentés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>Az SAP Cloud Appliance Library környezetbe
Az üzembe helyezéséhez az S/4HANA, BW/4HANA vagy SAP Cloud Appliance Library használatával további információkért lásd: [üzembe helyezése az SAP S/4HANA vagy BW/4hana-t a Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Az SAP HANA által támogatott operációs rendszerek
SAP HANA által támogatott operációs rendszerekkel kapcsolatos információkért lásd: [SAP támogatási Megjegyzés #2235581 – SAP HANA: támogatott operációs rendszerek](https://launchpad.support.sap.com/#/notes/2235581/E). Azure virtuális gépek ezen operációs rendszerek csak egy részhalmazát támogatjuk. Azure-beli SAP HANA üzembe helyezéséhez a következő operációs rendszerek támogatottak: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

További SAP dokumentációjában az SAP HANA és a különböző Linux operációs rendszert lásd:

* [SAP támogatási Megjegyzés #171356 - a linuxon futó SAP-szoftverek: általános információk](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP támogatási Megjegyzés #1944799 – SAP HANA irányelvek SLES operációs rendszer telepítése](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [SAP támogatási Megjegyzés #2205917 – SAP HANA-adatbázis ajánlott az operációs rendszer beállításait a SLES 12 SAP-alkalmazások](https://launchpad.support.sap.com/#/notes/2205917/E)
* [SAP támogatási #1984787 – SUSE Linux Enterprise Server 12 Megjegyzés: A telepítéssel kapcsolatos megjegyzések](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP támogatási Megjegyzés #1391070 - Linux UUID-megoldások](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP támogatási Megjegyzés #2009879 – SAP HANA-útmutató a Red Hat Enterprise Linux (RHEL) operációs rendszer](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 – SAP HANA-adatbázis: RHEL 7 operációs rendszer ajánlott beállítások](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>Az SAP az Azure-ban figyelése
Figyelés az Azure-beli SAP kapcsolatos információkért lásd:

* [SAP-Jegyzetnek 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Ez a Megjegyzés ismerteti SAP "bővített figyelés" Linux rendszerű virtuális gépeket az Azure-ban. 
* [SAP-Jegyzetnek 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Ezt a megjegyzést linuxon SAPOSCOL kapcsolatos tudnivalókat ismerteti. 
* [SAP-Jegyzetnek 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Ez a Megjegyzés alapvető figyelési metrikákat az SAP a Microsoft Azure ismerteti.

### <a name="azure-vm-types"></a>Az Azure virtuális gépek típusai
Az Azure Virtuálisgép-típusok és az SAP HANA használt SAP által támogatott munkaterhelés-forgatókönyvek szerepelnek [SAP-tanúsítvánnyal rendelkező IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Az SAP az SAP NetWeaver és az S/4HANA alkalmazásrétegre hitelesített Azure Virtuálisgép-típusok vannak dokumentálva [SAP Megjegyzés 1928533 - SAP-alkalmazások az Azure-on: támogatott termékek és Azure-beli Virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533/E).

>[!Note]
>Integráció az SAP-Linux-Azure-hoz csak Azure Resource Manager és a klasszikus üzemi modellben nem támogatott. 

## <a name="manual-installation-of-sap-hana"></a>SAP HANA manuális telepítése

> [!IMPORTANT]
> Győződjön meg róla, hogy az operációs rendszer választja SAP-minősítéssel rendelkező SAP Hana, az adott virtuális gépek típusai, használja a. Az SAP HANA listája tanúsított Virtuálisgép-típusok és az operációs rendszer kiadások az azokat kereshetők [SAP HANA Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ellenőrizze, hogy a virtuális gép típusú felsorolt lekérni az SAP HANA teljes listáját a részletek megtekintéséhez kattintson az adott virtuális gép típusa támogatott kiadásainak listáját. Vegye figyelembe, hogy a példában a jelen dokumentum használtuk a SLES operációsrendszer-kiadás, amely M-sorozat virtuális gépei az SAP Hana SAP által nem támogatott.
>

Ez az útmutató ismerteti, hogyan manuális telepítéséhez az SAP HANA az Azure virtuális gépek két különböző módon:

* A "telepítés adatbázispéldány" lépésben elosztott NetWeaver telepítés részeként SAP Software kiépítés Manager (SWPM) használatával
* Az SAP HANA az adatbázis-lifecycle manager eszközt, HDBLCM és majd a NetWeaver telepítése

Használhatja SWPM (SAP HANA, SAP-alkalmazáskiszolgáló és az ASC-példány) az összes összetevő telepítése egy egyetlen virtuális gépen, ez leírtak szerint [SAP HANA bejelentést a blogon](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Ezt a beállítást a nem a jelen rövid útmutatóban ismertetett, de figyelembe kell venni a problémákat azonosak.

A telepítés előtt javasoljuk, hogy olvassa el a "előkészítése az Azure virtuális gépek manuális telepítéséhez az SAP HANA" Ez az útmutató későbbi szakaszában talál. Ezzel megakadályozhatja néhány alapvető hibák csak egy alapértelmezett Azure-beli Virtuálisgép-konfiguráció használatakor előforduló.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Telepítés az SAP HANA, SAP SWPM használatakor a legfontosabb lépések
Ez a szakasz felsorolja a fontos lépések az SAP HANA, Egypéldányos manuális telepítési, SAP SWPM SAP NetWeaver 7.5 elosztott telepítés használatakor. Az egyes lépéseket mutatjuk be a képernyőfelvételeken az útmutató későbbi részében részletesebben.

1. Hozzon létre egy Azure virtuális hálózat, amely két teszt virtuális gépeket tartalmazza.
2. Telepítse a két Azure virtuális gépeket, az operációs rendszerek (a példánkban a SUSE Linux Enterprise Server (SLES) és a SLES for SAP alkalmazások 12 SP1), az Azure Resource Manager-modell alapján.
3. Két Azure standard vagy prémium szintű tárolólemezeket (például a 75 GB-os vagy 500 GB-os lemezt) csatlakoztatni a virtuális gép kiszolgáló.
4. Prémium szintű storage-lemez csatlakoztatható a HANA-adatbázis-kiszolgáló virtuális Géphez. További információkért lásd: a "Lemez beállítása" szakaszban az útmutató későbbi részében.
5. Mérete vagy feldolgozási sebessége követelményeitől függően több lemezt, és hozza létre a csíkozott kötetek vagy logikai adatmennyiség-kezelés, vagy egy több-eszközök felügyeleti eszköz (MDADM) használatával a virtuális gép operációs rendszer szintjén.
6. Hozzon létre a csatlakoztatott lemezek vagy kötetek logikai XFS fájlrendszerek.
7. Csatlakoztassa az új XFS fájlrendszer az operációs rendszer szintjén. Az SAP-szoftvereket egy fájlrendszert használja. Például a /sapmnt directory és a biztonsági másolatok, használja a fájlrendszer. Az SAP HANA-adatbázis-kiszolgálón csatlakoztassa a prémium szintű tárolólemezeket /hana és /usr/sap XFS fájlrendszerek. Ez a folyamat szükség megakadályozza, hogy a fájl nem nagyméretű Linuxos Azure virtuális gépeken, legfelső szintű rendszer betelőben.
8. Adja meg a teszt virtuális gépek helyi IP-címét a Hosts fájlt.
9. Adja meg a **nofail** az/etc/fstab fájl paramétert.
10. Állítsa be a Linux kernel paraméterek használata a Linux operációsrendszer-kiadás alapján. További információkért tekintse meg a megfelelő SAP-megjegyzések megfigyelését, HANA, és a jelen útmutató "Kernel paraméterek" című szakaszában.
11. A lapozóterület bővítése.
12. Szükség esetén telepítse a teszt virtuális gépek egy grafikus asztali. Ellenkező esetben használjon távoli SAPinst telepítés.
13. Töltse le az SAP-szoftvereket az SAP Service Marketplace-ről.
14. Az SAP ASCS-példány telepíthető az alkalmazás-kiszolgáló virtuális Géphez.
15. Ossza meg a teszt virtuális gépek között a /sapmnt könyvtár NFS használatával. A virtuális gép kiszolgáló az NFS-kiszolgáló.
16. Telepítse az adatbázispéldányt, HANA, beleértve az adatbázis-kiszolgáló virtuális gép SWPM használatával.
17. Az elsődleges (szolgáltatói CÍMEI) kiszolgáló telepíthető az alkalmazás-kiszolgáló virtuális Géphez.
18. Indítsa el az SAP felügyeleti konzolt (SAP minősítéssel, MC). Jelentkezzen be az SAP grafikus felhasználói felületének vagy a HANA Studio, például.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Fontos lépések az SAP HANA telepítése HDBLCM használatakor
Ez a szakasz felsorolja a fontos lépések az SAP HANA, Egypéldányos manuális telepítési, SAP HDBLCM SAP NetWeaver 7.5 elosztott telepítés használatakor. Az egyes lépéseket mutatjuk be részletesen ebben az útmutatóban a képernyőfelvételeken.

1. Hozzon létre egy Azure virtuális hálózat, amely két teszt virtuális gépeket tartalmazza.
2. Operációs rendszerek (a példánkban a SLES és SLES for SAP alkalmazások 12 SP1) az Azure Resource Manager-modell alapján két Azure virtuális gépek üzembe helyezése.
3. Két Azure standard vagy prémium szintű tárolólemezeket (például a 75 GB-os vagy 500 GB-os lemezt) csatlakoztatni az alkalmazás-kiszolgáló virtuális Géphez.
4. Prémium szintű storage-lemez csatlakoztatható a HANA-adatbázis-kiszolgáló virtuális Géphez. További információkért lásd: a "Lemez beállítása" szakaszban az útmutató későbbi részében.
5. Mérete vagy feldolgozási sebessége követelményeitől függően több lemezt, és a csíkozott kötetek létrehozása a virtuális gép operációs rendszer szintjén logikai adatmennyiség-kezelés és a egy több-eszközök felügyeleti eszköz (MDADM) használatával.
6. Hozzon létre a csatlakoztatott lemezek vagy kötetek logikai XFS fájlrendszerek.
7. Csatlakoztassa az új XFS fájlrendszer az operációs rendszer szintjén. Egy fájlrendszer az SAP-szoftverek, majd a /sapmnt directory és a biztonsági mentés, a másik egy példa. Az SAP HANA-adatbázis-kiszolgálón csatlakoztassa a prémium szintű tárolólemezeket /hana és /usr/sap XFS fájlrendszerek. Ez a folyamat szükség segítenek megakadályozni, hogy a fájl nem nagyméretű Linuxos Azure virtuális gépeken, legfelső szintű rendszer betelőben.
8. Adja meg a teszt virtuális gépek helyi IP-címét a Hosts fájlt.
9. Adja meg a **nofail** az/etc/fstab fájl paramétert.
10. Állítsa be a kernel-paraméterek használata a Linux operációsrendszer-kiadás alapján. További információkért tekintse meg a megfelelő SAP-megjegyzések megfigyelését, HANA, és a jelen útmutató "Kernel paraméterek" című szakaszában.
11. A lapozóterület bővítése.
12. Szükség esetén telepítse a teszt virtuális gépek egy grafikus asztali. Ellenkező esetben használjon távoli SAPinst telepítés.
13. Töltse le az SAP-szoftvereket az SAP Service Marketplace-ről.
14. Hozzon létre egy, sapsys, a HANA-adatbázis-kiszolgáló virtuális gép azonosítója 1001, csoporttal.
15. SAP HANA telepítése az adatbázis-kiszolgáló virtuális gép a HANA Database Lifecycle Manager (HDBLCM) használatával.
16. Az SAP ASCS-példány telepíthető az alkalmazás-kiszolgáló virtuális Géphez.
17. Ossza meg a teszt virtuális gépek között a /sapmnt könyvtár NFS használatával. A virtuális gép kiszolgáló az NFS-kiszolgáló.
18. Telepítse az adatbázispéldányt, HANA, beleértve a HANA-adatbázis-kiszolgáló virtuális gép SWPM használatával.
19. Az elsődleges (szolgáltatói CÍMEI) kiszolgáló telepíthető az alkalmazás-kiszolgáló virtuális Géphez.
20. Indítsa el az SAP minősítéssel, MC. SAP grafikus felhasználói felületének vagy a HANA Studio csatlakozhat.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Manuális telepítés az SAP Hana az Azure virtuális gépek előkészítése
Ez a szakasz témakörei a következők:

* Operációs rendszer frissítése
* Lemez beállítása
* Kernel-paraméterek
* Fájlrendszer
* A/etc/hosts fájl
* A/etc/fstab fájl

### <a name="os-updates"></a>Operációs rendszer frissítése
Ellenőrizze a Linux operációs rendszer frissítéseket és javításokat további szoftverek telepítése előtt. Telepítse a javítást, valószínűleg elkerülése érdekében a támogatási ügyfélszolgálati hívást.

Győződjön meg arról, hogy használja:
* SUSE Linux Enterprise Server, SAP-alkalmazások számára.
* Red Hat Enterprise Linux for SAP-alkalmazások vagy a Red Hat Enterprise Linux for SAP HANA. 

Ha még nem tette, regisztrálja az operációs rendszer telepítése a Linux-gyártó a Linux előfizetését. Vegye figyelembe, hogy SUSE operációsrendszer-lemezképek SAP alkalmazások számára, amely már tartalmazza a szolgáltatások, amely automatikusan regisztrálva van-e.

Íme egy példa keresése elérhető javítások SUSE Linux használatával a **zypper** parancsot:

 `sudo zypper list-patches`

A probléma típusától függően a javítások kategória és súlyosság szerint besorolt. A gyakran használt kategória értékei a következők: **biztonsági**, **ajánlott**, **választható**, **funkció**, **dokumentum**, vagy **yast**.
Súlyosság gyakran használt értékei a következők: **kritikus**, **fontos**, **mérsékelt**, **alacsony**, vagy **nincsmegadva**.

A **zypper** parancs csak megkeresi a telepített csomagokat kell a frissítéseket. Például használhatja ki ezt a parancsot:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

A paraméter is hozzáadhat `--dry-run` tesztelése a frissítés a rendszer ténylegesen frissítése nélkül.


### <a name="disk-setup"></a>Lemez beállítása
A legfelső szintű fájlrendszer, a Linux rendszerű virtuális gép az Azure-ban rendelkezik egy korlátozás. Ezért szükség további lemezterületet csatlakoztatása egy Azure virtuális Gépen futó SAP. Az SAP-alkalmazáskiszolgáló Azure virtuális gépek a standard szintű Azure storage használatát is elegendő lehet. Azonban az SAP HANA DBMS Azure virtuális gépekhez az Azure Premium Storage-lemez és az nem éles hitelesítés megvalósításához használata kötelező.

Alapján a [SAP HANA TDI tárhellyel kapcsolatos követelmények](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), a javaslat a következő Azure Premium Storage-konfiguráció: 

| A VM-TERMÉKVÁLTOZATOK | RAM |  / hana/adat- és naplófájlok/hana / <br /> az LVM vagy MDADM csíkozott | / hana/megosztott | / root kötet | / usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

A javasolt lemez konfigurációját a HANA adatmennyiség és a naplózási kötet ugyanahhoz az adatkészlethez, a prémium szintű Azure storage-lemez LVM vagy MDADM csíkozott helyezni. Nem kell meghatározni a bármilyen RAID tárhelyredundancia-szint, mert az Azure Premium Storage tartja a redundancia biztosítása érdekében a lemezek három kép. Győződjön meg arról, hogy konfigurálja-e elegendő tárhely, tekintse meg a [SAP HANA TDI tárhellyel kapcsolatos követelmények](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) és [SAP HANA-kiszolgáló telepítési és frissítési útmutatójának](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Is vegye figyelembe a különböző Azure prémium szintű tárolólemezek különböző virtuális merevlemez (VHD) átviteli mennyiségű leírtak szerint [nagy teljesítményű Premium Storage és a felügyelt lemezek virtuális gépekhez](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

Hozzáadhat további prémium szintű tárolólemezeket a HANA DBMS virtuális gépekhez, adatbázisban vagy tranzakciónaplóban naplóalapú biztonsági mentések tárolásához.

Csíkozást konfigurálásához használt két fő-eszközökkel kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Szoftveres RAID linuxon konfigurálása](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [LVM konfigurálása az Azure-beli Linuxos virtuális gépre](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Lemezek csatolása egy vendég operációs rendszer, Linux rendszert futtató Azure virtuális gépekre vonatkozó további információkért lásd: [lemez hozzáadása Linux rendszerű virtuális gép](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Az Azure Premium Storage gyorsítótárazási mód definiálását teszi lehetővé. A csíkozott csoporton /hana/data és /hana/log a lemezek gyorsítótárazása le kell tiltani. A további kötetek (lemezek), a gyorsítótárazási mód kell megadni **ReadOnly**.

További információkért lásd: [Premium Storage: nagy teljesítményű tárolási szolgáltatás Azure virtuális gépek számítási feladataihoz](../../windows/premium-storage.md).

Példa JSON-sablonok találja a virtuális gépek létrehozásának, [Azure gyorsindítási sablonok](https://github.com/Azure/azure-quickstart-templates).
A virtuálisgép-egyszerű – sles alapszintű sablon áll. Egy tároló rész alatt, egy további 100 GB-os adatlemezt tartalmaz. Ez a sablon alapján is használható. A sablon az adott konfigurációhoz tesztkörnyezetéhez igazíthatja.

>[!Note]
>Fontos, hogy az Azure storage-lemez csatolása UUID segítségével dokumentált módon [a Microsoft Azure SUSE Linux rendszerű virtuális gépeken futó SAP NetWeaver](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A tesztelési környezetben két Azure standard szintű tárolólemezek kiszolgálóhoz lenne csatlakoztatva a SAP alkalmazás virtuális Gépet, az alábbi képernyőképen látható módon. Egy lemezt a telepítés (beleértve a NetWeaver 7,5-öt, a SAP grafikus felhasználói felület és az SAP HANA) SAP software tárolja. A második lemez biztosítani, hogy elég szabad terület érhető el, további követelmények (például a biztonsági mentés és a teszt adatok) és az azonos SAP-rendszeren tartozó összes virtuális gép közötti megosztását /sapmnt könyvtár (vagyis a SAP-profilok) lenne.

![SAP HANA app server lemezek ablak megjelenítése a két adatlemezt, és azok méretek](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Kernel-paraméterek
Az SAP HANA megköveteli az adott Linux kernel beállítások, amelyek nem részei a normál Azure-katalógus rendszerképek, és manuálisan kell megadni. Ha használja SUSE vagy a Red Hat a paraméterek eltérő lehet. A korábban felsorolt SAP-megjegyzések paraméterek kapcsolatos információkat biztosít. A képernyőfelvételen látható a SUSE Linux 12 SP1 lett megadva. 

Az SLES for SAP alkalmazások 12 általánosan elérhető és a SLES for SAP alkalmazások 12 SP1 rendelkezik egy új eszköz **lehetőségeire adm**, amely felváltja a régi **sapconf** eszközt. Egy speciális SAP HANA-profil érhető el az **lehetőségeire adm**. A rendszer finomhangolása az SAP Hana-hoz, írja be a következő legfelső szintű felhasználóként:

   `tuned-adm profile sap-hana`

További információ **lehetőségeire adm**, tekintse meg a [lehetőségeire adm SUSE dokumentációjában](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Az alábbi képernyőképen látható hogyan **lehetőségeire adm** megváltozott a `transparent_hugepage` és `numa_balancing` értékeket, a szükséges SAP HANA-beállításoknak megfelelően.

![A beállított adm eszköz módosítja a szükséges SAP HANA-beállításoknak megfelelően értékek](./media/hana-get-started/image005.jpg)

Az SAP HANA-kernel beállítások véglegesítéséhez használja **grub2** a SLES 12 rendszert. További információ **grub2**, nyissa meg a [konfigurációs fájlstruktúra](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) szakaszban, a SUSE-dokumentáció.

Az alábbi képernyőfelvételen a kernel beállításokat módosítani a konfigurációs fájlban és majd lefordítják azt használatával hogyan **grub2-mkconfig**:

![Kernel beállításokat módosítani a konfigurációs fájlban, és grub2-mkconfig használatával összeállított](./media/hana-get-started/image006.jpg)

Egy másik lehetőség a beállítások módosítása YaST használatával, és a **rendszertöltő** > **Kernel paraméterek** beállítások:

![A Kernel paraméterek beállítások lapján YaST rendszertöltő](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Fájlrendszer
Az alábbi képernyőfelvételen két fájlrendszereket, a SAP alkalmazás kiszolgáló virtuális gép felett a két csatlakoztatott Azure standard szintű storage-lemez lettek létrehozva. Mindkét fájlrendszerek XFS típusú és /sapdata és /sapsoftware csatlakoztatva vannak.

Esetén nem kötelező, hogy megtervezhessük a fájlrendszerek ily módon. A lemezterület rendszerezéséhez más lehetőségek állnak rendelkezésre. A legfontosabb szempont, hogy a legfelső szintű fájlrendszer tiltsa le a futását szabad lemezterületét.

![Az SAP-alkalmazáskiszolgáló virtuális Géphez létrehozott két fájlrendszerek](./media/hana-get-started/image008.jpg)

Kapcsolódó elemek az SAP HANA DB virtuális gép egy adatbázist a telepítés során SAPinst (SWPM) használatakor és a **tipikus** telepítésen, minden /hana és /usr/sap van telepítve. Az alapértelmezett hely a SAP HANA-naplók biztonsági mentése /usr/sap alatt áll. Újra mivel fontos, hogy megakadályozza, hogy a legfelső szintű fájlrendszer tárolóhely elfogyását, ellenőrizze, hogy nincs elég szabad hely a /hana és /usr/sap SWPM az SAP HANA telepítése előtt.

Az SAP HANA standard fájlrendszer elrendezését, olvassa el a [SAP HANA-kiszolgáló telepítési és frissítési útmutatójának](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Az SAP-alkalmazáskiszolgáló virtuális Géphez létrehozott további fájlrendszerek](./media/hana-get-started/image009.jpg)

SAP NetWeaver egy standard SLES/SLES for SAP alkalmazások 12 Azure image z galerie telepíti, egy üzenet jelenik meg arról, hogy a hiba nem lapozóterület, az alábbi képernyőképen látható módon. Ez az üzenet elvetéséhez, manuálisan is hozzáadhat egy lapozófájl használatával **nn**, **mkswap**, és **swapon**. Megtudhatja, hogyan, keresse meg "Egy lapozófájl kézi hozzáadása" az a [használatával a YaST Partitioner](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) a SUSE-dokumentáció része.

Egy másik lehetőség, hogy a lapozófájl-kapacitás konfigurálása a Linux rendszerű Virtuálisgép-ügynök használatával. További információkért lásd: a [Azure Linux-ügynök használati útmutatója](../../extensions/agent-linux.md).

![Felbukkanó üzenet tájékoztatja, hogy nincs-e elegendő lapozófájl-kapacitás](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>A/etc/hosts fájl
Mielőtt elkezdené telepíteni az SAP, győződjön meg arról, szerepeltetni kívánt állomás nevét és az SAP virtuális gépek IP-címek a Hosts fájlt. Minden az SAP virtuális gép üzembe helyezése egy Azure virtuális hálózaton belül, és használja a belső IP-címek, itt látható módon:

![Gazdagép nevét és a Hosts fájlban felsorolt SAP virtuális IP-címek](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>A/etc/fstab fájl

Hasznos lehet hozzáadni a **nofail** paramétert az fstab fájlt. Így ha probléma merül fel a lemezeket, a virtuális gép nem lefagy a rendszerindítási folyamat során. De ne feledje, hogy további lemezterület nem feltétlenül érhető el, és a folyamatok előfordulhat, hogy töltse fel a legfelső szintű fájlrendszer. Ha /hana hiányzik, a SAP HANA nem indul el.

![Adja hozzá a nofail paramétert az fstab fájlt](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>A SLES 12/SLES for SAP alkalmazások 12 grafikus GNOME asztali
Ez a szakasz témakörei a következők:

* A GNOME asztali és telepíti xrdp SLES 12/SLES for SAP alkalmazások 12
* Futó Java-alapú SAP minősítéssel, MC Firefox segítségével SLES 12/SLES for SAP alkalmazások 12

Például Xterminal vagy VNC (nem a jelen útmutatóban ismertetett) lehetőségeket is használhatja.

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>A GNOME asztali és telepíti xrdp SLES 12/SLES for SAP alkalmazások 12
Ha Windows háttér, a Firefox, SAPinst, SAP grafikus felhasználói Felülettel, az SAP minősítéssel, MC vagy HANA Studio futtatásához, és csatlakozzon a virtuális Géphez a távoli asztal protokoll (RDP) keresztül egy Windows-számítógép könnyedén használhatja a grafikus asztali közvetlenül az SAP Linux rendszerű virtuális gépeken belül. Függő a vállalati házirendeknek való felvételével grafikus felhasználói felületeket éles környezetben, és nem éles Linux-alapú rendszereken, érdemes GNOME telepítése a kiszolgálón. A GNOME asztal telepítése egy Azure SLES 12/SLES SAP alkalmazások 12 virtuális gép számára:

1. Telepítse a GNOME asztal (például a PuTTY ablak) a következő parancs beírásával:

   `zypper in -t pattern gnome-basic`

2. Telepítse a xrdp, hogy a virtuális gép RDP-Kapcsolaton keresztül csatlakozni:

   `zypper in xrdp`

3. /Etc/sysconfig/windowmanager szerkesztheti, majd állítsa be az alapértelmezett kezelő GNOME:

   `DEFAULT_WM="gnome"`

4. Futtatás **chkconfig** , győződjön meg arról, hogy xrdp automatikusan elindul, a rendszer újraindítása után:

   `chkconfig -level 3 xrdp on`

5. Ha az RDP-kapcsolat problémáját, próbálja meg újraindítani (a PuTTY ablakban, a példában):

   `/etc/xrdp/xrdp.sh restart`

6. Ha xrdp újra kell indítani az előző lépésben említett nem működik, ellenőrizze .pid fájlba:

   `check /var/run` 

   Keressen `xrdp.pid`. Ha megtalálta, távolítsa el, és indítsa újra.

### <a name="starting-sap-mc"></a>SAP minősítéssel, MC indítása
Miután telepítette a GNOME asztali, Firefox az Azure SLES 12/SLES SAP alkalmazások 12 virtuális gép futtatása közben a grafikus Java-alapú SAP minősítéssel, MC kezdve előfordulhat, hogy hibát jelenít meg a hiányzó Java-böngészőbeli beépülő modul miatt.

Az URL-cím, az SAP minősítéssel, MC elindításához `<server>:5<instance_number>13`.

További információkért lásd: [a Web-alapú SAP-felügyeleti konzol elindítása](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Az alábbi képernyőképen látható a hibaüzenet jelenik meg, ha a Java-böngészőbeli beépülő modul nem található:

![Hiányzó Java-böngészőbővítményt jelző hibaüzenet](./media/hana-get-started/image013.jpg)

A probléma megoldásához egyik módja, hogy telepítése a hiányzó beépülő modul használatával YaST, az alábbi képernyőképen látható módon:

![Hiányzik a beépülő modul telepítéséhez YaST használatával](./media/hana-get-started/image014.jpg)

Újra meg az SAP felügyeleti konzol URL-cím, egy üzenet jelenik meg rákérdez arra, hogy a beépülő modul aktiválása:

![Beépülő modul aktiválási kérő párbeszédpanel](./media/hana-get-started/image015.jpg)

Akkor is megjelenik, megjelenik egy hibaüzenet, hiányzó fájl javafx.properties. Ez a grafikus felhasználói Felülettel 7.4 SAP, Oracle Java 1.8-as követelményeihez kapcsolódik. (Lásd: [SAP-Jegyzetnek 2059429](https://launchpad.support.sap.com/#/notes/2059424).) Az IBM-Java-verzió sem az openjdk csomagját csomag kiadásaiban a SLES/SLES for SAP alkalmazások 12 tartalmazza a szükséges javafx.properties fájlt. A megoldás, hogy töltse le és telepítse a Java SE 8 Oracle.

Az opensuse-alapú openjdk csomagját hasonló hibával kapcsolatos információkért tekintse meg a hozzászóláslánc [SAPGui 7.4 Java, az openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>SAP Hana manuális telepítés: SWPM
Az ebben a szakaszban szereplő képernyőképek sorozatát SAP NetWeaver 7,5-öt és az SAP HANA SP12 telepítése SWPM (SAPinst) használatakor fő lépéseit mutatja be. A NetWeaver 7.5 telepítésének részeként SWPM is telepítheti a HANA-adatbázis egy példányban.

Egy minta tesztkörnyezetben csak egy speciális üzleti alkalmazás programozási (ABAP) alkalmazás kiszolgálót telepítette. Ahogy az az alábbi képernyőfelvételen is látható, használtuk a **elosztott rendszer** az ascs rendszerbe fut be, és az elsődleges alkalmazáskiszolgáló-példányok telepítését egy Azure virtuális gép és az SAP HANA database rendszer egy másik Azure-beli virtuális gépen.

![Ascs rendszerbe fut be, és az elosztott rendszer lehetőség használatával telepíti elsődleges alkalmazáskiszolgáló-példányok](./media/hana-get-started/image012.jpg)

Miután az ASCS-példány telepítve van az app-kiszolgáló virtuális Géphez, és az SAP-kezelőkonzolon "zöld" értékre van állítva (jelenik meg az alábbi képernyőfelvételen látható), a /sapmnt könyvtár (beleértve az SAP-profil könyvtár) meg kell osztani a az SAP HANA-adatbázis-kiszolgáló virtuális Géphez. Az adatbázis-telepítési lépés ezt az információt hozzá kell férnie. Hozzáférés biztosítása a legjobb módja, hogy használja az NFS, amely YaST használatával konfigurálható.

![SAP-felügyeleti konzol megjelenítése az ASC-példány telepíthető az alkalmazás-kiszolgáló virtuális Géphez, és állítsa be "zöld"](./media/hana-get-started/image016.jpg)

Az alkalmazás kiszolgálón virtuális gép a /sapmnt könyvtár közösen kell NFS-n keresztül használatával a **rw** és **no_root_squash** beállítások. Az alapértelmezett érték **ro** és **root_squash**, amely vezethet problémákat, ha az adatbázis-példány telepítését.

![Az NFS-n keresztül /sapmnt könyvtár megosztása rw és no_root_squash lehetőségeinek használatával](./media/hana-get-started/image017b.jpg)

Ahogy a következő képernyőfelvételen látható, az alkalmazás-kiszolgáló virtuális Gépről a /sapmnt megosztást kell konfigurálni az SAP HANA-adatbázis-kiszolgáló virtuális gép használatával **NFS-ügyfél** (és YaST).

![Az NFS-ügyfél segítségével konfigurált /sapmnt megosztás](./media/hana-get-started/image018b.jpg)

Elosztott telepítés NetWeaver 7.5 (**adatbázispéldány**), az alábbi képernyőképen látható, jelentkezzen be az SAP HANA-adatbázis-kiszolgáló virtuális Géphez, és indítsa el a SWPM.

![Egy adatbázis-példányt telepítése bejelentkezik, az SAP HANA-adatbázis-kiszolgáló virtuális Géphez, és SWPM indítása](./media/hana-get-started/image019.jpg)

Miután kiválasztotta **tipikus** telepítési és a telepítési adathordozó elérési útját adja meg a DB SID-AZONOSÍTÓVAL, a gazdagép nevét, a példányok számát és a DB rendszergazda jelszava.

![Az SAP HANA database rendszer rendszergazdai bejelentkezési oldal](./media/hana-get-started/image035b.jpg)

Adja meg a jelszót a DBACOCKPIT séma:

![A DBACOCKPIT séma be a jelszó-entry](./media/hana-get-started/image036b.jpg)

Adjon meg egy kérdést a SAPABAP1 séma jelszó:

![Adjon meg egy kérdést a SAPABAP1 séma jelszó](./media/hana-get-started/image037b.jpg)

Után minden feladat befejeződött, a DB telepítési folyamat egyes fázisaiban mellett egy zöld pipa jelenik meg. Az üzenet "végrehajtási... Az adatbázis példány befejeződött"jelenik meg.

![A feladat befejeződött-ablakot megerősítő üzenet](./media/hana-get-started/image023.jpg)

A sikeres telepítés után az SAP-kezelőkonzolon emellett bemutatják a DB-példányra "zöld", és az SAP HANA-folyamatok (hdbindexserver hdbcompileserver és így tovább) teljes listájának megjelenítéséhez.

![SAP felügyeleti konzol ablakának az SAP HANA-folyamatok listája](./media/hana-get-started/image024.jpg)

Az alábbi képernyőfelvételen a fájlstruktúra a HANA-telepítés során létrehozott SWPM /hana/shared könyvtárban részeit. Mivel nincs lehetőség egy másik elérési utat adja meg, fontos SWPM használatával további lemezterületet a SAP HANA-telepítés előtt /hana könyvtár alá csatlakoztatásához. Ez megakadályozza, hogy a legfelső szintű fájlrendszer kevés a szabad hely.

![HANA telepítése során létrehozott /hana/shared directory fájlstruktúra](./media/hana-get-started/image025.jpg)

Ezen a képernyőfelvételen látható a fájlstruktúra a /usr/sap könyvtár:

![A/usr/sap directory fájlstruktúra](./media/hana-get-started/image026.jpg)

Az utolsó lépés az elosztott ABAP-telepítés a rendszer telepíti az elsődleges alkalmazás server-példány:

![ABAP telepítési ábrázoló elsődleges application server-példány, az utolsó lépés](./media/hana-get-started/image027b.jpg)

Miután az elsődleges alkalmazás server-példány és az SAP grafikus felhasználói Felülettel van telepítve, a **DBA vezérlőpultja** tranzakció, győződjön meg arról, hogy az SAP HANA-telepítés befejezése megfelelően:

![A sikeres telepítés megerősítésével DBA vezérlőpultja ablakban](./media/hana-get-started/image028b.jpg)

Utolsó lépésként, előfordulhat, hogy előbb telepítenie HANA Studio, a SAP app Server virtuális Gépet szeretne, és az SAP HANA-példány, amelyen fut az adatbázis-kiszolgáló virtuális Géphez, majd csatlakozzon:

![Az SAP HANA Studio telepítése a SAP app Server virtuális gép](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>SAP Hana manuális telepítés: HDBLCM
SAP HANA telepítése elosztott telepítés részeként SWPM használatával, valamint telepítheti a HANA különálló először HDBLCM használatával. SAP NetWeaver 7.5, például Ezután telepítheti. Ebben a szakaszban a képernyőfelvételek bemutatják, hogyan működik ez a folyamat.

A HANA HDBLCM eszközzel kapcsolatos további információkért lásd:

* [A feladat a megfelelő SAP HANA HDBLCM kiválasztása](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA életciklus-felügyeleti eszközök](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [Az SAP HANA-kiszolgáló telepítési és frissítési útmutató](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Az alapértelmezett csoport azonosítója beállítást a problémák elkerülése érdekében a `\<HANA SID\>adm user` (a HDBLCM eszközzel létrehozott), adjon meg egy új csoportot nevű `sapsys` Csoportazonosító használatával `1001` SAP HANA-n keresztül HDBLCM telepítése előtt:

![Új csoport "sapsys" használatával meghatározott csoport 1001-es azonosító](./media/hana-get-started/image030.jpg)

HDBLCM indításakor először egy egyszerű start menü jelenik meg. 1 elem kiválasztása **új rendszer telepítése**, ahogy az alábbi képernyőfelvételen látható:

!["Az új rendszer telepítése" beállítást a HDBLCM indítási ablak](./media/hana-get-started/image031.jpg)

Az alábbi képernyőképen a kulcsok beállításainak korábban kiválasztott jeleníti meg.

> [!IMPORTANT]
> HANA log és adat, valamint a telepítési útvonal (/ hana/ebben a példában a megosztott) és /usr/sap, nevű könyvtárak nem lehet a legfelső szintű fájlrendszer egy részét. Ezek a könyvtárak tartozik az Azure-adatlemezek, amely a virtuális gép (a "Lemez beállítása" szakaszban leírt) lenne csatlakoztatva. Ez a megközelítés segít a legfelső szintű fájlrendszer tiltsa le a futását lemezterületét. Az alábbi képernyőképen azt láthatja, hogy a HANA-rendszergazda rendelkezik-e a felhasználói azonosító `1005` részét képezi a `sapsys` csoport (azonosító `1001`), amely a telepítés előtt lett definiálva.

![Korábban kiválasztott összes fő SAP HANA összetevők listáját](./media/hana-get-started/image032.jpg)

Ellenőrizheti a `\<HANA SID\>adm user` (`azdadm` az alábbi képernyőképen) az/etc/passwd directory részletei:

![HANA \<HANA SID\>az/etc/passwd directory felsorolt adm – felhasználó adatai](./media/hana-get-started/image033.jpg)

Miután telepítette az SAP HANA HDBLCM használatával, láthatja a fájlstruktúra a SAP HANA Studio, az alábbi képernyőképen látható módon. SAPABAP1 séma, amely tartalmazza az SAP NetWeaver-táblázatok, még nem érhető el.

![Az SAP HANA Studio SAP HANA-fájlstruktúra](./media/hana-get-started/image034.jpg)

Miután telepítette az SAP HANA, SAP NetWeaver megkönnyítése is telepítheti. Ahogy az az alábbi képernyőfelvételen is látható, a telepítés hajtottak végre elosztott telepítése SWPM (az előző szakaszban leírt) használatával. Ha az adatbázispéldányt SWPM használatával telepíti, adja meg ugyanazokat az adatokat (például a gazdagépnév, HANA SID és példányszámának) HDBLCM használatával. SWPM ezután használja a már meglévő HANA-telepítés, és hozzáadja a sémák.

![Egy elosztott telepítés SWPM használatával](./media/hana-get-started/image035b.jpg)

A következő képernyőképen látható a SWPM telepítési lépés, ahol a DBACOCKPIT sémára vonatkozó adatokat adja meg:

![A SWPM telepítési lépés, ahol DBACOCKPIT séma adatok](./media/hana-get-started/image036b.jpg)

Adja meg a SAPABAP1 sémára vonatkozó adatokat:

![Írja be a SAPABAP1 sémára vonatkozó adatokat](./media/hana-get-started/image037b.jpg)

Az adatbázis-példány SWPM telepítés befejezése után tekintheti meg az SAP HANA Studio SAPABAP1 séma:

![Az SAP HANA Studio SAPABAP1 séma](./media/hana-get-started/image038b.jpg)

Végül az SAP-alkalmazáskiszolgáló és SAP grafikus telepítés befejezése után ellenőrizheti a HANA-adatbázis-példány használatával az **DBA vezérlőpultja** tranzakció:

![A HANA-adatbázis-példány a DBA vezérlőpultja tranzakcióval ellenőrzése](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Az ügyfélszoftver-letöltési SAP
Az SAP Service Marketplace-ről szoftvert letöltheti, ahogyan az az alábbi képernyőfelvételnek megfelelően.

Töltse le a NetWeaver 7.5 Linux/Hana:

 ![Az SAP-szolgáltatás telepítése és frissítése ablakának NetWeaver 7.5 letöltése](./media/hana-get-started/image001.jpg)

Töltse le a HANA SP12 Platform Edition:

 ![Az SAP-szolgáltatás telepítése és frissítése ablakának HANA SP12 Platform Edition letöltése](./media/hana-get-started/image002.jpg)

