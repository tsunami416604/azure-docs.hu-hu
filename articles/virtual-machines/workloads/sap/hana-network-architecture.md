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
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8794a93cecb50774f30746c22931db31a9fa9194
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239580"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (nagyméretű példányok) hálózati architektúra

Az architektúra az Azure hálózati szolgáltatások egyik legfontosabb összetevőjeként a sikeres üzembe helyezéséhez SAP-alkalmazások nagyméretű HANA-példány. Általában az SAP HANA Azure-ban (nagyméretű példányok) üzemelő példányok esetében egy nagyobb SAP-rendszeren, a számos különböző SAP-megoldások a különböző méretű készletben az adatbázisok, a Processzor-erőforrás-használat és a memóriahasználat rendelkezik. Valószínű, hogy nem minden informatikai rendszerét találhatók az Azure-ban már. SAP-környezetét legtöbbször a hibrid és a egy adatbázis-kezelő és az SAP alkalmazások szempontjából NetWeaver, és S/4hana-t és az SAP HANA és az egyéb adatbázis-kezelő használatával. Az Azure kínál a különböző szolgáltatások, amelyek lehetővé teszik, hogy a különböző DBMS NetWeaver és S/4HANA rendszert futtató Azure-ban. Az Azure is ajánlatok technológia, hogy keresse meg az Azure hálózati, például a helyi szoftverek központi telepítése a virtuális adatközpontok

Ha teljes körű IT-rendszereit az Azure-ban üzemel. Azure hálózatkezelési funkció segítségével a helyi világ összekapcsolása révén az Azure például egy virtuális adatközpont terveztük meg az Azure-eszközök. Az Azure hálózati funkciókat használja a következő: 

