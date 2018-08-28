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
ms.date: 08/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d335e135551b7b6faed8ee566acb14b46fd6c81
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43107511"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Hogyan telepítse és konfigurálja az SAP HANA (nagyméretű példányok) az Azure-ban

Az alábbiakban néhány fontos definíciót a jelen útmutató olvasása előtt. A [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) vezettünk be két különböző osztályú nagyméretű HANA-példány egységre is:

- S72, S72m, S144, S144m, S192, S192m és S192xm, mint a "Type I osztály" nevezzük, amely a termékváltozatok.
- S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm és S960m, amely szerint a "II. típusú class" termékváltozatok nevezzük.

A osztály megadása a nagyméretű HANA-példány a dokumentációban használt végül tekintse meg különböző lehetőségeket és a követelményeket a HANA nagyméretű példány SKU-k alapján történik.

Más definíciók gyakran használunk a következők:
- **Nagyméretű szolgáltatáspéldányban:** egy hardveres infrastruktúrát verem, amely az SAP HANA TDI minősítéssel rendelkezik, és dedikált Azure-beli SAP HANA-példányok futtatásához.
- **SAP HANA az Azure-ban (nagyméretű példányok):** az SAP HANA TDI-tanúsítvánnyal ellátott hardvert nagyméretű példány stampek különböző Azure-régióban üzembe helyezett példánya hivatalos név az ajánlat az Azure-beli HANA futtatásához. A kapcsolódó kifejezés **nagyméretű HANA-példány** rövid a SAP HANA az Azure-ban (nagyméretű példányok) és széles körben használt a műszaki telepítési útmutatóban.


SAP HANA telepítése az Ön felelőssége, és elkezdheti a tevékenység után egy új SAP Hana Azure-ban (nagyméretű példányok) kiszolgáló a handoff számára. És az Azure-hoz és a nagyméretű HANA-példány egység közötti kapcsolatot van létrehozása után. 

> [!Note]
> SAP szabályzatonként SAP Hana telepítése egy SAP HANA-telepítéshez minősítéssel rendelkező személy által hajtható végre. Egy személy, aki megfelelt a vizsgára Certified SAP technológia társítja, az SAP HANA-telepítés minősítő vizsga, vagy egy SAP-minősítéssel rendelkező rendszerintegrátor (SI).

