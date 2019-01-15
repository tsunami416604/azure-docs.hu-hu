---
title: Az SAP HANA az Azure-ban (nagyméretű példányok) SAP HANA telepítése |} A Microsoft Docs
description: Útmutató az SAP HANA telepítése egy SAP HANA az Azure-ban (nagyméretű példányok).
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
ms.openlocfilehash: 10c8c0043d04d99ad10e475f903979edb0ddcb70
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266897"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Hogyan telepítse és konfigurálja az SAP HANA (nagyméretű példányok) az Azure-ban

A cikk elolvasása előtt ismerkedjen meg [HANA nagyméretű példányok gyakori használati](hana-know-terms.md) és a [HANA nagyméretű példányok termékváltozatok](hana-available-skus.md).

SAP HANA telepítése a feladata. Megkezdése után a kapcsolatot az Azure virtuális hálózatok és a nagyméretű HANA-példány egység között egy új SAP HANA Azure-ban (nagyméretű példányok) kiszolgálóra telepíti. 

> [!Note]
> SAP szabályzatonként SAP Hana telepítése egy személy, aki a vizsgára Certified SAP technológia társítja, az SAP HANA-telepítés minősítő vizsga megfelelt, vagy az SAP-minősítéssel rendelkező rendszerintegrátor (SI) hajtható végre.

