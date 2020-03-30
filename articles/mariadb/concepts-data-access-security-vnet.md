---
title: Virtuális hálózat szolgáltatásvégpontjai – Azure-adatbázis a MariaDB-hez
description: Ez a témakör azt ismerteti, hogy a VNet-szolgáltatás végpontjai hogyan működnek az Azure Database for MariaDB-kiszolgálóhoz.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 54379c65850fa210e5523b53a64fe89705ed1f15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532110"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mariadb"></a>Virtuális hálózati szolgáltatásvégpontok és szabályok használata az Azure Database for MariaDB-hez

*A virtuális hálózati szabályok* egy tűzfalbiztonsági szolgáltatás, amely azt szabályozza, hogy az Azure Database for MariaDB-kiszolgáló elfogadja-e a virtuális hálózatok bizonyos alhálózataiból küldött kommunikációt. Ez a cikk ismerteti, hogy miért a virtuális hálózati szabály szolgáltatás néha a legjobb megoldás a biztonságos kommunikáció lehetővé tétele az Azure Database mariaDB-kiszolgáló.

Virtuális hálózati szabály létrehozásához először egy [virtuális hálózatnak][vm-virtual-network-overview] (VNet) és egy [virtuális hálózati szolgáltatás végpontjának][vm-virtual-network-service-endpoints-overview-649d] kell lennie a szabályhoz. Az alábbi képen látható, hogyan működik a virtuális hálózati szolgáltatás végpontja a MariaDB Azure Database szolgáltatással:

![Példa a VNet-szolgáltatásvégpont működésére](media/concepts-data-access-security-vnet/vnet-concept.png)

> [!NOTE]
> Ez a funkció az Azure minden régiójában elérhető, ahol az Azure Database for MariaDB általános célú és memóriaoptimalizált kiszolgálókra van telepítve.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminológia és leírás

**Virtuális hálózat:** Az Azure-előfizetéshez virtuális hálózatokat társíthat.

**Alhálózat:** A virtuális hálózat **alhálózatokat**tartalmaz. Minden Azure virtuális gépek (Virtuális gépek), amely rendelkezik az alhálózatokhoz van rendelve. Egy alhálózat több virtuális gépet vagy más számítási csomópontot tartalmazhat. A virtuális hálózaton kívüli számítási csomópontok csak akkor férhetnek hozzá a virtuális hálózathoz, ha a biztonságot úgy állítja be, hogy engedélyezze a hozzáférést.

**Virtuális hálózat szolgáltatás végpontja:** A [virtuális hálózati szolgáltatás végpontja][vm-virtual-network-service-endpoints-overview-649d] egy alhálózat, amelynek tulajdonságértékei egy vagy több hivatalos Azure-szolgáltatástípus-neveket tartalmaznak. Ebben a cikkben a **Microsoft.Sql**típusneve érdekel, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik. Ez a szolgáltatáscímke a MariaDB, a MySQL és a PostgreSQL-szolgáltatások Azure Database szolgáltatására is vonatkozik. Fontos megjegyezni, ha a **Microsoft.Sql** szolgáltatáscímke egy VNet szolgáltatás végpontra, akkor konfigurálja a szolgáltatás végponti forgalmat az összes Azure SQL-adatbázis, az Azure Database for MariaDB, az Azure Database for MySQL és az Azure Database for PostgreSQL-kiszolgálók az alhálózaton.

**Virtuális hálózati szabály:** Az Azure Database for MariaDB-kiszolgáló virtuális hálózati szabálya egy alhálózat, amely szerepel az Azure Database for MariaDB-kiszolgáló hozzáférés-vezérlési listájában (ACL). Ahhoz, hogy az Azure Database for MariaDB-kiszolgáló ACL-jében legyen, az alhálózatnak tartalmaznia kell a **Microsoft.Sql** típusnevet.

A virtuális hálózati szabály azt mondja az Azure Database for MariaDB-kiszolgáló, hogy fogadja el a kommunikációt minden csomópont, amely az alhálózaton található.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>A virtuális hálózati szabály előnyei

Aművelet megnem hozásáig az alhálózatokon lévő virtuális gépek nem tudnak kommunikálni az Azure-adatbázissal a MariaDB-kiszolgálóhoz. A kommunikációt létrehozó egyik művelet egy virtuális hálózati szabály létrehozása. A virtuális hálózat szabálymegközelítésének kiválasztásához a tűzfal által kínált versengő biztonsági beállításokat érintő összehasonlítási és kontraszti egyeztetést igényel.

### <a name="a-allow-access-to-azure-services"></a>A. Azure-szolgáltatásokhoz való hozzáférés engedélyezése

A Kapcsolat biztonsági ablaktábláján található egy **BE/OFF** gomb, amelynek címkéje **Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése.** Az **ON** beállítás lehetővé teszi az összes Azure IP-címről és az összes Azure-alhálózatról érkező kommunikációt. Ezek az Azure IP-k vagy alhálózatok előfordulhat, hogy nem az Ön tulajdonában vannak. **Ez az ON** beállítás valószínűleg nyitottabb, mint szeretné, hogy az Azure Database for MariaDB-adatbázis legyen. A virtuális hálózati szabály funkció sokkal finomabb részletes vezérlést kínál.

