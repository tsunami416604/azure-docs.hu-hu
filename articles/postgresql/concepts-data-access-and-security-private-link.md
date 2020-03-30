---
title: Privát hivatkozás - Azure-adatbázis a PostgreSQL-hez - Egyetlen kiszolgáló
description: Ismerje meg, hogyan működik a Privát hivatkozás az Azure Database for PostgreSQL - Single server szolgáltatáshoz.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4216abdf8cc8aae00e3ba0c57961c4b8b7403672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371681"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Privát hivatkozás az Azure-adatbázishoz postgreSQL-single kiszolgálóhoz

A Private Link lehetővé teszi, hogy privát végpontokat hozzon létre az Azure Database for PostgreSQL - Single server számára, és így az Azure-szolgáltatásokat a privát virtuális hálózaton (VNet) belülhozza. A privát végpont egy privát IP-címet tesz elérhetővé, amelynek segítségével ugyanúgy csatlakozhat az adatbázis-kiszolgálóhoz, mint a virtuális hálózat bármely más erőforrásához.

A Private Link funkciót támogató PaaS-szolgáltatások listáját a Private Link [dokumentációjában](https://docs.microsoft.com/azure/private-link/index)tekintheti meg. A privát végpont egy privát IP-cím egy adott [virtuális hálózaton](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) és alhálózaton belül.

> [!NOTE]
> Ez a funkció minden Olyan Azure-régióban elérhető, ahol az Azure Database for PostgreSQL Single server támogatja az általános célú és a memóriaoptimalizált tarifacsomagokat.

## <a name="data-exfiltration-prevention"></a>Adatszivárgás megelőzése

Az Azure Database for PostgreSQL Single server rendszerben az adatok ex-filtration az, amikor egy jogosult felhasználó, például egy adatbázis-rendszergazda, képes adatokat kinyerni az egyik rendszerből, és áthelyezni őket egy másik helyre vagy rendszerre a szervezeten kívül. Például a felhasználó áthelyezi az adatokat egy harmadik fél tulajdonában lévő tárfiókba.

Fontolja meg egy olyan forgatókönyv, amelyben egy felhasználó pgadmin-t futtat egy Azure virtuális gépen (VM) belül, amely az USA nyugati részén kiépített PostgreSQL Single kiszolgálóhoz csatlakozik. Az alábbi példa bemutatja, hogyan korlátozhatja a hozzáférést a nyilvános végpontok az Azure Database for PostgreSQL Single server hálózati hozzáférés-vezérlők használatával.

* Tiltsa le az Azure-szolgáltatás for-fortefektot az Azure Database for PostgreSQL Single server szolgáltatásba a nyilvános végponton keresztül az *Azure Services* kikapcsolása beállításával. Győződjön meg arról, hogy [tűzfalszabályok](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) vagy virtuális [hálózati szolgáltatás végpontjai](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet)segítségével nem férhetnek hozzá a kiszolgálóhoz IP-címek vagy tartományok.

* Csak a virtuális gép privát IP-címét használó egyetlen kiszolgáló azure-adatbázisára irányuló forgalmat engedélyezze. További információt a Service [Endpoint](concepts-data-access-and-security-vnet.md) és a [VNet tűzfalszabályokról](howto-manage-vnet-using-portal.md) szóló cikkekben talál.

* Az Azure virtuális gép, szűkítse le a kimenő kapcsolat hatókörét a hálózati biztonsági csoportok (NSG-k) és a szolgáltatáscímkék az alábbiak szerint

    * Adjon meg egy NSG-szabályt a Service Tag = SQL forgalom *engedélyezéséhez. WestUS* – csak az Azure Database for PostgreSQL Single server használatával való kapcsolat engedélyezése az USA nyugati részén
    * NSG-szabály megadása (magasabb prioritással) a *Service Tag = SQL* forgalom megtagadásához - a PostgreSQL-adatbázishoz való kapcsolatok megtagadása minden régióban</br></br>

A beállítás végén az Azure virtuális gép csak az Azure Database for PostgreSQL Single server az USA nyugati régiójában. A kapcsolat azonban nem korlátozódik egyetlen Azure-adatbázis postgreSQL single server. A virtuális gép továbbra is csatlakozhat bármely Azure Database for PostgreSQL Single server az USA nyugati régiójában, beleértve az adatbázisokat, amelyek nem részei az előfizetésnek. Bár a fenti forgatókönyvben az adatok kiszivárgásának hatókörét egy adott régióra csökkentettük, azt nem szüntettük meg teljesen.</br>

A Private Link segítségével mostmár beállíthatja a hálózati hozzáférés-vezérléseket, például az NSG-ket, hogy korlátozza a privát végponthoz való hozzáférést. Az egyes Azure PaaS-erőforrások ezután adott privát végpontokhoz vannak rendelve. Egy rosszindulatú bennfentes csak a leképezett PaaS-erőforráshoz férhet hozzá (például egy Azure Database for PostgreSQL Single server) és más erőforrást.

## <a name="on-premises-connectivity-over-private-peering"></a>Helyszíni kapcsolat a privát társviszony-létesítésen

Amikor a nyilvános végponthoz helyszíni gépekről csatlakozik, az IP-címet hozzá kell adni az IP-szintű tűzfalhoz egy kiszolgálószintű tűzfalszabály használatával. Bár ez a modell jól működik, amely lehetővé teszi az egyes gépek hez való hozzáférést a fejlesztési vagy tesztelési számítási feladatokhoz, éles környezetben nehéz kezelni.

A Privát kapcsolat segítségével engedélyezheti a magánvégponthoz való hozzáférést az [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), a privát társviszony-létesítés vagy a [VPN-alagút](https://docs.microsoft.com/azure/vpn-gateway/)használatával. Ezt követően letilthatják az összes nyilvános végponton keresztüli hozzáférést, és nem használhatják az IP-alapú tűzfalat.

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Privát hivatkozás konfigurálása az Azure Database számára a PostgreSQL egykiszolgálós kiszolgálóhoz

### <a name="creation-process"></a>Létrehozási folyamat

A privát végpontok szükségesek a privát kapcsolat engedélyezéséhez. Ezt az alábbi útmutatók segítségével teheti meg.

* [Azure-portál](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal)
* [parancssori felület](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Jóváhagyási folyamat
Miután a hálózati rendszergazda létrehozza a privát végpontot (PE), a PostgreSQL-rendszergazda kezelheti a privát végpont kapcsolat (PEC) az Azure Database for PostgreSQL. A hálózati rendszergazda és a DBA közötti feladatok elkülönítése hasznos az Azure Database for PostgreSQL-kapcsolat felügyeletéhez. 

* Keresse meg az Azure Database for PostgreSQL kiszolgálóerőforrást az Azure Portalon. 
    * A saját végpontkapcsolatok kijelölése a bal oldali ablaktáblában
    * Az összes magánvégpont-kapcsolat (PEC) listájának megjelenítése
    * Megfelelő magánvégpont (PE) létrehozva

![a privát végpontportál kiválasztása](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Válasszon ki egy egyedi PEC-et a listából annak kiválasztásával.

![jóváhagyásra váró privát végpont kiválasztása](media/concepts-data-access-and-security-private-link/select-private-link.png)

* A PostgreSQL szerver adminisztrátora dönthet úgy, hogy jóváhagyja vagy elutasítja a PEC-et, és opcionálisan rövid szöveges választ ad hozzá.

![a privát végpontüzenet kijelölése](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* A jóváhagyást vagy elutasítást követően a lista a megfelelő állapotot és a válaszszöveget

![a privát végpont végső állapotának kiválasztása](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>A PostgreSQL-hez készült Privát hivatkozás használati esetei

Az ügyfelek csatlakozhatnak a privát végponthoz ugyanarról a virtuális hálózatról, társviszony-létesített virtuális hálózat ugyanabban a régióban, vagy a virtuális hálózat és a virtuális hálózat közötti kapcsolat on régiók között. Emellett az ügyfelek a helyszíni kapcsolatfelvétel expressroute, privát társviszony-létesítés vagy VPN-bújtatás használatával csatlakozhatnak. Az alábbiakban egy egyszerűsített diagram mutatja be a gyakori használati eseteket.

![a privát végpont áttekintésének kiválasztása](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Csatlakozás egy Azure virtuális gépről társviszonyban lévő virtuális hálózatban (VNet)
Konfigurálja [a virtuális hálózat társviszony-létesítését](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) az Azure Database for PostgreSQL - Single server kapcsolat létrehozásához egy Azure virtuális gépről egy társviszony-létesített virtuális hálózatban.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Csatlakozás egy Azure-beli virtuális gépről virtuális hálózat és virtuális hálózat környezetben
Konfigurálja [a VNet-vNet VPN-átjárókapcsolatot,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) hogy kapcsolatot létesítsen egy Azure-adatbázissal a PostgreSQL- egyetlen kiszolgálóhoz egy azure-beli virtuális gépről egy másik régióban vagy előfizetésben.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Csatlakozás helyszíni környezetből VPN-en keresztül
A helyszíni környezetből a PostgreSQL-adatbázissal – egykiszolgálós kiszolgálóra való kapcsolódás létrehozásához válasszon és valósítson meg egy lehetőséget:

* [Pont-hely kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Helyek közötti VPN-kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute-kapcsolat](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privát kapcsolat tűzfalszabályokkal kombinálva

A következő helyzetek és eredmények akkor lehetségesek, ha a Privát kapcsolat szolgáltatást tűzfalszabályokkal kombinálja:

* Ha nem konfigurál ja a tűzfalszabályokat, akkor alapértelmezés szerint egyetlen forgalom sem lesz képes hozzáférni az Azure Database for PostgreSQL Single server szolgáltatáshoz.

* Ha konfigurálja a nyilvános forgalmat vagy egy szolgáltatásvégpontot, és privát végpontokat hoz létre, akkor a megfelelő típusú tűzfalszabály különböző típusú bejövő forgalmat engedélyez.

* Ha nem konfigurál semmilyen nyilvános forgalmat vagy szolgáltatásvégpontot, és privát végpontokat hoz létre, majd a PostgreSQL egyetlen kiszolgálóhoz készült Azure Database csak a privát végpontokon keresztül érhető el. Ha nem konfigurálja a nyilvános forgalmat vagy a szolgáltatás végpontját, miután az összes jóváhagyott privát végpontot elutasították vagy törölték, egyetlen forgalom sem férhet hozzá az Azure Database for PostgreSQL Single server hez.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Nyilvános hozzáférés megtagadása az Azure Database for PostgreSQL Single server szolgáltatáshoz

Ha csak privát végpontokra szeretne támaszkodni az Azure Database for PostgreSQL Single server eléréséhez, letilthatja az összes nyilvános végpont([tűzfalszabályok](concepts-firewall-rules.md) és [VNet szolgáltatásvégpontok)](concepts-data-access-and-security-vnet.md)beállítását az adatbázis-kiszolgáló **nyilvános hálózati hozzáférésének megtagadása** konfigurációjának beállításával. 

Ha ez a beállítás *IGEN* beállítás, csak a privát végpontokon keresztüli kapcsolatok engedélyezettek az Azure-adatbázis postgreSQL-hez. Ha ez a beállítás van beállítva, hogy *nincs* ügyfél csatlakozhat az Azure Database for PostgreSQL tűzfal vagy vnet szolgáltatás végpontbeállítása alapján. Továbbá, ha a magánhálózati hozzáférés értéke az ügyfelekre van állítva, nem adhat hozzá és/vagy nem frissítheti a meglévő "Tűzfalszabályok" és a "Virtuálishálózat-szolgáltatás végpontszabályát"

> [!Note]
> Ez a funkció minden Olyan Azure-régióban elérhető, ahol az Azure Database for PostgreSQL – Az egykiszolgálós verzió támogatja az általános célú és a memóriaoptimalizált tarifacsomagokat.
>
> Ez a beállítás nincs hatással az SSL és TLS konfigurációk az Azure Database for PostgreSQL Single server.

A Nyilvános hálózati **hozzáférés megtagadása** az Azure Database for PostgreSQL Single server szolgáltatáshoz az Azure Portalról című cikkből [megtudhatja: A Nyilvános hálózati hozzáférés megtagadása című](howto-deny-public-network-access.md)cikkben tájékozódhat.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Database for PostgreSQL Single server biztonsági funkcióiról, olvassa el az alábbi cikkeket:

* A PostgreSQL Single server Azure Database tűzfalának konfigurálásához olvassa el a Tűzfal támogatása című [témakört.](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules)

* Ha tudni szeretné, hogyan konfigurálhat virtuális hálózati szolgáltatásvégpontot az Azure Database for PostgreSQL Single server szolgáltatáshoz, olvassa el a [Hozzáférés konfigurálása virtuális hálózatokból című témakört.](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet)

* A PostgreSQL egykiszolgálós kapcsolathoz készült Azure Database for PostgreSQL-kapcsolat áttekintését az [Azure Database for PostgreSQL Connectivity Architecture (Azure Database for PostgreSQL Connectivity Architecture) című témakörben találja.](https://docs.microsoft.com/azure/postgresql/concepts-connectivity-architecture)