---
title: Telepítse az Azure (nagy példányok) SAP HANA SAP HANA |} Microsoft Docs
description: Hogyan SAP HANA telepíthet egy SAP HANA Azure (nagy példány).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ef85c098058c97e5ec6d758fcf1dab5b1a87786
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Hogyan kell telepíteni, és az SAP HANA (nagy példányok) konfigurálása az Azure-on

Az alábbiakban néhány fontos definíciókat az útmutató olvasása előtt. A [SAP HANA (nagy példányok) – áttekintés és Azure architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) HANA nagy példány egység, amelyek két különböző osztályú bevezetett azt:

- S72, S72m, S144, S144m, S192 és S192m, amely a "Type i. osztály" lesz az SKU.
- S384, S384m, S384xm, S576, S768 és S960, amely a "típusú class" SKU lesz az.

A osztály megadása lesz használható a HANA nagy példány dokumentációban végül különböző képességeket és HANA nagy példány termékváltozatok követelmények vonatkoznak.

Más definíciók gyakran használjuk a következők:
- **Nagy példány stamp:** hardver infrastruktúra verem egy SAP HANA TDI van hitelesített, és az Azure SAP HANA-példányok futtatásához dedikált.
- **SAP HANA Azure (nagy példány):** az SAP HANA TDI hardver, amely telepítve van a különböző Azure-régiók nagy példány bélyegzők hitelesített példánya HANA futtassa az Azure-ajánlatot hivatalos nevét. A kapcsolódó kifejezés **HANA nagy példány** rövid a SAP HANA Azure (nagy példányokat), és széles körben használt műszaki telepítési útmutatóban.


SAP HANA telepítése a feladata, és megkezdheti a tevékenység után egy új SAP HANA (nagy példányok) Azure-kiszolgálón a handoff számára. És az Azure VNet(s) és a HANA nagy példány egység(ek) közötti kapcsolat kapott létrehozása után. 

> [!Note]
> E házirend SAP az SAP HANA telepítése egy SAP HANA-telepítéshez hitelesített személy által hajtható végre. Egy személy, aki megfelelt a vizsgálathoz hitelesített SAP technológia társítani, a SAP HANA telepítési hitelesítő vizsgálatához, vagy egy SAP hitelesített rendszert integráló (SI).

