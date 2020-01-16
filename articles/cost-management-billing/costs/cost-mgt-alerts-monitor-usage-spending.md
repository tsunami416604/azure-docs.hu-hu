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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988499"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Költségriasztások használata a használat és a kiadások monitorozásához

Ez a cikk segít megérteni és használni Cost Management riasztásokat az Azure-használat és-kiadások figyeléséhez. A rendszer automatikusan generálja a költségeket, amikor Azure-erőforrásokat használ fel. A riasztások egyetlen helyen jelenítik meg az összes aktív költséghely-felügyeleti és számlázási riasztást. Ha a felhasználás eléri a megadott küszöbértéket, a rendszer a riasztásokat Cost Management hozza létre. Három típusú költségriasztás létezik: költségvetési riasztás, kreditriasztás és a részlegek költségkvótáinak riasztásai.

## <a name="budget-alerts"></a>Költségvetési riasztások

A költségvetési riasztások értesítést küldenek arról, ha a költségek felhasználás vagy költség alapján a [költségvetés riasztási feltételében](tutorial-acm-create-budgets.md)meghatározott összeget elérik vagy túllépik. Cost Management költségvetést a Azure Portal vagy az [Azure](https://docs.microsoft.com/rest/api/consumption) -használati API használatával hozza létre a rendszer.

Az Azure Portalon a költségvetéseket a költségek határozzák meg. Az Azure Consumption API használatakor a költségvetések a költségek vagy a felhasználás mértéke alapján lesznek meghatározva. A költségvetési riasztások a költségalapú és a használatalapú költségvetést is támogatják. A költségvetési riasztások automatikusan jönnek létre, amikor teljesülnek a költségvetési riasztás feltételei. Az Azure Portalon megtekintheti az összes költségriasztást. Amint létrejön egy riasztás, megjelenik a költségriasztásokban. A rendszer riasztási e-mailt is küld a költségvetés riasztási címzettjeinek listájában szereplők számára.

## <a name="credit-alerts"></a>Kreditriasztások

A kreditriasztások értesítik, ha felhasználta az Azure-kredit pénzügyi keretét. A pénzügyi keretek Nagyvállalati Szerződéssel rendelkező szervezetek számára elérhetők. A jóváírási riasztások automatikusan létrejönnek az Azure-kreditek egyenlegének 90%-os és 100%-os felhasználásakor. Amint létrejön egy riasztás, megjelenik a költségriasztásokban és az e-mailekben, amelyeket a fiók tulajdonosai kapnak.

## <a name="department-spending-quota-alerts"></a>Részleg költségkvótáinak riasztásai

A részlegek költségkvótájának riasztásai értesítik, ha a részleg költségei elérik a kvóta meghatározott küszöbértékét. A költségkvótát az EA portálon lehet konfigurálni. A küszöbértékek elérésekor a részleg tulajdonosai e-mailt kapnak, és ez megjelenik a költségriasztásokban is. Például a kvóta 50 vagy 75%-ának elérésekor.

## <a name="supported-alert-features-by-offer-categories"></a>Támogatott riasztási funkciók az ajánlati kategóriák alapján

A riasztástípusok támogatottsága az Azure-fiók típusától függ (Microsoft-ajánlat). Az alábbi táblázat összefoglalja, hogy mely Microsoft-ajánlatok mely riasztástípusokat támogatják. A Microsoft által kínált ajánlatok teljes listáját megtekintheti [Cost Management-adat megismerése](understand-cost-mgt-data.md)érdekében.

| A riasztás típusa | Nagyvállalati szerződés | Microsoft Ügyfélszerződés | Web Direct/utólagos elszámolású |
|---|---|---|---|
| Költségkeret | ✔ | ✔ | ✔ |
| Kredit | ✔ |✘ | ✘ |
| Részleg költségkvótái | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Költségriasztások megtekintése

A költségriasztások megtekintéséhez nyissa meg a kívánt hatókört az Azure Portalon, majd válassza ki a **Költségvetések** lehetőséget a menüben. A **Hatókör** elemmel másik hatókörre válthat. Válassza ki a **Költségriasztások** menüpontot. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

![A példa a Cost Managementben megjelenített riasztásokat mutatja](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Az aktív és bezárt riasztások teljes száma a költségriasztások oldalon jelenik meg.

Minden riasztás megjeleníti a riasztástípust. A költségvetési riasztások megjelenítik, hogy miért lettek létrehozva, illetve hogy melyik költségvetésre vonatkoznak. Mindegyik riasztás megjeleníti a Létrehozás dátuma, állapota és a riasztás által érintett hatókört (előfizetést vagy felügyeleti csoportot).

Lehetséges állapotok például az **aktív** és **bezárt**. Az aktív állapot jelzi, hogy a riasztás még mindig releváns. Az bezárt állapot azt jelzi, hogy valaki már nem relevánsként jelölte meg a riasztást.

Válasszon ki egy riasztást a listából a részletek megtekintéséhez. A riasztási részletek további információt jelenítenek meg a riasztásról. A költségvetési riasztások egy hivatkozást is tartalmaznak az adott költségvetéshez. Ha egy költségvetési riasztáshoz javaslat áll rendelkezésre, akkor a javaslatra mutató hivatkozás is megjelenik. A költségvetési, kredit- és a részlegek költségkvótáinak riasztásai egy hivatkozással rendelkeznek a költségelemzéshez, ahol megtekintheti a riasztás hatókörének költségeit. Az alábbi példa egy részleg kiadásait mutatja be a riasztás részleteivel.

![Példa a részleghez tartozó kiadásokra, a riasztás részleteit ábrázoló kép](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Ha megtekinti egy elutasított riasztás részleteit, akkor újraaktiválhatja, ha manuális beavatkozásra van szükség. Az alábbi képen egy példa látható.

![Kép – példa a beállítások bezárására és újraaktiválására](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Lásd még:

- Ha még nem hozott létre költségvetést, vagy riasztási feltételeket állított be a költségvetéshez, fejezze be a [költségvetés létrehozása és kezelése](tutorial-acm-create-budgets.md) oktatóanyagot.
