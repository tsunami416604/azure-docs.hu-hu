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
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: 356c1343443b33e565c65ef0693b8d8455ff1d1b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="extend-copy-alerts-from-oms-portal-into-azure"></a>Terjessze ki (Másolás) riasztások a OMS-portálon az Azure
Az Operations Management Suite (OMS) portál csak a Log Analyticshez riasztásokat jeleníti meg.  Az új riasztások felületet most integrálva van a riasztási élmény különböző szolgáltatások és a Microsoft Azure-része. Az új felület érhető el, **riasztások** Azure figyelése az Azure-ban a portál tartalmazza napló tevékenységriasztásokat, a metrika riasztások és a napló riasztások Naplóelemzés és az Application Insights. 


De az egyes felhasználók Naplóelemzési és allied funkciók, például a riasztások, keresztül lett [Microsoft művelet felügyeleti csomaggal (OMS) portal](../operations-management-suite/operations-management-suite-overview.md). És így és az egyéb Azure-erőforrások mellett Log Analytics - használatát, könnyedén kezelhető rendszeresen Microsoft rendelkezik lett annak biztosítására, hogy az OMS-portálon képességeit is elérhető az Azure portálon. Abban a legutóbb, Azure riasztások már lehetővé teszi a felhasználóknak a lekérdezés alapú riasztások kezelése Naplóelemzési, további információkért lásd: [Azure riasztások-riasztások jelentkezzen](monitor-alerts-unified-log.md). A riasztások az Azure-figyelő az OMS-portálon létrehozott riasztások már fel vannak sorolva megfelelő naplóelemzési munkaterület. De bármely szerkesztéséhez vagy megváltozik az ilyen riasztások az OMS-portálon létrehozott, a felhasználónak a Azure, és használja az OMS-portálon; Ha arra szükség kezeléséhez minden más szolgáltatáshoz, majd vissza Azure kíván. Az átmenet csökkentése érdekében Microsoft most engedélyezése felhasználók kiterjeszteni a riasztásokat az OMS-portálon az Azure.

## <a name="benefits-of-extending-your-alerts"></a>A riasztások kiterjesztése előnyei
Nem rendelkezik Azure-portálon kívül megnyitása a elhatárolt juttatásra, leszámítva szempontból is más mintájára riasztások bővíteni az OMS-portálon az Azure

- Eltérően az az OMS-portálon, ahol csak 250 riasztások nem sikerült létrehozni és várólistákból. az Azure-riasztások Ez a korlátozás nincs jelen
- Az Azure riasztásokból a riasztási típusokat kezelhető, előnyeiről, és megtekinthetők; nem csak a Naplóelemzési riasztást küld, mint az OMS-portálon esetében
- Az Azure riasztások használatára [művelet csoportok](monitoring-action-groups.md), amelyek lehetővé teszik, hogy az egyes riasztások, például SMS, hang-hívás, Automation-Runbook, Webhook, ITSM összekötő és egyéb egynél több művelettel rendelkezik. Mivel a Naplóelemzési riasztások mindkét a számát, valamint korlátozott, mivel a(z) típusú műveletek lehetséges

## <a name="process-of-extending-your-alerts"></a>A folyamat, amely a figyelmeztetéseket
A riasztások kiterjesztésének az OMS-portálon az Azure, a folyamat nem **nem** a riasztás definíciója, lekérdezés vagy konfiguráció az bármely módon módosítani. Mindössze annyi a változás, szükség, hogy az Azure, az összes műveleteket végez, például e-mail-értesítések webhook hívás, automation-runbook fut, vagy ITSM eszköz csatlakozik végzett művelet csoport keresztül. Ezért ha a megfelelő lépéseket csoporthoz társítva a riasztás - azok lesz lesz kiterjeszthetőek az Azure.

Mivel a folyamat, amely során visszafordítható, és nem interruptive, Microsoft kiterjesztése-e riasztások automatikusan létrehozni az OMS-portálon a riasztásokra az Azure - től kezdődő **2018. április 23**. A nap, a Microsoft megkezdődik a riasztások kiterjeszti Azure ütemezése és fokozatosan az OMS-portálon, kezelhető az Azure portálon található összes riasztás. 

Naplóelemzési munkaterület riasztások Azure kiterjeszti az beszerzése ütemezése, akkor azok továbbra is működnek, és a rendszer **nem** valamilyen módon veszélyeztetheti a figyelést. Ütemezése, a riasztások nem érhető el módosítása/szerkesztésre ideiglenesen; azonban új Azure riasztásokat továbbra is a rövid idő hozhatók létre. Rövid ebben az időszakban Ha bármely módosítása vagy a riasztás létrehozása az OMS-portálon végezhető el felhasználók kell a beállítást, így folytathatja az Azure Naplóelemzés vagy Azure riasztást.

 ![Ütemezett időszak alatt a riasztások átirányítja az Azure-bA felhasználói művelet](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Riasztások kiterjedő OMS-portálon az Azure-ba nem kerül sor, és Azure használatát riasztásainak lekérdezés alapú Naplóelemzési riasztások alapján nem számlázzuk, korlátozások és feltételek, mint a belül használatakor [házirend árképzési Azure-figyelő](https://azure.microsoft.com/en-us/pricing/details/monitor/)  

Felhasználók élvezheti az előnyöket, amely a riasztást; Ez a dátum előtt által önkéntesen engedélyezés, hogy a riasztások kezelhető az Azure-ban.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Hogyan önkéntesen kiterjesztése a riasztások
Egy egyszerű fázis Azure riasztások az OMS-felhasználók engedélyezéséhez Microsoft eszközöket kiterjesztése a riasztások hozott létre. Microsoft OMS-portál felhasználók kiterjesztheti a riasztásokat az Azure vagy az OMS-portálon (vagy) egy olyan új API-val programozott megközelítés varázsló. További információkért lásd: [riasztások kiterjeszti az OMS-portálon és API-t használó Azure](monitoring-alerts-extend-tool.md).


## <a name="usage-after-extending-your-alerts"></a>A riasztások történő bővítése után kihasználtsága
Amint azt, a Microsoft művelet felügyeleti csomag létrehozott riasztások kiterjeszthetőek Azure riasztások; Miután, amelyen kezelheti azokat az Azure-ból. Riasztások továbbra is az OMS-portálon - riasztási beállítás szakaszban szerepel. Alább látható módon:

 ![Az Azure kiterjesztendő után értesítések listázása OMS-portálon](./media/monitor-alerts-extend/PostExtendList.png)

Riasztások, például a Szerkesztés és az OMS-portálon végzett létrehozási műveletek felhasználók transzparens módon jutnak Azure riasztásokat. Riasztás létrehozása továbbra is a meglévő [napló Analytics API](../log-analytics/log-analytics-api-alerts.md) a korábbi, csak kisebb módosítását, hogy a után riasztások kiterjeszthetőek az Azure - művelet csoportok ütemezésben kell társítani kell.

## <a name="next-steps"></a>További lépések

* Ismerje meg, az eszközöket, amelyekkel a [kezdeményezhet kiterjeszti az OMS Szolgáltatáshoz riasztások Azure](monitoring-alerts-extend-tool.md)
* További információ az új [Azure riasztások élmény](monitoring-overview-unified-alerts.md).
* További tudnivalók [riasztások jelentkezzen be Azure riasztások](monitor-alerts-unified-log.md).
