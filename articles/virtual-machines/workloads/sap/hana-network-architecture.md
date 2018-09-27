---
title: A hálózati architektúra az Azure-ban (nagyméretű példányok) SAP Hana |} A Microsoft Docs
description: A hálózati architektúra az SAP HANA az Azure-ban (nagyméretű példányok) üzembe helyezése.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1262ed841fe8f6f9c2d5339d79abf06c1ab15a25
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392873"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (nagyméretű példányok) hálózati architektúra

Az architektúra az Azure hálózati szolgáltatások egyik legfontosabb összetevőjeként a sikeres üzembe helyezéséhez SAP-alkalmazások nagyméretű HANA-példány. Általában az SAP HANA Azure-ban (nagyméretű példányok) üzemelő példányok esetében egy nagyobb SAP-rendszeren, a számos különböző SAP-megoldások a különböző méretű készletben az adatbázisok, a Processzor-erőforrás-használat és a memóriahasználat rendelkezik. Valószínű, hogy nem minden ezeket SAP-rendszereinket SAP HANA alapulnak. Az SAP-rendszeren, akkor valószínűleg használó hibrid:

- Üzembe helyezett SAP rendszerek helyszíni. A méretek miatt ezek a rendszerek jelenleg nem futhat az Azure-ban. Ilyen például, egy éles SAP ERP-rendszer, amely (mint az adatbázis) SQL-kiszolgálón fut, és több CPU és memória erőforrást, mint a virtuális gépeket biztosíthatnak igényel.
- Az SAP az SAP HANA-alapú rendszerek a helyszíni rendszerbe.
- Üzembe helyezett SAP-rendszerek virtuális gépeket. Ezek a rendszerek fejlesztés, tesztelés, védőfal, vagy bármely, az SAP NetWeaver-alapú alkalmazások sikeres központi telepítése az Azure-ban (a VM-EK), az erőforrás-használat és a memória igény szerinti példányok éles környezetben. Ezek a rendszerek is alapulhat SQL Server-adatbázisok. További információkért lásd: [SAP támogatási Megjegyzés #1928533 – SAP alkalmazások az Azure-on: támogatott termékek és Azure-beli Virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533/E). És az SAP HANA-adatbázisok alapjául ezekben a rendszerekben is használható. További információkért lásd: [SAP HANA-tanúsítvánnyal rendelkező IaaS-platformon](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Üzembe helyezett SAP-alkalmazáskiszolgálókhoz az Azure-ban (a VM-EK), az SAP HANA az Azure-ban (nagyméretű példányok) az Azure-beli nagyméretű példány stampek.

Hibrid SAP-megoldás a négy vagy több különböző központi telepítési forgatókönyv jellemző. Nincsenek is befejeződött az Azure-ban futó SAP-környezetünk számos ügyfél eseteit. Virtuális gépek egyre nagyobb teljesítményű, helyezze át saját SAP-megoldások Azure-beli ügyfelek száma növekszik.

Azure-hálózat az Azure-ban üzembe helyezett SAP-rendszereinket kontextusában nem bonyolult. A következő alapelveket alapul:

- Azure virtuális hálózatok kapcsolódnia kell az ExpressRoute-kapcsolatcsoport, amely egy helyszíni hálózathoz csatlakozik.
- ExpressRoute-kapcsolatcsoport, amely kapcsolódik a helyszíni Azure általában 1 GB/s vagy nagyobb sávszélességet kell rendelkeznie. A minimális sávszélesség elegendő sávszélesség a helyszíni és a virtuális gépeken futó rendszerek közötti adatátvitelt tesz lehetővé. Lehetővé teszi elegendő sávszélesség-kapcsolat Azure rendszerek, a helyszíni felhasználók.
- Minden SAP-rendszereit az Azure-ban kell állítani a virtuális hálózatok egymással kommunikálni.
- Az Active Directory és DNS-a helyileg üzemeltetett helyszíni kiterjeszthetők az Azure expressroute-on keresztül.


> [!NOTE] 
> Elszámolási szempontból csak egy Azure-előfizetéssel is össze a egy nagyméretű szolgáltatáspéldányban egy adott Azure-régióban kizárólag egy bérlővel. Ezzel szemben egyetlen nagyméretű példány stamp bérlő kapcsolhatók csak egy Azure-előfizetéshez. Ez a követelmény nem konzisztensek legyenek más számlázható objektumok az Azure-ban.

SAP HANA az Azure-ban (nagyméretű példányok) több különböző Azure-régióban van üzembe helyezve, ha külön bérlőt a nagyméretű szolgáltatáspéldányban üzemel. Mindaddig, amíg ezek a példányok ugyanazt az SAP-rendszeren részét képező alatt az Azure-előfizetéshez is futtathatja. 

> [!IMPORTANT] 
> Csak az Azure Resource Manager üzembe helyezése az SAP HANA az Azure-ban (nagyméretű példányok) is támogatja.

 

## <a name="additional-virtual-network-information"></a>További virtuális hálózati adatai

Virtuális hálózat az expressroute-hoz csatlakozhat, létre kell hozni egy Azure-átjáró. További információkért lásd: [kapcsolatos az ExpressRoute virtuális hálózati átjárók](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Az infrastruktúra Azure-on kívül, vagy egy Azure nagyméretű szolgáltatáspéldányban egy Azure-átjáró használható az expressroute-tal. Emellett az Azure-átjáró használható virtuális hálózatok közötti kapcsolat. További információkért lásd: [Resource Manager-hálózatok közötti kapcsolat konfigurálása PowerShell-lel](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az Azure-átjáró négy különböző az ExpressRoute-kapcsolatok maximális csatlakoztathatja mindaddig, amíg a különböző Microsoft vállalati peremhálózati útválasztói biztosítja ezeket a kapcsolatokat. További információkért lásd: [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Az átviteli sebességet biztosít az Azure-átjáró eltér mindkét használati eseteket. További információkért lásd: [információk a VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A maximális átviteli sebességet érhet el a virtuális hálózati átjáró egy ExpressRoute-kapcsolat használatával 10 GB/s. A virtuális hálózatban található virtuális gép és a egy rendszer közötti fájlok másolása a helyszíni (adatfolyamként egyetlen példányt) nem érhet el a teljes átviteli képessége a különböző átjáró SKU-k. Kihasználhatja a virtuális hálózati átjáró a teljes sávszélesség, több Stream használja. Vagy különböző fájlokat egyetlen fájl párhuzamos Streamek kell másolnia.


## <a name="networking-architecture-for-hana-large-instance"></a>Nagyméretű HANA-példány a hálózati architektúra
A hálózati architektúra a nagyméretű HANA-példány négy részből tartalmaznak:

- Helyszíni hálózat és az Azure ExpressRoute-kapcsolat. Ez a rész az ügyfél a tartományhoz, és az Azure expressroute-on keresztül csatlakozik. Tekintse meg az alábbi ábrán a bal alsó részen.
- Az Azure hálózati szolgáltatások, előbb tárgyalt módon, újra kell az átjáró virtuális hálózatok használatával. Ez a rész egy adott területre, ahol meg kell keresnie a megfelelő tervek esetében az alkalmazás követelményei, biztonsági és megfelelőségi követelményeknek. Nagyméretű HANA-példány használatát egy másik pontot kell figyelembe venni a virtuális hálózatok és az Azure-átjáró SKU-k számát tekintetében közül választhat. Tekintse meg a jobb felső sarokban az ábrán látható.
- A nagyméretű HANA-példány keresztüli kapcsolat ExpressRoute technológia az Azure-bA. Ez a kijelző telepítve és a Microsoft által kezelt. Teendők csak adja meg az egyes IP-címtartományok nagyméretű HANA-példányt az eszközök telepítése után az ExpressRoute-kapcsolatcsoport csatlakozhat a virtuális hálózatok. További információkért lásd: [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Hálózatkezelés a nagyméretű HANA-példányt, amely nagyrészt átlátható az Ön számára.

![Az Azure-ban (nagyméretű példányok) és a helyszíni SAP Hana-hoz csatlakoztatott virtuális hálózat](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

A követelmény, hogy a helyszíni eszközök csatlakoznia kell az Azure expressroute-on keresztül nem módosítható, mert a nagyméretű HANA-példányt használ. Egy vagy több virtuális hálózatot, amely a virtuális gépek, amelyek az alkalmazásrétegre, amely kapcsolódik a HANA-példányok nagyméretű HANA-példány egységekben futtatott, hogy még nem változik. 

Az Azure-beli SAP-környezetekhez különbségek a következők:

- A nagyméretű HANA-példány egységeket az ügyfél-bérlő másik ExpressRoute-kapcsolatcsoporton keresztül csatlakoznak a virtuális hálózatokra. Betöltés feltételek külön, a helyszíni virtuális hálózatokat az ExpressRoute-kapcsolatok és virtuális hálózatok és a nagyméretű HANA-példány közötti kapcsolatot nem adjuk ki ugyanazt az útválasztók.
- A számítási feladat profilja az SAP alkalmazásrétegre és nagyméretű HANA-példány között különböző jellegű, és sok kisebb kérelem, és a tevékenységcsúcsok hasonlóan az alkalmazásrétegre, az adatforgalom az SAP HANA-ból (eredményhalmazt).
- A SAP alkalmazás architektúra olyan bizalmas adatokat kezelő a hálózati késés, mint a jellemző forgatókönyvek, ahol adatcsere a helyszíni és az Azure között.
- A virtuális hálózati átjárót már legalább két ExpressRoute-kapcsolatok. Mindkét kapcsolat osztozik a virtuális hálózati átjáró a bejövő adatok maximális sávszélessége.

A hálózati késések tapasztalhatók virtuális gépek és a nagyméretű HANA-példány között egységre is lehet magasabb, mint a szokásos virtuális gép virtuális gép hálózati körbejárási késés. Az Azure-régió függ mért értékek lépheti túl a besorolását, az átlagosnál rosszabb a 0,7-ms körbejárási késés [SAP Megjegyzés #1100926 – gyakori kérdések: hálózati teljesítményt](https://launchpad.support.sap.com/#/notes/1100926/E). Azure-régió és az eszköz hálózati egy Azure virtuális gép és a nagyméretű HANA-példány egység közötti körbejárási késés mérésére függ, a mért késést és lehet, legfeljebb 2 ezredmásodperc körül. Mindazonáltal az ügyfelek üzembe sikeresen SAP HANA nagyméretű példányok az SAP HANA-alapú üzemi SAP-alkalmazások. Ellenőrizze, hogy alaposan tesztelni az üzleti folyamatok az Azure nagyméretű HANA-példányt.
 
Ahhoz, hogy a virtuális gépek és a nagyméretű HANA-példány a determinisztikus hálózati késés, a virtuális hálózati átjáró Termékváltozata, amely alapvető fontosságú. Ellentétben a helyszíni és a virtuális gépek közötti forgalmat a virtuális gépek és a nagyméretű HANA-példány közötti forgalom minta kis, de nagy adatlöketekkel továbbítani a kéréseket és az adatokat kötetek is fejleszthet. Az ilyen adatlöketekkel jól kezelése érdekében az UltraPerformance átjáró-Termékváltozatot használata erősen ajánlott. A HANA nagyméretű példány termékváltozatok II. típusú osztályát az UltraPerformance átjáró-Termékváltozat használatát a virtuális hálózati átjáró megadása kötelező.

> [!IMPORTANT] 
> Adja meg a teljes hálózati forgalom az SAP-alkalmazások és adatbázis rétegek között, csak a nagy teljesítményű vagy a virtuális hálózatok az UltraPerformance átjáró-termékváltozatok támogatottak az Azure-ban (nagyméretű példányok) SAP Hana-val. Csak az UltraPerformance átjáró-Termékváltozatot HANA nagyméretű példány II. típusú termékváltozatokhoz, mint a virtuális hálózati átjáró támogatott.



## <a name="single-sap-system"></a>Egyetlen SAP-rendszerhez

A korábban látható a helyszíni infrastruktúra expressroute-on keresztül csatlakozik az Azure-bA. Az ExpressRoute-kapcsolatcsoporthoz az olyan vállalati peremhálózati útválasztó kapcsolódik. További információkért lásd: [ExpressRoute technikai áttekintése](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az útvonal létrejötte után, az Azure gerinchálózatán keresztül kapcsolódik, és az összes Azure-régiókban érhetők el.

> [!NOTE] 
> Futtassa SAP-környezetünk az Azure-ban, csatlakozzon az Azure-régióba az SAP-rendszeren, a legközelebbi vállalati peremhálózati útválasztó. Az Azure nagy példány stampek dedikált vállalati peremhálózati útválasztó eszközök az Azure IaaS és a nagyméretű példány stampek virtuális gépek közötti hálózati késés minimalizálása érdekében keresztül csatlakoznak.

Az SAP alkalmazáspéldányok üzemeltető virtuális gépek a virtuális hálózati átjárót az ExpressRoute-kapcsolatcsoporthoz csatlakozik. Ugyanahhoz a virtuális hálózathoz csatlakoztatva van egy külön vállalati peremhálózati útválasztó dedikált nagyméretű példány stampek való kapcsolódás.

A rendszer egyetlen SAP-rendszer egyszerű példát. Az SAP alkalmazásrétegre az Azure-ban üzemel. Az SAP HANA-adatbázis futtatja az SAP HANA az Azure-ban (nagyméretű példányok). Feltételezzük, hogy a virtuális hálózati átjáró sávszélesség 2-GB/s és 10-GB/s átviteli nem jelentenek szűk keresztmetszetté.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Több SAP-rendszereit, vagy a nagyméretű SAP-rendszerek

Ha több SAP-rendszereit, vagy a nagyméretű SAP-rendszereit az Azure-ban (nagyméretű példányok) SAP Hana-hoz kapcsolódáshoz van telepítve, az átviteli sebességet a virtuális hálózati átjáró szűk keresztmetszetté válhat. Ebben az esetben az alkalmazás rétegek felosztása több virtuális hálózat. Akkor is előfordulhat, hogy speciális virtuális hálózat létrehozása, amely csatlakozik a nagyméretű HANA-példány azokra az esetekre, mint például:

- Közvetlenül a HANA-példányok a nagyméretű HANA-példányt a biztonsági mentések végrehajtásához az NFS-megosztásokat üzemeltető Azure-beli virtuális géphez.
- Bemásolja a nagyméretű biztonsági másolatok és egyéb fájlok nagyméretű HANA-példány egység az Azure-ban felügyelt hely a lemezen.

Használjon egy külön virtuális hálózatot a gazdagép, amely kezeli a tárhelyet a virtuális gépek. Ezzel az elrendezéssel fokozott elkerülhetők a nagy méretű fájlok vagy az adatforgalmi HANA nagyméretű példányok az Azure-bA a virtuális hálózati átjárót, az SAP alkalmazásrétegre futtató virtuális gépek ellátó hatásait. 

Jobban skálázható hálózati architektúra:

- Használja ki a több virtuális hálózat egyetlen, nagyobb SAP alkalmazás réteg.
- Üzembe helyezése egy külön virtuális hálózatot az egyes üzembe helyezett, SAP-rendszerhez, ezeket külön alhálózatra alatt az azonos virtuális hálózatban SAP-rendszereinket kombinálásával képest.

 Egy jobban skálázható hálózati architektúra az SAP Hana az Azure-ban (nagyméretű példányok):

![SAP alkalmazásréteg üzembe több virtuális hálózaton keresztül](./media/hana-overview-architecture/image4-networking-architecture.png)

Az ábrán látható, az SAP alkalmazásréteg vagy -összetevőket, több virtuális hálózatokon üzembe helyezett. Ez a konfiguráció elkerülhetetlen késéses többletterhelést okoz, a virtuális hálózatok üzemeltetett alkalmazás közötti kommunikáció során fellépő vezetett be. Alapértelmezés szerint a különböző virtuális hálózatokban útvonalon keresztül a vállalati peremhálózati útválasztói ebben a konfigurációban található virtuális gépek közötti hálózati forgalom. A úgy optimalizálása és lefelé a késés két virtuális hálózat közötti kommunikációt a Kivágás, hogy társviszony-létesítés virtuális hálózatok ugyanazon a régión belül. Ez a módszer használható akkor is, ha a virtuális hálózatok különböző előfizetésekben találhatóak. A virtuális hálózatok közötti társviszony, két különböző virtuális hálózatokban lévő virtuális gépek közötti kommunikáció segítségével az Azure gerinchálózatába közvetlenül kommunikálnak egymással. Késés látható, ha a virtuális gépek ugyanazon a virtuális hálózaton. Az egyes virtuális hálózati átjáró a virtuális hálózat, amely az Azure virtuális hálózati átjárón keresztül csatlakozó IP-címtartományok forgalmat továbbít. 

Virtuális hálózatok közötti társviszony létesítésével kapcsolatos további információkért lásd: [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Útválasztás az Azure-ban

Három hálózati útválasztási szempontok fontosak az SAP Hana az Azure-ban (nagyméretű példányok):

* SAP HANA az Azure-ban (nagyméretű példányok) csak a virtuális gépek és a dedikált ExpressRoute-kapcsolat nem közvetlenül a helyszíni keresztül érhetők el. A nagyméretű HANA-példány egységek közvetlen hozzáférést a helyszíni, Microsoft által kiadott nem lehetséges közvetlenül. A tranzitív útválasztási korlátozások vonatkoznak az aktuális Azure-beli hálózati architektúra használt SAP HANA nagyméretű példányok miatt. Néhány felügyeleti ügyfelek és olyan alkalmazásokat, amelyek kell közvetlen hozzáférés, például SAP megoldás Manager a helyszínen futó SAP HANA-adatbázis nem lehet csatlakozni.

* Ha nagyméretű HANA-példány egységek vész-helyreállítási két különböző Azure régióban üzembe helyezve, az azonos átmeneti útválasztási érvényesek. Más szóval egy nagyméretű HANA-példány egység egy adott régióban (például USA nyugati régiója) IP-címek nem irányíthatók át egy nagyméretű HANA-példány egységet (például USA keleti régiója) egy másik régióban üzembe helyezve. Ez a korlátozás nem függ a régiók közötti társviszony-létesítés, vagy a nagyméretű HANA-példány egységek virtuális hálózatokhoz csatlakozó ExpressRoute-Kapcsolatcsoportok átívelő kapcsolódás Azure-hálózat használatát. Egy grafikus megjelenítését lásd: az ábra a szakaszban a "Használata nagyméretű HANA-példány egységek több régióban is." Ez a korlátozás, amelyben a telepített architektúrával, úgy, hogy a HANA-Rendszerreplikálást azonnali használata az vészhelyreállítási funkciók.

* SAP HANA az Azure-ban (nagyméretű példányok) egység van egy hozzárendelt IP-címet a kiszolgáló IP-készlet címtartomány elküldött. További információkért lásd: [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az IP-címet az Azure-előfizetések és a Hana-hoz az Azure-ban (nagyméretű példányok) virtuális hálózatokhoz csatlakozó ExpressRoute keresztül érhető el. Az IP-cím kiosztva közül, hogy kiszolgáló IP-készlet címtartománya közvetlenül hozzá rendelt hardver egységhez. Rendelkezik *nem* keresztül kiosztott NAT válik, mivel ez a megoldás első központi levő volt. 

> [!NOTE] 
> Átmeneti útválasztás a korlátozás áthidalható, az első két listaelemek leírtak szerint, a további összetevők használata útválasztást. A korlátozás áthidalható felhasználható összetevők lehetnek:

> * Egy proxykiszolgálói adatok, irányíthatja a. Például F5 BIG-IP-ot és NGINXET a Traffic Managerrel üzembe helyezett Azure-ra, egy virtuális tűzfal/forgalom-útválasztási megoldás.
> * Használatával [IPTables szabályait saját](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) a Linux rendszerű virtuális gép az Útválasztás a helyszínek és nagyméretű HANA-példány egységek vagy különböző régiókban lévő nagyméretű HANA-példány egységek között engedélyezése.

> Vegye figyelembe, hogy a megvalósítás és a támogatás az egyéni megoldások használata esetén a külső hálózati berendezések, vagy engedélyezze az IPTables a nem Microsoft által biztosított. Támogatási biztosítania kell a használt összetevő a szállító vagy az adatintegrálónak. 

## <a name="internet-connectivity-of-hana-large-instance"></a>A nagyméretű HANA-példány internetkapcsolat
Nagyméretű HANA-példány does *nem* rendelkezik közvetlen internetkapcsolattal. Tegyük fel ez a korlátozás előfordulhat, hogy korlátozni a regisztrálni közvetlenül az operációsrendszer-lemezkép az operációs rendszer szállítójához. Szüksége lehet a SUSE Linux Enterprise Server előfizetés felügyeleti eszköz helyi kiszolgáló vagy a Red Hat Enterprise Linux előfizetés-kezelő használata.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Virtuális gépek és a nagyméretű HANA-példány között adattitkosítás
Nagyméretű HANA-példány és a virtuális gépek között továbbított adatok nincsenek titkosítva. Azonban csak az exchange között a HANA DBMS-oldalon és a JDBC/ODBC-alapú alkalmazások, az szintén titkosítást alkalmazhat a forgalom. További információkért lásd: [ebben a dokumentációban, az SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Nagyméretű HANA-példány egységek használata több régióban

Előfordulhat, hogy üzembe helyezéséhez SAP HANA az Azure-ban (nagyméretű példányok) több Azure-régióban nem vész-helyreállítási okok miatt. Érdemes lehet az egyes régiókban a különböző virtuális hálózatokon üzembe helyezett virtuális nagyméretű HANA-példány eléréséhez. A különböző nagyméretű HANA-példány egységeket rendelt IP-címek a virtuális hálózatok, a példányok az átjárón keresztül közvetlenül csatlakozó túli nem kerülnek. Ennek eredményeképpen egy kis módosítást a virtuális hálózati kialakítás jelenik meg. A virtuális hálózati átjáró négy különböző ExpressRoute-Kapcsolatcsoportok kívül más vállalati peremhálózati útválasztók kezelésére képes. Minden virtuális hálózatnak, amelyhez csatlakozik egy nagyméretű példány stampek lehet csatlakozni a nagyméretű szolgáltatáspéldányban egy másik Azure-régióban.

![Virtuális hálózat csatlakozik a különböző Azure-régiókban lévő Azure-beli nagyméretű példány bélyegzők](./media/hana-overview-architecture/image8-multiple-regions.png)

Az ábra bemutatja, hogyan a különböző virtuális hálózatok mindkét régióban kapcsolódik két másik ExpressRoute-kapcsolatcsoporttal csatlakozhat az Azure-ban (nagyméretű példányok) SAP Hana-hoz használt két Azure-régióban. Az újonnan bevezetett kapcsolatok a téglalap alakú piros vonalak. Az ezeket a kapcsolatokat a virtuális hálózatokon kívül a különböző nagyméretű HANA-példány egységek, a két régióban üzembe helyezett minden egyes virtuális hálózatok egy futó virtuális gépek lehet elérni. Ahogy az ábrán látható, feltételezzük, hogy a két Azure-régiókhoz való két ExpressRoute-kapcsolatok a helyszíni. Ezzel az elrendezéssel fokozott a vész-helyreállítási okokból ajánlott.

> [!IMPORTANT] 
> Ha több ExpressRoute-Kapcsolatcsoportok használta, a AS Path előtag-Beillesztés és a helyi beállításokat szabályozó BGP-beállítások annak érdekében, hogy a forgalom megfelelő útválasztási használandó.

**Következő lépések**
- Tekintse meg [SAP HANA (nagyméretű példányok) tároló-architektúra](hana-storage-architecture.md)