---
title: Riasztások konfigurálása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ez a cikk bemutatja, hogyan konfigurálhat és érhet el metrikus riasztásokat Azure Database for PostgreSQL-nagy kapacitású (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a830f344023967f07ab809d67833f99e4e2958
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977607"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>A Azure Database for PostgreSQL-nagy kapacitású (Citus) metrikáinak beállítása a Azure Portal használatával

Ez a cikk bemutatja, hogyan állíthat be Azure Database for PostgreSQL riasztásokat a Azure Portal használatával. Riasztást az Azure-szolgáltatások metrikáinak monitorozása alapján kaphat.

A rendszer riasztást állít be, amely akkor aktiválódik, ha egy adott metrika értéke átlép egy küszöbértéket. A riasztás akkor aktiválódik, ha a feltétel először teljesül, és ezt követően folytatódik.

A következő műveletek elvégzéséhez beállíthatja a riasztást:
* E-mail-értesítések küldése a szolgáltatás-rendszergazdának és a rendszergazdáknak.
* Küldjön e-mailt a megadott további e-mailekre.
* Hívja meg a webhookot.

A riasztási szabályokkal kapcsolatos információkat a használatával konfigurálhatja és kérheti le:
* [Azure Portalra](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Riasztási szabály létrehozása metrika alapján a Azure Portal
1. A [Azure Portal](https://portal.azure.com/)válassza ki a figyelni kívánt Azure Database for PostgreSQL-kiszolgálót.

2. Az oldalsáv **figyelés** szakaszában válassza a **riasztások** lehetőséget az alábbiak szerint:

   ![Riasztási szabályok kiválasztása](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Válassza az **új riasztási szabály** (+ ikon) lehetőséget.

4. Megnyílik a **szabály létrehozása** lap az alább látható módon. Adja meg a szükséges információkat:

   ![Metrikus riasztási űrlap hozzáadása](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. A **feltétel** szakaszban válassza a **Hozzáadás**lehetőséget.

6. Válasszon ki egy mérőszámot azon jelek listájáról, amelyekről riasztást szeretne kapni. Ebben a példában válassza a "tárolási százalék" lehetőséget.
   
   ![Metrika kiválasztása](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. A riasztás logikájának konfigurálása:

    * **Operátor** (pl. "Nagyobb, mint")
    * **Küszöbérték** (pl. 85 százalék)
    * **Összesítési részletességi** időtartam, ameddig a metrikai szabálynak teljesülnie kell a riasztási eseményindítók előtt (pl. "Az elmúlt 30 percben")
    * és **értékelés gyakorisága** (pl. "1 perc")
   
   A Befejezés gombra kattintva válassza a **kész** lehetőséget.

   ![Metrika kiválasztása](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. A **műveleti csoportok** szakaszban válassza az **új létrehozása** lehetőséget egy új csoport létrehozásához, hogy értesítést kapjon a riasztásról.

9. Töltse ki a "műveleti csoport hozzáadása" űrlapot névvel, rövid névvel, előfizetéssel és erőforráscsoporthoz.

    ![Műveleti csoport](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. **E-mail/SMS/leküldéses/** hangműveletek típusának konfigurálása.
    
    Válassza az "e-mail-Azure Resource Manager szerepkör" lehetőséget, ha értesítéseket szeretne küldeni az előfizetés-tulajdonosoknak, közreműködőknek és olvasóknak.
   
    Ha elkészült, kattintson **az OK gombra** .

    ![Műveleti csoport](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Adja meg a riasztási szabály nevét, leírását és súlyosságát.

    ![Műveleti csoport](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. A riasztás létrehozásához válassza a **riasztási szabály létrehozása** lehetőséget.

    Néhány percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

## <a name="manage-your-alerts"></a>A riasztások kezelése

Miután létrehozott egy riasztást, kiválaszthatja, és elvégezheti a következő műveleteket:

* Megtekintheti a metrika küszöbértékét és a riasztásra vonatkozó előző naptól számított tényleges értékeket bemutató diagramot.
* A riasztási szabály **szerkesztése** vagy **törlése** .
* **Tiltsa le** vagy **engedélyezze** a riasztást, ha átmenetileg le kívánja állítani vagy folytatni szeretné az értesítések fogadását.

## <a name="next-steps"></a>Következő lépések
* További információ a [webhookok riasztásokban való konfigurálásáról](../azure-monitor/platform/alerts-webhooks.md).
* [Tekintse át a metrikák gyűjteményét](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , és győződjön meg arról, hogy a szolgáltatás elérhető és rugalmas.
