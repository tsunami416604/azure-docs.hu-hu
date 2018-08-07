---
title: Azure-bA az OMS-portálon |} A Microsoft Docs
description: Az OMS-portálon az összes funkciót, az Azure-portálra sunsetted folyamatban van. Ez a cikk részletesen az átállás.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: b9fb32f4f014f8e0fb67b558a2806d74edaac56c
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576015"
---
# <a name="oms-portal-moving-to-azure"></a>Azure-bA az OMS-portálon

> [!NOTE]
> Ez a cikk az Azure nyilvános felhő és a kormányzati felhő, kivéve, ha nincs másként jelölve vonatkozik.

Hallgassa meg ismételten a Log Analytics az ügyfelektől származó visszajelzés egy részét, a szükséges figyelését és kezelését mind a helyszíni és az Azure-beli számítási egységes felhasználói élményt. Valószínűleg ismeri az Azure Portalon a hub Azure-szolgáltatásokhoz, és egy gazdag felügyeleti funkciókat biztosítanak, mint az irányítópultokat a erőforrások, az intelligens keresési eredmény erőforrások és a resource Management-környezetet kínál. Összefogása és egyszerűsíthetők a figyelési és felügyeleti munkafolyamat, hogy elindult az OMS-portál funkciókkal be az Azure Portalra. Örömmel jelentjük be, az OMS-portálon funkcióját a legtöbb immár része az Azure Portalon. Sőt az új funkciók, például a Traffic Analytics némelyike csak az Azure Portalon érhető el. Nincsenek van hátra, beleértve néhány megoldás, amely továbbra is megtalálhatók az Azure Portalon történő áthelyezésének folyamat csak néhány hiányosságokat. Ha nem használ, akkor ezeket a funkciókat, lesz elérnie mindent csinált az OMS-portálon az Azure Portalon, stb. Ha ezt még nem tette meg, javasoljuk, indítsa el még ma az Azure portal használatával! 

Zárja le a két különböző portál érhető által 2018 augusztus közötti fennmaradó hézagok várhatóan. Ügyfeleink visszajelzései alapján, hogy fog kommunikálni az OMS-portálon sunsetting ütemtervét. Az Azure Portalra, és hatással vannak a Váltás könnyen izgatottak vagyunk. De tisztában vagyunk azzal módosítások nehéz és zavart okozhatnak. Esetleges kérdéseivel, visszajelzés, és a Küldés **LAUpgradeFeedback@microsoft.com**. Ez a cikk a főbb forgatókönyvek megvalósítását, a jelenlegi rések és az átállás ütemterv keresztül haladnak. 

## <a name="progress"></a>Előrehaladás
Az alábbiakban a jelen cikk korábbi verziók óta végrehajtott frissítéseket.

### <a name="july-27"></a>Július 27.

- [DNS Analytics](log-analytics-dns.md) most már teljes körűen működik az Azure Portalon.
- [Frissítéskezelés](../automation/automation-update-management.md) frissítve lett, hogy teljes körű működéséhez az Azure Portalon. Lásd: [Azure-bA Migrálhatja az OMS Frissítéstelepítések](../automation/migrate-oms-update-deployments.md) részleteiről.
- [Riasztások](#changes-to-alerts) most már teljes mértékben bővültek be az Azure Portalra.
- [Egyéni naplók előzetes verziójú funkció](log-analytics-data-sources-custom-logs.md) automatikusan engedélyezve van minden munkaterülethez.

## <a name="what-will-change"></a>Milyen változások? 
A következő módosításokat vannak alatt bejelentette, az OMS-portálon elavulása. Ezek a változások leírását az alábbi szakaszokban részletesebben.

- Hozzon létre új munkaterületek csak az Azure Portalon lehet.
- Az Új riasztási megoldást lecseréli a riasztás-kezelési megoldásban.
- Felhasználói hozzáférés kezelése az Azure Portalon az Azure szerepköralapú hozzáférés-vezérlés használatával történik.
- Az Application Insights-összekötő már nem szükséges, mivel ugyanazokat a funkciókat több munkaterületen lekérdezések keresztül engedélyezhető.
- Az OMS Mobilalkalmazásról elavulttá válik. 
- Az NSG-megoldás a Traffic Analytics megoldás elérhető a továbbfejlesztett funkció helyére.
- A System Center Operations Manager Log Analytics és az új kapcsolat szükséges a frissített felügyeleti csomagokat.


## <a name="current-known-gaps"></a>Jelenlegi ismert hézagok 
Jelenleg néhány funkciókorlátozásokat, amely még mindig használja az OMS-portálon. E hiányosságok a zárt vannak, és ez a dokumentum megfelelően frissülnek. Emellett tekintse át [Azure frissítések](https://azure.microsoft.com/updates/?product=log-analytics) -bővítmények és a változások áttekintése a folyamatban lévő hirdetmények.

- A következő megoldások még nem teljesen működőképes, az Azure Portalon. Kell továbbra is használhatja ezeket a megoldásokat a klasszikus portálon, amíg a mezők frissítése.

    - Windows Analytics megoldásokhoz ([Upgrade Readiness](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started), [Eszközállapot](https://docs.microsoft.com/windows/deployment/update/device-health-monitor), és [a frissítések megfelelősége](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started))
    - [Surface Hub](log-analytics-surface-hubs.md)

-  Az Azure Log Analytics-erőforrás eléréséhez a felhasználó hozzáférést kell biztosítani keresztül [Azure szerepköralapú hozzáférés-](#user-access-and-role-migration).


## <a name="what-should-i-do-now"></a>Mit tegyek most?  
Hivatkozzon [gyakori kérdések a Log Analytics-felhasználók az Azure Portalon OMS-portálon való váltás](../log-analytics/log-analytics-oms-portal-faq.md) az Azure Portalon való áttéréssel kapcsolatos információkat. Ha a [hézagokat a fent leírt](#current-known-gaps) nem vonatkoznak a környezetben, akkor érdemes lehet indítása az Azure portal használatával, az elsődleges felhasználói élményt. Küldése a olyan visszajelzést, kérdéseket vagy problémákat **LAUpgradeFeedback@microsoft.com**.

A legtöbb szolgáltatások továbbra is működni bármilyen migrálás végrehajtása nélkül. Kivételek az alábbiakban láthatók.

- Lásd: [Azure-bA Migrálhatja az OMS Frissítéstelepítések](../automation/migrate-oms-update-deployments.md) az Update Management megoldás transitioning részleteiért. 

## <a name="new-workspaces"></a>Új munkaterületek
Július 29-től kezdődően, már nem lesz létre tudja hozni az OMS-portálon új munkaterületek. Kövesse az útmutató [Log Analytics-munkaterület létrehozása az Azure Portalon](log-analytics-quick-create-workspace.md) új munkaterület létrehozása az Azure Portalon.

## <a name="changes-to-alerts"></a>Riasztások módosításait 

### <a name="alert-extension"></a>Riasztások kiterjesztése  

> [!NOTE]
> Riasztások most már teljes mértékben bővültek be az Azure Portalra, a nyilvános felhőben. Meglévő riasztási szabályait tekinthetnek meg az OMS-portálon, de azok csak az Azure Portalon lehet kezelni. Riasztások az Azure Portalon történő kiterjesztését az Azure government cloud 2018 október kezdődik.

Riasztások folyamatban vannak [kiterjeszthetők az Azure Portalon](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Miután ez befejeződött, a riasztásokat a felügyeleti műveletek csak az Azure Portalon érhető el. Meglévő riasztásokat továbbra is megjelennek az OMS-portálon. Ha a riasztásokat programozott a Log Analytics Alert REST API vagy a Log Analytics riasztási Resource-sablon használatával, szüksége Műveletcsoportok használata az API-hívások, Azure Resource Manager-sablonok és PowerShell-parancsok műveletei helyett.

### <a name="alert-management-solution"></a>Riasztáskezelés megoldás
Helyett a [felügyeleti megoldás riasztás](log-analytics-solution-alert-management.md), használható [Azure Monitor riasztási felület egyesített](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) jelenítheti meg és kezelheti a riasztásokat. Az új felületet is beleértve naplóriasztások az Azure Log Analytics belül több forrásból származó összesíti azokat a riasztásokat. Tekintse meg a riasztások disztribúciók, intelligens csoportok keresztül kapcsolódó riasztások Automatikus csoportosítás előnyeit, és megtekintheti a riasztásokat több előfizetésre kiterjedő gazdag szűrők alkalmazása közben. Ezek a szolgáltatások előzetes verziója 2018. június 4 kezdve érhetők el. A riasztási felügyeleti megoldás nem lesz elérhető az Azure Portalon. 

A riasztási felügyeleti megoldások (riasztás típussal rendelkező rekordok) által gyűjtött adatok továbbra is a Log Analytics mindaddig, amíg a megoldás telepítve van-e a munkaterületen. 2018 augusztus kezdődően a munkaterületek, egységes riasztásokban riasztások streamelési engedélyezve lesz, és cserélje le ezt a funkciót. Néhány sémaváltozások várható, és később lesz bejelentve.

## <a name="user-access-and-role-migration"></a>Felhasználói hozzáférés és a szerepkör áttelepítése
Az Azure portál kezelési gazdagabb és hatékonyabb, mint a hozzáférés-kezelés az OMS-portálon, de ehhez néhány átalakításhoz szükség. Lásd: [munkaterületeinek kezeléséhez](log-analytics-manage-access.md#manage-accounts-and-users) részletes hozzáférés-kezelés a Log Analyticsben.

Indítása. július 30 automatikus átalakítás a hozzáférés-vezérlési engedélyekkel az OMS-ből az Azure-portál engedélyeket a portálon indul el. Ha az átalakítás befejeződött, a az OMS-portál felhasználói felügyeleti szakaszban irányíthatja a felhasználókat a hozzáférés-vezérlés (IAM) az Azure-ban. 

Az átalakítás során a rendszer minden egyes felhasználó vagy biztonsági csoportot, amely engedélyekkel rendelkezik az OMS-portálon ellenőrizze, és határozza meg, ha rendelkezik azonos szinten vagy engedélyek az Azure-ban. Ha engedélyek hiányzik, azt fogja hozzárendelni a megfelelő munkaterületek és a megoldások a következő szerepkörök.

| OMS-portál engedély | Azure-szerepkörök |
|:---|:---|
| ReadOnly | Log Analytics olvasó |
| Közreműködő | Log Analytics közreműködő |
| Rendszergazda | Tulajdonos |

Győződjön meg arról, hogy nincs túl sok engedéllyel rendelkező felhasználókhoz rendelt, hogy a rendszer nem automatikusan rendel hozzá ezeket az engedélyeket az az erőforráscsoport szintjén. Ennek eredményeképpen munkaterület a rendszergazdák kell hozzárendelheti manuálisan, maguk _tulajdonosa_ vagy _közreműködői_ szerepkörök szintjén erőforrás csoportba vagy előfizetésbe a következő műveletek végrehajtásához.

- Megoldások hozzáadásához vagy eltávolításához
- Új egyéni nézeteinek definiálásához
- Riasztások kezelése 

Bizonyos esetekben az Automatikus átalakítás engedély nem alkalmazható, és felkéri a rendszergazdát, manuálisan az engedélyeket.

## <a name="oms-mobile-app"></a>OMS mobilalkalmazást
Az OMS mobilalkalmazást lesz sunsetted együtt az OMS-portálon. Helyett az OMS mobilalkalmazást az informatikai infrastruktúra, irányítópultok és mentett lekérdezések eléréséhez keresztül elérhető az Azure Portalon közvetlenül a böngészőből a mobil eszközére. Értesítéseket kaphat, konfigurálnia kell [Azure Action Groups általi](../monitoring-and-diagnostics/monitoring-action-groups.md) értesítések fogadásához, SMS vagy hanghívás formájában

## <a name="application-insights-connector-and-solution"></a>Application Insights-összekötő és megoldás
[Application Insights-összekötő](log-analytics-app-insights-connector.md) lehetővé teszi az Application Insights adatokat importálnak a Log Analytics-munkaterületet. Az adatdeduplikáció volt szükség infrastruktúra és az alkalmazás adatok láthatóságának engedélyezése.

A támogatási [erőforrások közötti lekérdezések](log-analytics-cross-workspace-search.md), már nem létezik a szükséges adatokat. Mint ilyen a meglévő Application Insights-megoldáshoz elavulttá válik. Július kezdve nem lesz új Application Insights-erőforrások összekapcsolása a Log Analytics-munkaterületeket. Meglévő kapcsolatokat, és az irányítópultok továbbra is működik. November 2018-ig.


## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
A [Azure hálózati biztonsági csoport Analytics megoldás](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) fogja írni a nemrégiben megjelent a [Traffic Analytics](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/) amelyek felhasználói és alkalmazástevékenységekbe rálátást biztosít a felhőalapú hálózatokhoz. A TRAFFIC Analytics segítségével a szervezet hálózati tevékenység, a biztonságos alkalmazások és adatok naplózása, a számítási feladatok teljesítményének optimalizálása és a megfelelnek. 

Ez a megoldás elemzi a hálózati biztonsági csoportok naplóinak és elemezheti a következő.

- A hálózatok között az Azure és az Internet, nyilvános felhő-régiók, virtuális hálózatok és alhálózatok közötti forgalmat.
- Alkalmazások és protokollok a hálózaton, szimatolók vagy dedikált folyamat gyűjtemény berendezéseket szükségessége nélkül.
- Fő kapcsolattartók, forgalmas alkalmazások, virtuális gép beszélgetések a felhőben, a legaktívabb.
- Forrásaként és céljaként a forgalom virtuális hálózatai, kritikus fontosságú üzleti szolgáltatásokat és alkalmazásokat között helyek közötti kapcsolatok között.
- Biztonság, többek között a rosszindulatú adatforgalom, az internethez, az alkalmazások és a virtuális gépek Internet-hozzáférés megkísérlése nyitott portokkal.
- Kapacitás használata, amely segít a problémák kiépítés vagy alulkihasználtságának kiküszöbölése.

Továbbra is számíthat a diagnosztikai beállítások NSG-naplók a Log Analytics szolgáltatásba, így a meglévő mentett keresések, riasztások, az irányítópultok továbbra is működni fog küldeni. Ügyfelek, akik már telepítették a megoldás továbbra is használhatja azt további értesítésig. Augusztus 15-től kezdődően a hálózati biztonsági csoport Analytics megoldás a rendszer eltávolítja a marketplace-ről és a Közösség keresztül elérhetővé tett egy [Azure gyorsindítási sablon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Ha [Log Analytics az Operations Manager felügyeleti csoport csatlakoztatva](log-analytics-om-agents.md), akkor továbbra is működik, nem kell módosítania. Az új kapcsolatok, kövesse az útmutató [konfigurálása az Operations Management Suite a Microsoft System Center Operations Manager felügyeleti csomag](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>További lépések
- Lásd: [gyakori kérdések a Log Analytics-felhasználók az Azure Portalon OMS-portálon való váltás](log-analytics-oms-portal-faq.md) útmutatást áthelyezését az OMS-portálon az Azure Portalon.