### <a name="b-ip-rules"></a>B. IP-szabályok

Az Azure Database for MariaDB tűzfal lehetővé teszi, hogy ip-címtartományokat adjon meg, amelyekből a kommunikációt elfogadják az Azure Database for MariaDB kiszolgálón. Ez a megközelítés az Azure magánhálózaton kívüli stabil IP-címek esetén is megfelel. Az Azure magánhálózatán belül azonban sok csomópont *dinamikus* IP-címekkel van konfigurálva. Dinamikus IP-címek változhatnak, például a virtuális gép újraindításakor. Ostobaság lenne dinamikus IP-címet megadni egy tűzfalszabályban, éles környezetben.

Az IP-opció mentheti egy *statikus* IP-címet a virtuális gép. További információt a [Privát IP-címek konfigurálása egy virtuális géphez az Azure Portal használatával című témakörben talál.][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]

Azonban a statikus IP-megközelítés válhat nehéz kezelni, és költséges, ha történik a skála. A virtuális hálózati szabályok at könnyebb létrehozni és kezelni.

### <a name="c-cannot-yet-have-azure-database-for-mariadb-on-a-subnet-without-defining-a-service-endpoint"></a>C. A MariaDB Azure Database még nem használható alhálózaton szolgáltatásvégpont definiálása nélkül

Ha a **Microsoft.Sql-kiszolgáló** a virtuális hálózat egyik alhálózatának csomópontja volt, a virtuális hálózaton belül minden csomópont kommunikálhat az Azure Database for MariaDB-kiszolgálóval. Ebben az esetben a virtuális gépek kommunikálhatnak az Azure Database for MariaDB anélkül, hogy a virtuális hálózati szabályok vagy IP-szabályok.

2018 augusztusától azonban az Azure Database for MariaDB szolgáltatás még nem szerepel a közvetlenül egy alhálózathoz rendelhető szolgáltatások között.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>A virtuális hálózati szabályok részletei

Ez a szakasz a virtuális hálózati szabályokkal kapcsolatos számos részletet ismerteti.

### <a name="only-one-geographic-region"></a>Csak egy földrajzi régió

Minden virtuális hálózati szolgáltatás végpont csak egy Azure-régióban vonatkozik. A végpont nem teszi lehetővé, hogy más régiók fogadják az alhálózatkommunikációt.

Minden virtuális hálózati szabály arra a régióra korlátozódik, amelyaz alapul szolgáló végpontja vonatkozik.

### <a name="server-level-not-database-level"></a>Kiszolgálószintű, nem adatbázisszintű

Minden virtuális hálózati szabály a MariaDB-kiszolgáló teljes Azure-adatbázisára vonatkozik, nem csak a kiszolgálón lévő egyetlen adatbázisra. Más szóval a virtuális hálózati szabály a kiszolgáló szintjén érvényes, nem pedig az adatbázis szintjén.

### <a name="security-administration-roles"></a>Biztonsági felügyeleti szerepkörök

A virtuális hálózat szolgáltatás végpontjainak felügyeletében a biztonsági szerepkörök elkülönülnek. A következő szerepkörök mindegyikéből szükség van műveletre:

- **Hálózati rendszergazda:** &nbsp; Kapcsolja be a végpontot.
- **Adatbázis-rendszergazda:** &nbsp; Frissítse a hozzáférés-vezérlési listát (ACL) a megadott alhálózat hozzáadásához az Azure Database for MariaDB-kiszolgálóhoz.

*RBAC alternatíva:*

A hálózati rendszergazda és az adatbázis-rendszergazda szerepkörei több képességgel rendelkeznek, mint amennyi a virtuális hálózati szabályok kezeléséhez szükséges. Képességeiknek csak egy részhalmazára van szükség.

Lehetősége van [szerepköralapú hozzáférés-vezérlés (RBAC)][rbac-what-is-813s] használatával az Azure-ban egyetlen egyéni szerepkör, amely csak a szükséges részhalmaza képességeit. Az egyéni szerepkör a hálózati rendszergazda vagy az adatbázis-rendszergazda helyett használható. A biztonsági expozíció felülete alacsonyabb, ha egy felhasználót hozzáad egy egyéni szerepkörhöz, szemben a felhasználó másik két fő rendszergazdai szerepkörrel való hozzáadásával.

> [!NOTE]
> Bizonyos esetekben a MariaDB Azure Database és a Virtuálishálózat-alhálózat különböző előfizetésekben található. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
> - Mindkét előfizetésnek ugyanabban az Azure Active Directory-bérlőben kell lennie.
> - A felhasználó rendelkezik a műveletek kezdeményezéséhez szükséges engedélyekkel, például a szolgáltatásvégpontok engedélyezéséhez és a virtuális hálózat adott kiszolgálóhoz való hozzáadásához szükséges engedélyekkel.
> - Győződjön meg arról, hogy mind az előfizetés rendelkezik a **Microsoft.Sql** erőforrás-szolgáltató regisztrálva. További információkért lásd [az erőforrás-kezelő-regisztrációt][resource-manager-portal]

