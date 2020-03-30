---
title: Az SAP HANA hálózati architektúrája az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Az SAP HANA azure-beli (nagy példányok) üzembe helyezésének hálózati architektúrája.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33684a6292d7e51c04f6bacc7c49ee5986dbec10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502396"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (nagy példányok) hálózati architektúrája

Az Azure hálózati szolgáltatások architektúrája az SAP-alkalmazások HANA nagy példányon történő sikeres telepítésének kulcsfontosságú eleme. Az SAP HANA az Azure-beli (nagy példányok) központi telepítések en általában egy nagyobb SAP-környezettel rendelkezik, több különböző MÉRETŰ adatbázissal, CPU-erőforrás-felhasználással és memóriahasználattal rendelkező SAP-megoldással. Valószínű, hogy nem minden informatikai rendszer található már az Azure-ban. Az SAP-környezet gyakran hibrid, valamint egy DBMS-pont és az SAP alkalmazási szempontból a NetWeaver, és az S/4HANA és az SAP HANA és más DBMS keverékével. Az Azure különböző szolgáltatásokat kínál, amelyek lehetővé teszik a különböző DBMS, NetWeaver és S/4HANA rendszerek futtatását az Azure-ban. Az Azure hálózati technológiát is kínál, hogy az Azure virtuális adatközpontnak tűnjön a helyszíni szoftvertelepítések számára

Kivéve, ha a teljes informatikai rendszerek üzemelteti az Azure-ban. Az Azure hálózati funkciói valamerre a helyszíni világ és az Azure-eszközök összekapcsolására szolgálnak, hogy az Azure az Ön virtuális adatközpontjának tűnjön. Az Azure hálózati funkciói a következők: 

