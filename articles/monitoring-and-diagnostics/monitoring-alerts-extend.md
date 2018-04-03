---
title: Terjessze ki a riasztások a OMS az Azure - áttekintése |} Microsoft Docs
description: Folyamat, amely során riasztások az OMS Szolgáltatáshoz az Azure riasztások, részletek körül közös ügyfél szempontok áttekintése.
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
ms.openlocfilehash: abab73a047fb923fa2cd57a1c7194e440697bf64
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="extend-alerts-from-oms-into-azure"></a>Terjessze ki a riasztások a OMS az Azure
Az új riasztások felületet most integrálva van a riasztási élmény különböző szolgáltatások és a Microsoft Azure-része. Az új felület érhető el, **riasztások** Azure figyelő portálon, a indított együtt közös helyen - napló tevékenységriasztásokat, a metrika riasztások és a napló Naplóelemzési, valamint az Application Insights-riasztások. 

De az egyes felhasználók Naplóelemzési és allied funkciók, például a riasztások, keresztül lett [Microsoft művelet felügyeleti csomaggal (OMS)](../operations-management-suite/operations-management-suite-overview.md). És így és az egyéb Azure-erőforrások mellett Log Analytics - használatát, könnyedén kezelhető rendszeresen Microsoft rendelkezik lett annak biztosítására, hogy az OMS képességeit is elérhető az Azure portálon. Abban a legutóbb, Azure riasztások már lehetővé teszi a felhasználóknak a lekérdezés alapú riasztások kezelése Naplóelemzési, további információkért lásd: [Azure riasztások-riasztások jelentkezzen](monitor-alerts-unified-log.md). A riasztások az Azure-figyelő OMS létrehozott riasztások már fel vannak sorolva megfelelő naplóelemzési munkaterület. De bármely szerkesztéséhez vagy megváltozik az ilyen riasztások OMS létrehozott, a felhasználónak a Azure, és használja az OMS; Ha arra szükség kezeléséhez minden más szolgáltatáshoz, majd vissza Azure kíván. Az átmenet csökkentése érdekében a Microsoft most engedélyezése felhasználók ki szeretné terjeszteni a riasztásokat az OMS Szolgáltatáshoz Azure.

## <a name="benefits-of-extending-your-alerts"></a>A riasztások kiterjesztése előnyei
Nem rendelkezik Azure-portálon kívül megnyitása a elhatárolt juttatásra, leszámítva szempontból is más mintájára riasztások bővíteni az OMS Szolgáltatáshoz az Azure

- Eltérően az OMS-ben, ahol csak 250 riasztások nem sikerült létrehozni és várólistákból. az Azure-riasztások Ez a korlátozás nincs jelen
- Az Azure riasztásokból a riasztási típusokat kezelhető, előnyeiről, és megtekinthetők; nem csak a Naplóelemzési riasztást küld, mint az OMS esetében
- Az Azure riasztások használatára [művelet csoportok](monitoring-action-groups.md), amelyek lehetővé teszik, hogy az egyes riasztások, például SMS, hang-hívás, Automation-Runbook, Webhook, ITSM összekötő és egyéb egynél több művelettel rendelkezik. Mivel az OMS-riasztások mindkét a számát, valamint korlátozott, mivel a(z) típusú műveletek lehetséges

## <a name="process-of-extending-your-alerts"></a>A folyamat, amely a figyelmeztetéseket
A riasztások kiterjesztésének az OMS Szolgáltatáshoz az Azure, a folyamat nem **nem** a riasztás definíciója, lekérdezés vagy konfiguráció az bármely módon módosítani. Mindössze annyi a változás, szükség, hogy az Azure, az összes műveleteket végez, például e-mail-értesítések webhook hívás, automation-runbook fut, vagy ITSM eszköz csatlakozik végzett művelet csoport keresztül. Ezért ha a megfelelő lépéseket csoporthoz társítva a riasztás - azok lesz lesz kiterjeszthetőek az Azure.

Mivel a folyamat, amely során visszafordítható, és nem interruptive, Microsoft kiterjesztése-e a riasztásokat az Operations Management Suite (OMS) automatikusan létrehozza a riasztásokra az Azure - től kezdődő **2018. április 23.**. A nap, a Microsoft ütemezése, a riasztások kiterjeszti Azure kezdődniük, és fokozatosan ellenőrizze az OMS-ben, kezelhető az Azure portálon található összes riasztás. 

Riasztások az OMS-munkaterület Azure kiterjeszti az beszerzése ütemezése, akkor azok továbbra is működnek, és a rendszer **nem** valamilyen módon veszélyeztetheti a figyelést. Ütemezése, a riasztások nem érhető el módosítása/szerkesztésre ideiglenesen; azonban új Azure riasztásokat továbbra is a rövid idő hozhatók létre. Rövid ebben az időszakban Ha bármely módosítása vagy a riasztás létrehozása történik az OMS Szolgáltatáshoz, felhasználók kell a beállítást, így folytathatja az Azure Naplóelemzés vagy Azure riasztást.

 ![Ütemezett időszak alatt a riasztások átirányítja az Azure-bA felhasználói művelet](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Kiterjesztése a riasztásokat az Operations Management Suite (OMS) az Azure-ba nem kerül sor, és Azure használatát riasztásainak lekérdezés alapú Naplóelemzési riasztások alapján nem számlázzuk, korlátozások és feltételek, mint a belül használatakor [házirend árképzési Azure-figyelő](https://azure.microsoft.com/en-us/pricing/details/monitor/)  

Felhasználók élvezheti az előnyöket, amely a riasztást; Ez a dátum előtt által önkéntesen engedélyezés, hogy a riasztások kezelhető az Azure-ban.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Hogyan önkéntesen kiterjesztése a riasztások
Egy egyszerű fázis Azure riasztások az OMS-felhasználók engedélyezéséhez Microsoft eszközöket kiterjesztése a riasztások hozott létre. A Microsoft Operations Management Suite (OMS) ügyfelek kiterjesztheti a riasztásokat az Azure vagy az OMS-portálon (vagy) egy olyan új API-val programozott megközelítés varázsló. További információkért lásd: [riasztások kiterjeszti az OMS-portálon és API-t használó Azure](monitoring-alerts-extend-tool.md).


## <a name="usage-after-extending-your-alerts"></a>A riasztások történő bővítése után kihasználtsága
Amint azt, a Microsoft művelet felügyeleti csomag létrehozott riasztások kiterjeszthetőek Azure riasztások; Miután, amelyen kezelheti azokat az Azure-ból. Riasztások továbbra is az OMS-portálon - riasztási beállítás szakaszban szerepel. Alább látható módon:

 ![Az Azure kiterjesztendő után értesítések listázása OMS-portálon](./media/monitor-alerts-extend/PostExtendList.png)

Riasztások, például a Szerkesztés és az OMS-portálon végzett létrehozási műveletek felhasználók transzparens módon jutnak Azure riasztásokat. Riasztás létrehozása továbbra is a meglévő [napló Analytics API](../log-analytics/log-analytics-api-alerts.md) a korábbi, csak kisebb módosítását, hogy a után riasztások kiterjeszthetőek az Azure - művelet csoportok ütemezésben kell társítani kell.

## <a name="next-steps"></a>További lépések

* Ismerje meg, az eszközöket, amelyekkel a [kezdeményezhet kiterjeszti az OMS Szolgáltatáshoz riasztások Azure](monitoring-alerts-extend-tool.md)
* További információ az új [Azure riasztások élmény](monitoring-overview-unified-alerts.md).
* További tudnivalók [riasztások jelentkezzen be Azure riasztások](monitor-alerts-unified-log.md).
