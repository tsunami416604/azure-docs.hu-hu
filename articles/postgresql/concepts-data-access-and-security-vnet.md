---
title: Azure-adatbázis PostgreSQL Server vnet szolgáltatások végpont áttekintése |} Microsoft Docs
description: Ismerteti a vnet Szolgáltatásvégpontok működéséről az Azure-adatbázis PostgreSQL-kiszolgáló számára.
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2018
ms.openlocfilehash: a832f45027fc5337d9e76ec9cc4898286121278c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655188"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql"></a>Virtuális hálózati szolgáltatás végpontok és szabályok használni az Azure Database PostgreSQL

*Virtuális hálózati szabályok* van egy tűzfal biztonsági szolgáltatás, amely meghatározza, hogy az Azure-adatbázis PostgreSQL-kiszolgáló elfogadja-e a virtuális hálózatok az adott alhálózat érkező kommunikációt. Ez a cikk azt ismerteti, miért a virtuális hálózati szabály használata egyes esetekben a legjobb lehetőség, a biztonságos használatának engedélyezése az Azure-adatbázis PostgreSQL-kiszolgáló kommunikációt.

A virtuális hálózati szabály létrehozásához először lennie kell egy [virtuális hálózati] [ vm-virtual-network-overview] (VNet) és egy [virtuális hálózati szolgáltatási végpont] [ vm-virtual-network-service-endpoints-overview-649d] a a szabály hivatkozásával. A következő kép bemutatja, hogyan egy virtuális hálózati szolgáltatási végpont működik az Azure Database PostgreSQL:

