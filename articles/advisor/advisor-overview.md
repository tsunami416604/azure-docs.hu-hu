---
title: Bevezetés a Azure Advisorba
description: Az Azure-beli üzembe helyezések optimalizálásához használja a Azure Advisor.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443093"
---
# <a name="introduction-to-azure-advisor"></a>Bevezetés a Azure Advisorba

Ismerkedjen meg Azure Advisor főbb képességeivel, és választ kaphat a gyakori kérdésekre.

## <a name="what-is-advisor"></a>Mi az Advisor?
Az Advisor egy személyre szabott felhőalapú tanácsadó, amely az Azure-beli üzembe helyezések optimalizálására szolgáló ajánlott eljárások követésében nyújt segítséget. Az Advisor elemzi az erőforrások konfiguráció- és használattelemetriáját, és megoldási javaslatokat tesz, amelyek segítségével javítható az Azure-erőforrások költséghatékonysága, teljesítménye, rendelkezésre állása és biztonsága.

Az Advisor használatával a következőket teheti:
* Proaktív, gyakorlatban hasznosítható és személyre szabott ajánlott eljárásokat kaphat. 
* Az erőforrások teljesítményének, biztonságának és magas rendelkezésre állásának javítása a teljes Azure-kiadások csökkentése érdekében.
* A javasolt műveleteket tartalmazó beágyazott javaslatokat kaphat.

Az Advisor a [Azure Portal](https://aka.ms/azureadvisordashboard)keresztül érhető el. Jelentkezzen be a [portálra](https://portal.azure.com), keresse meg az **Advisor** parancsot a navigációs menüben, vagy keresse meg a **minden szolgáltatás** menüben.

Az Advisor irányítópultja személyre szabott javaslatokat jelenít meg az összes előfizetéséhez.  Szűrőket alkalmazhat az egyes előfizetésekhez és erőforrástípusokhöz tartozó javaslatok megjelenítéséhez.  A javaslatok négy kategóriába tartoznak: 

* **Magas rendelkezésre állás**: az üzleti szempontból kritikus fontosságú alkalmazások folytonosságának biztosítása és javítása. További információ: [Advisor magas rendelkezésre állási javaslatai](advisor-high-availability-recommendations.md).
* **Biztonság**: az olyan fenyegetések és biztonsági rések észlelése, amelyek biztonsági réseket okozhatnak. További információ: [Advisor biztonsági javaslatok](advisor-security-recommendations.md).
* **Teljesítmény**: az alkalmazások sebességének növelése érdekében. További információ: [Advisor Performance javaslatok](advisor-performance-recommendations.md).
* **Költségek**: a teljes Azure-kiadások optimalizálása és csökkentése. További információ: [Advisor Cost javaslatok](advisor-cost-recommendations.md).
* **Működési kiválóság**: a folyamatok és a munkafolyamatok hatékonyságának, az erőforrás-kezelhetőség és az üzembe helyezés ajánlott eljárásainak a megvalósításához nyújt segítséget. . További információ: [Advisor Operational Excellence ajánlásai](advisor-operational-excellence-recommendations.md).

  ![Advisor-javaslatok típusai](./media/advisor-overview/advisor-dashboard.png)

Egy kategóriára kattintva megjelenítheti az adott kategórián belüli javaslatok listáját, és kiválaszthat egy javaslatot, amely további információkat tartalmaz.  Azt is megtudhatja, hogy milyen műveleteket hajthat végre a lehetőségek kihasználásához vagy a probléma megoldásához.

![Advisor-javaslat kategóriája](./media/advisor-overview/advisor-ha-category-example.png) 

A javaslat megvalósításához Válassza ki a javasolt műveletet.  Megnyílik egy egyszerű felület, amely lehetővé teszi a javaslat megvalósítását, vagy olyan dokumentációra hivatkozik, amely segítséget nyújt a megvalósításában.  A javaslat megvalósítása után akár egy napot is igénybe vehet, hogy az Advisor felismerje ezt.

Ha nem szeretne azonnali műveletet végrehajtani egy javaslaton, elhalaszthatja azt egy adott időszakra, vagy elvetheti azt.  Ha nem szeretne javaslatokat kapni egy adott előfizetéshez vagy erőforráscsoporthoz, úgy is beállíthatja az Advisorot, hogy csak a megadott előfizetésekre és erőforrás-csoportokra vonatkozó ajánlásokat állítson elő.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-access-advisor"></a>Hogyan érhetem el az Advisort?
Az Advisor a [Azure Portal](https://aka.ms/azureadvisordashboard)keresztül érhető el. Jelentkezzen be a [portálra](https://portal.azure.com), keresse meg az **Advisor** parancsot a navigációs menüben, vagy keresse meg a **minden szolgáltatás** menüben.

Az Advisor javaslatait a virtuális gép erőforrás-felületén is megtekintheti. Válasszon ki egy virtuális gépet, majd görgessen az Advisor javaslataihoz a menüben. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Milyen engedélyek szükségesek az Advisor eléréséhez?
 
Az Advisor-ajánlásokat *tulajdonosként*, *közreműködőként*vagy az előfizetés *olvasójának* is elérheti.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Milyen erőforrásokra vonatkozó ajánlásokat biztosít az Advisor?

Az Advisor ajánlásokat nyújt Application Gateway, App Services, rendelkezésre állási csoportok, Azure cache, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure ExpressRoute, Azure Cosmos DB, Azure Public IP-címek, SQL Data Warehouse, SQL-kiszolgálók, Storage-fiókok, Traffic Manager-profilok és virtuális gépek.

A Azure Advisor a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) javaslataival is rendelkezik, amelyek további erőforrástípusok esetén javaslatokat is tartalmazhatnak.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Elhalasztottam vagy Elvetem a javaslatot?

A javaslatok elhalasztásához vagy elvetéséhez kattintson az **elhalasztás** hivatkozásra. Megadhat egy elhalasztási időszakot, vagy kiválaszthatja, hogy **Soha ne** utasítsa el a javaslatot.

## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további tudnivalókért tekintse meg a következő témakört:

* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Advisor – magas rendelkezésre állási javaslatok](advisor-high-availability-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor – Cost-javaslatok](advisor-cost-recommendations.md)