Ellenőrizze ismét, különösen akkor, ha a HANA 2.0 telepítéséhez tervezési [SAP támogatási Megjegyzés #2235581 – SAP HANA: támogatott operációs rendszerek](https://launchpad.support.sap.com/#/notes/2235581/E) érdekében győződjön meg arról, hogy az operációs rendszer támogatott, a SAP HANA kiadási, úgy döntött, hogy telepítse. Kiderülhet, hogy az operációs rendszer támogatja a HANA 1.0-nál több korlátozott-e a támogatott operációs rendszer HANA 2.0. 

> [!IMPORTANT] 
> II. típusú egységek csak a SLES 12 SP2 operációsrendszer-verzió támogatott ezen a ponton. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Első lépések a HANA nagyméretű példány egység fogadása után

**Első lépés** fogadása a nagyméretű HANA-példány és a létrehozott hozzáférési és kapcsolati példányok után, hogy regisztrálja az operációs rendszer, a példány a operációsrendszer-szolgáltatónál. Ebben a lépésben a SUSE Linux operációs rendszert futtató regisztrálásához SUSE SMT, amelyekre szüksége van egy virtuális Gépet az Azure-ban üzembe helyezett példány tartalmazhat. A nagyméretű HANA-példány egység csatlakozhat a SMT példány (lásd az ebben a dokumentációban későbbi részében). Vagy a Red Hat-OS regisztrálva kell lennie a Red Hat előfizetés-kezelő használatával kell csatlakoznia. Lásd a jelen is megjegyzések [dokumentum](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ebben a lépésben is szükség az operációs rendszer javítási tudni. Egy feladat, amely az ügyfélnek kell gondoskodnia. SUSE, az itt elérhető dokumentáció révén telepítse és konfigurálja az SMT [Itt](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

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

Olvassa el a ismertető tanulmányt, és keresse meg a nagyméretű HANA-példány egység, akkor ügyeljen arra, hogy az egységek kapható inkább korlátozó lemezkötetet HANA/adatok és, hogy van-e a kötet HANA/log/biztonsági mentés. Miért tudjuk méretezni a HANA/data rendkívül nagy méretűek oka az, hogy a storage-pillanatképek biztosítunk, Microsoft használ a lemezen ugyanarra a kötetre. Azt jelenti, hogy a további tárhelyet a pillanatképek hajt végre, a több helyet használja fel a hozzárendelt tároló köteteken található pillanatkép. A HANA/log/biztonsági mentési mennyiségi nem gondoltam, kell az adatbázis biztonsági másolatait a helyezi. Azt az van méretezve, hogy a HANA tranzakciónaplók biztonsági mentését a biztonsági mentési mennyiségi felhasználhatók. A tároló verziói a jövőbeli önkiszolgáló szolgáltatást, hogy az adott kötet gyakoribb pillanatkép által megcélzott pillanatkép. És az adott több gyakori replikációk a vész-helyreállítási webhelyként, ha a beállítás be a nagyméretű HANA-példány infrastruktúra által biztosított vész helyreállítási funkciójának megfelel. A részleteket a [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás az Azure-ban](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

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

## <a name="time-synchronization"></a>Időszinkronizálás

Az SAP NetWeaver architektúrára épülő SAP-alkalmazások érzékenyek az időeltérést az SAP-rendszer alkotó különféle összetevők. Rövid SAP ABAP-ZDATE hiba címe memóriaképek\_nagy\_idő\_DIFF valószínűleg ismerős,, például az ezen rövid memóriaképek jelennek meg, amikor a rendszer pontos ideje szerinti különböző kiszolgálókon vagy virtuális gépek felé sodródik túl messze egymástól.

Az SAP Hana az Azure-ban (nagyméretű példányok), időszinkronizálás végezhető el az Azure adatforgalmi&#39;t a számítási egységek nagyméretű példány stampek a alkalmazni. Mivel az Azure biztosítja, hogy a rendszer, a szinkronizálás nem alkalmazható a futó SAP-alkalmazások natív Azure-beli virtuális gépeken,&#39;s időt szinkronizálva megfelelően be van állítva. Ennek eredményeképpen egy külön idő server kell beállítani, amelyek segítségével az SAP az Azure virtuális gépek és az SAP HANA rendszerű alkalmazáskiszolgálók adatbázis nagyméretű HANA-példányokon futó üzemelő példányok. A tároló-infrastruktúra, a nagyméretű példány stampek időt szinkronizálva az NTP-kiszolgálókra.

## <a name="setting-up-smt-server-for-suse-linux"></a>SUSE Linux SMT kiszolgáló beállítása
SAP HANA nagyméretű példányok nem rendelkezik közvetlen kapcsolódás az internethez. Ezért nem egy egyszerű folyamatot egy ilyen egység regisztrálása operációsrendszer-szolgáltatóval, és töltse le és -javítások alkalmazása a. SUSE Linux-alapú esetén egy megoldás lehet egy Azure-beli virtuális gépen SMT kiszolgáló beállítása. Mivel az Azure virtuális gép a felhőben egy Azure virtuális hálózat, amely kapcsolódik a nagyméretű HANA-példányt szeretne. Az ilyen az SMT kiszolgálóval a nagyméretű HANA-példány egység sikerült regisztrálni és létesít a javítókészletek letöltéséhez. 

SUSE Ez a témakör egy nagyobb útmutató azok [előfizetés felügyeleti eszköze SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

A feladatot választják a nagyméretű HANA-példány egy SMT kiszolgáló telepítésének előfeltétele, mint kell:

- Egy Azure virtuális hálózatot, amelyhez a HANA nagyméretű példány ER-kapcsolatcsoporthoz csatlakozik.
- SUSE-fiók, amely egy szervezet társítva van. Mivel a szervezet rendelkezik néhány érvényes SUSE-előfizetéssel kell.

### <a name="installation-of-smt-server-on-azure-vm"></a>Az Azure virtuális gépen SMT server telepítése

Ebben a lépésben egy Azure-beli virtuális gépen a SMT kiszolgálót telepíti. Az első mérték, hogy jelentkezzen be a [SUSE ügyfél Center](https://scc.suse.com/)

Bejelentkezett, lépjen a szervezet a szervezeti hitelesítő adataival-->. Az adott szakaszban keresse meg a szükséges hitelesítő adatokat az SMT kiszolgáló beállításához.

A harmadik lépésben pedig a SUSE Linux virtuális gép telepítése az Azure virtuális hálózat. A virtuális gép üzembe helyezéséhez hajtsa végre a SLES 12 SP2 image z galerie Azure. A telepítési folyamat során nem ad meg egy DNS-nevet, és ne használja statikus IP-címeket a képernyőfelvételen látható módon

![virtuális gép üzembe helyezésének SMT kiszolgáló](./media/hana-installation/image3_vm_deployment.png)

Az üzembe helyezett virtuális gép egy kisebb méretű virtuális gép volt, és az Azure virtuális hálózat 10.34.1.4 a belső IP-címe van. A virtuális gép nevét smtserver volt. A telepítés után a kapcsolat a HANA nagyméretű példány egység lett érvényesítve. Függ, hogyan vannak rendszerezve névfeloldás szüksége lehet a nagyméretű HANA-példány egységek megoldás konfigurálása a stb/a gazdagépeket, az Azure virtuális Gépen. Adjon hozzá egy további lemezt a virtuális gép, amelyet szeretne használni, amely tárolja a javítások. Lehet, hogy a rendszerindító lemez maga túl kicsi. A bemutatott esetben a lemez az alábbi képernyőképen látható módon /srv/www/htdocs van csatlakoztatva. Egy 100 GB-os lemezt elegendőnek kell lennie.

![virtuális gép üzembe helyezésének SMT kiszolgáló](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Jelentkezzen be a nagyméretű HANA-példány egység, Hosts karbantartása, és ellenőrizze, hogy az Azure virtuális Gépen, amely már szabadna futtatni a SMT kiszolgáló a hálózaton keresztül érhető el.

Ez az ellenőrzés sikeresen megtörtént, miután kell jelentkezzen be az Azure virtuális Gépen, amely az SMT kiszolgáló kell futnia. A putty használatával jelentkezzen be a virtuális Gépre, ha szüksége ezen parancsok sorozatát végrehajtása a bash-ablakban:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Ezek a parancsok végrehajtása után indítsa újra aktiválni a beállításokat a bash. Indítsa el YAST.

A YAST lépjen a szoftverfrissítési karbantartás, és keresse meg a smt. Válassza ki az smt, amely automatikusan vált, amennyiben az yast2-smt alább látható módon

![A yast SMT](./media/hana-installation/image5_smt_in_yast.PNG)


Fogadja el a kijelölést a smtserver telepíthető. A telepítést követően nyissa meg a SMT konfigurációjának, és adja meg a szervezeti hitelesítő adatokkal a korábban kapott SUSE-ügyfél központban. Az Azure virtuális gép állomásneve is adja meg az SMT kiszolgáló URL-címet. Ebben a bemutatóban volt https://smtserver jelenik meg a következő ábrák szerint.

![SMT kiszolgáló konfigurációja](./media/hana-installation/image6_configuration_of_smtserver1.png)

Következő lépésként tesztelje a kapcsolatot a SUSE-ügyfél Center működik-e kell. Ahogyan az a következő ábrák a bemutató esetben látható, működött.

![Teszt SUSE ügyfél Center csatlakozni.](./media/hana-installation/image7_test_connect.png)

Egyszer a SMT telepítő elindul, meg kell adnia jelszót. Mivel ez egy új telepítést, határozza meg, hogy a jelszó, ahogyan az a következő ábrák kell.

![Adatbázis jelszavának megadása](./media/hana-installation/image8_define_db_passwd.PNG)

A következő kapcsolati van akkor, ha a tanúsítvány jön létre. A párbeszédpanelen látható a következő módon meg, és lépjen tovább, a lépést.

![A SMT server tanúsítvány létrehozása](./media/hana-installation/image9_certificate_creation.PNG)

Előfordulhat, hogy néhány percet töltött "Futtatás szinkronizálásának ellenőrzése" lépésében a konfiguráció végén. A telepítés után a SMT kiszolgáló konfigurációját, a címtár-adattárat a csatlakoztatási pont /srv/www/htdocs alatt keresse meg és ezen kívül néhány adattár alárendelt könyvtárakat. 

Indítsa újra az SMT kiszolgáló és a hozzá kapcsolódó szolgáltatások, az alábbi parancsokkal.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>A csomagok SMT szerveren letöltése

Után minden szolgáltatás újra lesz indítva, jelölje ki a megfelelő csomagok SMT felügyelet Yast használatával. A csomag kiválasztása attól függ, a nagyméretű HANA-példányt kiszolgáló operációsrendszer-lemezkép, és ne a SLES kiadás vagy a SMT kiszolgálón futó virtuális gép verziója. Az alábbiakban látható egy példa a kiválasztására szolgáló képernyő megjelenítéséhez.

![Csomagok kiválasztása](./media/hana-installation/image10_select_packages.PNG)

Miután végzett a csomag kijelölése, akkor indítsa el a kezdeti másolatot készít az SMT kiszolgáló beállítása a select csomagok. Ezt a példányt a rendszerhéj használatával a parancs smt-tükör alább látható módon aktiválódik


![Töltse le a csomagokat SMT kiszolgálóra](./media/hana-installation/image11_download_packages.PNG)

Ahogyan fent látható, a csomagok kell másolja be azokat a címtárakat, a csatlakoztatási pont /srv/www/htdocs alatt létrejön. Ez a folyamat eltarthat egy ideig. Függő csomagok számát választja, azt is igénybe vehet egy óráig vagy tovább.
Mivel ez a folyamat befejezését követően kell helyezze át az SMT ügyfél telepítése. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>A nagyméretű HANA-példány egységek az SMT ügyfél beállítása

Az ügyféltől/ügyfelektől érkezők ebben az esetben a nagyméretű HANA-példány egységek. A SMT kiszolgáló telepítését a parancsfájl clientSetup4SMT.sh átmásolhatja az Azure virtuális Gépen. Parancsfájl keresztül a nagyméretű HANA-példány egység, példányt szeretne csatlakozni az SMT kiszolgálóhoz. Indítsa el a szkriptet a -h beállítással és adjon neki paraméterként az SMT kiszolgáló nevét. Az ebben a példában smtserver.

![SMT ügyfél konfigurálása](./media/hana-installation/image12_configure_client.PNG)

Előfordulhat, hogy egy olyan forgatókönyvet, ahol az ügyfél által a kiszolgálóról a tanúsítvány betöltése sikeres volt, de a regisztráció sikertelen volt, ahogy az alábbi.

![Ügyfél regisztrálása meghiúsul](./media/hana-installation/image13_registration_failed.PNG)

Ha a regisztráció sikertelen, olvassa el ezt [SUSE támogatja a dokumentum](https://www.suse.com/de-de/support/kb/doc/?id=7006024) , és hajtsa végre az ott ismertetett lépéseket.

> [!IMPORTANT] 
> Kiszolgáló neve, meg kell adnia a virtuális gép, ez megkülönbözteti a kis smtserver, anélkül, hogy a teljes tartománynév a nevét. Csak a virtuális gép neve működését. 

Után a lépések végrehajtása megtörtént, hajtsa végre a következő parancsot a nagyméretű HANA-példány egységen kell

```
SUSEConnect –cleanup
```

> [!Note] 
> A vizsgálatok során mindig becsületessége Várjon pár percet után ezt a lépést. Az azonnali végrehajtás clientSetup4SMT.sh után a korrekciós intézkedéseket, a SUSE-cikkben leírt fejeződött be, hogy a tanúsítvány nem lenne érvényes még üzeneteket. Általában 5 – 10 percet várakozik, és clientSetup4SMT.sh végrehajtása befejeződött, a sikeres ügyfél-konfigurációval.

Ha a probléma akkor szükséges, javítsa ki a lépéseket, a SUSE-cikk alapján történő futtatta, indítsa újra a nagyméretű HANA-példány egységen clientSetup4SMT.sh újra szeretne. Most azt sikeresen be kell alább látható módon.

![Sikeres ügyfél-regisztrációk](./media/hana-installation/image14_finish_client_config.PNG)

Az ebben a lépésben a nagyméretű HANA-példány egység való csatlakozáshoz a SMT kiszolgálón telepítette, az Azure-beli virtuális gépen az SMT ügyfél konfigurálva. Most már elvégezhető "mentése a zypper használatával" vagy "a zypper használatával" az operációs rendszer telepíthetnek nagyméretű HANA-példányokhoz, vagy további csomagokat telepíteni. Egyetértés, hogy csak kap, amely a SMT kiszolgálón előzőleg letöltött javítások.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>A példában egy nagyméretű HANA-példányokon futó SAP HANA-telepítés
Ez a szakasz azt ábrázolja, hogyan telepítse az SAP HANA nagyméretű HANA-példány található. Az indítási állapot, hogy kell kinéznie:

- A Microsoft megadott egy SAP HANA nagyméretű példányok üzembe helyezéséhez, az adatokat.
- Az SAP HANA nagyméretű példányok a Microsofttól kapott.
- Létrehozott egy Azure virtuális hálózat, amely a helyszíni hálózathoz csatlakozik.
- A ExpressRotue kapcsolatcsoportot, kapcsolat HANA nagyméretű példányok az Azure vnet felé.
- Telepítette az Azure virtuális Gépekhez, mint a jump boxon HANA nagyméretű példányokhoz használhatja.
- Ellenőrizze, hogy csatlakozhat a jump boxon a nagyméretű HANA-példány egységet, és ez fordítva is igaz.
- Be van jelölve, hogy a szükséges csomagokat és azok javításait telepítve vannak.
- Olvassa el az SAP-megjegyzések és a dokumentációra vonatkozó HANA telepítése az operációs rendszer használ, és ellenőrizze, hogy a választott HANA kiadás esetében támogatott az operációs rendszer kiadási.

A letöltés a HANA-telepítési csomagot kell a jump boxon, ebben az esetben a csomagokat, a nagyméretű HANA-példány egységhez példányát és a feladatütemezés a beállítása egy Windows operációs rendszeren futó virtuális gép mi jelenik meg a következő feladatütemezések.

### <a name="download-of-the-sap-hana-installation-bits"></a>Töltse le az SAP HANA telepítése bitek
A nagyméretű HANA-példány egységek nincs közvetlen kapcsolódás az internethez, mivel, nem közvetlenül letölthető telepítőcsomagok SAP HANA nagyméretű példány virtuális géphez. Kiküszöbölheti a hiányzó közvetlen internetkapcsolattal, a jump boxon van szükség. A jump boxon VM töltse le a csomagokat.

Töltse le a HANA-telepítési csomagokat, egy SAP-S-felhasználó vagy a többi felhasználó számára, amely lehetővé teszi, hogy az SAP-piactér eléréséhez szükséges. A bejelentkezés után a képernyők sorozatát meg:

Lépjen a [SAP Service Marketplace-en](https://support.sap.com/en/index.html) > kattintson a letöltés szoftver > telepítés és frissítés > betűrend szerinti rendezés Index által > mellett a H – SAP HANA Platform Edition > SAP HANA Platform Edition 2.0 > telepítési > töltse le a következő fájlok

![Töltse le a HANA telepítése](./media/hana-installation/image16_download_hana.PNG)

A bemutató esetben letöltött SAP HANA 2.0 telepítési csomagokat. A Azure jump boxon virtuális gép, bontsa ki az önkicsomagoló archívumok címtárba alább látható módon.

![Bontsa ki a HANA telepítése](./media/hana-installation/image17_extract_hana.PNG)

Ki kell olvasni az archívumot, másolja a könyvtár hozta létre a kinyerés, abban az esetben a fenti 51052030, a /hana/shared kötet, létrehozott egy olyan könyvtárba, HANA nagyméretű példány egységhez.

> [!Important]
> Ne másolja telepítőcsomagok be azokat a legfelső szintű vagy LUN-t, mivel a hely korlátozva, és egyéb eljárásokkal kell használni.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>A nagyméretű HANA-példány egységen az SAP HANA telepítése
Az SAP HANA telepítése, jelentkezzen be a felhasználó legfelső szintű kell. Csak a legfelső szintű számára elegendő az SAP HANA telepítése.
Az első lépésben kell tennie, hogy engedélyeket állíthat be a másolt keresztül/hana és a megosztott könyvtár. Az engedélyeket kell megadni, például

```
chmod –R 744 <Installation bits folder>
```

Az SAP HANA-t a grafikus telepítés telepíteni szeretné, ha a gtk2 csomag telepítve kell lennie a nagyméretű HANA-példányokhoz. Ellenőrizze, hogy telepítve van-e a parancs

```
rpm –qa | grep gtk2
```

A további lépések azt a SAP HANA beállítása a grafikus felhasználói felülettel is bemutatására. Következő lépésként nyissa meg a telepítési könyvtárba, és keresse meg a sub HDB_LCM_LINUX_X86_64 könyvtárba. Indítás

```
./hdblcmgui 
```
ki a könyvtárhoz. Most már első végigvezeti sorozata képernyők, ahol meg kell adnia az adatokat a telepítéshez. Abban az esetben mutatja be hogy telepíti az SAP HANA-kiszolgáló és az SAP HANA összetevőinek. Ezért az érték a "Az SAP HANA-adatbázis" alább látható módon

![Válassza ki a HANA telepítése](./media/hana-installation/image18_hana_selection.PNG)

A következő képernyőn válassza az "Új rendszer telepítése" lehetőséget

![Válassza ki az új HANA-telepítés](./media/hana-installation/image19_select_new.PNG)

Ebben a lépésben után kell választania számos további összetevők, amelyek emellett telepíthetők, az SAP HANA-kiszolgáló között.

![Válassza ki a további HANA-összetevők](./media/hana-installation/image20_select_components.PNG)

Ez a dokumentáció céljából választottuk az SAP HANA-ügyfelet és az SAP HANA Studio. Azt is telepíti a vertikális felskálázás példány. ezért a következő képernyőn meg kell adnia "Egy állomásra rendszer" 

![Válassza ki a telepítés vertikális felskálázás](./media/hana-installation/image21_single_host.PNG)

A következő képernyőn meg kell adnia néhány adatot

![Adja meg az SAP HANA biztonsági azonosítója](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Mint HANA rendszer azonosítója (SID), meg kell adnia a SID AZONOSÍTÓVAL, a Microsoft megadott, amikor a nagyméretű HANA-példány üzembe helyezési megrendelt. Eltérő SID AZONOSÍTÓVAL kiválasztása lehetővé teszi a telepítést, a különböző köteteken hozzáférési engedély problémák miatt meghiúsul

Telepítésként használja a/hana/directory megosztott könyvtár. A következő lépésben meg kell adnia a helyek a HANA-adatfájlok és a HANA-naplófájlok


![Adja meg a HANA-napló helye](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Kell meghatározni, adatok és a köteteket, amely már a csatlakoztatási pontokat tartalmazó képernyő kiválasztása előtt ezen a képernyőn kiválasztott SID-naplófájljai. A biztonsági azonosító nem egyezik a tartományfejlécben megadott, ha a képernyőn előtt, lépjen vissza, és állítsa be a SID-t a csatlakoztatási pontok értékre.

A következő lépésben tekintse át a gazdagép nevét, és végül javítsa ki. 

![Felülvizsgálat állomás neve](./media/hana-installation/image24_review_host_name.PNG)

A következő lépésben szükség, megadott a Microsoftnak, amikor a nagyméretű HANA-példány üzembe helyezési megrendelt adatainak beolvasása. 

![Adja meg a rendszer felhasználói felhasználó- és CSOPORTAZONOSÍTÓ](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Meg kell adnia a rendszer-felhasználói azonosító és a felhasználói csoport azonosítója, a Microsoft elsajátítania ahhoz az egység üzembe helyezés. Ha nem ad nagyon ugyanazokat az azonosítókat, a nagyméretű HANA-példány egységen SAP HANA telepítése sikertelen.

A következő két képernyő, és azt nem megjelennek ebben a dokumentációban, meg kell adnia a jelszót a rendszer felhasználó, az SAP HANA-adatbázis és a sapadm felhasználó jelszava, a használt az SAP az SAP HANA datab részeként telepített gazdagép ügynök ASE-példány.

Határozza meg a jelszót, miután egy visszaigazoló képernyő jelenik meg. Ellenőrizze az összes adat szerepel, és a telepítés folytatásához. Eléri a folyamat képernyője, amely a telepítési folyamatot, mint például az egyik az alábbi dokumentumok

![Ellenőrizze a telepítési folyamat](./media/hana-installation/image27_show_progress.PNG)

A telepítés befejezését követően a következő egy hasonló képet kell

![Telepítés befejeződött](./media/hana-installation/image28_install_finished.PNG)

Ezen a ponton az SAP HANA-példány lehet akár és fut, és már használatra. Meg kell tudni kapcsolódni az SAP HANA Studio. Győződjön meg arról, hogy ellenőrizze az SAP Hana a legújabb javításokat, és e-javítások alkalmazása a is.
























































 







 




