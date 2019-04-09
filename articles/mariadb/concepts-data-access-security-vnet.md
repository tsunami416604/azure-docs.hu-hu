---
title: Azure Database for MariaDB-kiszolgáló virtuális hálózati szolgáltatások végpont – áttekintés |} A Microsoft Docs
description: Ismerteti az Azure Database for MariaDB-kiszolgáló a virtuális hálózati Szolgáltatásvégpontok működését.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 5a4e6819eeff2a2c8efaf3807c38cc06f7c35002
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006688"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mariadb"></a>Virtuális hálózati Szolgáltatásvégpontok és szabályok használata az Azure Database for MariaDB

*A virtuális hálózati szabályok* van egy tűzfal biztonsági funkció, amely meghatározza, hogy az Azure Database for MariaDB-kiszolgáló fogad-e a virtuális hálózatok adott alhálózatain érkező kommunikációt. Ez a cikk elmagyarázza, hogy miért a virtuális hálózati szabály szolgáltatást néha a legjobb megoldás az, hogy biztonságosan lehetővé teszi a kommunikációt az Azure Database for MariaDB-kiszolgáló.

Hozzon létre egy virtuális hálózati szabályt, hogy először lennie kell egy [virtuális hálózati] [ vm-virtual-network-overview] (VNet) és a egy [virtuális hálózati szolgáltatásvégpont] [ vm-virtual-network-service-endpoints-overview-649d] a a szabály-referenciáját. A következő kép mutatja be, egy virtuális hálózati szolgáltatásvégpont működését az Azure Database for MariaDB:

![Példa egy virtuális hálózati szolgáltatásvégpont működése](media/concepts-data-access-security-vnet/vnet-concept.png)

> [!NOTE]
> Ez a funkció érhető el minden régióban az Azure, Azure Database for MariaDB általános célú és memóriahasználatra optimalizált kiszolgálók telepítési helyét.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminológia és leírás

**Virtuális hálózat:** Az Azure-előfizetéséhez társított virtuális hálózatok is rendelkezhet.

**Alhálózat:** Egy virtuális hálózatot tartalmaz **alhálózatok**. Minden olyan Azure virtuális gépeken (VM), amely rendelkezik hozzárendelt alhálózatok. Egy alhálózaton több virtuális gép vagy más számítási csomópontokon is tartalmazhat. A számítási csomópontot, amely a virtuális hálózatán kívüli nem a virtuális hálózat eléréséhez, ha nem konfigurál, hogy engedélyezze a hozzáférést a biztonsági.

**Virtuális hálózati szolgáltatásvégpont:** A [virtuális hálózati szolgáltatásvégpont] [ vm-virtual-network-service-endpoints-overview-649d] egy alhálózat, amelynek a következők: egy vagy több hivatalos Azure-szolgáltatás nevét. Ebben a cikkben azt érdeklő nevét **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. Ez a szolgáltatás címke az Azure Database for MariaDB, a MySQL és a PostgreSQL szolgáltatás is vonatkozik. Fontos, hogy alkalmazása esetén vegye figyelembe a **Microsoft.Sql** szolgáltatáscímke a virtuális hálózati szolgáltatásvégpont konfigurálja végpont forgalmának minden Azure SQL Database, Azure Database for MariaDB, Azure Database for MySQL és Azure -Adatbázis PostgreSQL-kiszolgálók az alhálózaton.

**Virtuális hálózati szabályt:** Az Azure Database for MariaDB-kiszolgáló egy virtuális hálózati szabályt, amely szerepel a hozzáférés-vezérlési lista (ACL) az alhálózat nem az Azure Database for MariaDB-kiszolgáló. A hozzáférés-Vezérlési az Azure Database for MariaDB-kiszolgálón lennie, az alhálózat tartalmaznia kell a **Microsoft.Sql** írja be a nevet.

Egy virtuális hálózati szabályt arra utasítja az Azure Database for MariaDB-kiszolgáló, az alhálózaton található minden egyes csomópontjáról-kommunikáció fogadására.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Egy virtuális hálózati szabályt előnyei

Amíg nem tesz semmit, a virtuális gépeket az alhálózatok nem tud kommunikálni az Azure Database for MariaDB-kiszolgáló. Egy műveletet, amely létrehozza a kommunikációt egy virtuális hálózati szabály létrehozása. A közösségértékek a VNet szabály módszer kiválasztása szükség van a versengő, a tűzfal által kínált biztonsági beállításokat érintő összehasonlítása és a kontraszt megbeszélésre.

### <a name="a-allow-access-to-azure-services"></a>A. Azure-szolgáltatásokhoz való hozzáférés engedélyezése

A kapcsolat biztonsági panelnek egy **be-és kikapcsolása** feliratú gomb **Azure-szolgáltatásokhoz való hozzáférés engedélyezése**. A **ON** beállítás lehetővé teszi, hogy az összes Azure IP-címek és az összes Azure-alhálózatok által kezdeményezett kommunikáció. Ezek az Azure IP-címek vagy az alhálózatok előfordulhat, hogy nem kell tulajdonában. Ez **ON** beállítás valószínűleg több nyitva, mint azt szeretné, hogy az Azure Database for MariaDB adatbázis használható. A virtuális hálózati szabály funkció lehetővé teszi a sok ennél a részletes.

### <a name="b-ip-rules"></a>B. IP-szabályok

Az Azure Database for MariaDB tűzfal lehetővé teszi, hogy adja meg, ahonnan kommunikáció elfogadták az Azure Database for MariaDB-kiszolgáló IP-címtartományok. Ez a megközelítés is megfelel a stabil, amelyek túlmutatnak az Azure magánhálózati IP-címek. Számos csomópontot az Azure privát hálózaton belül vannak konfigurálva, de *dinamikus* IP-címeket. Dinamikus IP-címek megváltozhatnak, például a virtuális gép újraindításakor. Adja meg a dinamikus IP-cím egy tűzfalszabályt, éles környezetben való folly lenne.

Az IP-beállítás akkor is maradványérték megszerzésével a *statikus* IP-címet a virtuális gép számára. További információkért lásd: [egy virtuális gép magánhálózati IP-címek konfigurálása az Azure portal használatával][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Azonban a statikus IP-megközelítés válhat kezelése bonyolult, és költséges méretekben. A virtuális hálózati szabályok használata egyszerűbb, létrehozására és kezelésére.

### <a name="c-cannot-yet-have-azure-database-for-mariadb-on-a-subnet-without-defining-a-service-endpoint"></a>C. Nem lehet még Azure Database MariaDB-hez készült alhálózat szolgáltatásvégpont definiálása nélkül

Ha a **Microsoft.Sql** volt a virtuális hálózat egy alhálózat csomópont, a virtuális hálózaton lévő összes csomópont sikerült kommunikálni az Azure Database for MariaDB-kiszolgáló. Ebben az esetben a virtuális gépek kommunikálni képes az Azure Database for MariaDB virtuális hálózati szabályt vagy IP-szabályok nélkül.

Azonban 2018 augusztus, az Azure Database for MariaDB szolgáltatás még nincs a szolgáltatások, amelyek közvetlenül egy alhálózathoz rendelhető.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>A virtuális hálózati szabályok részleteit

Ebben a szakaszban a virtuális hálózati szabályok több részleteit ismerteti.

### <a name="only-one-geographic-region"></a>Csak egy földrajzi régióban

Minden egyes virtuális hálózati szolgáltatásvégpont csak egy Azure-régióra vonatkozik. A végpont nem engedélyezi a más régiókban az alhálózat-kommunikációt fogad.

Minden olyan virtuális hálózati szabály korlátozódik a régiót, amelyben a mögöttes végpont vonatkozik.

### <a name="server-level-not-database-level"></a>Kiszolgálószintű, nem adatbázisszintű

Minden egyes virtuális hálózati szabályt alkalmazza, a teljes Azure Database for MariaDB-kiszolgálót, ne csak egy adott adatbázist a kiszolgálón. Más szóval virtuális hálózati szabályt alkalmazza, a kiszolgáló szintjén, nem az adatbázis szintjén.

### <a name="security-administration-roles"></a>Biztonsági felügyeleti szerepkörök

Nincs a felügyeleti virtuális hálózati Szolgáltatásvégpontok, a biztonsági szerepkörök elkülönítése. A művelet szükség az egyes a következő szerepkörök:

- **Hálózati rendszergazda:** &nbsp; Kapcsolja be a végponthoz.
- **Adatbázis-rendszergazda:** &nbsp; A hozzáférés-vezérlési lista (ACL) a megadott alhálózat hozzáadása az Azure Database for MariaDB-kiszolgáló frissítése.

*Az RBAC alternatív:*

A hálózati rendszergazda és az adatbázis-rendszergazdai szerepkörök kezelése a virtuális hálózati szabályok szükségesnél valamivel rendelkezik. Csak azok a funkciók egy része van szükség.

Lehetősége van a [szerepköralapú hozzáférés-vezérlés (RBAC)] [ rbac-what-is-813s] az Azure-ban, amely csak a szükséges funkcióinak részét, rendelkezik egy egyéni szerepkör létrehozása. Az egyéni szerepkör használható helyett használata esetén a hálózati rendszergazda vagy az adatbázis rendszergazdájával. A biztonsági fenyegetéseknek felületének alacsonyabb, ha egy felhasználó hozzá egy egyéni biztonsági szerepkört, és a felhasználót hozzáadnia a két fő rendszergazdai szerepkör.

> [!NOTE]
> Bizonyos esetekben az Azure Database for MariaDB és az alhálózatok közötti virtuális hálózatok különböző előfizetésekben találhatóak. Ezekben az esetekben biztosítania kell a következő beállításokat:
> - Mindkét előfizetés ugyanahhoz az Azure Active Directory-bérlőhöz kell tartoznia.
> - A felhasználók számára a szükséges műveleteket, például-Szolgáltatásvégpontok engedélyezését, és a egy virtuális hálózat alhálózat hozzáadása az adott kiszolgálóhoz kezdeményezni.

## <a name="limitations"></a>Korlátozások

Az Azure Database for MariaDB a virtuális hálózati szabályok funkció a következő korlátozások vonatkoznak:

- Személyes IP-címet a virtuális hálózat/alhálózat a webes alkalmazás is le lehet képezni. Akkor is, ha a Szolgáltatásvégpontok a megadott virtuális hálózat/alhálózat vannak bekapcsolva, a kiszolgáló és a webes alkalmazás közötti kapcsolatait lesz egy Azure nyilvános IP-forráshoz, nem egy virtuális hálózat/alhálózat forrás. Ahhoz, hogy a kapcsolat webalkalmazás és a egy kiszolgálóra, amelyen a VNet tűzfalszabályai, engedélyezi az Azure-szolgáltatások hozzáférésének a kiszolgálón kell.

- Az Azure Database for MariaDB-tűzfal, az egyes virtuális hálózati szabályt hivatkozik egy alhálózatra. Ezen hivatkozott alhálózatok ugyanabban a földrajzi régióban az Azure Database for MariaDB üzemeltető kiszolgálón kell futnia.

- Minden egyes, Azure Database for MariaDB-kiszolgáló legfeljebb 128 ACL-bejegyzések bármely megadott virtuális hálózat lehet.

- A virtuális hálózati szabályok alkalmazása csak az Azure Resource Managerbeli virtuális hálózat; és ne [klasszikus üzemi modellben] [ resource-manager-deployment-model-568f] hálózatok.

- Ne tudják bekapcsolni a virtuális hálózati Szolgáltatásvégpontok az Azure Database for MariaDB használatával a **Microsoft.Sql** szolgáltatáscímke is lehetővé teszi az összes Azure-adatbázis-szolgáltatás végpontjainak: Azure Database for MariaDB, Azure Database for MySQL, Azure Database for PostgreSQL, az Azure SQL Database és az Azure SQL Data warehouse-bA.

- Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

- A tűzfal IP-címtartományok alkalmazása a következő hálózati elemek, de a virtuális hálózati szabályok nem:
    - [Site-to-Site (S2S) virtuális magánhálózati (VPN)][vpn-gateway-indexmd-608y]
    - A helyszíni keresztül [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Ha a hálózat csatlakozik az Azure-hálózatot az használatával [ExpressRoute][expressroute-indexmd-744v], minden egyes-kapcsolatcsoport két nyilvános IP-címekkel rendelkező, a Microsoft Edge van konfigurálva. A két IP-cím segítségével csatlakozhat a Microsoft Services, mint például az Azure Storage, Azure nyilvános társviszony-létesítés.

Engedélyezi a kommunikációt a kapcsolatcsoporthoz az Azure Database for MariaDB, létre kell hoznia IP hálózati szabályok a Kapcsolatcsoportok nyilvános IP-címét. Annak érdekében, hogy a nyilvános IP-címét az ExpressRoute-kapcsolatcsoport található, nyisson egy támogatási jegyet az expressroute-tal az Azure portal használatával.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>A VNET tűzfalszabály nélkül szolgáltatják a virtuális hálózati Szolgáltatásvégpontok hozzáadása a kiszolgálóhoz

Csupán beállítása a tűzfalszabályok nem biztonságossá tétele a kiszolgáló és a vnet között. Virtuális hálózati Szolgáltatásvégpontok is be kell kapcsolnia **a** biztonsága érvénybe léptetéséhez. Szolgáltatásvégpontok engedélyezése **a**, a virtuális hálózat alhálózatához állásidő találkozik, amíg be nem fejezi az átállás **ki** való **a**. Ez különösen igaz nagy virtuális hálózatok kontextusában. Használhatja a **IgnoreMissingServiceEndpoint** jelző csökkentése, vagy a hidegindítás okozta üzemkimaradást áttérés során.

Beállíthatja a **IgnoreMissingServiceEndpoint** jelzőt az Azure CLI vagy a portál használatával.

## <a name="related-articles"></a>Kapcsolódó cikkek
- [Azure virtuális hálózatok][vm-virtual-network-overview]
- [Az Azure virtuális hálózati Szolgáltatásvégpontok][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>További lépések
Virtuális hálózati szabályainak létrehozásával a cikkekben talál:
- [Hozzon létre és kezelhető az Azure Database for MariaDB VNet-szabályok az Azure portal használatával](howto-manage-vnet-portal.md)
 
<!--
- [Create and manage Azure Database for MariaDB VNet rules using Azure CLI](howto-manage-vnet-using-cli.md)
-->

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml
