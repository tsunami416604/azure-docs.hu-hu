---
title: Terjessze ki (Másolás) riasztások a OMS-portálon az Azure - áttekintése |} Microsoft Docs
description: Másolás riasztásainak OMS-portálon az Azure riasztások, részletek körül közös vásárlói kérdésekre folyamatának áttekintése.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: vinagara
ms.openlocfilehash: 25dcbad8607a651a7dd4b79f4f418cc473a2bf0e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="extend-copy-alerts-from-oms-portal-into-azure"></a>Terjessze ki (Másolás) riasztások a OMS-portálon az Azure
Az Operations Management Suite (OMS) portál csak a Log Analyticshez riasztásokat jeleníti meg.  Az új riasztások felületet most integrálva van a riasztási élmény különböző szolgáltatások és a Microsoft Azure-része. Az új felület érhető el, **riasztások** Azure figyelése az Azure-ban a portál tartalmazza napló tevékenységriasztásokat, a metrika riasztások és a napló riasztások Naplóelemzés és az Application Insights. 


De az egyes felhasználók Naplóelemzési és allied funkciók, például a riasztások, keresztül lett [Microsoft művelet felügyeleti csomaggal (OMS) portal](../operations-management-suite/operations-management-suite-overview.md). És így és az egyéb Azure-erőforrások mellett Log Analytics - használatát, könnyedén kezelhető rendszeresen Microsoft rendelkezik lett annak biztosítására, hogy az OMS-portálon képességeit is elérhető az Azure portálon. Abban a legutóbb, Azure riasztások már lehetővé teszi a felhasználóknak a lekérdezés alapú riasztások kezelése Naplóelemzési, további információkért lásd: [Azure riasztások-riasztások jelentkezzen](monitor-alerts-unified-log.md). A riasztások az Azure-figyelő az OMS-portálon létrehozott riasztások már fel vannak sorolva megfelelő naplóelemzési munkaterület. De bármely szerkesztéséhez vagy megváltozik az ilyen riasztások az OMS-portálon létrehozott, a felhasználónak a Azure, és használja az OMS-portálon; Ha arra szükség kezeléséhez minden más szolgáltatáshoz, majd vissza Azure kíván. Az átmenet csökkentése érdekében Microsoft most engedélyezése felhasználók kiterjeszteni a riasztásokat az OMS-portálon az Azure.

## <a name="benefits-of-extending-your-alerts"></a>A riasztások kiterjesztése előnyei
Nem rendelkezik Azure-portálon kívül megnyitása a elhatárolt juttatásra, leszámítva szempontból is más mintájára riasztások bővíteni az OMS-portálon az Azure

- Eltérően az az OMS-portálon, ahol csak 250 riasztások nem sikerült létrehozni és várólistákból. az Azure-riasztások Ez a korlátozás nincs jelen
- Az Azure riasztásokból a riasztási típusokat kezelhető, előnyeiről, és megtekinthetők; nem csak a Naplóelemzési riasztást küld, mint az OMS-portálon esetében
- Felhasználók csak a figyelést és riasztást használatával való hozzáférés szabályozása [Azure figyelő szerepkör](monitoring-roles-permissions-security.md)
- Az Azure riasztások használatára [művelet csoportok](monitoring-action-groups.md), amelyek lehetővé teszik, hogy az egyes riasztások, például SMS, hang-hívás, Automation-Runbook, Webhook, ITSM összekötő és egyéb egynél több művelettel rendelkezik. 

## <a name="process-of-extending-your-alerts"></a>A folyamat, amely a figyelmeztetéseket
A riasztások kiterjesztésének az OMS-portálon az Azure, a folyamat nem **nem** a riasztás definíciója, lekérdezés vagy konfiguráció az bármely módon módosítani. Mindössze annyi a változás, szükség, hogy az Azure, az összes műveleteket végez, például e-mail-értesítések webhook hívás, automation-runbook fut, vagy ITSM eszköz csatlakozik végzett művelet csoport keresztül. Ezért ha a megfelelő lépéseket csoporthoz társítva a riasztás - azok lesz lesz kiterjeszthetőek az Azure.

Mivel a folyamat, amely során visszafordítható, és nem interruptive, Microsoft kiterjesztése-e riasztások automatikusan létrehozni az OMS-portálon a riasztásokra az Azure - től kezdődő **2018. május 14**. A nap, a Microsoft megkezdődik a riasztások kiterjeszti Azure ütemezése és fokozatosan jelen az OMS-portálon, Azure-portálon is kezelhető az összes riasztás. 

> [!NOTE]
> Május 14 2018 - megkezdése a Microsoft megkezdődik a folyamat automatikusan kiterjeszti a riasztások az Azure-bA. Nem minden munkaterületekkel és riasztások kibővíti a mai napon; helyette a Microsoft megkezdik terjesztheti ki az értesítések automatikusan részletben jövőbeli hét. Ezért a riasztásokat az OMS-portálon fog automatikus-terjed ki az Azure közvetlenül a 2018. május 14., és felhasználó is még [manuálisan kiterjesztése a figyelmeztetéseket](monitoring-alerts-extend-tool.md) ebben az időszakban.

Naplóelemzési munkaterület riasztások Azure kiterjeszti az beszerzése ütemezése, akkor azok továbbra is működnek, és a rendszer **nem** valamilyen módon veszélyeztetheti a figyelést. Ütemezése, a riasztások nem érhető el módosítása/szerkesztésre ideiglenesen; azonban új Azure riasztásokat továbbra is a rövid idő hozhatók létre. Rövid ebben az időszakban Ha bármely módosítása vagy a riasztás létrehozása az OMS-portálon végezhető el felhasználók kell a beállítást, így folytathatja az Azure Naplóelemzés vagy Azure riasztást.

 ![Ütemezett időszak alatt a riasztások átirányítja az Azure-bA felhasználói művelet](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Riasztások kiterjedő OMS-portálon az Azure-ba nem kerül sor, és Azure használatát riasztásainak lekérdezés alapú Naplóelemzési riasztások alapján nem számlázzuk, korlátozások és feltételek, mint a belül használatakor [házirend árképzési Azure-figyelő](https://azure.microsoft.com/pricing/details/monitor/)  

Felhasználók élvezheti az előnyöket, amely a riasztást; Ez a dátum előtt által önkéntesen engedélyezés, hogy a riasztások kezelhető az Azure-ban.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Hogyan önkéntesen kiterjesztése a riasztások
Egy egyszerű fázis Azure riasztások az OMS-felhasználók engedélyezéséhez Microsoft eszközöket kiterjesztése a riasztások hozott létre. Microsoft OMS-portál felhasználók kiterjesztheti a riasztásokat az Azure vagy az OMS-portálon (vagy) egy olyan új API-val programozott megközelítés varázsló. További információkért lásd: [riasztások kiterjeszti az OMS-portálon és API-t használó Azure](monitoring-alerts-extend-tool.md).


## <a name="usage-after-extending-your-alerts"></a>A riasztások történő bővítése után kihasználtsága
Amint azt, a Microsoft művelet felügyeleti csomag létrehozott riasztások kiterjeszthetőek Azure riasztások; Miután, amelyen kezelheti azokat az Azure-ból. Riasztások továbbra is az OMS-portálon - riasztási beállítás szakaszban szerepel. Alább látható módon:

 ![Az Azure kiterjesztendő után értesítések listázása OMS-portálon](./media/monitor-alerts-extend/PostExtendList.png)

Riasztások, például a Szerkesztés és az OMS-portálon végzett létrehozási műveletek felhasználók transzparens módon jutnak Azure riasztásokat. 

> [!NOTE]
> Amikor a felhasználók transzparens módon megnyílik az Azure-ba, a bármely hozzáadása vagy szerkesztése műveletet a megfelelő riasztásra OMS - biztosítása a felhasználók megfelelően vannak hozzárendelve a megfelelő [Azure figyelő és riasztás használatára vonatkozó engedélyek](monitoring-roles-permissions-security.md)

Riasztás létrehozása továbbra is a meglévő [napló Analytics API](../log-analytics/log-analytics-api-alerts.md) és [napló Analytics erőforrás sablon](../monitoring/monitoring-solutions-resources-searches-alerts.md) a korábbi, folyamatban, amely után a riasztások kiterjeszthetőek az Azure - művelet csoportok csak kismérvű változás az ütemezés kell társítani kell.

## <a name="next-steps"></a>További lépések

* Ismerje meg, az eszközöket, amelyekkel a [kezdeményezhet kiterjeszti az OMS Szolgáltatáshoz riasztások Azure](monitoring-alerts-extend-tool.md)
* További információ az új [Azure riasztások élmény](monitoring-overview-unified-alerts.md).
* További tudnivalók [riasztások jelentkezzen be Azure riasztások](monitor-alerts-unified-log.md).