## <a name="limitations"></a>Korlátozások

A MariaDB-hez való Azure Database esetében a virtuális hálózati szabályok szolgáltatás a következő korlátozásokkal rendelkezik:

- A webalkalmazás leképezhető egy privát IP-címre egy virtuális hálózatban/alhálózatban. Még akkor is, ha a szolgáltatás végpontjai be vannak kapcsolva a megadott virtuális hálózatról/alhálózatról, a webalkalmazás és a kiszolgáló kapcsolata azure-beli nyilvános IP-forrással fog rendelkezni, nem pedig virtuális hálózat/alhálózati forrással. Ahhoz, hogy engedélyezze a kapcsolatot egy webalkalmazásból egy olyan kiszolgálóra, amely rendelkezik virtuálishálózati tűzfalszabályokkal, engedélyeznie kell az Azure-szolgáltatások számára a kiszolgáló elérését a kiszolgálón.

- A MariaDB Azure-adatbázistűzfalon minden virtuális hálózati szabály egy alhálózatra hivatkozik. Mindezek a hivatkozott alhálózatok kell üzemeltetni ugyanabban a földrajzi régióban, amely az Azure Database for MariaDB.

- A MariaDB-kiszolgálók minden Egyes Azure-adatbázisa legfeljebb 128 ACL-bejegyzéssel rendelkezhet egy adott virtuális hálózathoz.

- A virtuális hálózati szabályok csak az Azure Resource Manager virtuális hálózataira vonatkoznak; és nem a [klasszikus üzembe helyezési modell][resource-manager-deployment-model-568f] hálózatok.

- A virtuális hálózati szolgáltatás végpontjainak bekapcsolása a MariaDB-hez készült Azure Database for MariaDB szolgáltatáshoz a **Microsoft.Sql** szolgáltatáscímke használatával lehetővé teszi az összes Azure Database-szolgáltatás végpontjait is: Az Azure Database for MariaDB, az Azure Database for MySQL, az Azure Database for PostgreSQL, az Azure SQL Database és az Azure SQL Data Warehouse.

- A Virtuálishálózat-szolgáltatás végpontjainak támogatása csak általános célú és memóriaoptimalizált kiszolgálókhoz érhető el.

- A tűzfalon az IP-címtartományok a következő hálózati elemekre vonatkoznak, de a virtuális hálózati szabályok nem:
    - [Helyek közötti (S2S) virtuális magánhálózat (VPN)][vpn-gateway-indexmd-608y]
    - Helyszíni műveletek az [ExpressRoute-on][expressroute-indexmd-744v] keresztül

## <a name="expressroute"></a>ExpressRoute

Ha a hálózat [expressroute][expressroute-indexmd-744v]használatával csatlakozik az Azure-hálózathoz, minden egyes kapcsolatcsoport két nyilvános IP-címmel van konfigurálva a Microsoft Edge-ben. A két IP-cím a Microsoft-szolgáltatásokhoz való csatlakozáshoz, például az Azure Storage-hoz való csatlakozáshoz használható az Azure nyilvános társviszony-létesítése használatával.

Ahhoz, hogy lehetővé tegye a kapcsolatot a kapcsolatcsoport ból az Azure Database for MariaDB, létre kell hoznia az IP-hálózati szabályok at a nyilvános IP-címek a körkapcsolatok. Az ExpressRoute-kapcsolat nyilvános IP-címeinek megkereséséhez nyisson meg egy támogatási jegyet az ExpressRoute-tal az Azure Portal használatával.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>VNET-tűzfalszabály hozzáadása a kiszolgálóhoz a VNET-szolgáltatás végpontjainak bekapcsolása nélkül

A tűzfalszabály beállítása nem segít a kiszolgáló virtuális hálózathoz való védelmében. A virtuális hálózat szolgáltatásvégpontjait is be kell **kapcsolnia** ahhoz, hogy a biztonság érvénybe lépjen. Ha bekapcsolja **a**szolgáltatásvégpontokat, a virtuális hálózat alhálózata állásidőt tapasztal, amíg be nem fejezi a **Ki** és **Be**állás ba való átmenetet. Ez különösen igaz a nagy virtuális hálózatok összefüggésében. Az **IgnoreMissingServiceEndpoint** jelzővel csökkentheti vagy megszüntetheti az állásidőt az átmenet során.

Az **IgnoreMissingServiceEndpoint** jelző az Azure CLI vagy a portál használatával állítható be.

## <a name="related-articles"></a>Kapcsolódó cikkek
- [Az Azure virtuális hálózatai][vm-virtual-network-overview]
- [Az Azure virtuális hálózati szolgáltatásának végpontjai][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>További lépések
A virtuális hálózati szabályok létrehozásáról szóló cikkeka következő témakörben tetthet:
- [Azure-adatbázis létrehozása és kezelése a MariaDB virtuális hálózat szabályaihoz az Azure Portal használatával](howto-manage-vnet-portal.md)
 
<!--
- [Create and manage Azure Database for MariaDB VNet rules using Azure CLI](howto-manage-vnet-using-cli.md)
-->

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
