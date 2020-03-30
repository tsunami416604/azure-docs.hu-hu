---
title: Privát hivatkozás – Azure-adatbázis a MariaDB-hez
description: Ismerje meg, hogyan működik a Privát hivatkozás a MariaDB Azure Database szolgáltatásában.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b05a202537492fe54a76cf40a3b15987e099a7e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367720"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>Privát hivatkozás a MariaDB Azure-adatbázisához

A Private Link lehetővé teszi, hogy privát végpontokat hozzon létre a MariaDB Azure Database számára, és így az Azure-szolgáltatásokat a privát virtuális hálózaton (VNet) belül hozza. A privát végpont egy privát IP-címet tesz elérhetővé, amelya MariaDB adatbázis-kiszolgálóhoz való csatlakozáshoz ugyanúgy csatlakozhat, mint a virtuális hálózat bármely más erőforrásához.

A Private Link funkciót támogató PaaS-szolgáltatások listáját a Private Link [dokumentációjában](https://docs.microsoft.com/azure/private-link/index)tekintheti meg. A privát végpont egy privát IP-cím egy adott [virtuális hálózaton](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) és alhálózaton belül.

> [!NOTE]
> Ez a funkció minden Olyan Azure-régióban elérhető, ahol az Azure Database for MariaDB támogatja az általános célú és a memóriaoptimalizált tarifacsomagokat.

## <a name="data-exfiltration-prevention"></a>Adatszivárgás megelőzése

Az Azure Database for MariaDB-ben az adatok ex-filtration az, amikor egy jogosult felhasználó, például egy adatbázis-rendszergazda, képes adatokat kinyerni az egyik rendszerből, és áthelyezni őket egy másik helyre vagy rendszerre a szervezeten kívül. Például a felhasználó áthelyezi az adatokat egy harmadik fél tulajdonában lévő tárfiókba.

Fontolja meg egy olyan forgatókönyv, amikor egy felhasználó MariaDB munkapadot futtat egy Azure-beli virtuális gépen belül, amely egy Azure-adatbázishoz csatlakozik a MariaDB-példányhoz. Ez a MariaDB-példány az USA nyugati adatközpontjában található. Az alábbi példa bemutatja, hogyan korlátozhatja a hozzáférést a nyilvános végpontok az Azure Database for MariaDB hálózati hozzáférés-vezérlés használatával.

* Tiltsa le az Azure-szolgáltatás for-for-for A MariaDB azure-adatbázisának összes Azure-szolgáltatásforgalmát a nyilvános végponton keresztül az Azure Services kikapcsolása beállításával. Győződjön meg arról, hogy [tűzfalszabályok](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules) vagy virtuális [hálózati szolgáltatás végpontjai](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)segítségével nem férhetnek hozzá a kiszolgálóhoz IP-címek vagy tartományok.

* Csak a virtuális gép privát IP-címe használatával engedélyezze a MariaDB-hez való Azure Database for MariaDB-t. További információt a [Szolgáltatásvégpont](concepts-data-access-security-vnet.md) és a [virtuális hálózat tűzfalszabályairól](howto-manage-vnet-portal.md)szóló cikkektartalmaznak.

* Az Azure virtuális gépen szűkítse le a kimenő kapcsolat hatókörét a hálózati biztonsági csoportok (NSG-k) és a szolgáltatáscímkék használatával az alábbiak szerint:

    * Adjon meg egy NSG-szabályt a Service Tag = SQL forgalom engedélyezéséhez. WestUs - csak a MariaDB Azure Database-hez való csatlakozás engedélyezése az USA nyugati részén
    * NSG-szabály megadása (magasabb prioritással) a Service Tag = SQL forgalom megtagadásához - a MariaDB-adatbázissal való kapcsolatok megtagadása minden régióban</br></br>

A beállítás végén az Azure virtuális gép csak az Azure Database for MariaDB-hez csatlakozhat az USA nyugati régiójában. A kapcsolat azonban nem korlátozódik egyetlen Azure-adatbázis mariadb. A virtuális gép továbbra is csatlakozhat bármely Azure Database for MariaDB az USA nyugati régiójában, beleértve az adatbázisokat, amelyek nem részei az előfizetésnek. Bár a fenti forgatókönyvben az adatok kiszivárgásának hatókörét egy adott régióra csökkentettük, azt nem szüntettük meg teljesen.</br>

A Private Link segítségével mostmár beállíthatja a hálózati hozzáférés-vezérléseket, például az NSG-ket, hogy korlátozza a privát végponthoz való hozzáférést. Az egyes Azure PaaS-erőforrások ezután adott privát végpontokhoz vannak rendelve. Egy rosszindulatú bennfentes csak a leképezett PaaS-erőforráshoz férhet hozzá (például egy Azure-adatbázis a MariaDB-hez), és nincs más erőforrás.

## <a name="on-premises-connectivity-over-private-peering"></a>Helyszíni kapcsolat a privát társviszony-létesítésen

Amikor a nyilvános végponthoz helyszíni gépekről csatlakozik, az IP-címet hozzá kell adni az IP-szintű tűzfalhoz egy kiszolgálószintű tűzfalszabály használatával. Bár ez a modell jól működik, amely lehetővé teszi az egyes gépek hez való hozzáférést a fejlesztési vagy tesztelési számítási feladatokhoz, éles környezetben nehéz kezelni.

A Privát kapcsolat segítségével engedélyezheti a magánvégponthoz való hozzáférést az [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), a privát társviszony-létesítés vagy a [VPN-alagút](https://docs.microsoft.com/azure/vpn-gateway/)használatával. Ezt követően letilthatják az összes nyilvános végponton keresztüli hozzáférést, és nem használhatják az IP-alapú tűzfalat.

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Privát kapcsolat konfigurálása az Azure-adatbázishoz a MariaDB-hez

### <a name="creation-process"></a>Létrehozási folyamat

A privát végpontok szükségesek a privát kapcsolat engedélyezéséhez. Ezt az alábbi útmutatók segítségével teheti meg.

* [Azure-portál](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal)
* [parancssori felület](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Jóváhagyási folyamat

Miután a hálózati rendszergazda létrehozza a privát végpontot (PE), a rendszergazda kezelheti a privát végpont kapcsolat (PEC) az Azure Database for MariaDB. A hálózati rendszergazda és a DBA közötti feladatok elkülönítése hasznos az Azure-adatbázis MariaDB-kapcsolat kezelésére. 

* Keresse meg az Azure Database for MariaDB kiszolgálói erőforrást az Azure Portalon. 
    * A saját végpontkapcsolatok kijelölése a bal oldali ablaktáblában
    * Az összes magánvégpont-kapcsolat (PEC) listájának megjelenítése
    * Megfelelő magánvégpont (PE) létrehozva

![a privát végpontportál kiválasztása](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Válasszon ki egy egyedi PEC-et a listából annak kiválasztásával.

![jóváhagyásra váró privát végpont kiválasztása](media/concepts-data-access-and-security-private-link/select-private-link.png)

* A MariaDB kiszolgáló rendszergazdája jóváhagyhatja vagy elutasíthatja a PEC-et, és szükség esetén rövid szöveges választ adhat hozzá.

![a privát végpontüzenet kijelölése](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* A jóváhagyást vagy elutasítást követően a lista a megfelelő állapotot és a válaszszöveget

![a privát végpont végső állapotának kiválasztása](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>A MariaDB Azure-adatbázishoz használt privát hivatkozás használati esetei

Az ügyfelek csatlakozhatnak a privát végponthoz ugyanarról a virtuális hálózatról, társviszony-létesített virtuális hálózat ugyanabban a régióban, vagy a virtuális hálózat és a virtuális hálózat közötti kapcsolat on régiók között. Emellett az ügyfelek a helyszíni kapcsolatfelvétel expressroute, privát társviszony-létesítés vagy VPN-bújtatás használatával csatlakozhatnak. Az alábbiakban egy egyszerűsített diagram mutatja be a gyakori használati eseteket.

![a privát végpont áttekintésének kiválasztása](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Csatlakozás egy Azure virtuális gépről társviszonyban lévő virtuális hálózatban (VNet)
Konfigurálja [a virtuális hálózat társviszony-létesítési](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) kapcsolatot az Azure Database for MariaDB egy Azure virtuális gép egy társviszony-létesített virtuális hálózat.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Csatlakozás egy Azure-beli virtuális gépről virtuális hálózat és virtuális hálózat környezetben
Konfigurálja [a VNet-vNet VPN-átjárókapcsolatot,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) és hozzon létre kapcsolatot egy Azure-adatbázissal a MariaDB számára egy másik régióban vagy előfizetésben lévő Azure-virtuális gépről.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Csatlakozás helyszíni környezetből VPN-en keresztül
A helyszíni környezetés a MariaDB Azure Database közötti kapcsolat létrehozásához válasszon és valósítson meg egy lehetőséget:

* [Pont-hely kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Helyek közötti VPN-kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute-kapcsolat](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privát kapcsolat tűzfalszabályokkal kombinálva

A következő helyzetek és eredmények akkor lehetségesek, ha a Privát kapcsolat szolgáltatást tűzfalszabályokkal kombinálja:

* Ha nem konfigurál ja a tűzfalszabályokat, akkor alapértelmezés szerint egyetlen forgalom sem lesz képes hozzáférni a MariaDB Azure-adatbázisához.

* Ha konfigurálja a nyilvános forgalmat vagy egy szolgáltatásvégpontot, és privát végpontokat hoz létre, akkor a megfelelő típusú tűzfalszabály különböző típusú bejövő forgalmat engedélyez.

* Ha nem konfigurál semmilyen nyilvános forgalmat vagy szolgáltatásvégpontot, és privát végpontokat hoz létre, majd a MariaDB Azure-adatbázis a privát végpontokon keresztül érhető el. Ha nem konfigurálja a nyilvános forgalmat vagy a szolgáltatás végpontját, miután az összes jóváhagyott magánvégpontot elutasították vagy törölték, egyetlen forgalom sem férhet hozzá a MariaDB-hez az Azure Database for MariaDB-hez.

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>Nyilvános hozzáférés megtagadása a MariaDB Azure Database szolgáltatásához

Ha teljesen csak a privát végpontokra szeretne támaszkodni a MariaDB Azure-adatbázisának eléréséhez, letilthatja az összes nyilvános végpont ([tűzfalszabályok](concepts-firewall-rules.md) és [virtuális hálózati végpontok](concepts-data-access-security-vnet.md)) beállítását a **Nyilvános hálózati hozzáférés megtagadása** konfiguráció az adatbázis-kiszolgálón. 

Ha ez a beállítás IGEN, csak a privát végpontokon keresztüli kapcsolatok engedélyezettek az Azure-adatbázis mariadb.When this setting is set to *YES,* only connections via private endpoints are allowed to your Azure Database for MariaDB. Ha ez a beállítás *NEM,* az ügyfelek csatlakozhatnak az Azure Database for MariaDB-hez a tűzfal vagy a Virtuálishálózat-szolgáltatás végpontbeállításai alapján. Továbbá, ha a magánhálózati hozzáférés értéke be van állítva, nem adhat hozzá és/vagy nem frissítheti a meglévő tűzfal- és virtuálishálózat-szolgáltatásvégpont-szabályokat.

> [!Note]
> Ez a funkció minden Olyan Azure-régióban elérhető, ahol az Azure Database for PostgreSQL – Az egykiszolgálós verzió támogatja az általános célú és a memóriaoptimalizált tarifacsomagokat.
>
> Ez a beállítás nincs hatással az SSL és TLS konfigurációk az Azure Database for MariaDB.

A Nyilvános hálózati **hozzáférés megtagadása** az Azure-adatbázishoz az Azure Portalon keresztül a Nyilvános hálózati hozzáférés megtagadása című cikkből megtudhatja: [A nyilvános hálózati hozzáférés megtagadásának konfigurálása](howto-deny-public-network-access.md)című részében.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Database for MariaDB biztonsági funkcióiról, olvassa el az alábbi cikkeket:

* A MariaDB Azure Database tűzfalának konfigurálásához olvassa el a Tűzfal támogatása című [témakört.](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules)

* A virtuális hálózati szolgáltatás végpontjának konfigurálásáról a MariaDB Azure Database szolgáltatásához a [Hozzáférés konfigurálása a virtuális hálózatokról](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)című témakörben olvashat.

* Az Azure Database for MariaDB-kapcsolat áttekintését az [Azure Database for MariaDB connectivity](https://docs.microsoft.com/azure/MariaDB/concepts-connectivity-architecture)
