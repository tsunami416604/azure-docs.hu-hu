---
title: Azure Database for MariaDB privát hivatkozása (előzetes verzió)
description: Megtudhatja, hogyan működik a privát hivatkozás a Azure Database for MariaDBhoz.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 92d7522c8382ded182c5f482df3f3d917b4b3a14
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982385"
---
# <a name="private-link-for-azure-database-for-mariadb-preview"></a>Azure Database for MariaDB privát hivatkozása (előzetes verzió)

A privát hivatkozás lehetővé teszi, hogy egy privát végponton keresztül kapcsolódjon az Azure-beli különböző Pásti-szolgáltatásokhoz. Az Azure Private link lényegében az Azure-szolgáltatásokat a privát Virtual Networkon (VNet) belül hozza elérhetővé. A Péter-erőforrások a magánhálózati IP-cím használatával ugyanúgy érhetők el, mint a VNet lévő többi erőforráshoz.

A privát kapcsolati funkciót támogató Pásti-szolgáltatások listáját a privát hivatkozás [dokumentációjában](https://docs.microsoft.com/azure/private-link/index)tekintheti meg. A privát végpont egy adott [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) és alhálózaton belüli magánhálózati IP-cím.

> [!NOTE]
> Ez a funkció minden olyan Azure-régióban elérhető, ahol a Azure Database for MariaDB támogatja a általános célú és a memória optimalizált díjszabási szintjeit.

## <a name="data-exfiltration-prevention"></a>Az adatkiszűrése megelőzése

Az adatok Azure Database for MariaDBban való szűrése akkor történik meg, amikor egy jogosult felhasználó, például egy adatbázis-rendszergazda képes az adatok kinyerésére egy rendszerből, és a szervezeten kívül más helyre vagy rendszerre helyezi át. A felhasználó például egy harmadik fél tulajdonában lévő Storage-fiókba helyezi át az adatátvitelt.

Vegye fontolóra egy olyan forgatókönyvet, amely egy MariaDB Workbench alkalmazást futtató felhasználón belül egy Azure Database for MariaDB-példányhoz csatlakozó Azure-beli virtuális gépen fut. Ez a MariaDB-példány az USA nyugati régiójának adatközpontjában található. Az alábbi példa bemutatja, hogyan korlátozható a hozzáférés nyilvános végpontokkal a Azure Database for MariaDB hálózati hozzáférés-vezérlés használatával.

* Tiltsa le a nyilvános végponton keresztül Azure Database for MariaDB összes Azure-szolgáltatás forgalmát az Azure-szolgáltatások kikapcsolásának engedélyezése beállítással. Győződjön meg arról, hogy az IP-címek vagy tartományok nem férhetnek hozzá a kiszolgálóhoz a [Tűzfalszabályok](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules) vagy a [virtuális hálózati szolgáltatás végpontján](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)keresztül.

* Csak a virtuális gép magánhálózati IP-címének használatával engedélyezze a Azure Database for MariaDB forgalmat. További információt a [szolgáltatás-végpont](concepts-data-access-security-vnet.md) és a [VNet-tűzfalszabályok](howto-manage-vnet-portal.md)című cikkben talál.

* Az Azure-beli virtuális gépen a következő módon Szűkítse le a kimenő kapcsolatok hatókörét hálózati biztonsági csoportok (NSG) és szolgáltatás-címkék használatával:

    * Olyan NSG-szabályt ad meg, amely engedélyezi a forgalmat a Service tag = SQL szolgáltatásban. WestUs – csak az USA nyugati régiójában lévő Azure Database for MariaDBhoz való kapcsolódás engedélyezése
    * NSG-szabály megadása (magasabb prioritással) a Service tag adatforgalmának megtagadásához = SQL – a MariaDB-adatbázishoz való csatlakozás megtagadása minden régióban</br></br>

A telepítés végén az Azure-beli virtuális gép csak az USA nyugati régiójában lévő Azure Database for MariaDBhoz tud csatlakozni. Azonban a kapcsolat nem korlátozódik egyetlen Azure Database for MariaDBra. A virtuális gép továbbra is csatlakozhat az USA nyugati régiójában található bármely Azure Database for MariaDBhoz, beleértve azokat az adatbázisokat is, amelyek nem részei az előfizetésnek. Noha a fenti forgatókönyvben lévő adatmennyiséget egy adott régióra csökkentették, nem kiszűrése el teljesen.</br>

A privát hivatkozással mostantól olyan hálózati hozzáférés-vezérlőket állíthat be, mint például a NSG a privát végponthoz való hozzáférés korlátozására. Az egyes Azure-beli Pásti-erőforrások ezután adott privát végpontokra vannak leképezve. A rosszindulatú bennfentesek csak a leképezett (például egy Azure Database for MariaDB) erőforráshoz férnek hozzá, és nincs más erőforrás.

## <a name="on-premises-connectivity-over-private-peering"></a>Helyszíni kapcsolat privát társon keresztül

Amikor a helyi gépekről csatlakozik a nyilvános végponthoz, az IP-címet egy kiszolgálói szintű tűzfalszabály használatával kell hozzáadnia az IP-alapú tűzfalhoz. Habár ez a modell jól működik, hogy lehetővé tegye az egyes gépekhez való hozzáférést a fejlesztési vagy tesztelési feladatokhoz, nehéz felügyelni éles környezetben.

Privát hivatkozással engedélyezheti a létesítmények közötti hozzáférést a privát végponthoz [Express Route](https://azure.microsoft.com/services/expressroute/) (er), privát vagy [VPN-alagút](https://docs.microsoft.com/azure/vpn-gateway/)használatával. Ezt követően a nyilvános végponton keresztül is letilthatják az összes hozzáférést, és nem használják az IP-alapú tűzfalat.

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Privát hivatkozás konfigurálása Azure Database for MariaDBhoz

### <a name="creation-process"></a>Létrehozási folyamat

Privát végpontok szükségesek a privát kapcsolat engedélyezéséhez. Ezt a következő útmutatók segítségével végezheti el.

* [Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal)
* [Parancssori felület](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Jóváhagyási folyamat

Miután a hálózati rendszergazda létrehozta a magánhálózati végpontot (PE), a rendszergazda felügyelheti a magánhálózati végponti kapcsolatokat (PEC) Azure Database for MariaDB.

> [!NOTE]
> Jelenleg Azure Database for MariaDB csak a privát végpont automatikus jóváhagyását támogatja.

* A Azure Portalban navigáljon a Azure Database for MariaDB Server-erőforráshoz. 
    * Válassza ki a privát végponti kapcsolatokat a bal oldali ablaktáblán
    * Az összes privát végponti kapcsolat (Pécs) listáját jeleníti meg
    * Megfelelő titkos végpont (PE) létrehozva

![Válassza ki a privát végpont portált](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Válasszon ki egy egyéni PEC-t a listából.

![Válassza ki a privát végpont jóváhagyását.](media/concepts-data-access-and-security-private-link/select-private-link.png)

* A MariaDB-kiszolgáló rendszergazdája dönthet úgy, hogy jóváhagyja vagy elutasítja a PEC-t, és opcionálisan egy rövid szöveges választ is hozzáadhat.

![Válassza ki a privát végpont üzenetet](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Jóváhagyás vagy elutasítás után a lista a megfelelő állapotot jeleníti meg a válasz szövegével együtt.

![Válassza ki a privát végpont végső állapotát](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Privát hivatkozás használata Azure Database for MariaDB esetén

Az ügyfelek csatlakozhatnak a privát végponthoz ugyanarról a VNet, egymással azonos régióban lévő VNet, illetve a régiók közötti VNet-VNet kapcsolaton keresztül. Emellett az ügyfelek a helyszíni ExpressRoute, a privát vagy a VPN-alagúton keresztül is csatlakozhatnak. Alább látható egy egyszerűsített diagram, amely a gyakori használati eseteket mutatja.

![Válassza ki a privát végpont áttekintését](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Csatlakozás egy Azure-beli virtuális gépről a Virtual Network (VNet)
Konfigurálja a [VNet](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) -társítást úgy, hogy az Azure-beli virtuális gép Azure Database for MariaDB kapcsolatot hozzon létre egy egyenrangú VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Csatlakozás egy Azure-beli virtuális gépről a VNet-VNet-környezetben
A [VNet-to-VNET VPN Gateway-kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) konfigurálása egy másik régióban vagy előfizetésben lévő Azure-beli virtuális gépről létesített Azure Database for MariaDB kapcsolat létesítéséhez.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Csatlakozás helyszíni környezetből VPN-kapcsolaton keresztül
A helyszíni környezet és a Azure Database for MariaDB közötti kapcsolat létesítéséhez válassza ki és implementálja a következő lehetőségek egyikét:

* [Pont – hely kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Helyek közötti VPN-kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute áramkör](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privát hivatkozás a tűzfalszabályok együttes használatával

A következő helyzetek és eredmények akkor lehetségesek, ha a privát hivatkozásokat a tűzfalszabályok kombinálva használják:

* Ha nem konfigurálja a tűzfalszabályok egyikét sem, akkor a rendszer alapértelmezés szerint nem fér hozzá a Azure Database for MariaDBhoz.

* Ha nyilvános forgalmat vagy szolgáltatási végpontot állít be, és privát végpontokat hoz létre, akkor a megfelelő tűzfalszabály engedélyezi a bejövő forgalom különböző típusait.

* Ha nem állít be nyilvános forgalmat vagy szolgáltatási végpontot, és privát végpontokat hoz létre, akkor a Azure Database for MariaDB csak a privát végpontokon keresztül érhető el. Ha nem konfigurálja a nyilvános forgalmat vagy a szolgáltatási végpontot, az összes jóváhagyott privát végpont elutasítása vagy törlése után sem lesz elérhető forgalom a Azure Database for MariaDB.

## <a name="next-steps"></a>Következő lépések

A Azure Database for MariaDB biztonsági funkcióival kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* Ha Azure Database for MariaDB tűzfalat szeretne konfigurálni, tekintse meg a [tűzfal támogatása](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules)című témakört.

* Ha meg szeretné tudni, hogyan konfigurálhatja a virtuális hálózati szolgáltatás végpontját a Azure Database for MariaDB számára, tekintse meg a [virtuális hálózatok elérésének konfigurálása](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)című témakört.

* A Azure Database for MariaDB kapcsolatok áttekintését lásd: [Azure Database for MariaDB kapcsolati architektúra](https://docs.microsoft.com/azure/MariaDB/concepts-connectivity-architecture)