Ha tervezi az HANA 2.0 telepítése, lásd: [SAP támogatási Megjegyzés #2235581 – SAP HANA: Támogatott operációs rendszerek](https://launchpad.support.sap.com/#/notes/2235581/E) , győződjön meg arról, hogy az operációs rendszer támogatott, az SAP HANA kiadás, amely telepíti. A támogatott operációs rendszer HANA 2.0 szigorúbb korlátozások vonatkoznak, mint a támogatott operációs rendszer HANA 1.0. 

> [!IMPORTANT] 
> II. típusú egységek, jelenleg csak a SLES 12 SP2 operációs rendszer verziója támogatott. 

A HANA-telepítés megkezdése előtt ellenőrizni kell a következőket:
- [HLI egység](#validate-the-hana-large-instance-units)
- [Az operációs rendszer konfigurálása](#operating-system)
- [Hálózati konfiguráció](#networking)
- [Tároló konfigurálása](#storage)


## <a name="validate-the-hana-large-instance-units"></a>A nagyméretű HANA-példány egység ellenőrzése

Miután a Microsoft megkapta a nagyméretű HANA-példány egység, érvényesíteni a következő beállításokat, és szükség szerint módosítsa.

A **első lépés** után a nagyméretű HANA-példányt kap, és hozzáférési és kapcsolati példányok, az, hogy regisztrálja az operációs rendszer, a példány a operációsrendszer-szolgáltatónál. Ezt a lépést tartalmaz, a SUSE Linux operációs rendszer regisztrálja az SUSE SMT egy virtuális Gépet az Azure-ban üzembe helyezett egy példányát. 

A nagyméretű HANA-példány egység a SMT példány csatlakozhat. (További információkért lásd: [SMT kiszolgáló beállítása a SUSE Linux](hana-setup-smt.md)). Másik lehetőségként a Red Hat-OS regisztrálva kell lennie a Red Hat előfizetés-kezelő használatával való csatlakozáshoz szükséges. További információkért lásd: a megjegyzések [Mi az SAP HANA az Azure-ban (nagyméretű példányok)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Ez a lépés is szükség az operációs rendszer, amely az ügyfél felelőssége a javítás céljából. A SUSE, keresse meg a dokumentációt telepítéséhez és konfigurálásához az SMT ezen az oldalon kapcsolatos [SMT telepítési](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

A **második lépése** új javítások és javítások, az adott operációs rendszer/verzió. Győződjön meg arról, hogy a javítási szintje, a nagyméretű HANA-példányt a legújabb állapotban van. Előfordulhatnak olyan esetekben, ahol a legújabb javításokat nem tartalmaz. Után egy nagyméretű HANA-példány egység átvenni, legyen kötelező, ellenőrizze, hogy javítást kell alkalmazni.

A **harmadik lépés** tekintse meg a megfelelő SAP-megjegyzések telepítéséhez és konfigurálásához az SAP HANA található az adott operációs rendszer/verzió. Javaslatok vagy a módosítások az SAP megjegyzések vagy egyéni telepítési forgatókönyvek függő konfigurációk módosítása, mert a Microsoft nem mindig lehet konfigurálni a nagyméretű HANA-példány egység tökéletesen. 

Ezért azt kötelező, a pontos Linuxos kiadás SAP Hana-hoz kapcsolódó ügyfél olvasni az SAP-megjegyzések. Is az operációs rendszer verzió konfigurációit, és a konfigurációs beállítások érvényesek, ha még nem tette.

Pontosabban a következő paramétereket, és végül állítsuk be:

- NET.Core.rmem_max = 16777216
- NET.Core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

SLES12 SP1 és a RHEL 7.2 kezdődően ezeket a paramétereket kell állítani a konfigurációs fájlban /etc/sysctl.d a címtárban. Ha például a 91 – NetApp-HANA.conf nevét egy konfigurációs fájl kell létrehozni. A régi SLES és RHEL kiadásokhoz ezek a paraméterek beállítása in/etc/sysctl.conf kell lennie.

Minden RHEL kiadásokban SLES12 kezdve vegye figyelembe a következőket: 
- A sunrpc.tcp_slot_table_entries = 128 in/etc/modprobe.d/sunrpc-local.conf paramétert kell beállítani. Ha a fájl nem létezik, szeretne létrehozni, először a következő bejegyzés hozzáadásával: 
    - beállítások sunrpc tcp_max_slot_table_entries = 128

A **negyedik lépést** annak ellenőrzése, a rendszer pontos ideje szerinti a nagyméretű HANA-példány egység. A példányok egy rendszer időzónával együtt települnek. Ezt az időzónát a az Azure-régió, amelyben a HANA nagyméretű szolgáltatáspéldányban helyét jelöli. A rendszer pontos ideje szerinti vagy a saját példányok időzónájának módosítása 

Ha több példány a bérlőbe, az adott időzóna az újonnan kézbesített példányainak alkalmazkodni szüksége. A Microsoft nem beállítása az osztályt a készenléti feladat átadása után a rendszer időzóna betekintést rendelkezik. Ebből kifolyólag újonnan üzembe helyezett példány beállítása nem azzal, akkor módosította az ugyanabban az időzónában található. Van szükség, hogy a rendszer át, a példány időzónája alkalmazkodni ügyfélként Ön felelőssége. 

A **ötödik lépés** etc/hosts ellenőrzése. A paneleket első gyorsítási értéknek, átadná, mert különböző felhasználási célokra hozzárendelt különböző IP-címek rendelkeznek. Ellenőrizze a etc/hosts fájlt. Egységek kerülnek be egy meglévő bérlőt, ha várhatóan nincs megfelelően fenntartott IP-címekről korábban kézbesítése rendszerek az újonnan telepített rendszerek stb/gazdagépre van szükség. Feladata, az ügyfél számára teszi arról, hogy egy újonnan üzembe helyezett példány kezelését, és a korábban a bérlő az egységek a nevek feloldásához. 

## <a name="operating-system"></a>Operációs rendszer

> [!IMPORTANT] 
> II. típusú egységek csak a SLES 12 SP2 operációsrendszer-verzió jelenleg támogatott. 

A lapozóterület a kézbesített operációsrendszer-lemezképek 2 GB értékre van állítva a [SAP támogatási Megjegyzés #1999997 – gyakori kérdések: SAP HANA memóriában](https://launchpad.support.sap.com/#/notes/1999997/E). Microsoft Ha azt szeretné, hogy egy másik beállítást be kell állítani saját magának.

[SUSE Linux Enterprise Server 12 SP1 SAP-alkalmazások](https://www.suse.com/products/sles-for-sap/download/) az SAP Hana az Azure-ban (nagyméretű példányok) telepített Linux-disztribúció. Az adott terjesztési SAP-specifikus képességeket biztosít "kulcsrakész" (beleértve az előre beállított paraméterek SLES SAP hatékonyan futtatásával).

Lásd: [erőforrás könyvtár-/ tanulmányok](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) a SUSE-webhelyen, és [SAP SUSE-](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) meg az SAP közösségi hálózati Állapotváltozás-az SAP HANA az (beleértve a telepítési magas SLES telepítésével kapcsolatos néhány hasznos források rendelkezésre állás, a biztonság megerősítése, hogy kifejezetten az SAP-műveletek, és egyéb).

A következő további és hasznos SAP SUSE – kapcsolódó hivatkozások a következő:

- [SAP HANA az SUSE Linux-hely](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Ajánlott eljárások az SAP: Sorba replikációs – az SAP NetWeaver SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – elleni védelem SLES for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (beleértve a SLES 12 rendszert az SAP-alkalmazások)

SAP támogatási megjegyzések, amelyek a alkalmazni, amelyek segítenek a SAP HANA SLES 12 a következők:

- [SAP támogatási Megjegyzés #1944799 – SAP HANA-útmutató a SLES operációs rendszer telepítése](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [SAP támogatási Megjegyzés #2205917 – SAP HANA-adatbázis ajánlott az operációs rendszer beállításait a SLES 12 rendszert SAP-alkalmazások](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP támogatási Megjegyzés #1984787 – SUSE Linux Enterprise Server 12: telepítési jegyzetek](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP támogatási Megjegyzés #171356 – SAP-szoftverek Linux rendszeren:  Általános információk](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP támogatási Megjegyzés #1391070 – Linux UUID-megoldások](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) van egy másik ajánlatra, a nagyméretű HANA-példányokon futó SAP HANA futtatásához. RHEL 6.7 és 7.2 kiadásaiban érhetők el. Vegye figyelembe, hogy helyett natív Azure virtuális gépek csak az RHEL 7.2 és újabb verziókban támogatott ahol, HANA nagyméretű példányok támogatja RHEL 6.7 is. Azt javasoljuk azonban, egy RHEL 7.x verzió használatával.

További hasznos SAP, a Red Hat kapcsolódó hivatkozások a következők:
- [SAP HANA, Red Hat Linux helyen](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

SAP támogatási megjegyzések érvényes SAP HANA végrehajtási Red hat a következők:

- [SAP támogatási Megjegyzés #2009879 – SAP HANA-irányelvek Red Hat Enterprise Linux (RHEL) operációs rendszerhez](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP támogatási Megjegyzés #2292690 – SAP HANA-adatbázis: RHEL 7 ajánlott az operációs rendszer beállításai](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP támogatási Megjegyzés #2247020 – SAP HANA-adatbázis: RHEL 6.7 ajánlott az operációs rendszer beállításai](https://launchpad.support.sap.com/#/notes/2247020)
- [SAP támogatási Megjegyzés #1391070 – Linux UUID-megoldások](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP támogatási Megjegyzés 2228351 # – Linux: Az SAP HANA Database Szervizcsomagok 11 változat 110 (vagy magasabb) az RHEL 6-os vagy SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [SAP támogatási Megjegyzés #2397039 – gyakori kérdések: Az RHEL SAP](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP támogatási Megjegyzés #1496410 – Red Hat Enterprise Linux 6.x: Telepítés és frissítés](https://launchpad.support.sap.com/#/notes/1496410)
- [SAP támogatási Megjegyzés #2002167 – Red Hat Enterprise Linux 7.x: Telepítés és frissítés](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Időszinkronizálás

SAP-alkalmazások, az SAP NetWeaver architektúrával beépített érzékenyek az SAP-rendszer alkotó különféle összetevők időeltérést. Rövid SAP ABAP-ZDATE hiba címe memóriaképek\_nagy\_idő\_DIFF valószínűleg ismeri. Ennek oka az, e rövid memóriaképek jelenik meg, amikor a rendszer pontos ideje szerinti különböző kiszolgálókon vagy virtuális gépek felé sodródik túl messze egymástól.

Az SAP Hana az Azure-ban (nagyméretű példányok) az Azure-ban elvégzett időszinkronizálás nagyméretű példány stampek számítási egységek nem vonatkozik. A szinkronizálás nem alkalmazható a futó SAP-alkalmazások natív Azure-beli virtuális gépeken, mert az Azure biztosítja, hogy a rendszer pontos ideje szerinti megfelelően van-e szinkronizálva. 

Ennek eredményeképpen, be kell állítania egy külön időkiszolgálóval használható és nagyméretű HANA-példányokon futó SAP HANA database-példányokat Azure virtuális gépeken futó SAP-alkalmazáskiszolgálókhoz által. A tároló-infrastruktúra, a nagyméretű példány stampek időt szinkronizálva az NTP-kiszolgálókra.


## <a name="networking"></a>Hálózat
Feltételezzük, hogy elvégezte a javaslatok az Azure virtuális hálózatok kialakítása és a virtuális hálózatok csatlakozik a HANA nagyméretű példányok az alábbi dokumentumokban leírtaknak megfelelően:

- [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Vannak bizonyos részletek, érdemes megemlíteni egyetlen egységek a hálózatokkal kapcsolatos. Két vagy három két vagy három hálózati adapterportot rendelt IP-címek nagyméretű HANA-példány egység tartalmaz. Három IP-címeket a HANA kibővített konfigurációkhoz és a HANA system replikációs forgatókönyvet használja. Az egység a hálózati Adapterhez hozzárendelt IP-címek egyikét kívül esik a kiszolgáló IP-címkészlet leírt [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Az architektúra részleteit Ethernet kapcsolatos további információkért lásd: a [HLI támogatott forgatókönyvek](hana-supported-scenario.md).

## <a name="storage"></a>Storage

A tárolási elrendezés az SAP Hana az Azure-ban (nagyméretű példányok) az Azure service management keresztül irányelvek ajánlott SAP az SAP Hana van konfigurálva. Ezeket az irányelveket vannak dokumentálva az [SAP HANA tárolási követelményei](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) tanulmányt. 

A különböző köteteket, a másik nagyméretű HANA-példányok termékváltozatok hozzávetőleges mérete a dokumentált [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A tároló kötetek elnevezési szabályai az alábbi táblázatban láthatók:

| Tárhelyhasználat | Csatlakozási név | Kötet neve | 
| --- | --- | ---|
| HANA-adatok | /hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA-napló | /hana/log/SID/mnt0000<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA naplóalapú biztonsági mentés | /Hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| A megosztott HANA | /hana/shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*Biztonsági azonosító* a HANA-példány azonosítóját a rendszer. 

*Bérlő* műveletek belső enumeráció van, egy bérlő üzembe helyezésekor.

HANA usr/sap ossza meg ugyanazt a kötetet. Az elnevezési rendszeréhez, akkor a csatlakozási a rendszer-azonosító, a HANA-példányokhoz, valamint a csatlakoztatási számát tartalmazza. Vertikális felskálázás telepítések csak egy csatlakoztatási, például mnt00001 van. A horizontális felskálázás telepítések esetén, másrészt látható annyi csatlakoztatása, feldolgozói és a fő csomóponttal rendelkezik. 

A horizontális felskálázás környezetek, az adatok, a napló és a log biztonsági mentési kötetek megosztott és a kibővített konfigurációs az egyes csomópontokhoz csatolt. Azok a konfigurációk, amelyek több SAP-példányok esetén a kötetek külön készletét létrehozása és csatlakoztatása a nagyméretű HANA-példány egységhez. Tárolási elrendezés az forgatókönyvhöz, lásd: [HLI támogatott forgatókönyvek](hana-supported-scenario.md).

Ha egy nagyméretű HANA-példány egység, kiderülhet, hogy az egységek kapható korlátozó lemezkötetet HANA/adatok, és hogy van-e a kötet HANA/log/biztonsági mentés. Végeztünk a HANA/data rendkívül nagy méretűek, oka az, hogy a tárolási pillanatképek biztosítunk a Microsoft használ azonos lemezen kötet. A további tárolási pillanatképek hajt végre, a több helyet használja fel a hozzárendelt tároló köteteken található pillanatkép. 

A HANA/log/biztonsági mentési mennyiségi nem várt adatbázis biztonsági másolatait a köteten. Azt az van méretezve, hogy a HANA tranzakciónaplók biztonsági mentését a biztonsági mentési mennyiségi használható. További információkért lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Megadott tároló mellett további tárolási kapacitása 1 TB-os lépésekben vásárolhat. A további tárhely is hozzáadhatók új kötetek egy nagyméretű HANA-példányt.

Bevezetés az SAP HANA az Azure service management, során az ügyfél és adja meg felhasználói Azonosítóját (UID) a sidadm felhasználói és sapsys csoport azonosítója (CSOPORTAZONOSÍTÓ) csoport (például: 1000,500). Az SAP HANA-rendszer a telepítés során ezeket ugyanazokat az értékeket kell használnia. Mivel egységen több HANA-példányok üzembe helyezéséhez, kötetek (minden példány egy set) több készletétől kap. Ennek eredményeképpen üzembe helyezéskor meg kell határoznia a következőket:

- A különböző HANA-példányok (sidadm abból származó) biztonsági azonosítója.
- A memória méretét a különböző HANA-példányokhoz. A memória méretét példányonként az egyes egyedi kötet határozza meg a kötetek méretét.

A tárolási szolgáltató javaslatok alapján, a következő csatlakoztatási lehetőségeket vannak konfigurálva az összes csatlakoztatott kötetek esetében (kivéve az rendszerindító LUN-t):

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Ezek a csatlakoztatási pontok vannak konfigurálva a /etc/fstab, ahogyan az alábbi ábrák:

![a nagyméretű HANA-példány egység csatlakoztatott kötetek fstab](./media/hana-installation/image1_fstab.PNG)

A parancs df -h S72m HANA nagyméretű példányok egységen kimenete hasonlóan néz ki:

![a nagyméretű HANA-példány egység csatlakoztatott kötetek fstab](./media/hana-installation/image2_df_output.PNG)


A tárolóvezérlő nagyméretű példány stampek csomópontok szinkronizálja az NTP-kiszolgálókra. Ha Azure-beli és az SAP HANA az Azure-ban (nagyméretű példányok) egység szinkronizálja egy NTP-kiszolgálóval szemben, az infrastruktúra és a számítási egységeket az Azure-ban vagy a nagyméretű példány stampek között nincs jelentős mennyiségű időt eltéréseket lehetnek.

A Storage alatt használt optimalizálható a SAP HANA, állítsa be az alábbi SAP HANA-konfigurációs paramétereket:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- az összes async_write_submit_blocks
 
A SAP HANA 1.0-s verzió legfeljebb SPS12, ezeket a paramétereket állítható be, az SAP HANA-adatbázis telepítése során leírtak szerint [SAP-jegyzetnek #2267798 – az SAP HANA-adatbázis konfigurációs](https://launchpad.support.sap.com/#/notes/2267798).

A hdbparam keretrendszer használatával a SAP HANA-adatbázis telepítése után is konfigurálhatja a paramétereket. 

Az SAP HANA 2.0-val a hdbparam keretrendszer elavult. Ennek eredményeképpen a paramétert kell beállítani az SQL-parancsok használatával. További információkért lásd: [SAP-jegyzetnek #2399079: A HANA 2 hdbparam felszámolása](https://launchpad.support.sap.com/#/notes/2399079).

Tekintse meg [HLI támogatott forgatókönyvek](hana-supported-scenario.md) további információ a tárolási elrendezés az architektúrához.


**Következő lépések**

- Tekintse meg [HLI HANA telepítése](hana-example-installation.md)










































 







 




