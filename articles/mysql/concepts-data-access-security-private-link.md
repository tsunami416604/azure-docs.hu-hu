---
title: Privát hivatkozás – Azure-adatbázis a MySQL-hez
description: Ismerje meg, hogyan működik a Privát hivatkozás az Azure Database for MySQL-hez.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c2cc4986542404281424286882c046dec39f5daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371290"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Privát hivatkozás a MySQL Azure-adatbázisához

A Private Link lehetővé teszi, hogy egy privát végponton keresztül csatlakozzon különböző PaaS-szolgáltatásokhoz az Azure-ban. Az Azure Private Link lényegében a privát virtuális hálózaton (VNet) belül hozza az Azure-szolgáltatásokat. A PaaS-erőforrások a privát IP-cím használatával érhető el, mint bármely más erőforrás a virtuális hálózatban.

A Private Link funkciót támogató PaaS-szolgáltatások listáját a Private Link [dokumentációjában](https://docs.microsoft.com/azure/private-link/index)tekintheti meg. A privát végpont egy privát IP-cím egy adott [virtuális hálózaton](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) és alhálózaton belül.

> [!NOTE]
> Ez a funkció minden Olyan Azure-régióban elérhető, ahol az Azure Database for MySQL támogatja az általános célú és a memóriaoptimalizált tarifacsomagokat.

## <a name="data-exfiltration-prevention"></a>Adatszivárgás megelőzése

Az Azure Database for MySQL-ben az adatok ex-filtration az, amikor egy jogosult felhasználó, például egy adatbázis-rendszergazda, képes adatokat kinyerni az egyik rendszerből, és áthelyezni őket egy másik helyre vagy rendszerre a szervezeten kívül. Például a felhasználó áthelyezi az adatokat egy harmadik fél tulajdonában lévő tárfiókba.

Fontolja meg egy olyan forgatókönyv, amelyben egy felhasználó egy Azure virtuális gépen (VM) belül futtatja a MySQL-munkaterületet, amely az USA nyugati részén kiépített Azure-adatbázishoz csatlakozik. Az alábbi példa bemutatja, hogyan korlátozhatja a hozzáférést a nyilvános végpontok az Azure Database for MySQL hálózati hozzáférés-vezérlés használatával.

* Tiltsa le az Azure-szolgáltatás for Tesi a nyilvános végponton keresztül az Azure Database for MySQL összes Azure-szolgáltatási forgalmát az *Azure Services* kikapcsolása beállításával. Győződjön meg arról, hogy [tűzfalszabályok](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) vagy virtuális [hálózati szolgáltatás végpontjai](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)segítségével nem férhetnek hozzá a kiszolgálóhoz IP-címek vagy tartományok.

* Csak a virtuális gép privát IP-címét használó Azure Database for MySQL forgalmat engedélyezze. További információt a [Szolgáltatásvégpont](concepts-data-access-and-security-vnet.md) és a [virtuális hálózat tűzfalszabályairól](howto-manage-vnet-using-portal.md)szóló cikkektartalmaznak.

* Az Azure virtuális gép, szűkítse le a kimenő kapcsolat hatókörét a hálózati biztonsági csoportok (NSG-k) és a szolgáltatáscímkék az alábbiak szerint

    * Adjon meg egy NSG-szabályt a Service Tag = SQL forgalom *engedélyezéséhez. WestUs* - csak az Azure Database for MySQL-hez való csatlakozás engedélyezése az USA nyugati részén
    * NSG-szabály megadása (magasabb prioritással) a *Service Tag = SQL* forgalom megtagadásához - a MySQL Azure-adatbázisához való frissítéshez való kapcsolatok megtagadása minden régióban</br></br>

A beállítás végén az Azure virtuális gép csak az Azure Database for MySQL-hez csatlakozhat az USA nyugati régiójában. A kapcsolat azonban nem korlátozódik egyetlen Azure-adatbázis a MySQL.However, the connectivity isn't restricted to a single Azure Database for MySQL. A virtuális gép továbbra is csatlakozhat bármely Azure Database for MySQL az USA nyugati régiójában, beleértve az adatbázisokat, amelyek nem részei az előfizetésnek. Bár a fenti forgatókönyvben az adatok kiszivárgásának hatókörét egy adott régióra csökkentettük, azt nem szüntettük meg teljesen.</br>

A Private Link segítségével mostmár beállíthatja a hálózati hozzáférés-vezérléseket, például az NSG-ket, hogy korlátozza a privát végponthoz való hozzáférést. Az egyes Azure PaaS-erőforrások ezután adott privát végpontokhoz vannak rendelve. Egy rosszindulatú bennfentes csak a leképezett PaaS-erőforráshoz férhet hozzá (például egy Azure Database for MySQL)- és más erőforrást.

## <a name="on-premises-connectivity-over-private-peering"></a>Helyszíni kapcsolat a privát társviszony-létesítésen

Amikor a nyilvános végponthoz helyszíni gépekről csatlakozik, az IP-címet hozzá kell adni az IP-szintű tűzfalhoz egy kiszolgálószintű tűzfalszabály használatával. Bár ez a modell jól működik, amely lehetővé teszi az egyes gépek hez való hozzáférést a fejlesztési vagy tesztelési számítási feladatokhoz, éles környezetben nehéz kezelni.

A Privát kapcsolat segítségével engedélyezheti a magánvégponthoz való hozzáférést az [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), a privát társviszony-létesítés vagy a [VPN-alagút](https://docs.microsoft.com/azure/vpn-gateway/)használatával. Ezt követően letilthatják az összes nyilvános végponton keresztüli hozzáférést, és nem használhatják az IP-alapú tűzfalat.

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Privát hivatkozás konfigurálása az Azure Database for MySQL-hez

### <a name="creation-process"></a>Létrehozási folyamat

A privát végpontok szükségesek a privát kapcsolat engedélyezéséhez. Ezt az alábbi útmutatók segítségével teheti meg.

* [Azure-portál](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [parancssori felület](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Jóváhagyási folyamat
Miután a hálózati rendszergazda létrehozza a privát végpontot (PE), a MySQL-rendszergazda kezelheti a privát végpont kapcsolat (PEC) az Azure Database for MySQL. A hálózati rendszergazda és a DBA közötti feladatok elkülönítése hasznos az Azure Database for MySQL-kapcsolat felügyeletéhez. 

* Keresse meg az Azure Database for MySQL-kiszolgálóerőforrást az Azure Portalon. 
    * A saját végpontkapcsolatok kijelölése a bal oldali ablaktáblában
    * Az összes magánvégpont-kapcsolat (PEC) listájának megjelenítése
    * Megfelelő magánvégpont (PE) létrehozva

![a privát végpontportál kiválasztása](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Válasszon ki egy egyedi PEC-et a listából annak kiválasztásával.

![jóváhagyásra váró privát végpont kiválasztása](media/concepts-data-access-and-security-private-link/select-private-link.png)

* A MySQL szerver adminisztrátora jóváhagyhatja vagy elutasíthatja a PEC-et, és opcionálisan hozzáadhat egy rövid szöveges választ.

![a privát végpontüzenet kijelölése](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* A jóváhagyást vagy elutasítást követően a lista a megfelelő állapotot és a válaszszöveget

![a privát végpont végső állapotának kiválasztása](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>A MySQL-hez készült Privát hivatkozás az Azure Database számára

Az ügyfelek csatlakozhatnak a privát végponthoz ugyanarról a virtuális hálózatról, társviszony-létesített virtuális hálózat ugyanabban a régióban, vagy a virtuális hálózat és a virtuális hálózat közötti kapcsolat on régiók között. Emellett az ügyfelek a helyszíni kapcsolatfelvétel expressroute, privát társviszony-létesítés vagy VPN-bújtatás használatával csatlakozhatnak. Az alábbiakban egy egyszerűsített diagram mutatja be a gyakori használati eseteket.

![a privát végpont áttekintésének kiválasztása](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Csatlakozás egy Azure virtuális gépről társviszonyban lévő virtuális hálózatban (VNet)
Konfigurálja [a virtuális hálózat társviszony-létesítési](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) kapcsolatot az Azure Database for MySQL egy Azure virtuális gép egy társviszony-létesített virtuális hálózat.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Csatlakozás egy Azure-beli virtuális gépről virtuális hálózat és virtuális hálózat környezetben
Konfigurálja [a VNet-vNet VPN-átjárókapcsolatot,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) és hozzon létre kapcsolatot egy Azure-adatbázismySQL-hez egy Azure virtuális gépről egy másik régióban vagy előfizetésben.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Csatlakozás helyszíni környezetből VPN-en keresztül
A helyszíni környezetés a MySQL Azure-adatbázis közötti kapcsolat létrehozásához válasszon és valósítson meg egy lehetőséget:

* [Pont-hely kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Helyek közötti VPN-kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute-kapcsolat](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privát kapcsolat tűzfalszabályokkal kombinálva

A következő helyzetek és eredmények akkor lehetségesek, ha a Privát kapcsolat szolgáltatást tűzfalszabályokkal kombinálja:

* Ha nem konfigurálja a tűzfalszabályokat, akkor alapértelmezés szerint egyetlen forgalom sem lesz képes hozzáférni az Azure Database for MySQL-hez.

* Ha konfigurálja a nyilvános forgalmat vagy egy szolgáltatásvégpontot, és privát végpontokat hoz létre, akkor a megfelelő típusú tűzfalszabály különböző típusú bejövő forgalmat engedélyez.

* Ha nem konfigurál semmilyen nyilvános forgalmat vagy szolgáltatásvégpontot, és privát végpontokat hoz létre, majd az Azure Database for MySQL csak a privát végpontokon keresztül érhető el. Ha nem konfigurálja a nyilvános forgalmat vagy a szolgáltatás végpontját, miután az összes jóváhagyott privát végpontot elutasították vagy törölték, egyetlen forgalom sem lesz képes hozzáférni az Azure Database for MySQL-hez.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Nyilvános hozzáférés megtagadása a MySQL Azure Database szolgáltatásához

Ha csak a mySQL-alapú Azure-adatbázis eléréséhez csak a privát végpontokra szeretne támaszkodni, letilthatja az összes nyilvános végpont (azaz [a tűzfalszabályok](concepts-firewall-rules.md) és a [virtuális hálózat végpontjai)](concepts-data-access-and-security-vnet.md)beállítását az adatbázis-kiszolgáló **nyilvános hálózati hozzáférés ének megtagadása** konfigurációjának beállításával. 

Ha ez a beállítás *IGEN,* csak a privát végpontokon keresztüli kapcsolatok engedélyezettek az Azure Database for MySQL-hez. Ha ez a beállítás *NEM,* az ügyfelek csatlakozhatnak az Azure Database for MySQL a tűzfal vagy a Virtuálishálózat-szolgáltatás végpontbeállításai alapján. Továbbá, ha a magánhálózati hozzáférés értéke be van állítva, nem adhat hozzá és/vagy nem frissítheti a meglévő tűzfal- és virtuálishálózat-szolgáltatásvégpont-szabályokat.

> [!Note]
> Ez a funkció minden Olyan Azure-régióban elérhető, ahol az Azure Database for PostgreSQL – Az egykiszolgálós verzió támogatja az általános célú és a memóriaoptimalizált tarifacsomagokat.
>
> Ez a beállítás nincs hatással az SSL és TLS konfigurációk az Azure Database for MySQL.

A Nyilvános hálózati hozzáférés megtagadása az Azure Database for MySQL azure-beli portálról **történő megtagadásáról** a Nyilvános hálózati hozzáférés megtagadása című cikkből megtudhatja, hogy miként állíthatja be a [nyilvános hálózati hozzáférés megtagadását.](howto-deny-public-network-access.md)

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Database for MySQL biztonsági funkcióiról, olvassa el az alábbi cikkeket:

* Az Azure Database for MySQL tűzfalának konfigurálásához olvassa el a Tűzfal támogatása című [témakört.](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)

* Ha tudni szeretné, hogyan konfigurálhat virtuális hálózati szolgáltatásvégpontot az Azure Database for MySQL-hez, olvassa el a [Hozzáférés konfigurálása virtuális hálózatokból című témakört.](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)

* Az Azure Database for MySQL-kapcsolat áttekintését az [Azure Database for MySQL connectivity (Azure Database for MySQL Connectivity Architecture) című témakörben találja.](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture)
