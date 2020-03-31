---
title: Az OMS portál az Azure-ba való áttérés | Microsoft dokumentumok
description: Az OMS-portál napnyugta alatt áll, és az összes funkció az Azure Portalra kerül. Ez a cikk az átmenet részleteit tartalmazza.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 680cd9b44cc447f9bdea38cb9d04fc661fba9c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659254"
---
# <a name="oms-portal-moving-to-azure"></a>Az OMS-portál az Azure-ba költözik

> [!NOTE]
> Ez a cikk az Azure nyilvános felhőre és a kormányzati felhőre egyaránt vonatkozik, kivéve, ha másként van feltüntetve.

**Az OmS-portál az Azure nyilvános felhő hivatalosan kivonva. 2019. május 15-én hivatalosan is megszüntették az Azure US Government-felhőhöz készült OMS-portált.** Izgatottan várjuk, hogy az Azure Portalra költözhetünk, és elvárjuk, hogy az átállás egyszerű legyen. De megértjük, hogy a változások nehézek és zavaróak lehetnek. A cikk további részében áttér a legfontosabb forgatókönyvekre és az átmenet ütemtervére.

Az Azure Portal az összes Azure-szolgáltatás központja, és gazdag felügyeleti élményt nyújt olyan képességekkel, mint például az erőforrások rögzítésére szolgáló irányítópultok, az erőforrások keresésére szolgáló intelligens keresés és az erőforrás-kezelés címkézése. A figyelési és felügyeleti munkafolyamat konszolidálása és egyszerűsítése érdekében elkezdtük hozzáadni az OMS-portál képességeit az Azure Portalhoz. Az OMS-portál összes szolgáltatása már az Azure Portal része. Valójában néhány új funkciók, például a Traffic Analytics csak az Azure Portalon érhetők el. Az Azure Portal segítségével mindent elérhet, amit az OMS-portálon végzett. Ha még nem tette meg, még ma kezdje el használni az Azure Portalt!

## <a name="what-is-changing"></a>Mi változik? 
Az OMS-portál e-edeprecációjával a következő módosításokat jelentik be. A módosítások mindegyikét az alábbi szakaszok részletesebben ismertetik.

