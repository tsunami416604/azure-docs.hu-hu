---
title: Privát hivatkozás – Azure Database for MySQL
description: Megtudhatja, hogyan működik a privát hivatkozás a Azure Database for MySQLhoz.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: f780bf946e81e9873a1828f9d697f69c81cef513
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509321"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Privát hivatkozás a Azure Database for MySQL

A privát hivatkozás lehetővé teszi, hogy egy privát végponton keresztül kapcsolódjon az Azure-beli különböző Pásti-szolgáltatásokhoz. Az Azure Private link lényegében az Azure-szolgáltatásokat a privát Virtual Networkon (VNet) belül hozza elérhetővé. A Péter-erőforrások a magánhálózati IP-cím használatával ugyanúgy érhetők el, mint a VNet lévő többi erőforráshoz.

A privát kapcsolati funkciót támogató Pásti-szolgáltatások listáját a privát hivatkozás [dokumentációjában](https://docs.microsoft.com/azure/private-link/index)tekintheti meg. A privát végpont egy adott [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) és alhálózaton belüli magánhálózati IP-cím.

> [!NOTE]
> Ez a funkció minden olyan Azure-régióban elérhető, ahol a Azure Database for MySQL támogatja a általános célú és a memória optimalizált díjszabási szintjeit.

## <a name="data-exfiltration-prevention"></a>Az adatkiszűrése megelőzése

Az adatok Azure Database for MySQLban való szűrése akkor történik meg, amikor egy jogosult felhasználó, például egy adatbázis-rendszergazda képes az adatok kinyerésére egy rendszerből, és a szervezeten kívül más helyre vagy rendszerre helyezi át. A felhasználó például egy harmadik fél tulajdonában lévő Storage-fiókba helyezi át az adatátvitelt.

Vegyünk például egy olyan forgatókönyvet, amely egy MySQL Workbench alkalmazást futtat egy Azure-beli virtuális gépen (VM), amely az USA nyugati régiójában kiépített Azure Database for MySQL kiszolgálóhoz csatlakozik. Az alábbi példa bemutatja, hogyan korlátozható a hozzáférés nyilvános végpontokkal a Azure Database for MySQL hálózati hozzáférés-vezérlés használatával.

* Tiltsa le a nyilvános végponton keresztül Azure Database for MySQL összes Azure-szolgáltatás forgalmát az *Azure-szolgáltatások* kikapcsolásának engedélyezése beállítással. Győződjön meg arról, hogy az IP-címek vagy tartományok nem férhetnek hozzá a kiszolgálóhoz a [Tűzfalszabályok](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) vagy a [virtuális hálózati szolgáltatás végpontján](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)keresztül.

* Csak a virtuális gép magánhálózati IP-címének használatával engedélyezze a Azure Database for MySQL forgalmat. További információt a [szolgáltatás-végpont](concepts-data-access-and-security-vnet.md) és a [VNet-tűzfalszabályok](howto-manage-vnet-using-portal.md)című cikkben talál.

* Az Azure-beli virtuális gépen a következő módon Szűkítse le a kimenő kapcsolatok hatókörét hálózati biztonsági csoportok (NSG) és szolgáltatás-címkék használatával.

    * Olyan NSG-szabályt ad meg, amely engedélyezi a forgalmat a *Service tag = SQL szolgáltatásban. WestUs* – csak az USA nyugati régiójában lévő Azure Database for MySQLhoz való kapcsolódás engedélyezése
    * NSG-szabály megadása (magasabb prioritással) a *Service Azure Database for MySQL tag* adatforgalmának megtagadásához</br></br>

A telepítés végén az Azure-beli virtuális gép csak az USA nyugati régiójában lévő Azure Database for MySQLhoz tud csatlakozni. Azonban a kapcsolat nem korlátozódik egyetlen Azure Database for MySQLra. A virtuális gép továbbra is csatlakozhat az USA nyugati régiójában található bármely Azure Database for MySQLhoz, beleértve azokat az adatbázisokat is, amelyek nem részei az előfizetésnek. Noha a fenti forgatókönyvben lévő adatmennyiséget egy adott régióra csökkentették, nem kiszűrése el teljesen.</br>

A privát hivatkozással mostantól olyan hálózati hozzáférés-vezérlőket állíthat be, mint például a NSG a privát végponthoz való hozzáférés korlátozására. Az egyes Azure-beli Pásti-erőforrások ezután adott privát végpontokra vannak leképezve. A rosszindulatú bennfentesek csak a leképezett (például egy Azure Database for MySQL) erőforráshoz férnek hozzá, és nincs más erőforrás.

## <a name="on-premises-connectivity-over-private-peering"></a>Helyszíni kapcsolat privát társon keresztül

Amikor a helyi gépekről csatlakozik a nyilvános végponthoz, az IP-címet egy kiszolgálói szintű tűzfalszabály használatával kell hozzáadnia az IP-alapú tűzfalhoz. Habár ez a modell jól működik, hogy lehetővé tegye az egyes gépekhez való hozzáférést a fejlesztési vagy tesztelési feladatokhoz, nehéz felügyelni éles környezetben.

Privát hivatkozással engedélyezheti a létesítmények közötti hozzáférést a privát végponthoz [Express Route](https://azure.microsoft.com/services/expressroute/) (er), privát vagy [VPN-alagút](https://docs.microsoft.com/azure/vpn-gateway/)használatával. Ezt követően a nyilvános végponton keresztül is letilthatják az összes hozzáférést, és nem használják az IP-alapú tűzfalat.

> [!NOTE]
> Bizonyos esetekben a Azure Database for MySQL és a VNet különböző előfizetésekben találhatók. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
> - Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. DBforMySQL** erőforrás-szolgáltatónál. További információ: [Resource-Manager-regisztráció][resource-manager-portal]

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Privát hivatkozás konfigurálása Azure Database for MySQLhoz

### <a name="creation-process"></a>Létrehozási folyamat

Privát végpontok szükségesek a privát kapcsolat engedélyezéséhez. Ezt a következő útmutatók segítségével végezheti el.

* [Azure Portal](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [Parancssori felület](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Jóváhagyási folyamat
Miután a hálózati rendszergazda létrehozta a magánhálózati végpontot (PE), a MySQL-rendszergazda felügyelheti a magánhálózati végponti kapcsolatokat (PEC) Azure Database for MySQL. A hálózati rendszergazda és a DBA közötti feladatok elkülönítése hasznos lehet az Azure Database for MySQL-kapcsolat kezeléséhez. 

* A Azure Portalban navigáljon a Azure Database for MySQL Server-erőforráshoz. 
    * Válassza ki a privát végponti kapcsolatokat a bal oldali ablaktáblán
    * Az összes privát végponti kapcsolat (Pécs) listáját jeleníti meg
    * Megfelelő titkos végpont (PE) létrehozva

![Válassza ki a privát végpont portált](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Válasszon ki egy egyéni PEC-t a listából.

![Válassza ki a privát végpont jóváhagyását.](media/concepts-data-access-and-security-private-link/select-private-link.png)

* A MySQL-kiszolgáló rendszergazdája dönthet úgy, hogy jóváhagyja vagy elutasítja a PEC-t, és opcionálisan egy rövid szöveges választ is hozzáadhat.

![Válassza ki a privát végpont üzenetet](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Jóváhagyás vagy elutasítás után a lista a megfelelő állapotot jeleníti meg a válasz szövegével együtt.

![Válassza ki a privát végpont végső állapotát](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Privát hivatkozás használata Azure Database for MySQL esetén

Az ügyfelek csatlakozhatnak a privát végponthoz ugyanarról a VNet, egymással azonos régióban lévő VNet, illetve a régiók közötti VNet-VNet kapcsolaton keresztül. Emellett az ügyfelek a helyszíni ExpressRoute, a privát vagy a VPN-alagúton keresztül is csatlakozhatnak. Alább látható egy egyszerűsített diagram, amely a gyakori használati eseteket mutatja.

![Válassza ki a privát végpont áttekintését](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Csatlakozás egy Azure-beli virtuális gépről a Virtual Network (VNet)
Konfigurálja a [VNet](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) -társítást úgy, hogy az Azure-beli virtuális gép Azure Database for MySQL kapcsolatot hozzon létre egy egyenrangú VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Csatlakozás egy Azure-beli virtuális gépről a VNet-VNet-környezetben
A [VNet-to-VNET VPN Gateway-kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) konfigurálása egy másik régióban vagy előfizetésben lévő Azure-beli virtuális gépről létesített Azure Database for MySQL kapcsolat létesítéséhez.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Csatlakozás helyszíni környezetből VPN-kapcsolaton keresztül
A helyszíni környezet és a Azure Database for MySQL közötti kapcsolat létesítéséhez válassza ki és implementálja a következő lehetőségek egyikét:

* [Pont – hely kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Helyek közötti VPN-kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute áramkör](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Privát hivatkozás a tűzfalszabályok együttes használatával

A következő helyzetek és eredmények akkor lehetségesek, ha a privát hivatkozásokat a tűzfalszabályok kombinálva használják:

* Ha nem konfigurálja a tűzfalszabályok egyikét sem, akkor a rendszer alapértelmezés szerint nem fér hozzá a Azure Database for MySQLhoz.

* Ha nyilvános forgalmat vagy szolgáltatási végpontot állít be, és privát végpontokat hoz létre, akkor a megfelelő tűzfalszabály engedélyezi a bejövő forgalom különböző típusait.

* Ha nem állít be nyilvános forgalmat vagy szolgáltatási végpontot, és privát végpontokat hoz létre, akkor a Azure Database for MySQL csak a privát végpontokon keresztül érhető el. Ha nem konfigurálja a nyilvános forgalmat vagy a szolgáltatási végpontot, az összes jóváhagyott privát végpont elutasítása vagy törlése után sem lesz elérhető forgalom a Azure Database for MySQL.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Azure Database for MySQL nyilvános hozzáférésének megtagadása

Ha csak privát végpontokon szeretné használni a Azure Database for MySQL elérését, letilthatja az összes nyilvános végpont (például a [Tűzfalszabályok](concepts-firewall-rules.md) és a [VNet-végpontok](concepts-data-access-and-security-vnet.md)) beállítását az adatbázis-kiszolgálón a **nyilvános hálózati hozzáférési konfiguráció megtagadása** beállítás megadásával. 

Ha ezt a beállítást az *Igen*értékre állítja, csak a magánhálózati végpontokon keresztül létesített kapcsolatok engedélyezettek a Azure Database for MySQL. Ha ez a beállítás *nem*értékre van állítva, akkor az ügyfelek a tűzfal vagy a VNet szolgáltatás végpontjának beállításai alapján kapcsolódhatnak a Azure Database for MySQLhoz. Emellett, ha a magánhálózati hozzáférés értéke be van állítva, az ügyfelek nem tudják felvenni és/vagy frissíteni a meglévő "tűzfalszabályok" és "VNet szolgáltatás végpontjának szabályait".

> [!Note]
> Ez a funkció minden olyan Azure-régióban elérhető, ahol a Azure Database for PostgreSQL-Single Server támogatja a általános célú és a memóriára optimalizált díjszabási szintet.
>
> Ez a beállítás nem befolyásolja a Azure Database for MySQL SSL-és TLS-konfigurációit.

Ha meg szeretné tudni, hogyan állíthatja be a Azure Database for MySQL számára a **nyilvános hálózati hozzáférés Megtagadását** Azure Portal, tekintse meg a [nyilvános hálózati hozzáférés megtagadásának konfigurálása](howto-deny-public-network-access.md)című témakört.

## <a name="next-steps"></a>Következő lépések

A Azure Database for MySQL biztonsági funkcióival kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* Ha Azure Database for MySQL tűzfalat szeretne konfigurálni, tekintse meg a [tűzfal támogatása](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)című témakört.

* Ha meg szeretné tudni, hogyan konfigurálhatja a virtuális hálózati szolgáltatás végpontját a Azure Database for MySQL számára, tekintse meg a [virtuális hálózatok elérésének konfigurálása](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)című témakört.

* A Azure Database for MySQL kapcsolatok áttekintését lásd: [Azure Database for MySQL kapcsolati architektúra](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md