---
title: Az SAP HANA az Azure-ban (nagyméretű példányok) SAP HANA telepítése |} A Microsoft Docs
description: Hogyan lehet egy SAP HANA az Azure-ban (nagyméretű példányos) SAP HANA telepítése.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dad088138fea2dd4fadc0cc9eed71245c32a8e0b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162662"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Hogyan telepítse és konfigurálja az SAP HANA (nagyméretű példányok) az Azure-ban

A cikk elolvasása előtt ismerkedjen meg [HANA nagyméretű példányok gyakori használati](hana-know-terms.md) és a [HANA nagyméretű példányok termékváltozatok](hana-available-skus.md).

SAP HANA telepítése az Ön felelőssége, és elkezdheti a tevékenység után egy új SAP Hana Azure-ban (nagyméretű példányok) kiszolgáló a handoff számára. És az Azure-hoz és a nagyméretű HANA-példány egység közötti kapcsolatot van létrehozása után. 

> [!Note]
> SAP szabályzatonként SAP Hana telepítése egy SAP HANA-telepítéshez minősítéssel rendelkező személy által hajtható végre. Egy személy, aki megfelelt a vizsgára Certified SAP technológia társítja, az SAP HANA-telepítés minősítő vizsga, vagy egy SAP-minősítéssel rendelkező rendszerintegrátor (SI).