Ellenőrizze ismét, különösen akkor, ha a tervezési HANA 2.0-s verzióját [SAP támogatási Megjegyzés #2235581 - SAP HANA: támogatott operációs rendszerek](https://launchpad.support.sap.com/#/notes/2235581/E) érdekében győződjön meg arról, hogy az operációs rendszer támogatja-e a a SAP HANA kiadási hogy telepítéséhez. Akkor vegye figyelembe, hogy több a korlátozás, mint az operációs rendszer támogatott HANA 1.0-e a támogatott operációs rendszer HANA 2.0. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Első lépések a HANA nagy példány egység(ek) fogadása után

**Első lépés** után a HANA nagy példány fogadására, és hogy meghatározott hozzáférési és a példányok, az, hogy az operációs rendszer, a példány regisztrálása az operációs rendszer szolgáltató. Ez a lépés beletartozik a SUSE Linux operációs rendszer regisztrálja az SUSE SMT, amelyekre szüksége van az Azure virtuális gépen telepített példánya. A HANA nagy példány egység csatlakozhat a SMT példány (lásd a jelen dokumentációban később). Vagy a RedHat OS regisztrálva kell lennie a Red Hat előfizetés-kezelő használatával csatlakozni kell. Lásd: is megjegyzések ezen [dokumentum](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ezt a lépést is tudjanak javítás az operációs rendszer szükséges. Egy feladat, hogy az ügyfelek felelőssége megtalálható. A SUSE, telepítéséhez és konfigurálásához SMT dokumentációjában található [Itt](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**A második lépésben** keressen új javítások és javításairól az adott operációsrendszer-kiadás/verzió. Ellenőrizze, hogy a javítási szintje nagy HANA-példány az aktuális állapotát. Az operációs rendszerhez – javítás/rendszereinek kiadásait és a változások a Microsoft telepítheti lemezképhez időzítési alapján, néhány esetben előfordulhat amikor a legújabb javítások nem kerülhet. Ezért is kötelező lépés után egy HANA nagy példány egység kell alkalmazni, és ellenőrizze, hogy a biztonsági, funkciókat, rendelkezésre állás és teljesítmény szükséges javítások kiadott viszont az adott Linux szállító által tovább tart.

**Harmadik lépés** tekintse meg a megfelelő SAP megjegyzéseket telepítéséről és beállításáról az adott operációs rendszer/verzió SAP HANA-hoz. Javaslatok, illetve módosítások miatt SAP megjegyzések vagy egyedi telepítési forgatókönyvek függő konfigurációk, a Microsoft nem mindig fogja tudni egy HANA nagy példány egység tökéletesen konfigurálva van. Ezért fontos kötelező az Ön ügyfélként, az SAP megjegyzések kapcsolódik SAP HANA pontos Linux kiadási olvasni. Is ellenőrzi a beállításait az operációs rendszer kiadási vagy verziója szükséges a, és a konfigurációs beállításokat alkalmazza, ha ezt még nem tette.

A specifikus, ellenőrizze a következő paramétereket, és végül állítva:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

RHEL 7.2 és SLES12 SP1-től kezdődően ezeket a paramétereket kell állítani a /etc/sysctl.d könyvtárban a konfigurációs fájlban. Például a konfigurációs fájlt, amely a 91-NetApp-HANA.conf nevét kell létrehozni. A régebbi SLES és RHEL kiadásai ezek a paraméterek beállítása in/etc/sysctl.conf kell lennie.

Minden RHEL kiadott és SLES12, kezdve a 
- sunrpc.tcp_slot_table_entries = 128

a paraméter in/etc/modprobe.d/sunrpc-local.conf kell beállítani. Ha a fájl nem létezik, akkor létre kell hozni a következő bejegyzés hozzáadásával: 
- options sunrpc tcp_max_slot_table_entries=128

**Negyedik lépés** annak ellenőrzése, a rendszer pontos ideje HANA nagy példány egységének. A példányok telepítik, amelyek megfelelnek az Azure-régió, a HANA nagy példány Stamp található helyét rendszer időzónát. Szabadon rendszeridő vagy a saját példányok időzónájának módosítása. Ennek során, és a bérlői további példányokat rendelés, készüljön, hogy szeretné-e az újonnan kézbesített példányok időzónájának igazítja. A Microsoft operations nem állít be a osztályt átadása után a rendszer időzóna betekintést rendelkezik. Ezért újonnan telepített példányok előfordulhat, hogy nem állítható be ugyanabban az időzónában megegyezik a értékre módosult. Ennek eredményeképpen feladata a ellenőrizze és szükség esetén az-példányokat, átadná időzónájának ügyfélként. 

**Ötödik** etc/hosts ellenőrzése. Mivel a paneleket az beszerzése átadná, többféle célra (lásd a következő szakaszt) hozzárendelt különböző IP-címek rendelkeznek. Ellenőrizze a etc/hosts fájlt. Azokban az esetekben, ahol egységek hozzáadja egy meglévő bérlői nem várt stb/állomásokat az újonnan telepített rendszerek karban kézbesített rendszerek korábbi IP-címmel. Így van, ellenőrizheti a megfelelő beállításokat ügyfélként, hogy egy újonnan telepített példányt interaktívan és oldja meg a korábban telepített egység a bérlő nevét. 

## <a name="networking"></a>Hálózat
Feltételezzük, hogy követték-e az Azure Vnetekhez tervezését, és ezeket a Vnetek csatlakozik a HANA nagy példányok, ezeket a dokumentumokat a javaslatokat:

- [SAP HANA (nagy példány) – áttekintés és az Azure-architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA (nagy példányok) infrastruktúra és az Azure-kapcsolat](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Nincsenek egyes részletek érdemes egyetlen egység a hálózatokkal kapcsolatos felhívja. Minden HANA nagy példány egység két vagy három két vagy három hálózati portok egység rendelt IP-címeket tartalmaz. Három IP-címek HANA kibővített konfigurációk és a HANA replikációs forgatókönyvek szerepelnek. Az egység a hálózati adapterhez hozzárendelt IP-címek egyikére a kiszolgáló IP-címkészlet, amelyek a rendszer kifogyott a [SAP HANA (nagy példány) – áttekintés és Azure architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Az egységek két IP-címek hozzárendelve a terjesztési hasonlóan kell kinéznie:

- eth0.xx kell egy hozzárendelt IP-címet, amely a kiszolgáló IP-készlet címtartománya, a Microsoftnak küldött kívül esik. Az IP-cím alkalmazzák az operációs rendszer/etc/hosts kezelésével.
- eth1.xx egy hozzárendelt IP-címet az NFS-kommunikációhoz használt kell rendelkeznie. Ezért ezeknél a címeknél tegye **nem** kell annak érdekében, hogy a bérlő belül példányt forgalom etc/hosts kezelhetők.

Két IP-címek hozzárendelve a panel-konfiguráció nem megfelelő HANA replikációs vagy HANA kibővített esetekben. Ha két IP-címtartományból csak, és meg szeretne ilyen konfiguráció alkalmazását, lépjen kapcsolatba az Azure szolgáltatásfelügyelet egy harmadik IP-cím beolvasása a harmadik SAP HANA VLAN van társítva. HANA nagy példány egységek három IP-címtartományból három hálózati portokra a következő használati szabályokat alkalmazza:

- eth0.xx kell egy hozzárendelt IP-címet, amely a kiszolgáló IP-készlet címtartománya, a Microsoftnak küldött kívül esik. Ezért az IP-címet kell nem használható az/etc/hosts az operációs rendszer karbantartásához.
- eth1.xx kell egy hozzárendelt IP-címet, amely az NFS-tárhelyre kommunikációra használja. Ezért az ilyen típusú cím nem fenn kell tartani a etc/hosts.
- eth2.xx kizárólag használandó stb/gazdagépeken a különböző példányok közötti kommunikáció fenntartásához. Ezek a címek is használhatók lesznek az IP-cím HANA használ a csomópontok közötti konfigurációs kibővített HANA konfigurációk fenntartásához az IP-címeket.



## <a name="storage"></a>Tárolás

A tárolási elrendezés SAP Hana Azure (nagy példányok) be van állítva az Azure szolgáltatásfelügyeleti ajánlott útmutatás, ahogy SAP keresztül SAP HANA [SAP HANA tárhellyel kapcsolatos követelmények](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) találhatók meg. A különböző köteteket a különböző nagy HANA-példányok termékváltozatok nyers mérete a kapott részletes ismertetését lásd: [SAP HANA (nagy példány) – áttekintés és Azure architektúra](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A tároló kötetek elnevezési szabályai a következő táblázatban felsorolt:

| Tárhely kihasználtsága | Csatlakoztatási neve | Kötet neve | 
| --- | --- | ---|
| HANA adatok | /hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA-napló | /hana/log/SID/mnt0000<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA napló biztonsági mentése | /Hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| Megosztott HANA | /Hana/Shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Ha SID a HANA példány Rendszerazonosító = 

És a bérlői egy belső számbavételi műveletek = a bérlő központi telepítésekor.

Ahogy látja, megosztott HANA és usr/sap megosztó ugyanazon a köteten. A csatlakozási pontok le az elnevezéseket tartalmazza a rendszer Azonosítót a HANA példányok, valamint a csatlakoztatási számát. Méretezett telepítések esetén csak van egy csatlakoztatási, például a mnt00001. Kibővített telepítésben mutatunk be annyi csatlakoztatások, mivel vannak olyan munkavégző és fő csomópontok. Kibővített környezet, adatok, napló a napló biztonsági mentési köteteken megosztott, az ütemezett feladatok minden csomópont a kibővített konfigurációban kapcsolódik. Konfigurációk több SAP példánya fut egy másik olyan kötetek készlete létrehozott, és a HAN nagy példány egység csatolva.

Olvassa el a dokumentum, és keresse meg a HANA nagy példány egység, akkor vegye figyelembe, hogy a egységek kapható helyett korlátozó lemezkötetet HANA/adatok és, hogy a kötet HANA/naplómásolat van. Miért azt méretű nagy HANA/adatok oka az, hogy a storage-pillanatfelvételekkel ügyfélként fel, az azonos lemezkötetet használ. Ez azt jelenti, hogy a további tárhely pillanatképek hajt végre, a több helyet a kiosztott köteteket a pillanatképek fel. A HANA/naplómásolat kötet nem re lesz, amelyre az adatbázis biztonsági mentése kötet. Azt a HANA tranzakciónapló biztonsági mentései használható biztonsági mentési kötet mérete. A jövőben a tároló verziói pillanatkép-self service, a Microsoft által megcélzott az adott köteten a gyakoribb pillanatképekkel rendelkezik. És az, hogy több gyakran használják replikációk a vész-helyreállítási hely ha felügyelni kívánt beállítás-a a HANA nagy példány infrastruktúra által biztosított vész helyreállítási funkciójának. A részleteket a [SAP HANA (nagy példányok) magas rendelkezésre állás és vészhelyreállítás Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Megadott tároló mellett további tárolási kapacitás 1 TB-os lépésekben vásárolható. A további tárhely HANA nagy példányokhoz adhatók új kötetként.

Bevezetés az Azure szolgáltatásfelügyelet az SAP HANA, során az ügyfél határozza meg a felhasználói azonosító (UID) és a csoport azonosító (CSOPORTAZONOSÍTÓ) a sidadm felhasználói és sapsys csoport (pl.: 1000,500) szükséges, hogy a SAP HANA-rendszer telepítésekor, ezek azonos értékeket fogja használni. Egy egységen több HANA példányok telepítése kívánt, több példányban kötetek (minden példány egy set) kap. Ennek eredményeképpen a központi telepítéskor akkor kell megadni:

- A különböző HANA-példányok (sidadm származtatott belőle) biztonsági azonosítója.
- A különböző HANA példányok a memória méretét. Mivel a példányok száma a memória méretét a minden egyes kötetet készletet határozza meg a kötetek méretét.

A következő csatlakoztatási beállításokat az összes csatlakoztatott kötetek esetében a tárolási szolgáltató javaslatok alapján (nem tartalmazza a rendszerindító LUN):

- NFS-RW lemezt használ, illesztők = 4, nehéz, timeo = 600, rsize = 1048576, wsize = 1048576, megszakítás, noatime, zárolása 0 0

A csatlakoztatási pontok vannak konfigurálva az/etc/fstab például a következő grafikus látható:

![a csatlakoztatott kötetek HANA nagy példány egység fstab](./media/hana-installation/image1_fstab.PNG)

A parancs df -h egy S72m HANA nagy példány egységen kimenete alábbihoz hasonlóan fog kinézni:

![a csatlakoztatott kötetek HANA nagy példány egység fstab](./media/hana-installation/image2_df_output.PNG)


A tárolóvezérlő nagy példány bélyegzők csomópontjának szinkronizálja NTP-kiszolgálóhoz. Az SAP HANA Azure (nagy példányok) egységeken és az Azure virtuális gépek szinkronizálása NTP-kiszolgálóval együtt nincs jelentős mennyiségű időt mivel azonban az infrastruktúra és az Azure vagy nagy példány stamp számítási egység között kell lennie.

Ahhoz, hogy optimalizálja a SAP HANA alatt használt tárhelyhez, a következő SAP HANA-konfigurációs paramétereket is be kell állítani:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- minden async_write_submit_blocks
 
A SAP HANA 1.0 verzióit egészen az SPS12, ezek a paraméterek állítható be a SAP HANA-adatbázis, a telepítés során a [SAP Megjegyzés #2267798 - konfigurációja az SAP HANA-adatbázisból](https://launchpad.support.sap.com/#/notes/2267798)

Is konfigurálhatja a paramétereit a SAP HANA-adatbázis telepítése után a hdbparam keretrendszer használatával. 

A SAP HANA 2.0 a hdbparam keretrendszer elavult. Ennek eredményeképpen a paramétert kell beállítani az SQL-parancsok használatával. További információkért lásd: [SAP Megjegyzés #2399079: HANA 2 hdbparam felszámolása](https://launchpad.support.sap.com/#/notes/2399079).


## <a name="operating-system"></a>Operációs rendszer

A kézbesített operációsrendszer-lemezképek lapozóterület 2 GB értékre van állítva a [SAP támogatási Megjegyzés #1999997 - gyakran ismételt kérdések: SAP HANA memória](https://launchpad.support.sap.com/#/notes/1999997/E). Bármely másik beállítást kívánt kell az ügyfél által állítható be.

[SUSE Linux Enterprise Server 12 SP1 SAP-alkalmazásokból](https://www.suse.com/products/sles-for-sap/hana) van az Azure (nagy példányok) SAP Hana telepített Linux terjesztését. Az adott terjesztési SAP-specifikus képességeket biztosít &quot;a kezdő verzióról&quot; (beleértve az SAP futó SLES hatékonyan előre beállított paramétereinek).

Lásd: [erőforrás könyvtár-/ tanulmányok](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) a SUSE webhelyen és [SAP SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) a az SAP közösségi hálózati Állapotváltozás-(beleértve a magas rendelkezésre állású, SAP műveletek vonatkozó biztonsági vezethet, és több telepítési) SLES az SAP HANA telepítésével kapcsolatos néhány hasznos erőforrások.

További és hasznos SAP SUSE kapcsolódó hivatkozások:

- [SAP HANA SUSE Linux webhelyen](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Az ajánlott eljárás az SAP: sorba helyezni replikációs – SAP NetWeaver a SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP – SAP SLES védelmet](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (beleértve a SLES 12 rendszert az SAP-alkalmazások).

SAP támogatási megjegyzések az SAP HANA végrehajtási SLES 12:

- [SAP támogatási Megjegyzés #1944799 – SAP HANA-irányelvek SLES operációs rendszer telepítéséhez](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [SAP támogatási Megjegyzés #2205917 – SAP HANA DB ajánlott az operációs rendszer beállításait a SLES 12 SAP-alkalmazásokból](https://launchpad.support.sap.com/#/notes/2205917/E).
- [SAP támogatási Megjegyzés #1984787 – SUSE Linux Enterprise Server 12: Telepítési jegyzetek](https://launchpad.support.sap.com/#/notes/1984787).
- [SAP támogatási Megjegyzés #171356 – SAP szoftverek Linux: általános információk](https://launchpad.support.sap.com/#/notes/1984787).
- [SAP támogatási Megjegyzés #1391070 – Linux UUID megoldások](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux SAP Hana](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) egy másik ajánlat SAP HANA futtatási nagy HANA-példányokon. RHEL 6.7 és 7.2 kiadásaiban érhetők el. Adjon Megjegyzés: a natív Azure virtuális gépek ahol csak RHEL 7.2 és újabb verziókban támogatott, HANA nagy példányok leváló támogatja RHEL 6.7 is. Azonban az RHEL 7.x kiadási használatát javasoljuk.

Red Hat kapcsolódó hivatkozások további és hasznos SAP:
- [SAP HANA a Red Hat Linux hely](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

SAP támogatási megjegyzések az SAP HANA végrehajtási Red Hat:

- [SAP támogatási Megjegyzés #2009879 - SAP HANA-irányelvek a Red Hat Enterprise Linux (RHEL) operációs rendszer](https://launchpad.support.sap.com/#/notes/2009879/E).
- [SAP támogatási #2292690 - SAP HANA DB Megjegyzés: az operációs rendszer ajánlott beállítások RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [SAP támogatási #2247020 - SAP HANA DB Megjegyzés: Ajánlott operációs rendszer beállításait RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [SAP támogatási Megjegyzés #1391070 – Linux UUID megoldások](https://launchpad.support.sap.com/#/notes/1391070).
- [SAP támogatási Megjegyzés #2228351 - Linux: SAP HANA-adatbázis szervizcsomag-verzió 11 változat 110 (vagy magasabb) RHEL 6 vagy SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [SAP támogatási #2397039 - gyakran ismételt kérdések Megjegyzés: Az RHEL SAP](https://launchpad.support.sap.com/#/notes/2397039).
- [SAP támogatási Megjegyzés #1496410 - Red Hat Enterprise Linux 6.x: telepítés és frissítés](https://launchpad.support.sap.com/#/notes/1496410).
- [SAP támogatási Megjegyzés #2002167 - Red Hat Enterprise Linux 7.x: telepítés és frissítés](https://launchpad.support.sap.com/#/notes/2002167).

## <a name="time-synchronization"></a>Időszinkronizálás

Az SAP NetWeaver architektúra épülő SAP-alkalmazásokból érzékenyek a különböző összetevőket az SAP rendszer alkotó időeltérést. SAP ABAP rövid memóriaképek ZDATE hiba címére\_nagy\_idő\_Különbözeti nem valószínűleg ismerős, mivel ezek rövid memóriaképek jelenik meg, amikor a rendszer pontos ideje különböző kiszolgálókon vagy virtuális gépek felé sodródik túl távolságra.

SAP Hana (nagy példányok) Azure-on, időszinkronizálás az Azure nem&#39;t a számítási egység nagy példány bélyegzők vonatkozik. A szinkronizálás esetén nem alkalmazható futó SAP alkalmazások natív Azure virtuális gépeken, mivel Azure biztosítja a rendszer&#39;s idő megfelelően szinkronizálva. Emiatt egy SAP által használható külön időpontot kell állítani a kiszolgáló alkalmazáskiszolgálók futó Azure virtuális gépek és az SAP HANA-adatbázis használati ideje HANA nagy példányok futó példányok. A tároló-infrastruktúra nagy példány bélyegzők az az idő szinkronizálva az NTP-kiszolgáló.

## <a name="setting-up-smt-server-for-suse-linux"></a>SUSE Linux SMT kiszolgáló beállítása
SAP HANA nagy példányok nem rendelkezik közvetlen internetkapcsolattal. Ezért nincs egy egyszerű folyamat ilyen egység regisztrálása az operációs rendszer szolgáltatót, és töltse le és telepíthetnek. SUSE Linux esetén egy megoldás lehet egy Azure virtuális gép SMT kiszolgáló beállítása. Mivel az Azure virtuális gép egy Azure virtuális hálózatot, csatlakoztatva a nagy HANA-példány a kiszolgálón kell lennie. Ilyen SMT kiszolgálóval a HANA nagy példány egység sikerült regisztrálni és létesít a javítókészletek letöltéséhez. 

SUSE Ez a témakör egy nagyobb útmutató a [előfizetés felügyeleti eszköz a SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Egy SMT kiszolgáló, amely megfelel a feladat nagy HANA-példány telepítésének előfeltétele, mint kell:

- Egy Azure virtuális hálózatot, amely a HANA nagy példány ER áramkör csatlakozik.
- Egy szervezet társított SUSE fiókot. Mivel a szervezet kell néhány érvényes SUSE előfizetéssel rendelkezik.

### <a name="installation-of-smt-server-on-azure-vm"></a>Az Azure virtuális gépen SMT kiszolgáló

Ebben a lépésben a SMT kiszolgáló egy Azure virtuális gép telepítése. Az első intézkedés, hogy jelentkezzen be a [SUSE ügyfél Center](https://scc.suse.com/)

Jelentkezett be, mert a szervezet Ugrás szervezeti hitelesítő-->. Ezen részében keresse meg a szükséges hitelesítő adatokat a SMT kiszolgáló beállításához.

A harmadik telepíteni kell az SUSE Linux virtuális Gépet az Azure vnet. A virtuális gép üzembe helyezéséhez igénybe vehet egy Azure SLES 12 SP2 gyűjtemény képe. A telepítési folyamat során ne adja meg egy DNS-nevet, és ne használjon statikus IP-címek létrehozása ezen a képernyőn látható módon

![a virtuálisgép-telepítéshez SMT kiszolgáló](./media/hana-installation/image3_vm_deployment.png)

A központilag telepített virtuális gép egy kisebb méretű, és kapott a belső IP-címet a 10.34.1.4 az Azure virtuális hálózat. A virtuális gép neve smtserver volt. A telepítés után a kapcsolat a HANA nagy példány egység(ek) be lett jelölve. Függ, hogyan vannak rendszerezve névfeloldás szükség lehet HANA nagy példány egység feloldási stb/gazdagépeken az Azure virtuális gép konfigurálása. Egy további lemez hozzáadása a virtuális gép, amelyet a javítások tárolásához használni szeretne. Lehet, hogy a rendszerindító lemezzel túl kicsi. A bemutatott esetben a lemez /srv/www/htdocs az alábbi képernyőfelvételen látható módon lett csatlakoztatva. A 100 GB-os lemezenként elegendőnek kell lennie.

![a virtuálisgép-telepítéshez SMT kiszolgáló](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Jelentkezzen be a HANA nagy példány egység(ek)/etc/hosts karbantartása, és ellenőrizze, hogy az Azure virtuális Gépen, amely a SMT kiszolgálón futtassa a hálózaton keresztül kellene érhető el.

Ezt az ellenőrzést sikeresen megtörtént, után kell bejelentkezni az Azure virtuális Gépen, amely a SMT kiszolgálón fusson. A putty segítségével jelentkezzen be a virtuális Gépet, ha szüksége ebben a sorozatban a parancsok végrehajtása a bash ablakban:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Ezek a parancsok végrehajtása után indítsa újra a bash aktiválásához a beállításokat. Majd indítsa el a YAST.

A YAST váltson Szoftverkarbantartást és smt megkereséséhez. Válassza ki a smt vált automatikusan yast2-smt alább látható módon

![A yast SMT](./media/hana-installation/image5_smt_in_yast.PNG)


Fogadja el a kijelölés a smtserver telepítésre. Telepítése után nyissa meg a SMT kiszolgálókonfiguráció, és adja meg a szervezeti hitelesítő adatokat a SUSE ügyfél központjából korábban kapott. Az Azure virtuális gép állomásnevet is adja meg az SMT kiszolgáló URL-címet. Ebben a bemutatóban volt https://smtserver a következő grafikus megjelenő.

![SMT kiszolgáló konfigurációja](./media/hana-installation/image6_configuration_of_smtserver1.png)

Következő lépésként kell annak megállapítására, hogy a kapcsolat a SUSE ügyfél Center működik. Ahogyan a képen látható, a következő grafikus bemutató esetben azt működött.

![Csatlakozás a teszt SUSE ügyfél Center](./media/hana-installation/image7_test_connect.png)

Egyszer a SMT telepítő elindul, meg kell adnia egy jelszót. Mivel ez egy új telepítés, adja meg, hogy a jelszó, ahogy az a következő grafikus kell.

![Az adatbázis jelszó megadása](./media/hana-installation/image8_define_db_passwd.PNG)

A következő lépést akkor, ha egy tanúsítvány jön létre. Nyissa meg a párbeszédpanelen látható a következő, és a lépés kell eljárnia.

![SMT server tanúsítvány létrehozása](./media/hana-installation/image9_certificate_creation.PNG)

Előfordulhat, hogy néhány percig, a konfiguráció végén a "Futtatás szinkronizálásának ellenőrzése" lépés töltött. A telepítés és a SMT kiszolgáló konfigurációját, a címtár-tárház a csatlakoztatási pont /srv/www/htdocs alatt keresse meg vagy és a tárház néhány alkönyvtárat. 

Indítsa újra a SMT kiszolgáló és a kapcsolódó szolgáltatások, a következő parancsokkal.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Töltse le a csomagok SMT-kiszolgálóra

Miután a szolgáltatás újraindul, válassza ki a megfelelő csomagokat SMT felügyeleti Yast használatával. A csomag kiválasztása függ a nagy HANA-példány kiszolgálójának operációsrendszer-lemezképet, és nem a SLES kiadás vagy a SMT server rendszerű virtuális gép verziója. A képernyőhöz példát alább láthatók.

![Csomagok kiválasztása](./media/hana-installation/image10_select_packages.PNG)

Miután befejezte a csomag választás, akkor indítsa el a kezdeti másolatot készít a select csomagok a SMT kiszolgálóra állít be. Ezt a példányt a rendszerhéj használatával a parancs smt-tükör alább látható módon elindul


![Töltse le a csomagok SMT kiszolgálóra](./media/hana-installation/image11_download_packages.PNG)

Fent látható, a csomagok kell beolvasni értékeit másolja a program a csatlakoztatási pont /srv/www/htdocs létrehozott címtárakat. A folyamat eltarthat egy ideig. Függ a kiválasztott csomagok száma, eltarthat egy óra vagy több.
Mivel ez a folyamat befejezése után kell a SMT ügyféltelepítés áthelyezése. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>HANA nagy példány egységek SMT-ügyfél beállítása

Az ügyfél ebben az esetben a HANA nagy példány egységek. A SMT kiszolgáló telepítését a parancsfájl clientSetup4SMT.sh átkerül az Azure virtuális Géphez. Másolat parancsfájl keresztül HANA nagy példány egységhez, amelyhez csatlakozni kíván SMT kiszolgálóját. Indítsa el a -h lehetőséggel a parancsfájlt, és adjon neki paraméterként a SMT kiszolgáló nevét. Az ebben a példában smtserver.

![SMT-ügyfél konfigurálása](./media/hana-installation/image12_configure_client.PNG)

Előfordulhat, hogy egy olyan forgatókönyvet, ahol a tanúsítványt az ügyfél a kiszolgálótól betöltése sikeres volt, de a regisztrálása sikertelen volt a lent látható módon.

![Ügyfél-regisztráció sikertelen lesz.](./media/hana-installation/image13_registration_failed.PNG)

Ha a regisztráció sikertelen volt, olvassa el ezt [SUSE támogatja a dokumentum](https://www.suse.com/de-de/support/kb/doc/?id=7006024) és végrehajtási hiba leírt lépéseket.

> [!IMPORTANT] 
> Kiszolgáló neve meg kell adnia a virtuális Gépet, a nagybetűk smtserver, anélkül, hogy a teljes tartománynév a neve. Csak a virtuális gép neve működik. 

A lépések végrehajtása után szeretné-e végre az alábbi parancsot a HANA nagy példány egység

```
SUSEConnect –cleanup
```

> [!Note] 
> A tesztelés le mindig kellett Várjon néhány percet, hogy a lépés után. Azonnali végrehajtás clientSetup4SMT.sh SUSE cikkben ismertetett kiigazító intézkedések után véget ért, hogy a tanúsítvány nem lesz érvényes még üzeneteket. Általában 5 – 10 percig várakozik, és clientSetup4SMT.sh végrehajtása befejeződött, a sikeres ügyfél-konfigurációval.

Ha a probléma, amelyek akkor javítsa ki a SUSE cikkben szereplő lépések alapján ütközött, egyszer indítsa újra a HANA nagy példány egységen clientSetup4SMT.sh szeretné. Most azt sikeresen be kell fejeződnie alább látható módon.

![Ügyfél-regisztráció sikeres](./media/hana-installation/image14_finish_client_config.PNG)

Az ebben a lépésben a HANA nagy példány egység az Azure virtuális Gépen telepített SMT-kiszolgálóval kapcsolódni az SMT ügyfél konfigurálva. Most már végre "zypper be" vagy "zypper a" operációsrendszer-javítások nagy HANA-példányokban kívánja telepíteni, vagy további csomagok telepítése. Megértése, hogy csak beszerezheti a SMT kiszolgálón előtt letöltött javítások.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Egy SAP HANA-telepítést HANA nagy példányok – példa
Ez a szakasz bemutatja, hogyan SAP HANA telepítsen egy HANA nagy példány egység. A start állapotban van a következőhöz hasonló:

- A megadott Microsoft SAP HANA nagy példányt telepítése az összes adatot.
- Az SAP HANA nagy példány a Microsofttól kapott.
- Létrehozott egy Azure virtuális hálózatot, amely a helyi hálózathoz csatlakozik.
- A ExpressRotue áramkör, kapcsolat HANA nagy az azonos Azure VNet-példányokat.
- Telepítette az Azure virtuális gép HANA nagy példányok jump dobozban használják.
- Végzett meg arról, hogy lehet csatlakoztatni az Ugrás mezőbe a HANA nagy példány egységhez, és ez fordítva is igaz.
- Ellenőrzi, hogy a szükséges csomagokat és a javítások vannak telepítve.
- Olvassa el a SAP megjegyzések és a dokumentációra vonatkozó HANA telepítése az operációs rendszer használ, és ellenőrizze, hogy a választott HANA kiadása támogatja-e az operációs rendszer kiadása.

A letöltés a HANA telepítési csomagot kell a jump mezőbe, a Windows operációs rendszer, a csomagok HANA nagy példány egységhez példányát és a telepítő feladatütemezési ebben az esetben futó virtuális gép a következő feladatütemezések megjelenő.

### <a name="download-of-the-sap-hana-installation-bits"></a>Töltse le az SAP HANA-telepítés bitek
Mivel a nagy példány HANA egység nem rendelkezik közvetlen internetkapcsolattal, akkor nem közvetlenül tölthető le telepítőcsomagok SAP HANA nagy példány VM. A hiányzó közvetlen internetkapcsolattal kapcsolatos, a jump mezőben van szükség. Az Ugrás mezőben VM töltse le a csomagokat.

Töltse le a HANA telepítőcsomagok, egy SAP-felhasználó vagy más felhasználó, amely lehetővé teszi az SAP piactér eléréséhez szükséges. Ebben a sorozatban képernyők végrehajtania a bejelentkezés után:

Lépjen [SAP szolgáltatás piactér](https://support.sap.com/en/index.html) > kattintson a szoftver letöltése > telepítés és frissítés > betűrendes Index által > a H – SAP HANA Platform Edition > SAP HANA Platform Edition 2.0 > telepítési > a következő fájlok letöltése

![Töltse le a HANA telepítése](./media/hana-installation/image16_download_hana.PNG)

A bemutató esetben SAP HANA 2.0 telepítőcsomagok töltöttük le. Azure Ugrás a keret VM akkor bontsa ki a önkicsomagoló archívumokat a könyvtárba alább látható módon.

![Bontsa ki a HANA telepítése](./media/hana-installation/image17_extract_hana.PNG)

Mivel archívum kibontása, másolja a könyvtárat, hozta létre a kinyerési, abban az esetben 51052030, fent be egy olyan könyvtárba, létrehozott /hana/shared kötet HANA nagy példány egységhez.

> [!Important]
> Nem másolja a telepítőcsomagok a gyökér- vagy rendszerindító LUN óta terület korlátozva, és egyéb eljárásokkal kell használni.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>SAP HANA telepíthető a HANA nagy példány egység
SAP HANA telepítéséhez kell bejelentkeznie felhasználó legfelső szintű. Csak a legfelső szintű SAP HANA telepítéséhez megfelelő engedélyekkel rendelkezik.
Először is végre kell hajtani is, hogy engedélyek be/hana/megosztott keresztül másolja a könyvtárhoz. Például állítsa be kell a jogosultságokat

```
chmod –R 744 <Installation bits folder>
```

A grafikus telepítés SAP HANA telepíteni szeretné, ha a gtk2 csomag telepítve kell lennie a nagy HANA-példányokon. Ellenőrizze, hogy van-e telepítve a parancs

```
rpm –qa | grep gtk2
```

A további lépések azt is, amely tartalmazza az SAP HANA beállítása a grafikus felhasználói felülettel. Következő lépésként nyissa meg a telepítési könyvtárba, és keresse meg a HDB_LCM_LINUX_X86_64 alkönyvtárában. Indítás

```
./hdblcmgui 
```
kívül könyvtárhoz. Most már első végigvezeti képernyők sorozatát ahol meg kell adnia az adatokat a telepítéshez. Abban az esetben egy a SAP HANA-kiszolgáló és az SAP HANA-ügyfél összetevők telepíti azt. Ezért az érték a "SAP HANA-adatbázisból" alább látható módon

![Válassza ki a HANA telepítés](./media/hana-installation/image18_hana_selection.PNG)

A következő képernyőn akkor választania "Új rendszer telepítéséhez"

![Válassza ki az új HANA-telepítés](./media/hana-installation/image19_select_new.PNG)

Ez a lépés után kell kiválasztania, amely az SAP HANA-kiszolgálóhoz is telepíthető, továbbá számos további összetevők közötti.

![További HANA összetevők kiválasztása](./media/hana-installation/image20_select_components.PNG)

Ebben a dokumentációban céljából választottuk az SAP HANA-ügyfél és az SAP HANA Studio. Azt is telepíti a méretezett példánya. ezért a következő képernyőn, meg kell adnia "Egy állomásra rendszer" 

![Válassza ki a méretezett telepítése](./media/hana-installation/image21_single_host.PNG)

A következő képernyőn meg kell adnia néhány adat

![Adja meg az SAP HANA SID](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Mint HANA rendszer azonosítója (SID), meg kell adnia a SID AZONOSÍTÓVAL, Ön által megadott Microsoft, amikor a HANA nagy példány telepítési rendelt. A telepítés sikertelen lesz, ugyanazon a köteten a hozzáférési engedélyt problémák miatt eltérő SID AZONOSÍTÓVAL kiválasztása révén

Telepítésként használja a/hana/directory megosztott könyvtár. A következő lépésben meg kell adnia a helyek a HANA az adatfájlok és a HANA naplófájlok


![Adja meg a HANA naplófájl helye](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Kell meghatározni, adatok és naplófájlok már mellékelt a csatlakoztatási pontokat, amelyek tartalmazzák a biztonsági AZONOSÍTÓT, a képernyő kiválasztása előtt erre a képernyőre lehetőséget választotta, hogy a köteteket. A SID eltérés van a megadott találhatóval, ha a képernyő ez előtt, lépjen vissza, és állítsa be a biztonsági AZONOSÍTÓT az értékre, hogy rendelkezzen a csatlakoztatási pontokat.

A következő lépésben tekintse át az állomásnév, és végül javítható ki. 

![Tekintse át a gazdagép neve](./media/hana-installation/image24_review_host_name.PNG)

A következő lépésben szükség akkor megadott a Microsoftnak, amikor a HANA nagy példány telepítési rendelt adatainak beolvasása. 

![Adja meg a rendszer felhasználói sémabővítményét](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Meg kell adnia a felhasználói azonosító és a felhasználói csoport azonosítója, ha Ön Microsoft megadja azt a egység telepítési sorrendje. Ha nem ad nagyon ugyanazokat az azonosítókat, HANA nagy példány egységben SAP HANA telepítése sikertelen lesz.

A következő két képernyőn, amely jelenleg nem láthatók a jelen dokumentációban lévő meg kell adnia a jelszót a rendszer felhasználó SAP HANA-adatbázisból, és a sapadm felhasználó jelszavát, amellyel az SAP állomás ügynök, amely az SAP HANA-adatbázispéldány részeként telepíti.

Határozza meg a jelszót, miután egy visszaigazoló képernyő jelenik meg. Ellenőrizze az adatokat a listában, és a telepítés folytatásához. A folyamatban lévő képernyőn, amely dokumentálja a telepítési folyamatot, például az egyik alábbi

![Ellenőrizze a telepítési folyamat](./media/hana-installation/image27_show_progress.PNG)

Mivel a telepítés befejezése után kell-e például az alábbi kép

![Telepítés befejeződött](./media/hana-installation/image28_install_finished.PNG)

Ezen a ponton az SAP HANA-példány kell működik és fut, és kész használatra. Az SAP HANA Studio kapcsolódhatnak kell lennie. Győződjön meg arról is, hogy a legújabb javítások az SAP HANA keressen, és ezeket a javításokat.
























































 







 