- Az Azure virtuális hálózatok a helyszíni hálózati eszközökhöz kapcsolódó [ExpressRoute-kapcsolathoz](https://azure.microsoft.com/services/expressroute/) kapcsolódnak.
- A helyszíni Azure-ral összekötő ExpressRoute-kapcsolatminimális sávszélességének [legalább 1 Gb/s-os sávszélességgel kell rendelkeznie.](https://azure.microsoft.com/pricing/details/expressroute/) Ez a minimális sávszélesség megfelelő sávszélességet tesz lehetővé a helyszíni rendszerek és a virtuális gépeken futó rendszerek közötti adatátvitelhez. Emellett lehetővé teszi a megfelelő sávszélességet az Azure-rendszerekhelyszíni felhasználóktól való csatlakozáshoz.
- Az Azure-ban minden SAP-rendszer virtuális hálózatokban van beállítva, hogy kommunikáljanak egymással.
- A helyszíni üzemeltetett Active Directory és DNS az ExpressRoute-on keresztül a helyszínen bővül az Azure-ban, vagy az Azure-ban teljes mértékben futnak.

A HANA nagypéldányok Azure adatközponthálózati hálóba való integrálásának konkrét esetében az Azure ExpressRoute technológiát is használják


> [!NOTE] 
> Csak egy Azure-előfizetés csak egy bérlőhöz kapcsolható egy hana nagy példánybélyegzőjében egy adott Azure-régióban. Ezzel szemben egy hana nagy példány bélyegző bérlő csak egy Azure-előfizetéshez kapcsolható. Ez a követelmény konzisztens az Azure más számlázható objektumaival.

Ha az SAP HANA az Azure-ban (nagy példányok) több különböző Azure-régióban van telepítve, egy külön bérlő a HANA nagy példány bélyegzőjében. Futtathatja mindkettőt ugyanazon Azure-előfizetés alatt, feltéve, hogy ezek a példányok ugyanannak az SAP-környezetnek a részét képezik. 

> [!IMPORTANT] 
> Csak az Azure Resource Manager telepítési módszer támogatja az SAP HANA az Azure-ban (nagy példányok).

 

## <a name="additional-virtual-network-information"></a>További virtuális hálózati információk

Virtuális hálózat ExpressRoute-hoz való csatlakoztatásához létre kell hozni egy Azure ExpressRoute-átjárót. További információ: [About Expressroute gateways for ExpressRoute.](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Az Azure ExpressRoute-átjáró az ExpressRoute-mal az Azure-on kívüli infrastruktúrára vagy egy Nagy Azure-példány bélyegzőre szolgál. Az Azure ExpressRoute-átjárót legfeljebb négy különböző ExpressRoute-kapcsolathoz csatlakoztathatja, feltéve, hogy ezek a kapcsolatok a Microsoft vállalati peremhálózati útválasztóitól származnak. További információ: [SAP HANA (Large Instances) infrastructure and connectivity on Azure.](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

> [!NOTE] 
> Az ExpressRoute-átjáróval elérhető maximális átviteli sebesség 10 Gb/s egy ExpressRoute-kapcsolat használatával érhető el. A virtuális hálózatban és a helyszíni rendszerben (egyetlen másolási adatfolyamként) található virtuális gépek közötti fájlok másolása nem éri el a különböző átjáró-sk-ek teljes átviteli csatornáját. Az ExpressRoute-átjáró teljes sávszélességének kihasználásához használjon több adatfolyamot. Vagy különböző fájlokat kell másolnia egyetlen fájl párhuzamos adatfolyamaiba.


## <a name="networking-architecture-for-hana-large-instance"></a>A HANA nagypéldány hálózati architektúrája
A HANA nagy példány hálózati architektúrája négy különböző részre bontható:

- Helyszíni hálózati és ExpressRoute-kapcsolat az Azure-ral. Ez a rész az ügyfél tartománya, és az ExpressRoute-on keresztül kapcsolódik az Azure-hoz. Ezt az Expressroute-áramkört teljes mértékben ön, mint ügyfél fizeti ki. A sávszélességnek elég nagynak kell lennie ahhoz, hogy kezelje a helyszíni eszközök és az Azure-régió közötti hálózati forgalmat, amelyhez csatlakozik. Lásd a jobb alsó ábrán az alábbi ábrán.
- Az Azure hálózati szolgáltatások, a korábban tárgyalt, a virtuális hálózatok, amelyek ismét expressroute-átjárók hozzáadása. Ez a rész egy olyan terület, ahol meg kell találnia a megfelelő terveket a jelentkezési követelményeknek, a biztonsági és megfelelőségi követelményeknek. Hogy a HANA nagy példány egy másik szempont, hogy fontolja meg a virtuális hálózatok száma és az Azure átjáró sk-ek közül lehet választani. Lásd a jobb felső az ábrán.
- Hana large instance kapcsolata az ExpressRoute technológián keresztül az Azure-ba. Ezt a részt a Microsoft telepíti és kezeli. Mindössze annyit kell tennie, hogy néhány IP-címtartományok telepítése után az eszközök hana nagy példány a connect the ExpressRoute-kapcsolat a virtuális hálózatok. További információ: [SAP HANA (Large Instances) infrastructure and connectivity on Azure.](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Az Azure adatközponti hálózati háló és a HANA large instance egységek közötti kapcsolatért nincs további díj az Ön számára, mint ügyfél számára.
- Hálózatkezelés a HANA nagy példány bélyegző, amely többnyire átlátszó az Ön számára.

![Az SAP HANA-hoz csatlakoztatott virtuális hálózat az Azure-ban (nagy példányok) és a helyszíni](./media/hana-overview-architecture/image1-architecture.png)

Az a követelmény, hogy a helyszíni eszközök kell csatlakoznia az ExpressRoute-on keresztül az Azure-hoz nem változik, mert a HANA nagy példány használata. Az a követelmény, hogy egy vagy több virtuális hálózatok, amelyek futtatják a virtuális gépeket, amelyek az alkalmazásréteget, amely csatlakozik a HANA nagy példányegységekben üzemeltetett HANA-példányok, szintén nem változik. 

Az Azure-beli SAP-telepítések közötti különbségek a következők:

- Az ügyfél-bérlő HANA nagy példányegységei egy másik ExpressRoute-áramkörön keresztül kapcsolódnak a virtuális hálózatokhoz. A betöltési feltételek elkülönítéséhez a helyszíni virtuális hálózati ExpressRoute-áramkörök, valamint az Azure virtuális hálózatok és a HANA nagy példányok közötti áramkörök nem azonos útválasztók.
- Az SAP alkalmazásréteg és a HANA nagy példány közötti számítási feladatok profilja más jellegű, sok kis kéréssel és adatlöketekkel, például az SAP HANA-ból az alkalmazásrétegbe irányuló adatátvitellel és adathalmazokkal.
- Az SAP-alkalmazásarchitektúra érzékenyebb a hálózati késésre, mint a tipikus forgatókönyvek, ahol a helyszíni és az Azure közötti adatcsere.
- Az Azure ExpressRoute-átjáró legalább két ExpressRoute-kapcsolattal rendelkezik. Egy kapcsolat, amely a helyszíni és a HANA nagy példányok csatlakoztatott áramkör. Ez csak további két további, az ExpressRoute-átjárón keresztül csatlakozó további két kapcsolatkapcsolati kapcsolatot hagy. Ez a korlátozás független az ExpressRoute gyorselérési út használatától. Az összes csatlakoztatott áramkör osztozik az ExpressRoute-átjáró bejövő adatainak maximális sávszélességén.

A HANA nagy példány bélyegzőinek 3. Az Azure-régiótól függően a mért értékek meghaladhatják az SAP Note #1100926 [– GYAKORI KÉRDÉSEK: Hálózati teljesítmény](https://launchpad.support.sap.com/#/notes/1100926/E)értéke imázs ának értéke az átlagosnál alacsonyabbként osztályozott 0,7 ms-os oda-vissza késést. Az Azure-régiótól és az Azure virtuális gép és a HANA nagypéldány-egység közötti hálózati oda-vissza késés méréséhez használt eszköztől függően a mért késés legfeljebb 2 ezredmásodperc lehet. Mindazonáltal az ügyfelek az SAP HANA-alapú éles SAP-alkalmazásokat sikeresen telepítik az SAP HANA nagy példányon. Győződjön meg arról, hogy az azure HANA nagy példányban alaposan teszteli az üzleti folyamatokat. Egy új funkció, az ExpressRoute gyors elérési út, képes csökkenteni a hálózati késés között HANA nagy példányok és az alkalmazásréteg virtuális gépek az Azure-ban jelentősen (lásd alább). 

A HANA nagy példánybélyegzőinek 4- es verziójával a HANA nagypéldány-bélyegző közelében üzembe helyezett Azure-beli virtuális gépek közötti hálózati késés az [SAP-#1100926 – GYAKORI KÉRDÉSEK: Hálózati teljesítmény,](https://launchpad.support.sap.com/#/notes/1100926/E) ha az Azure ExpressRoute gyorselérési út konfigurálva van (lásd alább). Az Azure-beli virtuális gépek telepítéséhez a 4-es verzió HANA nagypéldányegységeinek közvetlen közelében kell használnia az [Azure közelségelhelyezési csoportjait.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) Az Azure Közelségelhelyezési csoportok ismertetik, hogyan használható a közelségi elhelyezési csoportok az SAP-alkalmazásréteg megkeresésére ugyanabban az Azure-adatközpontban, mint a 4-es verzióban üzemeltetett HANA nagy példányegységek az [Azure Közelségelhelyezési csoportok ban, hogy optimális hálózati késést biztosíthasson az SAP-alkalmazásokkal.](sap-proximity-placement-scenarios.md)

A virtuális gépek és a HANA nagypéldány közötti determinisztikus hálózati késés biztosításához elengedhetetlen az ExpressRoute átjáró termékváltozatának kiválasztása. A helyszíni és a virtuális gépek közötti forgalmi mintákkal ellentétben a virtuális gépek és a HANA nagy példány közötti forgalmi minta kis, de nagy mennyiségű kérést és adatkötetet hozhat ki. Az ilyen sorozatok jól kezeléséhez javasoljuk az UltraPerformance átjáró termékváltozatának használatát. A HANA nagypéldány-sku-k II típusú osztálya esetén az UltraPerformance átjáró termékváltozatának ExpressRoute-átjáróként való használata kötelező.

> [!IMPORTANT] 
> Tekintettel az SAP-alkalmazás és az adatbázis-rétegek közötti teljes hálózati forgalomra, csak a HighPerformance vagy UltraPerformance átjáró sk-ek a virtuális hálózatok hoz az Azure-beli SAP HANA (nagy példányok) csatlakozásához. Hana nagypéldány type II termékváltozatok esetében csak az UltraPerformance átjáró termékváltozata támogatott ExpressRoute-átjáróként. Kivételek vonatkoznak az ExpressRoute gyorselérési út használatára (lásd alább)

### <a name="expressroute-fast-path"></a>ExpressRoute gyorselérési út
A késés csökkentése érdekében az ExpressRoute gyorselérési út 2019 májusában lett bevezetésre kerül, és 2019 májusában elérhetővé lett adva a HANA nagy példányok azure-beli virtuális hálózatokhoz való, az SAP-alkalmazás virtuális gépeit tároló virtuális hálózatokhoz való konkrét kapcsolatához. A fő különbség a megoldás eddig bevezetett, hogy a virtuális gépek és a HANA nagy példányok közötti adatfolyamok már nem az ExpressRoute-átjárón keresztül. Ehelyett az Azure virtuális hálózat alhálózatában(i)hoz rendelt virtuális gépek közvetlenül kommunikálnak a dedikált vállalati peremhálózati útválasztóval. 

> [!IMPORTANT] 
> Az ExpressRoute gyorselérési út funkció megköveteli, hogy az SAP-alkalmazás virtuális gépeit futtató alhálózatok ugyanabban az Azure-beli virtuális hálózatban vannak, amely a HANA nagy példányaihoz csatlakozott. Az Azure virtuális hálózatokban található virtuális virtuális gépek, amelyek az Azure virtuális hálózat közvetlenül csatlakozik a HANA nagy példány egységek nem részesülnek ExpressRoute gyors elérési út. Ennek eredményeként a tipikus hub és küllős virtuális hálózati tervek, ahol az ExpressRoute-áramkörök csatlakoznak egy hub virtuális hálózat és a virtuális hálózatok, amelyek az SAP alkalmazás réteg (küllők) egyre társviszonyban, az optimalizálás expressroute fast Az elérési út nem fog működni. Az addtion, ExpressRoute gyors elérési út nem támogatja a felhasználó által definiált útválasztási szabályok (UDR) ma. További információ: [ExpressRoute virtuális hálózati átjáró és FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Az ExpressRoute gyorselérési út konfigurálásáról további információt a [Virtuális hálózat csatlakoztatása hana nagy példányokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)című dokumentumban talál.    

> [!NOTE]
> Az ExpressPerformance ExpressRoute-átjáró működéséhez UltraPerformance ExpressRoute-átjáró szükséges


## <a name="single-sap-system"></a>Egyetlen SAP-rendszer

A korábban megjelenített helyszíni infrastruktúra az ExpressRoute-on keresztül csatlakozik az Azure-hoz. Az ExpressRoute-áramkör egy Microsoft vállalati peremhálózati útválasztóhoz (MSEE) csatlakozik. További információ: [ExpressRoute technical overview](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az útvonal létrehozása után csatlakozik az Azure gerinchálózata.

> [!NOTE] 
> Sap-tájak futtatásához az Azure-ban, csatlakozzon a vállalati peremhálózati útválasztó legközelebb az Azure-régióban az SAP-környezetben. A HANA nagypéldány-bélyegzők dedikált vállalati peremhálózati útválasztó-eszközökön keresztül kapcsolódnak a virtuális gépek közötti hálózati késés minimalizálásához az Azure IaaS és a HANA Large Instance bélyegzőkben.

Az ExpressRoute-átjáró az SAP-alkalmazáspéldányokat üzemeltető virtuális gépek hez egy expressroute-kapcsolathoz csatlakozik, amely a helyszíni kapcsolathoz csatlakozik. Ugyanaz a virtuális hálózat csatlakozik egy külön vállalati peremhálózati útválasztóhoz, amely a nagypéldány-bélyegzőkhöz való csatlakozáshoz van elválasztva. Az ExpressRoute gyors elérési útjának használatával a HANA nagy példányokból az SAP-alkalmazásréteg virtuális gépeire irányuló adatfolyam már nem kerül átirányítva az ExpressRoute-átjárón, és ezzel csökkenti a hálózati oda-vissza késést.

Ez a rendszer egy egyszerű példa egyetlen SAP-rendszerre. Az SAP alkalmazásréteg az Azure-ban található. Az SAP HANA-adatbázis sap HANA az Azure-ban (nagy példányok) fut. A feltételezés az, hogy az ExpressRoute átjáró sávszélessége 2 Gb/s vagy 10 Gbit/s átviteli sebesség nem jelent szűk keresztmetszetet.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Több SAP-rendszer vagy nagy SAP-rendszer

Ha több SAP-rendszer vagy nagy SAP-rendszerek vannak telepítve az SAP HANA-hoz való csatlakozáshoz az Azure-ban (nagy példányok), az ExpressRoute-átjáró átviteli hatása szűk keresztmetszetté válhat. Vagy szeretné elkülöníteni az éles és nem éles rendszereket a különböző Azure virtuális hálózatokban. Ebben az esetben ossza fel az alkalmazásrétegeket több virtuális hálózatra. Emellett létrehozhat egy speciális virtuális hálózatot is, amely a HANA nagy példányhoz csatlakozik az olyan esetekben, mint például:

- Biztonsági mentések végrehajtása közvetlenül a HANA-példányok hana nagy példányegy virtuális gép az Azure-ban, amely nfs-megosztásokat üzemeltet.
- Nagy biztonsági mentések vagy más fájlok másolása a HANA nagy példányegységeiből az Azure-ban felügyelt lemezterületre.

Használjon külön virtuális hálózatot olyan virtuális gépek üzemeltetéséhez, amelyek a HANA nagy példányok és az Azure közötti tömeges adatátvitelhez szolgáló tárolást kezelik. Ez a megállapodás elkerüli a nagy fájl vagy adatátvitel hana nagy példány ból az Azure-ba az ExpressRoute-átjáró, amely az SAP-alkalmazásréteget kiszolgáló virtuális gépeket szolgál ki. 

Méretezhetőbb hálózati architektúráért:

- Használja ki a több virtuális hálózatok egyetlen, nagyobb SAP-alkalmazásréteg.
- Egyetlen külön virtuális hálózat üzembe helyezése minden üzembe helyezett SAP-rendszerhez, összehasonlítva ezekkel az SAP-rendszerekkel külön alhálózatokban, ugyanazon virtuális hálózaton.

  Az SAP HANA skálázhatóbb hálózati architektúrája az Azure-ban (nagy példányok):

![SAP-alkalmazásréteg telepítése több virtuális hálózaton keresztül](./media/hana-overview-architecture/image4-networking-architecture.png)

A szabályoktól és korlátozásoktól függően, a különböző SAP-rendszerek virtuális gépeit üzemeltető különböző virtuális hálózatok között szeretné alkalmazni, ezért a virtuális hálózatokat társviszonyban kell tartania. A virtuális hálózati társviszony-létesítésről a [Virtuális hálózati társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)című témakörben talál további információt.


## <a name="routing-in-azure"></a>Útválasztási útválasztás az Azure-ban

Alapértelmezés szerint a telepítés három hálózati útválasztási szempontok fontosak az SAP HANA az Azure-ban (nagy példányok):

* SAP HANA az Azure-ban (nagy példányok) csak az Azure virtuális gépeken keresztül érhető el, és a dedikált ExpressRoute-kapcsolat, nem közvetlenül a helyszíni. A helyszíni hozzáférés a HANA nagy példány egységek, a Microsoft által az Ön számára szállított, nem lehetséges azonnal. A tranzitív útválasztási korlátozások az SAP HANA nagy példányhoz használt aktuális Azure hálózati architektúrának köszönhetők. Egyes felügyeleti ügyfelek és a közvetlen hozzáférést igénylő alkalmazások, például a helyszíni en-sap solution manager nem tud csatlakozni az SAP HANA-adatbázishoz. Kivételek esetén ellenőrizze a "Közvetlen útválasztás hana nagy példányok" című szakaszt.

* Ha a HANA nagy példány egységek üzembe helyezett két különböző Azure-régiókban a vész-helyreállítás, ugyanazokat az átmeneti útválasztási korlátozásokat a múltban alkalmazott. Más szóval egy HANA nagy példány egység IP-címét az egyik régióban (például us west) nem irányították egy hana nagy példány egység egy másik régióban (például az USA keleti). Ez a korlátozás független volt az Azure-hálózati társviszony-létesítés régiók közötti használatától, vagy az ExpressRoute-áramkörök, amelyek a HANA nagy példányegységeket virtuális hálózatokhoz csatlakoztatják, független volt attól, hogy az Azure-hálózati társviszony-létesítést használja. A grafikus ábrázolásról a "HANA nagy példányegységek használata több régióban" című szakaszban található ábrán látható. Ez a korlátozás, amely az üzembe helyezett architektúrával jött, megtiltotta a HANA rendszerreplikáció azonnali használatát vész-helyreállítási funkcióként. A legutóbbi módosítások, keresse meg a "Hana nagy példány egységek használata több régióban" szakaszban. 

* SAP HANA az Azure-ban (nagy példányok) egységek egy hozzárendelt IP-címet a kiszolgáló IP-készlet címtartományából, amely a HANA nagy példány központi telepítésének kérésekor küldött. További információ: [SAP HANA (Large Instances) infrastructure and connectivity on Azure.](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Ez az IP-cím az Azure-előfizetések és az Azure virtuális hálózatok hana nagy példányok összekötő áramkörön keresztül érhető el. Az adott kiszolgáló IP-készletcímtartományából hozzárendelt IP-cím közvetlenül a hardveregységhez van rendelve. Már *nincs* hozzárendelve a NAT-on keresztül, ahogy az a megoldás első telepítéseinél is történt. 

### <a name="direct-routing-to-hana-large-instances"></a>Közvetlen útválasztás hana nagy példányokba

Alapértelmezés szerint a tranzitív útválasztás a következő esetekben nem működik:

* Hana nagy példány egységek és egy helyszíni telepítés között.

* Hana nagy példány útválasztás, amely két különböző régióban üzembe helyezett között.

Az ilyen esetekben háromféleképpen engedélyezheti a tranzitív útválasztást:

- Fordított proxy az adatok továbbításához, és innen. Például az F5 BIG-IP, NGINX az Azure virtuális hálózatában telepített Traffic Manager, amely csatlakozik a HANA nagy példányokhoz és a helyszíni virtuális tűzfal/forgalom útválasztási megoldásként.
- [IPTables szabályok](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) használata egy Linux virtuális gép, amely lehetővé teszi a helyszíni helyek és a HANA nagy példány egységek közötti útválasztás, vagy a HANA nagy példány egységek különböző régiókban közötti útválasztás engedélyezéséhez. Az IPTables-et futtató virtuális gépet telepíteni kell az Azure virtuális hálózatában, amely csatlakozik a HANA nagy példányokhoz és a helyszíni kapcsolatokhoz. A virtuális gép kell méretezni ennek megfelelően, így a virtuális gép hálózati átviteli igénye elegendő a várható hálózati forgalomhoz. A virtuális gép hálózati sávszélességével kapcsolatos részletekért tekintse meg a cikk [Linux-méretei virtuális gépek az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Az Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) egy másik megoldás a helyszíni és a HANA Large példányegységek közötti közvetlen forgalom engedélyezésére. 

Ezeknek a megoldásoknak az összes forgalma egy Azure virtuális hálózaton keresztül lenne irányítva, és mint ilyen, a forgalmat a soft appliances által használt vagy az Azure Network Security Groups is korlátozhatja, így bizonyos IP-címek vagy IP-címek helyszíni lehet blokkolni, vagy explicit módon engedélyezett a HANA nagy példányok eléréséhez. 

> [!NOTE]  
> Ne feledje, hogy a külső hálózati készülékeket vagy IP-táblázatokat érintő egyéni megoldások megvalósítását és támogatását a Microsoft nem biztosítja. A támogatást a felhasznált alkatrész szállítójának vagy az integrátornak kell biztosítania. 

#### <a name="express-route-global-reach"></a>Expressz útvonal globális elérése
A Microsoft bevezette az [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)nevű új funkciót. A globális elérés hana nagy példányok hoz használható két esetben:

- Közvetlen hozzáférés engedélyezése a helyszíni létesítményekből a hana nagy példány egységek telepített különböző régiókban
- Közvetlen kommunikáció engedélyezése a hana nagy példány egységek telepített különböző régiókban


##### <a name="direct-access-from-on-premises"></a>Közvetlen hozzáférés a helyszíni
Azokban az Azure-régiókban, ahol a Globális elérés érhető el érhető el, kérheti a Globális elérés funkció engedélyezését az ExpressRoute-kapcsolaton, amely összeköti a helyszíni hálózatot az Azure virtuális hálózatával, amely a HANA nagypéldányegységeihez is csatlakozik. Az ExpressRoute-kapcsolat helyszíni oldalára bizonyos költségvonzatok vonatkoznak. Az árak, ellenőrizze az árakat a [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). Nincsenek további költségek a HANA nagy példány egységét az Azure-hoz csatlakoztató áramkörhöz kapcsolódóan. 

> [!IMPORTANT]  
> Abban az esetben, ha a Globális elérés t használja a HANA nagypéldányegységek és a helyszíni eszközök közötti közvetlen hozzáférés engedélyezéséhez, a hálózati adatok és a vezérlési folyamat nem az Azure virtuális hálózatokon keresztül, hanem közvetlenül a Microsoft vállalati csereútválasztók között kerül **átirányítva.** Ennek eredményeképpen az NSG- vagy ASG-szabályok, illetve az Azure virtuális hálózatban üzembe helyezett bármilyen típusú tűzfal, NVA vagy proxy nem kerül elő. **Ha az ExpressRoute globális elérési használatával közvetlen hozzáférést biztosít a helyszíni HANA-ról a HANA Nagy példányegységek korlátozásai és a HANA nagy példányegységek elérésére vonatkozó engedélyek a helyszíni tűzfalakban kell definiálni** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Hana nagy példányok csatlakoztatása különböző Azure-régiókban
Ugyanúgy, ahogy az ExpressRoute Globális elérési út használható a helyszíni hana nagy példány egységek csatlakoztatásához, két HANA nagy példány bérlőinek csatlakoztatására használható, amelyek két különböző régióban vannak üzembe helyezve. Az elkülönítés az ExpressRoute-áramkörök, amelyek a HANA nagy példány bérlői az Azure-hoz való csatlakozáshoz mindkét régióban. Nincsenek további díjak két HANA nagy példány bérlők, amelyek két különböző régiókban üzembe helyezett két további díjakat. 

> [!IMPORTANT]  
> Az adatfolyam és a vezérlőfolyamat a hálózati forgalom a különböző HANA Nagy példány bérlők nem lesz az azure-hálózatokon keresztül. Ennek eredményeképpen nem használhatja az Azure-funkciókat vagy nva-kat a két HANA nagy példány bérlőközötti kommunikációs korlátozások érvényesítéséhez. 

Az ExpressRoute Global Reach engedélyezésével kapcsolatos további részletekért olvassa el a [virtuális hálózat csatlakoztatása nagy példányokhoz című dokumentumot.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA nagy példány internetkapcsolata
A HANA nagy példány *nem* rendelkezik közvetlen internetkapcsolattal. Például ez a korlátozás korlátozhatja az operációs rendszer lemezképének közvetlen regisztrálását az operációs rendszer szállítójával. Előfordulhat, hogy a helyi SUSE Enterprise Server Subscription Management Tool kiszolgálóval vagy a Red Hat Enterprise Linux Subscription Manager rel kell dolgoznia.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Adattitkosítás virtuális gépek és HANA nagy példány között
A HANA nagy példány és a virtuális gépek között továbbított adatok nincsenek titkosítva. Azonban kizárólag a HANA DBMS oldal és a JDBC/ODBC-alapú alkalmazások közötti adatcsere esetén engedélyezheti a forgalom titkosítását. További információt az [SAP dokumentációjában](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)talál.

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Hana nagy példányegységek használata több régióban

A vész-helyreállítási set sets megvalósításához több Azure-régióban shana nagypéldány-egységeket kell létrehoznia. Még az Azure [Globális virtuális társviszony-létesítés] használatával is, a tranzitív útválasztás alapértelmezés szerint nem működik a HANA nagy példány bérlői között két különböző régióban. A Globális elérés azonban megnyitja a kommunikációs útvonalat a HANA nagy példány egységek két különböző régióban kiépített egységek között. Az ExpressRoute globális elérési út ezen használati forgatókönyve a következőket teszi lehetővé:

 - HANA rendszerreplikáció további proxyk vagy tűzfalak nélkül
 - Biztonsági másolatok másolása a HANA nagy példányegységei között két különböző régióban a rendszermásolatok vagy rendszerfrissítések végrehajtásához


![Az Azure Large Instance bélyegzőihez csatlakoztatott virtuális hálózat különböző Azure-régiókban](./media/hana-overview-architecture/image8-multiple-regions.png)

Az ábra bemutatja, hogy a különböző virtuális hálózatok mindkét régióban csatlakozik két különböző ExpressRoute-áramkörök, amelyek az SAP HANA-hoz való csatlakozáshoz az Azure-ban (nagy példányok) mindkét Azure-régióban (szürke vonalak). Ennek a két keresztkapcsolatnak az az oka, hogy mindkét oldalon meg kell védeni a kkv-k kimaradását. A két Azure-régióban lévő két virtuális hálózat közötti kommunikációs folyamatot a két különböző régióban (kék pontozott vonal) lévő két virtuális hálózat [globális társviszony-létesítésén](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) keresztül kell kezelni. A vastag piros vonal az ExpressRoute globális elérési kapcsolatot írja le, amely lehetővé teszi, hogy a bérlők HANA nagy példányegységei két különböző régióban kommunikáljanak egymással. 

> [!IMPORTANT] 
> Ha több ExpressRoute-áramkört használt, az AS-elérési út elő-, valamint a Helyi preferencia BGP-beállításait kell használni a forgalom megfelelő útválasztásának biztosításához.

**További lépések**
- SAP [HANA (nagy példányok) tárolási architektúrájának ajánlása](hana-storage-architecture.md)
