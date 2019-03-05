---
title: Használat és költségek költség riasztások figyelése |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan költség riasztások a Súgó használat és költségek az Azure Cost Management figyelheti.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/28/2019
ms.topic: conceptual
ms.service: cost-management
manager: alavital
ms.custom: ''
ms.openlocfilehash: d6f7a597ff5737bb5e9a3f07122ece313bd432ff
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337776"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Költség riasztások, a használat és költségek monitorozása

Ez a cikk segít megismerni, és figyelheti az Azure Cost Management riasztások használata használat és költségek. A Cost riasztások automatikusan jönnek létre, amikor az Azure-erőforrások használnak fel. Költségkezelés és elszámolási értesítések együtt, egyetlen helyen riasztások jelennek meg minden aktív. A használatalapú elér egy megadott küszöbértéket, amikor riasztások jönnek létre Cost Management. Költség riasztások három típusa van: költségvetés riasztások, a kredit riasztásokat és a részleg kvóta riasztások költségeit.

## <a name="budget-alerts"></a>Költségvetés riasztások

Költségvetés riasztások figyelmeztetik, ha a kiadások, a használat és költségek alapján eléri vagy meghaladja a meghatározott a [riasztási feltétel a költségvetés](tutorial-acm-create-budgets.md). A Cost Management költségvetése jönnek létre az Azure portal használatával, vagy a [Azure Consumption](https://docs.microsoft.com/rest/api/consumption) API-t.

Az Azure Portalon költségvetések költség határozzák meg. Az Azure Consumption API-val, költségvetések vannak meghatározva költség vagy szolgáltatáshasználatot. Költségvetés riasztások költségvetése költség-alapú és a használat alapú is támogatja. Költségvetés riasztások automatikusan jönnek létre, amikor a költségvetés riasztási feltételek teljesülnek. Megtekintheti az összes költség riasztások az Azure Portalon. A rendszer riasztást állít elő, amikor a költségek riasztások jelenik meg. Riasztás e-mailt is zajlik a személyeknek a költségvetés riasztási címzettek listájában.

## <a name="credit-alerts"></a>Kredit riasztások

Kredit riasztások figyelmeztetik, ha az Azure-kredit program pénzügyi kötelezettségvállalásainak keretében használnak fel. Program pénzügyi kötelezettségvállalásainak keretében olyan nagyvállalati szerződéssel rendelkező szervezetek számára. Kredit riasztások akkor jönnek létre, 90 %-os és 100 %-os az Azure-kredit egyenlegéről. A rendszer riasztást állít elő, amikor a költségek riasztások és az e-mailben küldött a fióktulajdonosok megjelenik.

## <a name="department-spending-quota-alerts"></a>Részleg költségkeret kvóta riasztások

Részleg költségkeret kvóta riasztások figyelmeztetik, ha a department költségkeret-beállítási eléri a rögzített méretű kvóta. A nagyvállalati szerződések portáljának költségkeret kvóták lehet konfigurálni. Minden alkalommal, amikor egy küszöbértéket állít elő, egy e-mailek részleg tulajdonosai, és költség riasztás jelenik meg. Például 50 %-os vagy 75 %-a kvótát.

## <a name="supported-alert-features-by-offer-categories"></a>Riasztási funkciók támogatott ajánlat kategóriák szerint

Riasztástípusok támogatása (Microsoft ajánlat), hogy rendelkezik Azure-fiók típusú függ. Az alábbi táblázat a riasztási funkciók által támogatott különböző Microsoft-ajánlatok. A Microsoft-ajánlatok teljes listáját megtekintheti [megismerheti a Cost Management adataihoz](understand-cost-mgt-data.md).

| Riasztás típusa | Nagyvállalati szerződés | Microsoft Ügyfélszerződés | Web direct/Pay-As-You-Go |
|---|---|---|---|
| Költségkeret | ✔ | ✔ | ✔ |
| Kreditösszeg | ✔ |✘ | ✘ |
| Részleg költségkvótája | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Riasztások megtekintése

Az Azure Portalon kattintson a **Költségkezelés + Számlázás** elemre a szolgáltatások listáján. Ezt a listában **Cost Management**válassza **riasztások költség**.

![Példa a Cost Management látható riasztások képe](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Aktív és elvetettként értesítések teljes száma a költségek riasztások lapon jelenik meg.

Az összes riasztás megjelenítése a riasztási típus. Egy költségvetési figyelmeztetés látható, az oka, hogy miért lett létrehozva, és a költségvetés vonatkozik nevét. Minden riasztás jeleníti meg a dátumot létrejött, az állapota, és a hatókör (előfizetés vagy a felügyeleti csoport), amely a riasztás vonatkozik.

Lehetséges állapota magában foglalja **aktív** és **elvetett**. Aktív állapot azt jelzi, hogy a riasztás még mindig megfelelő. Elbocsátott állapot azt jelzi, hogy valaki megjelölte a riasztást való beállításához már nem releváns.

A listában a részletek megtekintéséhez válasszon ki egy riasztást. A riasztás részleteinek megjelenítése a riasztással kapcsolatos további információkat. Költségvetés riasztásokat a költségvetés mutató hivatkozást. A költségvetés riasztáshoz javaslat érhető el, ha a javaslat mutató hivatkozás is látható. Költségvetés, jóváírás és részleg költségkeret kvóta riasztások van elemzés az költségelemzés mutató hivatkozást ahol megismerheti a riasztás hatókör költségeket. Az alábbi példa bemutatja, hogy a riasztás részleteinek Intézet költségeit.

![A példában a riasztás részleteinek Intézet kiadások képe](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Ha egy elbocsátott riasztás részleteinek megtekintéséhez újraaktiválhatja, ha manuális beavatkozásra van szükség. Az alábbi képen egy példa látható.

![Példa kép megjelenítés bezárása, és aktiválja újra a beállítások](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Lásd még

- Ha még nem már létrehozott egy költségvetés vagy költségvetési a riasztási feltételek megadására, végezze el a [létrehozása és kezelése a költségvetés](tutorial-acm-create-budgets.md) oktatóanyag.