- Csak az Azure Portalon hozhat létre új [munkaterületeket.](#new-workspaces)
- Az új riasztáskezelési felület [felváltja a riasztáskezelési megoldást.](#changes-to-alerts)
- [A felhasználói hozzáférés kezelése](#user-access-and-role-migration) most már az Azure-portálon történik az Azure szerepköralapú hozzáférés-vezérlés használatával.
- Az [Application Insights-összekötő már nincs szükség,](#application-insights-connector-and-solution) mivel ugyanazt a funkciót a munkaterületközi lekérdezések en keresztül engedélyezve van.
- Az [OMS mobilalkalmazás](#oms-mobile-app) elavult. 
- Az [NSG-megoldást a](#azure-network-security-group-analytics) Traffic Analytics-megoldáson keresztül elérhető továbbfejlesztett funkciókkal helyettesítik.
- A System Center Operations Manager és a Log Analytics új [kapcsolataihoz frissített felügyeleti csomagokra](#system-center-operations-manager)van szükség.
- Az [OMS-frissítési telepítések áttelepítése az Azure-ba](../../automation/migrate-oms-update-deployments.md) a [frissítéskezelés](../../automation/automation-update-management.md)változásairól további információt tartalmaz.


## <a name="what-should-i-do-now"></a>Most mit csináljak?
Bár a legtöbb szolgáltatás továbbra is működni fog az áttelepítés végrehajtása nélkül, a következő feladatokat kell végrehajtania:

- Át kell [telepítenie a felhasználói engedélyeket](#user-access-and-role-migration) az Azure Portalra.
- Az UPDATE Management megoldás áttérésével kapcsolatos részletekért tekintse meg az [OMS-frissítési központi telepítések áttelepítése az Azure-ba](../../automation/migrate-oms-update-deployments.md) című témakört.

Az Azure Portalra való áttéréssel kapcsolatos információkért tekintse meg az [OMS-portálról az Azure-portálra való áttéréssel](oms-portal-faq.md) kapcsolatos gyakori kérdéseket. 

## <a name="user-access-and-role-migration"></a>Felhasználói hozzáférés és szerepkör-áttelepítés
Az Azure Portal hozzáférés-kezelése gazdagabb és hatékonyabb, mint az OMS-portál hozzáférés-kezelése. [Az Azure Monitor naplók munkaterület tervezése a](design-logs-deployment.md) Log Analytics hozzáférés-kezelés részleteiért olvassa el.

> [!NOTE]
> A cikk korábbi verziói azt állították, hogy az engedélyek automatikusan konvertálódnak az OMS-portálról az Azure Portalra. Ez az automatikus átalakítás már nem tervezett, és a konverziót saját kezűleg kell elvégeznie.

Előfordulhat, hogy már rendelkezik megfelelő hozzáféréssel az Azure Portalon, amely esetben nem kell módosításokat végrehajtania. Van néhány olyan eset, amikor előfordulhat, hogy nem rendelkezik megfelelő hozzáféréssel, amely esetben a rendszergazdának engedélyeket kell rendelnie Önhöz.

- Olvasási felhasználói engedélyekkel rendelkezik az OMS-portálon, de az Azure Portalon nincs engedélye. 
- Közreműködői engedélyekkel rendelkezik az OMS-portálon, de csak az Azure Portalon lévő Reader-hozzáférés.
 
Mindkét esetben a rendszergazdának manuálisan kell hozzárendelnie a megfelelő szerepkört az alábbi táblázatból. Azt javasoljuk, hogy ezt a szerepkört erőforráscsoport vagy előfizetési szinten rendelje hozzá.  Mindkét esetben hamarosan több előíró jellegű iránymutatást adnak.

| OMS portál engedély | Azure-szerepkör |
|:---|:---|
| ReadOnly | Log Analytics olvasó |
| Közreműködő | Log Analytics közreműködő |
| Rendszergazda | Tulajdonos | 
 

## <a name="new-workspaces"></a>Új munkaterületek
Az OMS-portálhasználatával már nem hozhat létre új munkaterületeket. Kövesse a [Log Analytics-munkaterület létrehozása az Azure Portalon](../learn/quick-create-workspace.md) egy új munkaterületet az Azure Portalon című útmutatójában.

## <a name="changes-to-alerts"></a>A riasztások módosításai

### <a name="alert-extension"></a>Riasztásbővítmény  

Riasztások [bővültek az Azure Portalon](alerts-extend.md) meglévő riasztások továbbra is megjelennek az OMS-portálon, de csak az Azure Portalon kezelheti őket. Ha a riasztásokat programozott módon éri el a Log Analytics Riasztási REST API vagy a Log Analytics riasztási erőforrássablon használatával, az API-hívásokban, az Azure Resource Manager-sablonokban és a PowerShell-parancsokban műveletek helyett műveletcsoportokat kell használnia.

### <a name="alert-management-solution"></a>Riasztáskezelési megoldás
Egy korábbi közleményhez képest a [riasztáskezelési megoldás](alert-management-solution.md) továbbra is elérhető és teljes mértékben támogatott lesz az Azure Portalon. Folytathatja a megoldás telepítését az Azure Marketplace-ről.

Bár a riasztáskezelési megoldás továbbra is elérhető, javasoljuk, hogy az [Azure Monitor egységes riasztási felületét](alerts-overview.md) használja az Azure-ban az összes riasztás megjelenítéséhez és kezeléséhez. Ez az új felület natívmódon összesíti az Azure-on belül több forrásból származó riasztásokat, beleértve a Log Analytics naplóriasztásait is. Ha az Azure Monitor egységes ített riasztási felületét használja, akkor a riasztáskezelési megoldás csak a System Center Operation Manager től az Azure-ba érkező riasztások integrálásához szükséges. Az Azure Monitor egységes riasztási felületén láthatja a riasztások disztribúcióit, kihasználhatja a kapcsolódó riasztások intelligens csoportokon keresztüli automatikus csoportosítását, és több előfizetésben is megtekintheti a riasztásokat, miközben gazdag szűrőket alkalmaz. A riasztáskezelés jövőbeli fejlesztései elsősorban ebből az új felületből lesznek elérhetők. 

A riasztáskezelési megoldás (riasztási típusú rekordok) által gyűjtött adatok továbbra is a Log Analytics,amíg a megoldás telepítve van a munkaterületre. 

## <a name="oms-mobile-app"></a>OMS mobilalkalmazás
Az OMS mobilalkalmazás az OMS portállal együtt napnyugta lesz. Az OMS mobilalkalmazás helyett az informatikai infrastruktúrával, irányítópultokkal és mentett lekérdezésekkel kapcsolatos információk eléréséhez az Azure Portalt közvetlenül a mobileszköz böngészőjéből érheti el. A riasztások lehívásához be kell állítania az [Azure műveletcsoportokat,](action-groups.md) hogy SMS vagy hanghívás formájában értesítéseket kapjanak

## <a name="application-insights-connector-and-solution"></a>Application Insights-összekötő és megoldás
[Az Application Insights-összekötő](app-insights-connector.md) lehetővé teszi az Application Insights-adatok befoglalását a Log Analytics-munkaterületbe. Erre az adatmásolásra azért volt szükség, hogy lehetővé váljon az infrastruktúra és az alkalmazásadatok láthatósága. Az Application Insights 2019 márciusában nyújtott kiterjesztett adatmegőrzési támogatással, valamint az erőforrások közötti lekérdezések végrehajtásával, valamint több Azure Monitor Application [Insights-erőforrás](../log-query/cross-workspace-query.md) [megtekintésének](../log-query/unify-app-resource-data.md)képességével nincs szükség az Application Insights-erőforrásokból származó adatok duplikálására és a Log Analytics-nek való elküldésre. Továbbá az összekötő elküldi az alkalmazások tulajdonságainak egy részét a Log Analytics, míg az erőforrások közötti lekérdezések nagyobb rugalmasságot biztosít.  

2019. március 30-án az Application Insights-összekötő elavult, és az OMS-portál e-hetetlenségével együtt elavult, és eltávolították az Azure Marketplace-ről. A meglévő kapcsolatok 2019. június 30-ig működnek. Az OMS-portál eprecációjával nincs mód a meglévő kapcsolatok konfigurálására és eltávolítására a portálról. Ezt a REST API-val fogjuk támogatni, amely 2019 januárjában lesz elérhető, és értesítést küldünk az [Azure-frissítésekre.](https://azure.microsoft.com/updates/) 

## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
Az [Azure Network Security Group Analytics megoldás](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) helyébe a nemrég indított Traffic [Analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) lép, amely betekintést nyújt a felhőhálózatokon végzett felhasználói és alkalmazástevékenységekbe. A Traffic Analytics segítségével ellenőrizheti a szervezet hálózati tevékenységét, biztonságossá teszi az alkalmazásokat és az adatokat, optimalizálhatja a számítási feladatok teljesítményét, és megfelelőséget biztosít. 

Ez a megoldás elemzi az NSG flow naplókat, és betekintést nyújt a következőkbe.

- A forgalom az Azure és az internet, a nyilvános felhőrégiók, a vnetek és az alhálózatok között áramlik.
- Alkalmazások és protokollok a hálózaton, anélkül, hogy a szippantás vagy dedikált flow collection készülékek.
- Top talkers, beszédes alkalmazások, VM beszélgetések a felhőben, a közlekedési hotspotok.
- A VNET-k közötti forgalom forrásai és célállomásai, a kritikus üzleti szolgáltatások és alkalmazások közötti kapcsolatok.
- Biztonság, beleértve a rosszindulatú forgalmat, az internetre nyitott portokat, az internet-hozzáférést megkísérelő alkalmazásokat vagy virtuális gépeket.
- Kapacitáskihasználtság, amely segít kiküszöbölni a túlkiépítés vagy az alulkihasználtság problémáit.

Továbbra is támaszkodhat a diagnosztikai beállításokra, hogy NSG-naplókat küldjön a Log Analytics szolgáltatásba, így a meglévő mentett keresések, riasztások és irányítópultok továbbra is működni fognak. Azok az ügyfelek, akik már telepítették a megoldást, további értesítésig továbbra is használhatják azt. Szeptember 5-től a Network Security Group Analytics megoldás törlődik a piactérről, és a közösségen keresztül elérhetővé válik [Azure QuickStart sablonként.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights)

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Ha [összekapcsolta az Operations Manager felügyeleti csoportját a Log Analytics szolgáltatással,](om-agents.md)akkor továbbra is módosítások nélkül fog működni. Új kapcsolatok esetén azonban az Operations Management Suite konfigurálásához kövesse a [Microsoft System Center Operations Manager Felügyeleti csomag utasításait.](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/)

## <a name="next-steps"></a>További lépések
- [Az OMS-portálról az Azure-portálra a Log Analytics-felhasználók számára való áttéréssel kapcsolatos gyakori kérdések](oms-portal-faq.md) című témakörben útmutatást talál az OMS-portálról az Azure Portalra való áttéréshez.