Ellenőrizze ismét, különösen akkor, ha a HANA 2.0 telepítéséhez tervezési [SAP támogatási Megjegyzés #2235581 – SAP HANA: támogatott operációs rendszerek](https://launchpad.support.sap.com/#/notes/2235581/E) érdekében győződjön meg arról, hogy az operációs rendszer támogatott, a SAP HANA kiadási, úgy döntött, hogy telepítse. Kiderülhet, hogy az operációs rendszer támogatja a HANA 1.0-nál több korlátozott-e a támogatott operációs rendszer HANA 2.0. 

> [!IMPORTANT] 
> II. típusú egységek csak a SLES 12 SP2 operációsrendszer-verzió támogatott ezen a ponton. 

A HANA-telepítés megkezdése előtt ellenőrizni kell a következőket:
- [Ellenőrizze a HLI egység](#validate-the-hana-large-instance-units)
- [Az operációs rendszer konfigurálása](#operating-system)
- [Hálózati konfiguráció](#networking)
- [Tároló konfigurálása](#storage)


## <a name="validate-the-hana-large-instance-units"></a>A HANA nagyméretű példányok egység ellenőrzése

Miután megkapta a Microsoft a nagyméretű HANA-példány egység érvényesítése a következő beállításokat, és szükség szerint módosíthatja.

**Első lépés** fogadása a nagyméretű HANA-példány és a létrehozott hozzáférési és kapcsolati példányok után, hogy regisztrálja az operációs rendszer, a példány a operációsrendszer-szolgáltatónál. Ebben a lépésben a SUSE Linux operációs rendszert futtató regisztrálásához SUSE SMT, amelyekre szüksége van egy virtuális Gépet az Azure-ban üzembe helyezett példány tartalmazhat. A nagyméretű HANA-példány egység csatlakozhat a SMT példány (lásd: [SMT server telepítő SUSE Linux hogyan](hana-setup-smt.md)). Vagy a Red Hat-OS regisztrálva kell lennie a Red Hat előfizetés-kezelő használatával kell csatlakoznia. Lásd a jelen is megjegyzések [dokumentum](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ebben a lépésben is szükség az operációs rendszer javítási tudni. Egy feladat, amely az ügyfélnek kell gondoskodnia. SUSE, az itt elérhető dokumentáció révén telepítse és konfigurálja az SMT [Itt](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**A második lépésben** új javítások és javítások, az adott operációs rendszer/verzió. Ellenőrizze, hogy a nagyméretű HANA-példány javítási szintjét legfrissebb állapotát. A javítás/kiadásainak listáját és a módosítások a lemezképen, telepítheti a Microsoft időzítési alapul, néhány esetben előfordulhat, a legújabb javítások előfordulhat, hogy nem adhatók meg kéréstörzsek. Ezért fontos kötelező lépés után egy nagyméretű HANA-példány egység, ellenőrizze, hogy javítások biztonsági, Funkciók, rendelkezésre állás és teljesítmény szempontjából az adott Linux-gyártó által csapattagok pedig nyugodtabban aludhatnak kiadott, és a alkalmazni kell átvenni.

**Harmadik lépés** tekintse meg a megfelelő SAP-megjegyzések telepítéséhez és konfigurálásához az SAP HANA található az adott operációs rendszer/verzió. Javaslatok és a módosítás módosítása miatt SAP-megjegyzések vagy konfigurációk, amelyek az egyes telepítési forgatókönyvek függenek, a Microsoft nem mindig lesz képes egy nagyméretű HANA-példány egység tökéletesen konfigurálva van. Ezért azt kötelező, Microsoft, olvasni, a pontos Linux kiadásban az SAP Hana-hoz kapcsolódó az SAP-megjegyzések. Is ellenőrizheti az operációs rendszer/verzió szükséges konfigurációit, és a konfigurációs beállítások alkalmazására, ha még nem adta.

Adott, ellenőrizze a következő paramétereket, és végül igazítva:

- NET.Core.rmem_max = 16777216
- NET.Core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- NET.IPv4.tcp_wmem 65536 16777216 16777216 =

SLES12 SP1 és a RHEL 7.2 kezdődően ezeket a paramétereket kell állítani a konfigurációs fájlban /etc/sysctl.d a címtárban. Ha például a 91 – NetApp-HANA.conf nevét egy konfigurációs fájl kell létrehozni. A régi SLES és RHEL kiadásokhoz ezek a paraméterek beállítása in/etc/sysctl.conf kell lennie.

Az összes RHEL-kiadások és SLES12, kezdve a 
- sunrpc.tcp_slot_table_entries = 128

a paraméter in/etc/modprobe.d/sunrpc-local.conf kell beállítani. Ha a fájl nem létezik, akkor létre kell hozni a következő bejegyzés hozzáadásával: 
- beállítások sunrpc tcp_max_slot_table_entries = 128

**Negyedik lépés** annak ellenőrzése, a rendszer pontos ideje szerinti a HANA nagyméretű példány egység. A példányok vannak üzembe helyezve, a rendszer időzónát, amely az Azure-régióban található a HANA nagyméretű Szolgáltatáspéldányban helyét jelölik. Ön ingyenes, a rendszer pontos ideje szerinti vagy a saját példányok időzónájának módosítása. Ezzel és rendezése a bérlőhöz példányok több, elő kell készíteni, hogy kell az újonnan kézbesített példányok időzónája. A Microsoft operations nincs betekintést a rendszer időzóna beállítása az osztályt a készenléti feladat átadása után van. Ezért újonnan üzembe helyezett példány beállítása nem azzal, akkor módosította az ugyanabban az időzónában található. Ennek eredményeképpen feladata az ügyfélként, ellenőrizze és szükség esetén mindig a gyorsítási értéknek, átadná példány időzónája. 

**Ötödik lépés** etc/hosts ellenőrzése. A paneleket első át, mivel rendelkeznek (lásd a következő szakaszban) különböző célokra hozzárendelt különböző IP-címeket. Ellenőrizze a etc/hosts fájlt. Azokban az esetekben, ahol egységek be egy meglévő bérlőt kerülnek várhatóan nem megfelelően fenntartott IP-címekről korábban kézbesített rendszerek az újonnan telepített rendszerek stb/gazdagépre van szükség. Ezért helyezkedik el, mert az ügyfél ellenőrzi a megfelelő beállításokat, így, hogy egy újonnan üzembe helyezett példány interaktívan, és oldja meg a korábban üzembe helyezett egységek a bérlő nevét. 

## <a name="operating-system"></a>Operációs rendszer

> [!IMPORTANT] 
> II. típusú egységek csak a SLES 12 SP2 operációsrendszer-verzió támogatott ezen a ponton. 

A kézbesített operációsrendszer-lemezképek lapozóterület 2 GB értékre van állítva a [SAP támogatási Megjegyzés #1999997 – gyakori kérdések: SAP HANA memóriában](https://launchpad.support.sap.com/#/notes/1999997/E). Bármely másik beállítást kívánt Microsoft Ön által megadott kell.

[SUSE Linux Enterprise Server 12 SP1 SAP-alkalmazások](https://www.suse.com/products/sles-for-sap/hana) az telepítve van az Azure-ban (nagyméretű példányok) SAP Hana Linux-disztribúció. Az adott terjesztési SAP-specifikus képességeket biztosít &quot;beépített&quot; (beleértve az előre beállított paraméterek SLES SAP hatékonyan futtatásával).

Lásd: [erőforrás könyvtár-/ tanulmányok](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) a SUSE-webhelyen, és [SAP SUSE-](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) meg az SAP közösségi hálózati Állapotváltozás-az SAP HANA az (beleértve a telepítési magas SLES telepítésével kapcsolatos néhány hasznos források Rendelkezésre állás, biztonság megerősítését adott, az SAP-műveletekhez és más).

További és hasznos SAP SUSE – kapcsolódó hivatkozások:

- [SUSE Linux hely az SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Ajánlott eljárás az SAP: sorba replikációs – az SAP NetWeaver SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP – elleni védelem SLES for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (beleértve a SLES 12 rendszert az SAP-alkalmazások).

SAP támogatási megjegyzések az SAP HANA megvalósítása a SLES 12:

- [SAP támogatási Megjegyzés #1944799 – SLES operációsrendszer-telepítés az SAP HANA irányelvek](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [SAP támogatási Megjegyzés #2205917 – SAP HANA-adatbázis ajánlott az operációs rendszer beállításait a SLES 12 SAP-alkalmazások](https://launchpad.support.sap.com/#/notes/2205917/E).
- [SAP támogatási Megjegyzés #1984787 – SUSE Linux Enterprise Server 12: Telepítési jegyzetek](https://launchpad.support.sap.com/#/notes/1984787).
- [SAP támogatási Megjegyzés #171356 – SAP-szoftverek Linux rendszeren: általános információk](https://launchpad.support.sap.com/#/notes/1984787).
- [SAP támogatási Megjegyzés #1391070 – Linux UUID megoldások](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) van egy másik ajánlatra, a nagyméretű HANA-példányokon futó SAP HANA futtatásához. RHEL 6.7 és 7.2 kiadásaiban érhetők el. . Megjegyzés: a natív Azure virtuális gépek ahol csak az RHEL 7.2 és újabb verziókban támogatott, HANA nagyméretű példányok leváló támogatja RHEL 6.7 is. Azt javasoljuk azonban egy RHEL 7.x kiadással.

Red hat további és hasznos SAP kapcsolódó hivatkozások:
- [SAP HANA-Red Hat Linux-hely](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

SAP támogatási megjegyzések az SAP HANA végrehajtási Red hat:

- [SAP támogatási Megjegyzés #2009879 – SAP HANA-útmutató a Red Hat Enterprise Linux (RHEL) operációs rendszer](https://launchpad.support.sap.com/#/notes/2009879/E).
- [SAP támogatási #2292690 – SAP HANA-adatbázis Megjegyzés: az operációs rendszer ajánlott beállítások RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [SAP támogatási #2247020 – SAP HANA-adatbázis Megjegyzés: az operációs rendszer ajánlott beállításainak RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [SAP támogatási Megjegyzés #1391070 – Linux UUID megoldások](https://launchpad.support.sap.com/#/notes/1391070).
- [SAP támogatási Megjegyzés #2228351 – Linux: SAP HANA Database Szervizcsomagok 11 változat 110 (vagy magasabb) az RHEL 6-os vagy SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [Az SAP támogatási Megjegyzés #2397039 – gyakori kérdések: Az RHEL SAP](https://launchpad.support.sap.com/#/notes/2397039).
- [SAP támogatási Megjegyzés #1496410 – Red Hat Enterprise Linux 6.x: telepítés és frissítés](https://launchpad.support.sap.com/#/notes/1496410).
- [SAP támogatási Megjegyzés #2002167 – Red Hat Enterprise Linux 7.x: telepítés és frissítés](https://launchpad.support.sap.com/#/notes/2002167).

### <a name="time-synchronization"></a>Időszinkronizálás

Az SAP NetWeaver architektúrára épülő SAP-alkalmazások érzékenyek az időeltérést az SAP-rendszer alkotó különféle összetevők. Rövid SAP ABAP-ZDATE hiba címe memóriaképek\_nagy\_idő\_DIFF valószínűleg ismerős,, például az ezen rövid memóriaképek jelennek meg, amikor a rendszer pontos ideje szerinti különböző kiszolgálókon vagy virtuális gépek felé sodródik túl messze egymástól.

Az SAP Hana az Azure-ban (nagyméretű példányok), időszinkronizálás végezhető el az Azure adatforgalmi&#39;t a számítási egységek nagyméretű példány stampek a alkalmazni. Mivel az Azure biztosítja, hogy a rendszer, a szinkronizálás nem alkalmazható a futó SAP-alkalmazások natív Azure-beli virtuális gépeken,&#39;s időt szinkronizálva megfelelően be van állítva. Ennek eredményeképpen egy külön idő server kell beállítani, amelyek segítségével az SAP az Azure virtuális gépek és az SAP HANA rendszerű alkalmazáskiszolgálók adatbázis nagyméretű HANA-példányokon futó üzemelő példányok. A tároló-infrastruktúra, a nagyméretű példány stampek időt szinkronizálva az NTP-kiszolgálókra.


## <a name="networking"></a>Hálózat
Feltételezzük, hogy elvégezte-e a javaslatok az Azure virtuális hálózatok tervezését, és ezeket a virtuális hálózatok csatlakoztatása a HANA nagyméretű példányok ezeket a dokumentumokat leírtak szerint:

- [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Vannak bizonyos részletek, érdemes egyetlen egységek a hálózatokkal kapcsolatos beszélve. Két vagy három egység két vagy három hálózati adapter portokhoz rendelt IP-címek nagyméretű HANA-példány egység tartalmaz. Három IP-címeket a HANA kibővített konfigurációkhoz és a HANA-Rendszerreplikálást forgatókönyvet használja. Az egység a hálózati adapterhez hozzárendelt IP-címek egyikét mutattuk be kiszolgálói IP-cím készletet kívül esik a [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Tekintse meg [HLI támogatott forgatókönyvek](hana-supported-scenario.md) ethernet részletekkel az architektúrához.

## <a name="storage"></a>Storage

A tárolási elrendezés az SAP Hana az Azure-ban (nagyméretű példányok) SAP HANA az Azure Service Management keresztül útmutató sorok ajánlott leírtak szerint az SAP által konfigurált [SAP HANA tárolási követelményei](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) tanulmányt. A különböző köteteket, a másik nagyméretű HANA-példányok termékváltozatok hozzávetőleges méretét van dokumentálva [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A tároló kötetek elnevezési szabályai az alábbi táblázatban láthatók:

| Tárhelyhasználat | Csatlakozási név | Kötet neve | 
| --- | --- | ---|
| HANA-adatok | /hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA-napló | /hana/log/SID/mnt0000<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA naplóalapú biztonsági mentés | /Hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| A megosztott HANA | /Hana/Shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Ha biztonsági azonosító a HANA-példány: azonosító = 

És a bérlői = belső felsorolásoknak meg műveleteket, egy bérlő üzembe helyezésekor.

Amint láthatja, a megosztott HANA és a usr/sap megosztanak ugyanazt a kötetet. Az elnevezési rendszeréhez, akkor a csatlakozási a rendszer-azonosító, a HANA-példányokhoz, valamint a csatlakoztatási számát tartalmazza. Vertikális felskálázás telepítések esetén csak van egy csatlakoztatási, például a mnt00001. Mivel a bővítő telepítés jelennének meg annyi csatlakoztatása, feldolgozói és a fő csomóponttal rendelkezik. Horizontális felskálázás környezet, adatok, log, a napló biztonsági mentési kötetek megosztott és a kibővített konfigurációs az egyes csomópontokhoz csatolt. Több SAP-példányt futtató konfiguráció esetén a köteteket külön készletét létrehozása és csatlakoztatása a nagyméretű példány HAN egységhez. Tekintse meg [HLI támogatott forgatókönyvek](hana-supported-scenario.md) a forgatókönyvnek a tárolási elrendezés részletekért.

Olvassa el a ismertető tanulmányt, és keresse meg a nagyméretű HANA-példány egység, akkor ügyeljen arra, hogy az egységek kapható inkább korlátozó lemezkötetet HANA/adatok és, hogy van-e a kötet HANA/log/biztonsági mentés. Miért tudjuk méretezni a HANA/data rendkívül nagy méretűek oka az, hogy a storage-pillanatképek biztosítunk, Microsoft használ a lemezen ugyanarra a kötetre. Azt jelenti, hogy a további tárhelyet a pillanatképek hajt végre, a több helyet használja fel a hozzárendelt tároló köteteken található pillanatkép. A HANA/log/biztonsági mentési mennyiségi nem gondoltam, kell az adatbázis biztonsági másolatait a helyezi. Azt az van méretezve, hogy a HANA tranzakciónaplók biztonsági mentését a biztonsági mentési mennyiségi felhasználhatók. A részleteket a [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás az Azure-ban](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

A megadott tároló, mellett további tárolási kapacitása 1 TB-os lépésekben vásárolhat. A további tárhely is hozzáadhatók új kötetek egy nagyméretű HANA-példányokhoz.

Bevezetés az SAP HANA az Azure Service Management, során az ügyfél és adja meg felhasználói Azonosítóját (UID) csoport azonosítója (CSOPORTAZONOSÍTÓ) a sidadm sapsys és a felhasználói csoport számára (például: 1000,500) szükséges, hogy az SAP HANA-rendszer a telepítés során ezek azonos értékeket használják. A kívánt egységen több HANA-példányok üzembe helyezéséhez, kötetek (minden példány egy set) több készletétől kap. Ennek eredményeképpen üzembe helyezéskor akkor kell megadni:

- A különböző HANA-példányok (sidadm származtatott kihozni) biztonsági azonosítója.
- A különböző HANA-példányok memóriaméretekkel. Mivel a példányok száma a memória mérete az egyes egyedi kötet határozza meg a kötetek méretét.

A következő csatlakoztatási lehetőségeket vannak konfigurálva az összes csatlakoztatott kötetek esetében a tárolási szolgáltató javaslatok alapján (kivéve az rendszerindító LUN-t):

- NFS-rw, vers = 4, nehéz, timeo = 600, rsize = 1048576, wsize = 1048576, megszakítás, noatime, zárolása 0 0

Ezek a csatlakoztatási pontok vannak konfigurálva az/etc/fstab, mint például a következő grafikus látható:

![a nagyméretű HANA-példány egység csatlakoztatott kötetek fstab](./media/hana-installation/image1_fstab.PNG)

A parancs df -h S72m HANA nagyméretű példányok egységen kimenete jelenne meg:

![a nagyméretű HANA-példány egység csatlakoztatott kötetek fstab](./media/hana-installation/image2_df_output.PNG)


A tárolóvezérlő nagyméretű példány stampek csomópontok szinkronizálja az NTP-kiszolgálókra. Az Azure-beli és az SAP HANA az Azure-ban (nagyméretű példányok) egységek szinkronizálása egy NTP-kiszolgálóval szemben, nincs jelentős mennyiségű időt eltéréseket azonban az infrastruktúra és a számítási egységeket az Azure-ban vagy a nagyméretű példány stampek között kell lennie.

Annak érdekében, hogy a Storage alatt használt optimalizálása az SAP HANA, az alábbi SAP HANA-konfigurációs paramétereket is be kell állítani:

- max_parallel_io_requests 128
- a async_read_submit
- async_write_submit_active on
- az összes async_write_submit_blocks
 
A SAP HANA 1.0-s verzió legfeljebb SPS12, ezeket a paramétereket állítható be, az SAP HANA-adatbázis telepítése során leírtak szerint [SAP Megjegyzés #2267798 – az SAP HANA-adatbázis konfigurálása](https://launchpad.support.sap.com/#/notes/2267798)

Emellett konfigurálhatja a paramétereket a SAP HANA-adatbázis telepítése után a hdbparam keretrendszer használatával. 

Az SAP HANA 2.0-val a hdbparam keretrendszer elavult. Ennek eredményeképpen a paramétert kell beállítani az SQL-parancsok használatával. További információkért lásd: [SAP Megjegyzés #2399079: HANA 2 hdbparam felszámolása](https://launchpad.support.sap.com/#/notes/2399079).

Tekintse meg [HLI támogatott forgatókönyvek](hana-supported-scenario.md) további tárolási elrendezés az architektúrához.


**Következő lépések**

- Tekintse meg [HLI HANA telepítése](hana-example-installation.md)










































 







 




