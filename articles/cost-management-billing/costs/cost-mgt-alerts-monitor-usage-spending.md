---
title: A használat és a költések monitorozása költségriasztásokkal | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan segítenek a költségriasztások az Azure Cost Management-használat és -költségek monitorozásában.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: ''
ms.openlocfilehash: 3ce643767ad8ba6116c9e93f23b1c8a7819d7277
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199942"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Költségriasztások használata a használat és a kiadások monitorozásához

Ebből a cikkből megtudhatja, hogyan használhatja a Cost Management-riasztásokat a használat és a költségek monitorozásához az Azure-ban. A költségriasztások létrehozása automatikusan történik az Azure-erőforrások használatakor. A riasztások egy helyen jelenítik meg az aktív költségkezelési és számlázási riasztásokat. A Cost Management riasztásokat hoz létre, amikor a használat elér egy adott küszöbértéket. Három típusú költségriasztás létezik: költségvetési riasztás, kreditriasztás és a részlegek költségkvótáinak riasztásai.

## <a name="budget-alerts"></a>Költségvetési riasztások

A költségvetési riasztások értesítést küldenek arról, ha a költségek használat vagy költség alapján elérik vagy túllépik a [költségvetés riasztási feltételében](tutorial-acm-create-budgets.md) meghatározott összeget. A Cost Management-költségvetéseket az Azure Portal vagy az [Azure Consumption](https://docs.microsoft.com/rest/api/consumption) API segítségével tudja létrehozni.

Az Azure Portalon a költségvetéseket a költségek határozzák meg. Az Azure Consumption API használatakor a költségvetések a költségek vagy a felhasználás mértéke alapján lesznek meghatározva. A költségvetési riasztások a költségalapú és a használatalapú költségvetést is támogatják. A költségvetési riasztások automatikusan jönnek létre, amikor teljesülnek a költségvetési riasztás feltételei. Az Azure Portalon megtekintheti az összes költségriasztást. Amint létrejön egy riasztás, megjelenik a költségriasztásokban. A rendszer riasztási e-mailt is küld a költségvetés riasztási címzettjeinek listájában szereplők számára.

## <a name="credit-alerts"></a>Kreditriasztások

A kreditriasztások értesítik, ha felhasználta az Azure-kredit pénzügyi keretét. A pénzügyi keretek Nagyvállalati Szerződéssel rendelkező szervezetek számára elérhetők. A jóváírási riasztások automatikusan létrejönnek az Azure-kreditek egyenlegének 90%-os és 100%-os felhasználásakor. Amint létrejön egy riasztás, megjelenik a költségriasztásokban és az e-mailekben, amelyeket a fiók tulajdonosai kapnak.

## <a name="department-spending-quota-alerts"></a>Részleg költségkvótáinak riasztásai

A részlegek költségkvótájának riasztásai értesítik, ha a részleg költségei elérik a kvóta meghatározott küszöbértékét. A költségkvótát az EA Portalon lehet konfigurálni. A küszöbértékek elérésekor a részleg tulajdonosai e-mailt kapnak, és ez megjelenik a költségriasztásokban is. Például a kvóta 50 vagy 75%-ának elérésekor.

## <a name="supported-alert-features-by-offer-categories"></a>Támogatott riasztási funkciók az ajánlati kategóriák alapján

A riasztástípusok támogatottsága az Azure-fiók típusától függ (Microsoft-ajánlat). Az alábbi táblázat összefoglalja, hogy mely Microsoft-ajánlatok mely riasztási funkciókat támogatják. A Microsoft-ajánlatok teljes listáját lásd: [A Cost Management adatainak értelmezése](understand-cost-mgt-data.md).

| Riasztástípus | Nagyvállalati Szerződés | Microsoft-ügyfélszerződés | Web Direct/Használatalapú fizetés |
|---|---|---|---|
| Költségvetés | ✔ | ✔ | ✔ |
| Kredit | ✔ |✘ | ✘ |
| Részleg költségkvótája | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Költségriasztások megtekintése

A költségriasztások megtekintéséhez nyissa meg a kívánt hatókört az Azure Portalon, majd válassza ki a **Költségvetések** lehetőséget a menüben. A **Hatókör** elemmel másik hatókörre válthat. Válassza ki a **Költségriasztások** menüpontot. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

![Szemléltető kép, amelyen a Cost Managementben megjelenített riasztások láthatók](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Az aktív és bezárt riasztások teljes száma a költségriasztások oldalán jelenik meg.

Minden riasztás megjeleníti a riasztástípust. A költségvetési riasztások megjelenítik, hogy miért lettek létrehozva, illetve hogy melyik költségvetésre vonatkoznak. Minden egyes riasztás megjeleníti a létrehozás dátumát, az állapotát és a riasztás által érintett hatókört (előfizetés vagy felügyeleti csoport).

Lehetséges állapotok például az **aktív** és **bezárt**. Az aktív állapot jelzi, hogy a riasztás még mindig releváns. A bezárt állapot azt jelzi, hogy valaki már nem relevánsként jelölte meg a riasztást.

Válasszon ki egy riasztást a listából a részletek megtekintéséhez. A riasztási részletek további információt jelenítenek meg a riasztásról. A költségvetési riasztások egy hivatkozást is tartalmaznak az adott költségvetéshez. Ha egy költségvetési riasztáshoz javaslat áll rendelkezésre, akkor a javaslatra mutató hivatkozás is megjelenik. A költségvetési, kredit- és a részlegek költségkvótáinak riasztásai egy hivatkozással rendelkeznek a költségelemzéshez, ahol megtekintheti a riasztás hatókörének költségeit. Az alábbi példa egy részleg kiadásait mutatja be a riasztási részletekkel.

![Szemléltető kép, amelyen egy részleg költségei láthatók a riasztási részletekkel együtt](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Egy bezárt riasztással kapcsolatos részletek megtekintésekor Újraaktiválhatja azt, ha további manuális műveletekre van szükség. Az alábbi képen egy példa látható.

![Szemléltető kép, amelyen az elvetési és az újraaktiválási lehetőség látható](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Lásd még

- Ha még nem hozott létre költségvetést, vagy nem állított be riasztási feltételeket az adott költségvetéshez, végezze el a [Költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md) oktatóanyagot.