- Azure virtuális hálózatokhoz csatlakoznak a [ExpressRoute](https://azure.microsoft.com/services/expressroute/) kapcsolatcsoport, amely kapcsolódik a helyszíni hálózati eszközöket.
- A helyszíni szolgáltatással az Azure ExpressRoute-kapcsolatcsoport kell rendelkeznie a sávszélességnek [1 GB/s vagy újabb](https://azure.microsoft.com/pricing/details/expressroute/). A minimális sávszélesség elegendő sávszélesség a helyszíni és a virtuális gépeken futó rendszerek közötti adatátvitelt tesz lehetővé. Lehetővé teszi elegendő sávszélesség-kapcsolat Azure rendszerek, a helyszíni felhasználók.
- Az Azure-ban minden SAP-rendszereinket állíthatók be a virtuális hálózatok egymással kommunikálni.
- Az Active Directory és DNS a helyileg üzemeltetett kiterjeszthetők az Azure expressroute-on keresztül a helyszíni vagy a teljes Azure-ban futnak.

A HANA nagyméretű példányok integrálása az Azure data center hálózati háló adott esetben Azure ExpressRoute-technológia is használatos


> [!NOTE] 
> Csak egy Azure-előfizetéssel is össze a egy HANA nagyméretű szolgáltatáspéldányban egy adott Azure-régióban kizárólag egy bérlővel. Ezzel szemben egyetlen nagyméretű HANA-példány stamp bérlő kapcsolhatók csak egy Azure-előfizetéshez. Ez a követelmény nem konzisztensek legyenek más számlázható objektumok az Azure-ban.

SAP HANA az Azure-ban (nagyméretű példányok) több különböző Azure-régióban van üzembe helyezve, ha külön bérlőt a HANA nagyméretű szolgáltatáspéldányban üzemel. Mindaddig, amíg ezek a példányok ugyanazt az SAP-rendszeren részét képező alatt az Azure-előfizetéshez is futtathatja. 

> [!IMPORTANT] 
> Az Azure Resource Manager üzembe helyezési módszert az SAP HANA az Azure-ban (nagyméretű példányok) támogatott.

 

## <a name="additional-virtual-network-information"></a>További virtuális hálózati adatai

Virtuális hálózat az expressroute-hoz csatlakozni egy Azure ExpressRoute-átjárót kell létrehozni. További információkért lásd: [kapcsolatos az Expressroute-átjárót az expressroute-hoz](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Egy Azure ExpressRoute-átjárót az expressroute-tal szolgál az infrastruktúra Azure-on kívül, vagy egy Azure nagyméretű szolgáltatáspéldányban. Az Azure ExpressRoute-átjárót legfeljebb négy különböző ExpressRoute-Kapcsolatcsoportok csatlakoztathatja mindaddig, amíg a különböző Microsoft vállalati peremhálózati útválasztói biztosítja ezeket a kapcsolatokat. További információkért lásd: [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> A maximális átviteli sebességet érhet el egy ExpressRoute-átjáróval 10 GB/s egy ExpressRoute-kapcsolat használatával. A virtuális hálózatban található virtuális gép és a egy rendszer közötti fájlok másolása a helyszíni (adatfolyamként egyetlen példányt) nem érhet el a teljes átviteli képessége a különböző átjáró SKU-k. Kihasználhatja a teljes sávszélesség, az ExpressRoute-átjárót, több Stream használja. Vagy különböző fájlokat egyetlen fájl párhuzamos Streamek kell másolnia.


## <a name="networking-architecture-for-hana-large-instance"></a>Nagyméretű HANA-példány a hálózati architektúra
A hálózati architektúra a nagyméretű HANA-példány négy részből tartalmaznak:

- Helyszíni hálózat és az Azure ExpressRoute-kapcsolat. Ez a rész az ügyfél a tartományhoz, és az Azure expressroute-on keresztül csatlakozik. Az Expressroute-kapcsolatcsoport Ön teljes mértékben kifizetni a Microsoft. A sávszélesség elég nagynak kell lennie a helyi eszközök és elleni kapcsolódik az Azure-régió közötti hálózati forgalom kezeléséhez. Tekintse meg az alábbi ábrán a bal alsó részen.
- Az Azure hálózati szolgáltatások, előbb tárgyalt módon, újra kell az ExpressRoute-átjáró hozzáadása virtuális hálózatok használatával. Ez a rész egy adott területre, ahol meg kell keresnie a megfelelő tervek esetében az alkalmazás követelményei, biztonsági és megfelelőségi követelményeknek. Nagyméretű HANA-példány használatát egy másik pontot kell figyelembe venni a virtuális hálózatok és az Azure-átjáró SKU-k számát tekintetében közül választhat. Tekintse meg a jobb felső sarokban az ábrán látható.
- A nagyméretű HANA-példány keresztüli kapcsolat ExpressRoute technológia az Azure-bA. Ez a kijelző telepítve és a Microsoft által kezelt. Teendők csak adja meg az egyes IP-címtartományok nagyméretű HANA-példányt az eszközök telepítése után az ExpressRoute-kapcsolatcsoport csatlakozhat a virtuális hálózatok. További információkért lásd: [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Nincs az Azure data center hálózati háló és nagyméretű HANA-példány egység közötti kapcsolatot a Microsoft Önnek nincs további díja.
- Hálózatkezelés a HANA nagyméretű szolgáltatáspéldányban belül, amely nagyrészt átlátható az Ön számára.

![Az Azure-ban (nagyméretű példányok) és a helyszíni SAP Hana-hoz csatlakoztatott virtuális hálózat](./media/hana-overview-architecture/image1-architecture.png)

A követelmény, hogy a helyszíni eszközök csatlakoznia kell az Azure expressroute-on keresztül nem módosítható, mert a nagyméretű HANA-példányt használ. Egy vagy több virtuális hálózatot, amely a virtuális gépek, amelyek az alkalmazásrétegre, amely kapcsolódik a HANA-példányok nagyméretű HANA-példány egységekben futtatott, hogy még nem változik. 

Az Azure-beli SAP-környezetekhez különbségek a következők:

- A nagyméretű HANA-példány egységeket az ügyfél-bérlő másik ExpressRoute-kapcsolatcsoporton keresztül csatlakoznak a virtuális hálózatokra. Betöltés feltételek külön, a helyszíni Azure virtuális hálózat ExpressRoute-Kapcsolatcsoportok és a Kapcsolatcsoportok az Azure virtuális hálózatok és a nagyméretű HANA-példányok között nem adjuk ki ugyanazt az útválasztók.
- A számítási feladat profilja az SAP alkalmazásrétegre és nagyméretű HANA-példány között különböző jellegű, és sok kisebb kérelem, és a tevékenységcsúcsok hasonlóan az alkalmazásrétegre, az adatforgalom az SAP HANA-ból (eredményhalmazt).
- A SAP alkalmazás architektúra olyan bizalmas adatokat kezelő a hálózati késés, mint a jellemző forgatókönyvek, ahol adatcsere a helyszíni és az Azure között.
- Az Azure ExpressRoute-átjárót már legalább két ExpressRoute-kapcsolatok. Egy kapcsolatcsoport, amely kapcsolódik a helyszíni és a egy, a nagyméretű HANA-példányokhoz kapcsolódó. Csak a másik két további Kapcsolatcsoportok szoba különböző Msee az ExpressRoute-átjáró csatlakozni a kihasználatlan marad. Ez a korlátozás nem függ a az ExpressRoute gyors elérési út használatának. A csatlakoztatott Kapcsolatcsoportok ossza meg az ExpressRoute-átjárót a bejövő adatok maximális sávszélessége.

A hálózati késések tapasztalhatók virtuális gépek és a nagyméretű HANA-példány között egységre is lehet magasabb, mint a szokásos virtuális gép virtuális gép hálózati körbejárási késés. Az Azure-régió függ mért értékek lépheti túl a besorolását, az átlagosnál rosszabb a 0,7-ms körbejárási késés [SAP Megjegyzés #1100926 – gyakori kérdések: Hálózati teljesítményt](https://launchpad.support.sap.com/#/notes/1100926/E). Azure-régió és az eszköz hálózati egy Azure virtuális gép és a nagyméretű HANA-példány egység közötti körbejárási késés mérésére függ, a mért késést és lehet, legfeljebb 2 ezredmásodperc körül. Mindazonáltal az ügyfelek üzembe sikeresen SAP HANA nagyméretű példányok az SAP HANA-alapú üzemi SAP-alkalmazások. Ellenőrizze, hogy alaposan tesztelni az üzleti folyamatok az Azure nagyméretű HANA-példányt. Egy új funkciót, az ExpressRoute gyors útvonal elnevezésű, a nagyméretű HANA-példányok és ügyfélalkalmazási rétegben az Azure-beli virtuális gépek közötti hálózati késés jelentős mértékben csökkenteni (lásd alább). 

A virtuális gépek és a nagyméretű HANA-példány, az ExpressRoute-átjárót a kiválasztott közötti determinisztikus hálózati késést tudjon biztosítani a Termékváltozat elengedhetetlen. Ellentétben a helyszíni és a virtuális gépek közötti forgalmat a virtuális gépek és a nagyméretű HANA-példány közötti forgalom minta kis, de nagy adatlöketekkel továbbítani a kéréseket és az adatokat kötetek is fejleszthet. Az ilyen adatlöketekkel jól kezelése érdekében az UltraPerformance átjáró-Termékváltozatot használata erősen ajánlott. A HANA nagyméretű példány termékváltozatok II. típusú osztályát az UltraPerformance átjáró-Termékváltozatot ExpressRotue átjáróként használata kötelező.

> [!IMPORTANT] 
> Adja meg a teljes hálózati forgalom az SAP-alkalmazások és adatbázis rétegek között, csak a nagy teljesítményű vagy a virtuális hálózatok az UltraPerformance átjáró-termékváltozatok támogatottak az Azure-ban (nagyméretű példányok) SAP Hana-val. Csak az UltraPerformance átjáró-Termékváltozatot HANA nagyméretű példány II. típusú termékváltozatokhoz, mint egy ExpressRoute-átjáró támogatott. Kivételek alkalmazza, ha az ExpressRoute gyors elérési út (lásd alább)

### <a name="expressroute-fast-path"></a>Az ExpressRoute gyors elérési útja
Csökkentheti a késést, az ExpressRoute gyors elérési út bevezetett van, és megjelent 2019. május HANA nagyméretű példányok adott csatlakoztatásához az Azure virtuális hálózatokhoz, amelyeken az SAP alkalmazás virtuális gépeire. A fő különbség az eddigi jelennek meg a megoldás az, hogy az adatfolyamok közötti virtuális gépek és a nagyméretű HANA-példányokhoz nem kerülnek az ExpressRoute-átjárót már. A virtuális gépek az Azure virtuális hálózathoz a alhálózat(ok) hozzárendelt helyett közvetlenül kommunikál a dedikált vállalati peremhálózati útválasztó. 

> [!IMPORTANT] 
> Az ExpressRoute gyors elérési út funkciókat igényel, hogy vannak-e az alhálózatok, a SAP alkalmazás virtuális gépek futtatása az azonos Azure virtuális hálózatban, amely csatlakoztatva van a HANA nagyméretű példányok. -Beli virtuális gépeken az Azure virtuális hálózatok társviszonyban állnak az Azure virtuális hálózat, a nagyméretű HANA-példány egységek közvetlenül csatlakozik az ExpressRoute gyors elérési út nem részesülő. Eredményeképpen tipikus küllős virtuális hálózati kialakításokat, ahol ellen a központi virtuális hálózaton az ExpressRoute-Kapcsolatcsoportok csatlakozik, és az SAP alkalmazásrétegre (küllők) tartalmazó virtuális hálózatok társviszonyban állnak első, által az ExpressRoute gyors optimalizálása Elérési út nem fog működni. Gatewayen az ExpressRoute gyors elérési út nem támogatja a felhasználó által megadott útválasztási szabályok (UDR) még ma. További információkért lásd: [ExpressRoute virtuális hálózati átjáró és a FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


A konfigurálása ExpressRoute gyors elérési út, a további részletekért olvassa el a dokumentumot [egy virtuális hálózat csatlakoztatása HANA nagyméretű példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> Egy UltraPerformance ExpressRoute-átjárót kell rendelkeznie az ExpressRoute gyors elérési út használata


## <a name="single-sap-system"></a>Egyetlen SAP-rendszerhez

A korábban látható a helyszíni infrastruktúra expressroute-on keresztül csatlakozik az Azure-bA. Az ExpressRoute-kapcsolatcsoporthoz csatlakozik, a Microsoft vállalati peremhálózati útválasztó (MSEE). További információkért lásd: [ExpressRoute technikai áttekintése](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az útvonal létrejötte után, az Azure gerinchálózatán keresztül kapcsolódik.

> [!NOTE] 
> Futtassa SAP-környezetünk az Azure-ban, csatlakozzon az Azure-régióba az SAP-rendszeren, a legközelebbi vállalati peremhálózati útválasztó. Nagyméretű HANA-példány stampek dedikált vállalati peremhálózati útválasztó eszközök az Azure IaaS virtuális gépek és a nagyméretű HANA-példány stampek közötti hálózati késés minimalizálása érdekében keresztül csatlakoznak.

Az ExpressRoute-átjárót, hogy az SAP alkalmazást üzemeltető virtuális gépek, amely kapcsolódik a helyszíni egy ExpressRoute-kapcsolatcsoporthoz csatlakozik. Ugyanahhoz a virtuális hálózathoz csatlakoztatva van egy külön vállalati peremhálózati útválasztó dedikált nagyméretű példány stampek való kapcsolódás. Az ExpressRoute gyors elérési út, az adatfolyam HANA nagyméretű példányok az SAP alkalmazásrétegre a virtuális gépek nem kerülnek az ExpressRoute-átjárót már és, csökkentheti a hálózat körbejárási késés.

A rendszer egyetlen SAP-rendszer egyszerű példát. Az SAP alkalmazásrétegre az Azure-ban üzemel. Az SAP HANA-adatbázis futtatja az SAP HANA az Azure-ban (nagyméretű példányok). Feltételezzük, hogy az ExpressRoute-átjáró sávszélességét 2-GB/s és 10-GB/s átviteli nem jelentenek szűk keresztmetszetté.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Több SAP-rendszereit, vagy a nagyméretű SAP-rendszerek

Ha több SAP-rendszereit, vagy a nagyméretű SAP-rendszereit az Azure-ban (nagyméretű példányok) SAP Hana-hoz kapcsolódáshoz van telepítve, az átviteli sebességet, az ExpressRoute-átjáró szűk keresztmetszetté válhat. Vagy el szeretné különíteni az éles környezetben, és nem éles rendszerek különböző Azure virtual networkök. Ebben az esetben az alkalmazás rétegek felosztása több virtuális hálózat. Akkor is előfordulhat, hogy speciális virtuális hálózat létrehozása, amely csatlakozik a nagyméretű HANA-példány azokra az esetekre, mint például:

- Közvetlenül a HANA-példányok a nagyméretű HANA-példányt a biztonsági mentések végrehajtásához az NFS-megosztásokat üzemeltető Azure-beli virtuális géphez.
- Bemásolja a nagyméretű biztonsági másolatok és egyéb fájlok nagyméretű HANA-példány egység az Azure-ban felügyelt hely a lemezen.

Tárolási tömeges HANA nagyméretű példányok és Azure közötti adatátvitel kezelése virtuális gépek gazdagépre egy külön virtuális hálózatot használja. Ezzel az elrendezéssel fokozott elkerülhetők a nagy méretű fájlok vagy az adatforgalmi HANA nagyméretű példányok az Azure-bA az ExpressRoute-átjárót, az SAP alkalmazásrétegre futtató virtuális gépek ellátó hatásait. 

Jobban skálázható hálózati architektúra:

- Használja ki a több virtuális hálózat egyetlen, nagyobb SAP alkalmazás réteg.
- Üzembe helyezése egy külön virtuális hálózatot az egyes üzembe helyezett, SAP-rendszerhez, ezeket külön alhálózatra alatt az azonos virtuális hálózatban SAP-rendszereinket kombinálásával képest.

  Egy jobban skálázható hálózati architektúra az SAP Hana az Azure-ban (nagyméretű példányok):

![SAP alkalmazásréteg üzembe több virtuális hálózaton keresztül](./media/hana-overview-architecture/image4-networking-architecture.png)

A szabályokat és korlátozásokat függ, alkalmazni szeretné az SAP-rendszereit különböző virtuális gépeket üzemeltet a különböző virtuális hálózatok között, meg kell ezen virtuális hálózatok társviszonyba állítása. Virtuális hálózatok közötti társviszony létesítésével kapcsolatos további információkért lásd: [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Útválasztás az Azure-ban

Alapértelmezett központi telepítési hálózati három útválasztási szempontok fontosak az SAP HANA az Azure-ban (nagyméretű példányok):

* SAP HANA az Azure-ban (nagyméretű példányok) csak az Azure virtuális gépek és a dedikált ExpressRoute-kapcsolat nem közvetlenül a helyszíni keresztül érhetők el. A nagyméretű HANA-példány egységek közvetlen hozzáférést a helyszíni, Microsoft által kiadott nem lehetséges közvetlenül. A tranzitív útválasztási korlátozások vonatkoznak az aktuális Azure-beli hálózati architektúra használt SAP HANA nagyméretű példányok miatt. Néhány felügyeleti ügyfelek és olyan alkalmazásokat, amelyek kell közvetlen hozzáférés, például SAP megoldás Manager a helyszínen futó SAP HANA-adatbázis nem lehet csatlakozni. Kivételek ellenőrizze a "Közvetlen útválasztás a HANA nagyméretű példányok" szakaszban.

* Ha két különböző Azure-régió vész-helyreállítási az azonos átmeneti útválasztási korlátozásokat korábban üzembe helyezett nagyméretű HANA-példány egységek. Más szóval egy nagyméretű HANA-példány egység egy adott régióban (például USA nyugati régiója) IP-címek is nem irányít rá egy nagyméretű HANA-példány egységet (például USA keleti régiója) egy másik régióban üzembe helyezve. Ez a korlátozás lett független a régiók közötti társviszony-létesítés, vagy a nagyméretű HANA-példány egységek virtuális hálózatokhoz csatlakozó ExpressRoute-Kapcsolatcsoportok átívelő kapcsolódás Azure-hálózat használatát. Egy grafikus megjelenítését lásd: az ábra a szakaszban a "Használata nagyméretű HANA-példány egységek több régióban is." Ez a korlátozás az üzembe helyezett architektúra kapott, a HANA-Rendszerreplikálást azonnali használata az vészhelyreállítási funkciók zakázaná. Az útmutató legutóbbi módosításait nézzük meg a "Használata nagyméretű HANA-példány egységek több régióban" szakaszban. 

* SAP HANA az Azure-ban (nagyméretű példányok) egység van egy hozzárendelt IP-címet a kiszolgáló IP-készlet címtartomány a nagyméretű HANA-példány üzembe helyezési kérésekor elküldött. További információkért lásd: [SAP HANA (nagyméretű példányok) infrastruktúra és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az IP-címet az Azure-előfizetések és a nagyméretű HANA-példányok Azure virtuális hálózatokhoz csatlakozó kapcsolatcsoport keresztül érhető el. Az IP-cím kiosztva közül, hogy kiszolgáló IP-készlet címtartománya közvetlenül hozzá rendelt hardver egységhez. Rendelkezik *nem* keresztül kiosztott NAT válik, mivel ez a megoldás első központi levő volt. 

### <a name="direct-routing-to-hana-large-instances"></a>Közvetlen útválasztás a nagyméretű HANA-példányok
Alapértelmezés szerint a tranzitív útválasztással nagyméretű HANA-példány egységek és a helyszíni vagy nagyméretű HANA-példány útválasztás üzembe helyezett két különböző régióban között nem működik. Ennek több oka ilyen egy tranzitív útválasztással engedélyezéséhez.

- Egy proxykiszolgálói adatok, irányíthatja a. Például F5 BIG-IP-ot és NGINXET a Traffic Managerrel üzembe helyezett Azure virtuális hálózatban, amely nagyméretű HANA-példányok és csatlakozik egy virtuális tűzfal/forgalom-útválasztási megoldásként helyszíni.
- Használatával [IPTables szabályait saját](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) a Linux rendszerű virtuális gép az Útválasztás a helyszínek és nagyméretű HANA-példány egységek vagy különböző régiókban lévő nagyméretű HANA-példány egységek között engedélyezése. Engedélyezze az IPTables futó virtuális gép kell telepíteni, amely kapcsolódik a HANA nagyméretű példányok az Azure virtuális hálózatban, és a helyszíni. A virtuális gépet kell méretezni, így, a hálózat átviteli sebessége, a virtuális gép elegendő várt hálózati forgalmához. A részleteket a virtuális gép hálózati sávszélesség, ellenőrizze a cikk [méretek Linux virtuális gépek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Az Azure tűzfal](https://azure.microsoft.com/services/azure-firewall/) lenne, a helyszíni és a HANA nagyméretű példány egység közötti forgalom egy másik megoldás. 

Ezek a megoldások a forgalmat egy Azure virtuális hálózaton keresztül lesz átirányítva, és mint ilyen a forgalmat Emellett korlátozott a helyreállítható készülékek használt, vagy az Azure hálózati biztonsági csoportok, tehát, amely az egyes IP-cím vagy IP-cím lehet a helyszíni letiltott vagy kifejezetten engedélyezett a nagyméretű HANA-példányok eléréséhez. 

> [!NOTE]  
> Vegye figyelembe, hogy a megvalósítás és a támogatás az egyéni megoldások használata esetén a külső hálózati berendezések, vagy engedélyezze az IPTables a nem Microsoft által biztosított. Támogatási biztosítania kell a használt összetevő a szállító vagy az adatintegrálónak. 

#### <a name="express-route-global-reach"></a>Express Route globális elérhetőséggel
A Microsoft bevezetett egy új funkció nevű [ExpressRoute globális elérhetőségű](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Globális elérhetőség HANA nagyméretű példányok két esetben használható:

- Helyszínről a nagyméretű HANA-példány egységeket különböző régióban üzembe helyezett közvetlen elérésének lehetővé tétele
- A különböző régióban üzembe helyezett nagyméretű HANA-példány egység közötti közvetlen kommunikáció engedélyezése


##### <a name="direct-access-from-on-premise"></a>Közvetlen hozzáférés a helyszíni rendszerből
Az Azure-régióban, globális elérhetőségű érhető el kérheti a globális elérhetőségű funkció, amely a helyszíni hálózat csatlakozik az Azure virtuális hálózat, amely a nagyméretű HANA-példány egységeket is csatlakozik az ExpressRoute-kapcsolatcsoport engedélyezése. Vannak bizonyos költség következmények az ExpressRoute-kapcsolatcsoport helyi oldalára. Az árak, ellenőrizze a árai [globális elérni bővítmény](https://azure.microsoft.com/pricing/details/expressroute/). Nincsenek meg a kapcsolatcsoport, amely kapcsolódik a nagyméretű HANA-példány egység az Azure-hoz kapcsolódó további költségek nélkül. 

> [!IMPORTANT]  
> Esetén használja a globális elérhetőségű a nagyméretű HANA-példány egységek és a helyszíni eszközök közötti közvetlen hozzáférésének engedélyezéséhez szükséges, a hálózati adat- és flow-t **nem továbbítja a rendszer Azure virtuális hálózatok keresztül**, de a Microsoft közötti közvetlen Vállalati exchange útválasztók. Ennek eredményeképpen NSG-n vagy Alkalmazásbiztonsági szabályokat, vagy a tűzfal, nva-t vagy egy Azure virtuális hálózaton üzembe helyezett proxy bármilyen típusú vannak nem első csinált semmit. **Ha használ ExpressRoute globális elérhetőségű HANA nagyméretű példány egységek korlátozások helyszínről közvetlen hozzáférést és engedélyeket az eléréséhez a HANA nagyméretű példány egységek kell definiálni kell a helyszíni oldalon tűzfalak** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Csatlakozás a HANA nagyméretű példányok különböző Azure-régióban
Ugyanúgy mivel ExpressRoute globális elérhetőségű használhatja a helyszíni csatlakozás nagyméretű HANA-példány egység, használat, amely két különböző régióban vannak telepítve a bérlők HANA nagyméretű példányok fonókábel csatlakozni. Az elkülönítés az ExpressRoute-Kapcsolatcsoportok a nagyméretű HANA-példány bérlők által használt csatlakozni az Azure mindkét régióban. Nincsenek további díjmentesen használható két különböző régióban üzembe helyezett két nagyméretű HANA-példány-bérlő összekapcsolása. 

> [!IMPORTANT]  
> Az adatfolyam és átvitelvezérlés a hálózati forgalom között a különböző HANA nagyméretű példány bérlők keresztül az azure-hálózatok nem lesznek irányítva. Ennek eredményeképpen nem használhat Azure funkciót vagy nva-k való kommunikáció kikényszerítheti a két HANA nagyméretű példányok bérlők között. 

Hogyan kérhet ExpressRoute globális elérhetőségű engedélyezve van a további részletekért olvassa el a dokumentum [egy virtuális hálózat csatlakoztatása HANA nagyméretű példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>A nagyméretű HANA-példány internetkapcsolat
Nagyméretű HANA-példány does *nem* rendelkezik közvetlen internetkapcsolattal. Tegyük fel ez a korlátozás előfordulhat, hogy korlátozni a regisztrálni közvetlenül az operációsrendszer-lemezkép az operációs rendszer szállítójához. Szüksége lehet a SUSE Linux Enterprise Server előfizetés felügyeleti eszköz helyi kiszolgáló vagy a Red Hat Enterprise Linux előfizetés-kezelő használata.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Virtuális gépek és a nagyméretű HANA-példány között adattitkosítás
Nagyméretű HANA-példány és a virtuális gépek között továbbított adatok nincsenek titkosítva. Azonban csak az exchange között a HANA DBMS-oldalon és a JDBC/ODBC-alapú alkalmazások, az szintén titkosítást alkalmazhat a forgalom. További információkért lásd: [ebben a dokumentációban, az SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Nagyméretű HANA-példány egységek használata több régióban

Vész-helyreállítási set ups megvalósításához, szüksége lesz a nagyméretű példány SHANA egységek több Azure-régióban. [Globális virtuális társhálózatok létesítésének] Azure használatával mellett is tranzitív útválasztással alapértelmezés szerint nem működik a két különböző régiókban lévő nagyméretű HANA-példány bérlők között. Ugyanakkor globális elérhetőségű megnyílik a nagyméretű HANA-példány egységek kiépítése két különböző régióban közötti kommunikációs útvonal. Ez lehetővé teszi, hogy az ExpressRoute globális elérhetőségű használati forgatókönyv:

 - HANA-Rendszerreplikálást további proxyk és tűzfalak nélkül
 - Hajtsa végre a rendszer másolja vagy a rendszer frissíti a két különböző régiókban lévő nagyméretű HANA-példány egység közötti másolását biztonsági mentések


![Virtuális hálózat csatlakozik a különböző Azure-régiókban lévő Azure-beli nagyméretű példány bélyegzők](./media/hana-overview-architecture/image8-multiple-regions.png)

Az ábrán látható hogyan csatlakozik a különböző virtuális hálózatok mindkét régióban két különböző ExpressRoute-kapcsolatcsoporttal csatlakozhat az Azure-ban (nagyméretű példányok) SAP Hana-hoz használt Azure-régióban mindkét (szürke vonal). Ez két közötti kapcsolat oka az msee-k, sem a kimaradás utáni védelme érdekében. A két Azure-régióban a két virtuális hálózat közötti kommunikációs folyamat keresztül kezelni kellene a [globális társviszony-létesítés](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) , a két virtuális hálózat két különböző régióban (kék pontozott vonal). A piros vastag vonal a globális elérhetőségű ExpressRoute kapcsolat, amely lehetővé teszi a bérlők számára két különböző régióban kommunikálnak egymással, nagyméretű HANA-példány egységei ismerteti. 

> [!IMPORTANT] 
> Ha több ExpressRoute-Kapcsolatcsoportok használta, a AS Path előtag-Beillesztés és a helyi beállításokat szabályozó BGP-beállítások annak érdekében, hogy a forgalom megfelelő útválasztási használandó.

**Következő lépések**
- Tekintse meg [SAP HANA (nagyméretű példányok) tároló-architektúra](hana-storage-architecture.md)