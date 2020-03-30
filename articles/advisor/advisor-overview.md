---
title: Bevezetés az Azure Advisorba
description: Az Azure Advisor használatával optimalizálhatja az Azure-telepítéseket.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443093"
---
# <a name="introduction-to-azure-advisor"></a>Bevezetés az Azure Advisorba

Ismerje meg az Azure Advisor legfontosabb funkcióit, és válaszokat kaphat a gyakran feltett kérdésekre.

## <a name="what-is-advisor"></a>Mi az Advisor?
Az Advisor egy személyre szabott felhőtanácsadó, amely segít az ajánlott eljárások követésében az Azure-telepítések optimalizálásához. Az Advisor elemzi az erőforrások konfiguráció- és használattelemetriáját, és megoldási javaslatokat tesz, amelyek segítségével javítható az Azure-erőforrások költséghatékonysága, teljesítménye, rendelkezésre állása és biztonsága.

Az Advisor segítségével a következőket teheti:
* Proaktív, gyakorlatban hasznosítható és személyre szabott ajánlott eljárásokat kaphat. 
* Javítsa az erőforrások teljesítményét, biztonságát és magas rendelkezésre állását, mivel azonosítja a teljes Azure-kiadások csökkentésének lehetőségeit.
* A javasolt műveleteket tartalmazó beágyazott javaslatokat kaphat.

Az [Azure Portalon](https://aka.ms/azureadvisordashboard)keresztül érheti el az Advisort. Jelentkezzen be a [portálra,](https://portal.azure.com)keresse meg az **Advisort** a navigációs menüben, vagy keresse meg a **Minden szolgáltatás** menüben.

Az Advisor irányítópultja személyre szabott javaslatokat jelenít meg az összes előfizetéséhez.  Szűrőket alkalmazhat az adott előfizetésekre és erőforrástípusokra vonatkozó javaslatok megjelenítéséhez.  A javaslatok négy kategóriába tartoznak: 

* **Magas rendelkezésre állás:** Az üzleti legkritikusabb alkalmazások folyamatosságának biztosítása és javítása. További információ: [Advisor High Availability recommendations.](advisor-high-availability-recommendations.md)
* **Biztonság**: A biztonság megsértéséhez vezető fenyegetések és biztonsági rések észlelése. További információ: [Advisor Security recommendations](advisor-security-recommendations.md).
* **Teljesítmény**: Az alkalmazások sebességének növelése. További információ: [Advisor Performance recommendations](advisor-performance-recommendations.md).
* **Költség**: Az Azure-kiadások optimalizálása és csökkentése. További információ: [Advisor Cost recommendations](advisor-cost-recommendations.md).
* **Működési kiválóság:** A folyamat- és munkafolyamat-hatékonyság, az erőforrás-kezelhetőség és a telepítési gyakorlati tanácsok elérésének elősegítése. . További információ: [Advisor Operational Excellence recommendations](advisor-operational-excellence-recommendations.md).

  ![Tanácsadóajánlás-típusok](./media/advisor-overview/advisor-dashboard.png)

Egy kategóriára kattintva megjelenítheti az adott kategórián belüli javaslatok listáját, és kiválaszthat egy javaslatot, ha többet szeretne megtudni róla.  Megismerheti azokat a műveleteket is, amelyeket egy lehetőség kihasználásához vagy a probléma megoldásához hajthat végre.

![Tanácsadói ajánlási kategória](./media/advisor-overview/advisor-ha-category-example.png) 

Válassza ki a javaslat ajánlott műveletét a javaslat megvalósításához.  Egy egyszerű felület nyílik meg, amely lehetővé teszi, hogy végre a javaslatot, vagy utalja át a dokumentációt, amely segíti önt a végrehajtás.  Miután megvalósított egy ajánlást, akár egy napot is igénybe vehet, amíg az Advisor felismeri ezt.

Ha nem kíván azonnali lépéseket tenni egy javaslat alapján, elhalaszthatja azt egy meghatározott időtartamra, vagy elvetheti azt.  Ha nem szeretne javaslatokat kapni egy adott előfizetéshez vagy erőforráscsoporthoz, konfigurálhatja az Advisort, hogy csak a megadott előfizetésekhez és erőforráscsoportokhoz hozzon javaslatokat.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-access-advisor"></a>Hogyan érhetem el az Advisort?
Az [Azure Portalon](https://aka.ms/azureadvisordashboard)keresztül érheti el az Advisort. Jelentkezzen be a [portálra,](https://portal.azure.com)keresse meg az **Advisort** a navigációs menüben, vagy keresse meg a **Minden szolgáltatás** menüben.

Az Advisor-javaslatokat a virtuálisgép-erőforrás felületén keresztül is megtekintheti. Válasszon ki egy virtuális gépet, majd görgessen az Advisor-javaslatok hoz a menüben. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Milyen engedélyekre van szükségem az Advisor eléréséhez?
 
Az Advisor-javaslatokat előfizetés *tulajdonosaként,* *közreműködőjeként*vagy *olvasójaként* érheti el.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Milyen erőforrásokat ajánl a Tanácsadó?

Az Advisor javaslatokat nyújt az Application Gateway, az App Services, a rendelkezésre állási csoportok, az Azure cache, az Azure Data Factory, az Azure Database for MySQL, az Azure Database for PostgreSQL, az Azure Database for MariaDB, az Azure ExpressRoute, az Azure Cosmos DB, az Azure Database nyilvános IP-címek, SQL Data Warehouse, SQL-kiszolgálók, tárfiókok, Traffic Manager-profilok és virtuális gépek.

Az Azure Advisor az [Azure Security Centertől](https://docs.microsoft.com/azure/security-center/security-center-recommendations) származó javaslatokat is tartalmaz, amelyek további erőforrástípusokra vonatkozó javaslatokat is tartalmazhatnak.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Elhalaszthatom vagy elutasíthatom az ajánlást?

A javaslat elhalasztásához vagy elvetéséhez kattintson a **Halasztás** hivatkozásra. Megadhatja az elhalasztási időszakot, vagy a **Soha** lehetőséget választva elvetheti a javaslatot.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Advisor-ajánlásokról, olvassa el a következő témakört:

* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Tanácsadó magas rendelkezésre állásra vonatkozó ajánlásai](advisor-high-availability-recommendations.md)
* [Tanácsadó biztonsági ajánlásai](advisor-security-recommendations.md)
* [Tanácsadó teljesítményre vonatkozó ajánlásai](advisor-performance-recommendations.md)
* [Tanácsadói költségajánlások](advisor-cost-recommendations.md)