![Hogyan működik a virtuális hálózat szolgáltatásvégpont – példa](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Azure-adatbázis PostgreSQL Ez a szolgáltatás érhető el nyilvános előzetes verziójában minden régióban, az Azure nyilvános felhőjében PostgreSQL az Azure-adatbázis telepítési helyét.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminológia és leírása

**Virtuális hálózat:** lehet az Azure-előfizetéshez társított virtuális hálózatokat.

**Alhálózati:** A virtuális hálózatok **alhálózatok**. Bármely Azure virtuális gépek (VM), hogy rendelkezik alhálózatok vannak hozzárendelve. Egy alhálózat több virtuális gép vagy egyéb számítási csomópontok szerepelhet. A számítási csomópontot, amely a virtuális hálózaton kívül nem tud hozzáférni a virtuális hálózat, ha nem állít be, hogy a hozzáférést a biztonsági.

**Virtuális hálózati szolgáltatási végpont:** A [virtuális hálózati szolgáltatási végpont] [ vm-virtual-network-service-endpoints-overview-649d] egy alhálózat, amelyek a következők: egy vagy több hivatalos Azure-szolgáltatás neve. Ebben a cikkben azt is nevét **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. A szolgáltatás címke az Azure-adatbázishoz PostgreSQL és a MySQL-szolgáltatások is vonatkozik. Fontos figyelembe venni, amikor alkalmazza a **Microsoft.Sql** címke szolgáltatás a virtuális hálózat szolgáltatásvégpont konfigurálja szolgáltatási végpont forgalmat az összes Azure SQL Database, a PostgreSQL az Azure-adatbázis és a MySQL-kiszolgálók Azure-adatbázis az alhálózaton. 

**Virtuális hálózati szabály:** az Azure-adatbázis PostgreSQL-kiszolgáló virtuális hálózati szabály szerepel-e a hozzáférés-vezérlési lista (ACL) alhálózat az Azure-adatbázis PostgreSQL-kiszolgáló. Ahhoz a hozzáférés-vezérlési listában, az Azure-adatbázis PostgreSQL-kiszolgáló, az alhálózati tartalmaznia kell a **Microsoft.Sql** típusnév.

A virtuális hálózati szabály van állítva, az Azure Database PostgreSQL-kiszolgáló minden csomópont található az alhálózat-kommunikáció fogadására.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>A virtuális hálózati szabály előnyei

Hajtsa végre a műveletet, amíg a virtuális gépeket az alhálózaton az Azure-adatbázis PostgreSQL-kiszolgáló nem tud kommunikálni. Egy műveletet, amely kapcsolatot hoz létre a rendszer egy virtuális hálózati szabály létrehozása. A VNet szabály módszer kiválasztása indoklása használata esetén a versengő biztonsági beállításokat, a tűzfal által kínált hasonlítsa össze, és ezzel szemben döntéseken igényel.

### <a name="a-allow-access-to-azure-services"></a>A. Azure-szolgáltatásokhoz való hozzáférés engedélyezése

A kapcsolat biztonsági ablaktáblán egy **be-és kikapcsolása** nevű gomb **Azure-szolgáltatásokhoz való hozzáférés engedélyezése**. A **ON** folytatott kommunikáció minden Azure IP-címeket és minden Azure alhálózat-beállítással. Ezeknek az Azure IP-címek és alhálózatok előfordulhat, hogy nem kell tulajdonában. Ez **ON** beállítás valószínűleg több nyissa meg az Azure-adatbázis PostgreSQL-adatbázisban kell lennie a szükségesnél. A virtuális hálózati szabály funkciót kínál sok eszközzel kombinálva felhasználóbarát tanúsítványhasználat pontos szabályzása.

### <a name="b-ip-rules"></a>B. IP-szabályok

Az Azure-adatbázishoz PostgreSQL tűzfal adja meg az IP-címtartományok, amelyből kommunikációs PostgreSQL-adatbázisból az Azure-adatbázisba elfogadás teszi lehetővé. Erre akkor finom stabil IP-címek, amelyek az Azure magánhálózaton kívülről. Az Azure magánhálózaton belül számos csomópontok be van állítva, de *dinamikus* IP-címeket. Dinamikus IP-címek megváltozhatnak, például újraindításakor a virtuális Gépet. Ha meg szeretné adni egy dinamikus IP-címet egy tűzfalszabályt, éles környezetben folly lenne.

Azt az IP-cím lehetőségét is maradványérték megszerzésével a *statikus* IP-címet a virtuális gép számára. További információkért lásd: [virtuális gépek magánhálózati IP-címek konfigurálása az Azure-portál használatával][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Azonban a statikus IP-megközelítés válhat kezelése bonyolult, és költséges, amikor léptékű hajtja végre. Virtuális hálózati szabályok lettek könnyebb létrehozásához és kezeléséhez.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Nem lehet még rendelkezik Azure-adatbázis PostgreSQL alhálózaton szolgáltatásvégpont megadása nélkül

Ha a **Microsoft.Sql** volt a virtuális hálózati alhálózat csomópont, a virtuális hálózaton belüli összes csomópontján kommunikál az Azure-adatbázis PostgreSQL-kiszolgáló. Ebben az esetben a virtuális gépek tudott kommunikálni az Azure Database PostgreSQL a bármely virtuális hálózati szabályok vagy IP-szabályok anélkül.

Azonban előfordulhat, hogy 2018, az Azure-adatbázishoz PostgreSQL-szolgáltatás nincs még a szolgáltatásokat, amelyeket rendelhet közvetlenül egy alhálózat között.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Virtuális hálózati szabályokra vonatkozó adatokat

Ez a szakasz ismerteti a tényezőktől virtuális hálózati szabályokat.

### <a name="only-one-geographic-region"></a>Csak egy földrajzi terület

Minden egyes virtuális hálózati szolgáltatási végpont csak egy Azure-régiót vonatkozik. A végpont nem engedélyezi az alhálózat keresztüli más régiókban.

Virtuális hálózati szabályok korlátozódik, a régiót, amelyben a mögöttes végpont vonatkozik.

### <a name="server-level-not-database-level"></a>Kiszolgálószintű, nem az adatbázis-szintjét

Minden egyes virtuális hálózati szabály a teljes Azure-adatbázis PostgreSQL-kiszolgáló, nem csak egy adott adatbázis a kiszolgálón. Ez azt jelenti a virtuális hálózati szabály a kiszolgáló szintjén-, nem az adatbázis szintjén-vonatkozik.

#### <a name="security-administration-roles"></a>Felügyeleti szerepkörök

Nincs a felügyeleti virtuális hálózati szolgáltatás végpontok a biztonsági szerepkörök elkülönítése. Beavatkozásra szükség, a következő szerepkörök:

- **Hálózati rendszergazda:** &nbsp; kapcsolja be a végpont.
- **Adatbázis-rendszergazda:** &nbsp; a hozzáférés-vezérlési lista (ACL) az adott alhálózat hozzáadása az Azure-adatbázishoz PostgreSQL-kiszolgáló frissítése.

*Az RBAC alternatív:*

A hálózati rendszergazda és az adatbázis-rendszergazdai szerepkörök rendelkezik, mint a virtuális hálózati szabályok kezeléséhez szükséges további képességeket. Van szükség, azok képességeinek csak egy részét.

Lehetősége van a [szerepköralapú hozzáférés-vezérlést (RBAC)] [ rbac-what-is-813s] , amely csak a szükséges részét képességek rendelkezik egy egyéni szerepkör létrehozása az Azure-ban. Az egyéni biztonsági szerepkört használható helyett használata esetén a hálózati rendszergazda vagy az adatbázis-rendszergazdához. A biztonsági kockázatokat felületének verziója korábbi, ha a felhasználó hozzáadása egy egyéni biztonsági szerepkört, és helyezze a felhasználót a két fő rendszergazdai szerepköröket.

> [!NOTE]
> Bizonyos esetekben az Azure-adatbázishoz PostgreSQL és a VNet-alhálózathoz van különböző előfizetésekhez. Ebben az esetben gondoskodnia kell a következő beállításokat:
> - Az azonos Azure Active Directory-bérlő mindkét előfizetéshez kell tartoznia.
> - A felhasználó rendelkezik a szükséges engedélyekkel a műveletek, például a szolgáltatás végpontok engedélyezésére és VNet-alhálózat hozzáadása az adott kiszolgálóhoz kezdeményezni.

## <a name="limitations"></a>Korlátozások

Azure-adatbázis PostgreSQL a virtuális hálózati szabályok szolgáltatás rendelkezik a következő korlátozások vonatkoznak:

- A tűzfalon az Azure-adatbázis a PostgreSQL minden egyes virtuális hálózati szabály alhálózat hivatkozik. A hivatkozott alhálózatok ugyanabban a földrajzi régióban, amely az Azure-adatbázishoz PostgreSQL kell futnia.

- Minden Azure-adatbázis PostgreSQL-kiszolgáló legfeljebb 128 ACL-bejegyzések a megadott virtuális hálózat lehet.

- Virtuális hálózati szabályok vonatkoznak csak Azure Resource Manager virtuális hálózatok; és nem arra [klasszikus üzembe helyezési modellel] [ arm-deployment-model-568f] hálózatok.

- Bekapcsolása esetén a virtuális hálózati szolgáltatás végpontok Azure Database PostgreSQL használatára vonatkozó a **Microsoft.Sql** szolgáltatás címke is lehetővé teszi, hogy az összes Azure-adatbázis szolgáltatás végpontok: MySQL az Azure-adatbázis, PostgreSQL az Azure-adatbázis , Az azure SQL Database és az Azure SQL Data Warehouse.

- A nyilvános előzetes időpontjában nincs virtuális hálózat áthelyezési műveletek nem támogatottak. Helyezze át a virtuális hálózati szabály, dobja el, és hozza létre újra.

- Virtuális hálózat Szolgáltatásvégpontok támogatása csak olyan általános célú és a Memóriaoptimalizált van.

- A tűzfalon az IP-címtartományok alkalmazása a következő hálózati elemek, de a virtuális hálózati szabályok azonban nem:
    - [Webhelyek (közötti S2S) virtuális magánhálózati (VPN)][vpn-gateway-indexmd-608y]
    - A helyszíni keresztül [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Ha a hálózat csatlakozik-e az Azure-hálózat használata [ExpressRoute][expressroute-indexmd-744v], a kapcsolatok a Microsoft Edge két nyilvános IP-címek van konfigurálva. A két IP-címek vannak való kapcsolódáshoz használt Microsoft Services, például Azure Storage Azure nyilvános társviszony-létesítés használatával.

Engedélyezi a kommunikációt a kapcsolatcsoport az Azure-adatbázishoz PostgreSQL, létre kell hoznia IP hálózati szabályok a Kapcsolatcsoportok nyilvános IP-címét. Ahhoz, hogy a nyilvános IP-címét az ExpressRoute-kapcsolatcsoportot található, nyisson meg egy támogatási jegy ExpressRoute az Azure portál használatával.

## <a name="related-articles"></a>Kapcsolódó cikkek
- [Egy Azure virtuális hálózatot][vm-virtual-network-overview]
- [Azure-beli virtuális hálózat Szolgáltatásvégpontok][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>További lépések
A VNet-szabályok létrehozása a cikkekben talál:
- [Létrehozása és kezelése az Azure-adatbázis PostgreSQL VNet szabályok az Azure portál használatával](howto-manage-vnet-using-portal.md)
- [Létrehozása és kezelése az Azure-adatbázis PostgreSQL VNet szabályok Azure parancssori felület használatával](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same Github repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml