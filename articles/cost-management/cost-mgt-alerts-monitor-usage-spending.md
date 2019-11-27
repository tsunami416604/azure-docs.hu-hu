---
title: A használat és a kiadások figyelése a költségekkel kapcsolatos riasztásokkal | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan segít a költségekkel kapcsolatos riasztások a használat és a kiadások figyelésében a Azure Cost Managementban.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: alavital
ms.custom: ''
ms.openlocfilehash: 4be484cdff2014f11c872da9a246ef8406447712
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230117"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Költségriasztások használata a használat és a kiadások monitorozásához

Ez a cikk segít megérteni és használni Cost Management riasztásokat az Azure-használat és-kiadások figyeléséhez. A rendszer automatikusan generálja a költségeket, amikor Azure-erőforrásokat használ fel. A riasztások egyetlen helyen jelenítik meg az összes aktív költséghely-felügyeleti és számlázási riasztást. Ha a felhasználás eléri a megadott küszöbértéket, a rendszer a riasztásokat Cost Management hozza létre. A költségekkel kapcsolatos riasztások három típusa létezik: költségvetési riasztások, jóváírási riasztások és részlegi költségkeret-riasztások.

## <a name="budget-alerts"></a>Költségvetési riasztások

A költségvetési riasztások értesítést küldenek arról, ha a költségek felhasználás vagy költség alapján a [költségvetés riasztási feltételében](tutorial-acm-create-budgets.md)meghatározott összeget elérik vagy túllépik. Cost Management költségvetést a Azure Portal vagy az [Azure](https://docs.microsoft.com/rest/api/consumption) -használati API használatával hozza létre a rendszer.

A Azure Portal a költségvetéseket a költségek határozzák meg. Az Azure-beli használati API-val a költségvetést költséggel vagy felhasználással határozzák meg. A költségvetési riasztások a Cost-alapú és a használati alapú költségvetést is támogatják. A költségvetési riasztások automatikusan jönnek létre, amikor teljesülnek a költségvetési riasztás feltételei. A Azure Portal összes díjait megtekintheti. Riasztások létrehozásakor a riasztások a költségeket mutatják be. A rendszer értesítő e-mailt is küld a felhasználók számára a költségvetés riasztás címzettjei listájában.

## <a name="credit-alerts"></a>Jóváírási riasztások

A jóváírási riasztások arra figyelmeztetnek, ha az Azure-kreditek pénzügyi kötelezettségvállalásait felhasználják. A pénzügyi kötelezettségvállalások a nagyvállalati szerződéssel rendelkező szervezetekre vonatkoznak. A jóváírási riasztásokat az Azure-kreditek 90%-os és 100%-ában automatikusan generáljuk. A riasztások előállításakor a rendszer a költségeket és a fiók tulajdonosai számára elküldött e-maileket is megjeleníti.

## <a name="department-spending-quota-alerts"></a>A Tanszék kiadásaira vonatkozó kvóta-riasztások

A részlegek költségkeretével kapcsolatos riasztások értesítik, ha a részleg kiadásai elérik a kvóta rögzített küszöbértékét. A költségkeretek az EA portálon konfigurálhatók. A küszöbértékek teljesülése esetén e-mailt hoz létre a részleg tulajdonosai számára, és megjelenik a költségeket jelző riasztások. Például a kvóta 50%-a vagy a 75%-a.

## <a name="supported-alert-features-by-offer-categories"></a>Támogatott riasztási funkciók az ajánlati kategóriák alapján

A riasztási típusok támogatása a már használt Azure-fiók típusától (Microsoft-ajánlat) függ. A következő táblázat a különböző Microsoft-ajánlatok által támogatott riasztási funkciókat mutatja be. A Microsoft által kínált ajánlatok teljes listáját megtekintheti [Cost Management-adat megismerése](understand-cost-mgt-data.md)érdekében.

| A riasztás típusa | Nagyvállalati Szerződés | Microsoft-ügyfélszerződés | Web Direct/utólagos elszámolású |
|---|---|---|---|
| Költségvetés | ✔ | ✔ | ✔ |
| Kredit | ✔ |✘ | ✘ |
| Részleg kiadási kvótája | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>A Cost-riasztások megtekintése

A költségekkel kapcsolatos riasztások megtekintéséhez nyissa meg a kívánt hatókört a Azure Portalban, és válassza a **költségvetések** lehetőséget a menüben. Egy másik hatókörre váltáshoz használja a **hatókör** pirulát. A menüben válassza a **Cost riasztások** lehetőséget. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

![Példa a Cost Management látható riasztások képe](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Az aktív és a kihagyott riasztások teljes száma megjelenik a Cost riasztások oldalon.

Minden riasztás a riasztás típusát jeleníti meg. A költségvetési riasztások azt jelzik, hogy miért lett létrehozva, és annak a költségvetésnek a neve, amelyre vonatkozik. Mindegyik riasztás megjeleníti a Létrehozás dátuma, állapota és a riasztás által érintett hatókört (előfizetést vagy felügyeleti csoportot).

A lehetséges állapot magában foglalja az **aktív** és a **kihagyott**állapotot. Az aktív állapot azt jelzi, hogy a riasztás továbbra is releváns. Az elvetett állapot azt jelzi, hogy valaki úgy jelölte meg a riasztást, hogy már nem relevánsnak kell lennie.

A részletek megtekintéséhez válasszon ki egy riasztást a listából. A riasztás részletei a riasztással kapcsolatos további információkat jelenítenek meg. A költségvetési riasztások közé tartozik a költségvetésre mutató hivatkozás. Ha egy költségvetési riasztáshoz javaslat áll rendelkezésre, akkor a javaslatra mutató hivatkozás is megjelenik. A költségvetés, a kreditek és a részlegek kiadásaira vonatkozó kvóta-riasztások egy hivatkozással rendelkeznek a Cost Analysis elemzéséhez, ahol megtekintheti a riasztás hatókörének költségeit. Az alábbi példa egy részleg kiadásait mutatja be a riasztás részleteivel.

![Példa a részleghez tartozó kiadásokra, a riasztás részleteit ábrázoló kép](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Ha megtekinti egy elutasított riasztás részleteit, akkor újraaktiválhatja, ha manuális beavatkozásra van szükség. Az alábbi képen egy példa látható.

![Kép – példa a beállítások bezárására és újraaktiválására](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Lásd még

- Ha még nem hozott létre költségvetést, vagy riasztási feltételeket állított be a költségvetéshez, fejezze be a [költségvetés létrehozása és kezelése](tutorial-acm-create-budgets.md) oktatóanyagot.
