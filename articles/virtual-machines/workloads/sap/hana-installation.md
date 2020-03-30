---
title: Telepítse az SAP HANA-t az SAP HANA-ra az Azure-on (nagy példányok) | Microsoft dokumentumok
description: Hogyan telepítsük az SAP HANA-t egy SAP HANA-ra az Azure-ban (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca59305b22fcf1e81ef518612910731cb6edea5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617098"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Az SAP HANA (nagy példányok) telepítése és konfigurálása az Azure-ban

A cikk elolvasása előtt ismerkedjen meg a [HANA nagypéldányokkal kapcsolatos gyakori kifejezésekkel](hana-know-terms.md) és a [HANA nagy példányok skus-aival.](hana-available-skus.md)

Az SAP HANA telepítése az Ön felelőssége. Az Azure virtuális hálózatai és a HANA nagy példányok közötti kapcsolat létrehozása után megkezdheti egy új SAP HANA telepítését az Azure (Large Instances) kiszolgálón. 

> [!Note]
> SAP-házirend szerint az SAP HANA telepítését olyan személynek kell elvégeznie, aki megfelelt a Certified SAP Technology Associate vizsgán, az SAP HANA telepítési minősítő vizsgán, vagy aki SAP-tanúsítvánnyal rendelkező rendszerintegrátor (SI).

Ha a HANA 2.0 telepítését tervezi, olvassa el [az SAP támogatási megjegyzése #2235581 - SAP HANA: Támogatott operációs rendszerek](https://launchpad.support.sap.com/#/notes/2235581/E) győződjön meg arról, hogy az operációs rendszer támogatja az SAP HANA-kiadás, amely et telepít. A HANA 2.0 támogatott operációs rendszer e-nél szigorúbb, mint a HANA 1.0 támogatott operációs rendszer. Azt is ellenőriznie kell, hogy az Ön számára érdekelt operációs rendszer kiadása támogatottként szerepel-e az adott HLI egység számára ezen a közzétett [listán.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Kattintson az egységre, hogy a teljes részleteket a támogatott operációs rendszer listáját, hogy az egység. 

A HANA-telepítés megkezdése előtt ellenőrizze a következőket:
- [HLI egység(ek)](#validate-the-hana-large-instance-units)
- [Operációs rendszer konfigurációja](#operating-system)
- [Hálózati konfiguráció](#networking)
- [Tároló konfigurálása](#storage)


## <a name="validate-the-hana-large-instance-units"></a>A HANA nagypéldány-egység(ek) ellenőrzése

Miután megkapta a HANA nagypéldány-egységet a Microsofttól, ellenőrizze a következő beállításokat, és szükség szerint módosítsa.

Az **első lépés** a HANA nagy példány fogadása és a példányok hozásához való hozzáférés és kapcsolat létrehozása után, hogy ellenőrizze az Azure Portalon, hogy a példány(ok) jelennek-e meg a megfelelő SK-k és operációs rendszer. Olvassa el [az Azure HANA nagypéldányok vezérlését](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal) az Azure Portalon keresztül az ellenőrzések végrehajtásához szükséges lépésekhez.

A **második lépés** a HANA nagy példány fogadása után, és hozzon létre hozzáférést és kapcsolatot a példányok, regisztrálni a példány operációs rendszer az operációs rendszer az operációs rendszer szolgáltatójának. Ez a lépés magában foglalja a SUSE Linux operációs rendszer regisztrálását a SUSE SMT egy olyan példányában, amely az Azure-ban egy virtuális gépben van telepítve. 

A HANA nagy példány egység csatlakozhat ehhez az SMT-példányhoz. (További információ: [SMT-kiszolgáló beállítása SUSE Linux hoz).](hana-setup-smt.md) Másik lehetőségként a Red Hat operációs rendszer regisztrálva kell lennie a Red Hat Előfizetés-kezelő, hogy meg kell csatlakozni. További információkért tekintse meg a megjegyzések [mi az SAP HANA az Azure-ban (nagy példányok)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Ez a lépés szükséges az operációs rendszer javításához, amely az ügyfél felelőssége. A SUSE esetében keresse meg az SMT telepítésével és konfigurálásával kapcsolatos dokumentációt ezen az oldalon az [SMT telepítéséről.](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)

A **harmadik lépés** az, hogy ellenőrizze az új javítások és javítások az adott operációs rendszer release / version. Ellenőrizze, hogy a HANA nagy példány javítási szintje a legújabb állapotban van-e. Előfordulhatnak olyan esetek, amikor a legújabb javítások nem szerepelnek. A HANA nagypéldány-egység átvétele után kötelező ellenőrizni, hogy a javításokat kell-e alkalmazni.

A **negyedik lépés** az, hogy nézd meg a megfelelő SAP-jegyzetek telepítéséhez és konfigurálásához SAP HANA az adott operációs rendszer kiadása/verziója. Az EGYES telepítési forgatókönyvektől függő SAP-megjegyzések vagy konfigurációk módosításai miatt a Microsoft nem mindig tudja tökéletesen konfigurálni a HANA nagypéldány-egységét. 

Ezért az Ügyfél számára kötelező elolvasni az SAP HANA-hoz kapcsolódó SAP-megjegyzéseket a pontos Linux-kiadáshoz. Ellenőrizze az operációs rendszer kiadásának/verziójának konfigurációit is, és alkalmazza a konfigurációs beállításokat, ha még nem tette meg.

Pontosabban ellenőrizze a következő paramétereket, és végül igazodjon:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Az SLES12 SP1 és RHEL 7.2 fájltól kezdve ezeket a paramétereket az /etc/sysctl.d könyvtár konfigurációs fájljában kell beállítani. Például létre kell hozni egy 91-NetApp-HANA.conf nevű konfigurációs fájlt. A régebbi SLES és RHEL kiadások esetén ezeket a paramétereket a/etc/sysctl.conf fájlban kell beállítani.

Az RHEL 6.3-as sal kezdődő rhel 6.3-as kiadások esetében tartsa szem előtt a következőket: 
- A sunrpc.tcp_slot_table_entries = 128 paramétert be kell állítani az in/etc/modprobe.d/sunrpc-local.conf fájlba. Ha a fájl nem létezik, először a következő bejegyzést kell hozzáadnia: 
    - lehetőségek sunrpc tcp_max_slot_table_entries=128

Az **ötödik lépés** a HANA nagypéldány-egység rendszeridejének ellenőrzése. A példányok rendszeridőzónával vannak telepítve. Ez az időzóna az Azure-régió helyét jelöli, amelyben a HANA nagy példány bélyegzője található. Módosíthatja a saját példányainak rendszeridejét vagy időzónáját. 

Ha több példányt rendel a bérlőbe, módosítania kell az újonnan szállított példányok időzónáját. A Microsoft nem rendelkezik betekintést a rendszer időzónáját beállított a példányok átadása után. Így előfordulhat, hogy az újonnan telepített példányok nincsenek beállítva ugyanabban az időzónában, mint amelyet módosított. Az Ön felelőssége, mint ügyfél, hogy szükség esetén igazítsa az átadott példány(ok) időzónáját. 

A **hatodik lépés** az, hogy ellenőrizze, stb / házigazdák. Ahogy a kések átadásra kerülnek, különböző IP-címekkel rendelkeznek, amelyek különböző célokra vannak hozzárendelve. Ellenőrizze az etc/hosts fájlt. Amikor egységek et adnak hozzá egy meglévő bérlőhöz, ne számítson arra, hogy az újonnan üzembe helyezett rendszerek etc/hosts megfelelően karbantartható a korábban leszállított rendszerek IP-címével. Az Ön felelőssége, mint ügyfél, hogy megbizonyosodjon arról, hogy egy újonnan üzembe helyezett példány kölcsönhatásba léphet, és feloldja a nevét, hogy az egységek, amelyek korábban üzembe helyezett a bérlőben. 


## <a name="operating-system"></a>Operációs rendszer

A leszállított operációsrendszer-lemezkép csereterülete 2 GB az [SAP támogatási megjegyzésének #1999997 - GYIK: SAP HANA memória](https://launchpad.support.sap.com/#/notes/1999997/E)szerint. Vevőként, ha más beállítást szeretne, saját magának kell beállítania.

[SUSE Linux Enterprise Server 12 SP1 SAP-alkalmazások](https://www.suse.com/products/sles-for-sap/download/) a Linux, amely telepítve van az SAP HANA az Azure-ban (nagy példányok). Ez a bizonyos disztribúció sap-specifikus képességeket biztosít "a dobozból" (beleértve az SAP SLES-en való hatékony futtatásához beállított paramétereket).

Tekintse meg [a Forráskönyvtár/tanulmányok a](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) SUSE-webhelyen és az [SAP-suse-on](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) az SAP közösségi hálózaton (SCN) az SAP HANA SLES-en történő üzembe helyezéséhez kapcsolódó számos hasznos erőforrást (beleértve a magas rendelkezésre állás ú, az SAP-műveletekre jellemző biztonsági edzésbeállítását stb.).

A következőkben további és hasznos SAP a SUSE-hoz kapcsolódó linkeken:

- [SAP HANA a SUSE Linux webhelyen](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Gyakorlati tanácsok az SAP-hoz: Várólistára helyezett replikáció – SAP NetWeaver a SUSE Linux Enterprise 12 rendszeren](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – SLES vírusvédelem SAP (beleértve](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) az SLES 12 SAP alkalmazások)

Az sap hana sles 12-es gépen történő megvalósításához az SAP támogatási megjegyzések a következők:

- [SAP támogatási megjegyzés #1944799 – SAP HANA irányelvek az SLES operációs rendszer telepítéséhez](http://service.sap.com/sap/support/notes/1944799)
- [SAP támogatási megjegyzés #2205917 – Az SAP HANA DB ajánlott operációs rendszer beállításai az SLES 12-hez az SAP-alkalmazásokhoz](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP támogatási megjegyzés #1984787 – SUSE Linux Enterprise Server 12: telepítési megjegyzések](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP támogatási megjegyzés #171356 - SAP szoftver Linuxon: Általános információk](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP támogatási megjegyzés #1391070 – Linux UUID megoldások](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) egy másik ajánlat az SAP HANA hana hana hana hana hana nagy példányokon futtatásához. Az RHEL 7.2 és 7.3 kiadásai elérhetők és támogatottak. 

Az alábbiakban további hasznos SAP a Red Hat kapcsolódó linkek:
- [SAP HANA a Red Hat Linux oldalon](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

A következőkben az SAP támogatási megjegyzések, amelyek az SAP HANA red hat on végrehajtásához alkalmazhatók:

- [SAP támogatási megjegyzés #2009879 - SAP HANA irányelvek a Red Hat Enterprise Linux (RHEL) operációs rendszerhez](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP támogatási megjegyzés #2292690 - SAP HANA DB: Ajánlott operációsrendszer-beállítások az RHEL 7-hez](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP támogatási megjegyzés #1391070 – Linux UUID megoldások](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP támogatási megjegyzés #2228351 - Linux: SAP HANA Database SPS 11 11 (vagy újabb) rhel 6 vagy Sles 11 rendszeren](https://launchpad.support.sap.com/#/notes/2228351)
- [SAP támogatási megjegyzés #2397039 - GYIK: SAP az RHEL-en](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP támogatási megjegyzés #2002167 - Red Hat Enterprise Linux 7.x: Telepítés és frissítés](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Időszinkronizálás

Az SAP NetWeaver architektúrára épülő SAP-alkalmazások érzékenyek az SAP-rendszert alkotó különböző összetevők időkülönbségére. Sap ABAP rövid dumps a hiba\_\_címe\_ZDATE NAGY IDŐ DIFF valószínűleg ismerős. Ennek az az oka, hogy ezek a rövid memóriaképek akkor jelennek meg, amikor a különböző kiszolgálók vagy virtuális gépek rendszerideje túl messze van egymástól.

Az Sap HANA az Azure-ban (nagy példányok) időszinkronizálás, amely az Azure-ban végzett nem vonatkozik a számítási egységek a nagy példány bélyegek. Ez a szinkronizálás nem alkalmazható sap-alkalmazások natív Azure-beli virtuális gépeken való futtatásához, mivel az Azure biztosítja, hogy a rendszer ideje megfelelően szinkronizálva legyen. 

Ennek eredményeképpen be kell állítania egy külön időkiszolgálót, amelyet az Azure virtuális gépeken és a HANA nagy példányokon futó SAP HANA-adatbázispéldányok on futó SAP-alkalmazáskiszolgálók használhatnak. A nagypéldány-bélyegzők tárolóinfrastruktúrája időszinkronizált az NTP-kiszolgálókkal.


## <a name="networking"></a>Hálózat
Feltételezzük, hogy követte az Azure virtuális hálózatok tervezésére és a virtuális hálózatok nak a HANA nagy példányokhoz való csatlakoztatására vonatkozó javaslatokat, a következő dokumentumokban leírtak szerint:

- [SAP HANA (nagy példány) áttekintése és architektúrája az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA (nagy példányok) infrastruktúrája és kapcsolata az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Van néhány említésre méltó részlet az egyes egységek hálózatba való kihálózatával kapcsolatban. Minden HANA nagy példány egység két vagy három IP-címeket, amelyek két vagy három hálózati adapter portok van hozzárendelve. Három IP-cím hana horizontális felskálázási konfigurációk és a HANA rendszer replikációs forgatókönyv. Az egység hálózati adapteréhez rendelt IP-címek egyike az [SAP HANA (Nagy példányok) áttekintésében és architektúrájában az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)ismertetett kiszolgálóI IP-készleten kívüli.

Az architektúra Ethernet-részleteiről a [HLI által támogatott forgatókönyvekben](hana-supported-scenario.md)talál további információt.

## <a name="storage"></a>Storage

Az SAP HANA tárolási elrendezését az Azure-ban (nagy példányok) az SAP HANA konfigurálja az Azure-ban `service management` az SAP ajánlott irányelvein keresztül. Ezeket az irányelveket az [SAP HANA tárolási követelmények](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) tanulmány dokumentálja. 

A különböző kötetek durva méretei a különböző HANA nagy példányok sku-k dokumentálva sap [HANA (nagy példányok) áttekintése és architektúrája az Azure-ban.](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

A tárolókötetek elnevezési konvencióit az alábbi táblázat tartalmazza:

| Tárolási használat | Csatlakoztatás neve | Kötet neve | 
| --- | --- | ---|
| HANA-adatok | /hana/data/SID/mnt0000\<m> | Tárolási IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA-napló | /hana/log/SID/mnt0000\<m> | Tárolási IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA napló biztonsági mentése | /hana/log/backups | Tárolási IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA megosztva | /hana/shared/SID | Tárolási IP:/hana_shared_SID_mnt00001_tenant_vol/megosztott |
| usr/sap | /usr/sap/SID | Tárolási IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* a HANA-példány rendszerazonosítója. 

*Bérlő* egy belső felsorolása műveletek üzembe helyezésekor egy bérlő.

HANA usr/sap ugyanazt a kötetet. A csatlakoztatási pontok nómenklatúrája tartalmazza a HANA-példányok rendszerazonosítóját, valamint a csatlakoztatási számot. A felskálázási telepítésekben csak egy csatlakoztatás van, például az mnt00001. A horizontális felskálázási központi telepítések, másrészt, annyi csatlakoztatások, mint afeldolgozó és a fő csomópontok. 

Kibővített környezetekben az adatok, a napló és a napló biztonsági mentési kötetei meg vannak osztva, és a kibővített konfigurációban minden csomóponthoz kapcsolódnak. Több SAP-példány konfigurációk esetén egy másik kötetkészlet jön létre, és a HANA nagy példány egységhez csatlakozik. A forgatókönyv tárolási elrendezésének részleteit a [HLI által támogatott forgatókönyvek című témakörben találja.](hana-supported-scenario.md)

Ha megnézi a HANA nagy példány egység, rájössz, hogy az egységek jönnek a hana/data nagyvonalú lemezkötettel, és hogy van egy kötet HANA/log/backup. Az ok, hogy mi történt a HANA/adatok olyan nagy, hogy a tárolási pillanatképek kínálunk Önnek, mint egy ügyfél használja ugyanazt a lemezkötetet. Minél több tárolási pillanatképet hajt végre, annál több helyet használnak fel a hozzárendelt tárolókötetek pillanatképei. 

A HANA/log/backup kötet nem lehet az adatbázis biztonsági másolatainak kötete. A HANA tranzakciónapló biztonsági másolatainak biztonsági másolataként használható. További információ: [SAP HANA (Large Instances) magas rendelkezésre állású és vész-helyreállítási az Azure-ban.](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

A rendelkezésre álló tárhelymellett további tárolókapacitást is vásárolhat 1 TB-os lépésekben. Ez a további tároló új kötetekként adható ke- és hana nagy példány.

Az SAP HANA Azure-beli `service management`bevezetés során az ügyfél megadja a sidadm-felhasználó és a sapsys csoport felhasználói azonosítóját (UID) és csoportazonosítót (GID) (például: 1000 500). Az SAP HANA rendszer telepítése során ugyanazokat az értékeket kell használnia. Mivel több HANA-példányt szeretne telepíteni egy egységre, több kötetkészletet kap (minden példányhoz egy készletet). Ennek eredményeképpen a telepítés időpontjában meg kell határoznia:

- A különböző HANA-példányok SID-je (a sidadm származik belőle).
- A különböző HANA-példányok memóriamérete. A példányonkénti memóriaméret határozza meg az egyes kötetek köteteinek méretét.

A tárolószolgáltató ajánlásai alapján a következő csatlakoztatási beállítások vannak konfigurálva az összes csatlakoztatott kötethez (a rendszerindító lun kivételével):

- nfs rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Ezek a csatlakoztatási pontok az /etc/fstab kapcsolóban vannak konfigurálva, ahogy az a következő ábrákon látható:

![fstab a csatlakoztatott kötetek hana nagy példány egység](./media/hana-installation/image1_fstab.PNG)

A df -h parancs kimenete egy S72m HANA nagy példány egységen így néz ki:

![fstab a csatlakoztatott kötetek hana nagy példány egység](./media/hana-installation/image2_df_output.PNG)


A tárolóvezérlő és a nagy példány bélyegzőinek csomópontjai szinkronizálódnak az NTP-kiszolgálókkal. Ha szinkronizálja az SAP HANA az Azure-ban (nagy példányok) egységek és az Azure-beli virtuális gépek egy NTP-kiszolgáló, nem kell jelentős időeltolódás az infrastruktúra és a számítási egységek az Azure-ban vagy a nagy példány bélyegzések között.

Az SAP HANA optimalizálásához az alatta használt tárolóra állítsa be a következő SAP HANA konfigurációs paramétereket:

- max_parallel_io_requests 128
- async_read_submit
- async_write_submit_active
- async_write_submit_blocks összes
 
Az SAP HANA 1.0-s verziók sps12-ig, ezek a paraméterek az SAP HANA adatbázis telepítése során állíthatók be, az [SAP #2267798 - Az SAP HANA adatbázis konfigurációja](https://launchpad.support.sap.com/#/notes/2267798)című részében leírtak szerint.

A paramétereket az SAP HANA adatbázis telepítése után is konfigurálhatja a hdbparam keretrendszer használatával. 

A HANA nagy példányokban használt tároló fájlméret-korlátozással rendelkezik. A méretkorlátozás fájlonként [16 TB.](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) Az EXT3 fájlrendszerek fájlméret-korlátozásaival ellentétben a HANA nem ismeri hallgatólagosan a HANA nagy példányok tároló által kikényszerített tárolási korlátozást. Ennek eredményeképpen a HANA nem hoz létre automatikusan új adatfájlt, amikor eléri a 16 TB-os fájlméretkorlátot. Ahana megpróbálja növelni a fájlt túl 16 TB, HANA hibákat jelent, és az index szerver összeomlik a végén.

> [!IMPORTANT]
> Annak megakadályozása érdekében, hogy a HANA megpróbálja növelni az adatfájlokat a HANA nagypéldány-tároló 16 TB-os fájlméret-korláton túl, be kell állítania a következő paramétereket az SAP HANA global.ini konfigurációs fájlban
> 
> - datavolume_striping=igaz
> - datavolume_striping_size_gb = 15000
> - Lásd még: [SAP-#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Vegye figyelembe az [SAP-#2631285](https://launchpad.support.sap.com/#/notes/2631285)


Az SAP HANA 2.0-s, a hdbparam keretrendszer elavult. Ennek eredményeképpen a paramétereket SQL-parancsokkal kell beállítani. További információ: [SAP note #2399079: A hdbparam megszüntetése a HANA 2-ben](https://launchpad.support.sap.com/#/notes/2399079).

Tekintse meg a [HLI által támogatott forgatókönyveket,](hana-supported-scenario.md) ha többet szeretne megtudni az architektúra tárolási elrendezéséről.


**További lépések**

- Lásd: [HANA telepítés HLI](hana-example-installation.md)










































 







 




