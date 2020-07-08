---
title: Riasztások konfigurálása – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk bemutatja, hogyan konfigurálhat és érhet el metrikai riasztásokat Azure Database for PostgreSQL – egyetlen kiszolgáló számára a Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: fe099dcb49d176d27466c08749a5873904d1ae2f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74766837"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>Riasztások beállítása Azure Database for PostgreSQL – egyetlen kiszolgáló esetén a Azure Portal használatával

Ez a cikk bemutatja, hogyan állíthat be Azure Database for PostgreSQL riasztásokat a Azure Portal használatával. Riasztást az Azure-szolgáltatások metrikáinak monitorozása alapján kaphat.

A riasztás akkor aktiválódik, ha egy megadott metrika értéke átlépi a hozzárendelt küszöbértéket. A riasztás akkor is aktiválódik, ha a feltétel először teljesül, majd ezt követően, ha a feltétel már nem teljesül. 

A következő műveletek elvégzéséhez beállíthatja a riasztást:
* E-mail-értesítések küldése a szolgáltatás-rendszergazdának és a társ-rendszergazdáknak.
* Küldjön e-mailt a megadott további e-mailekre.
* Hívja meg a webhookot.

A riasztási szabályokkal kapcsolatos információkat a használatával konfigurálhatja és kérheti le:
* [Azure Portalra](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Riasztási szabály létrehozása metrika alapján a Azure Portal
1. A [Azure Portal](https://portal.azure.com/)válassza ki a figyelni kívánt Azure Database for PostgreSQL-kiszolgálót.

2. Az oldalsáv **figyelés** szakaszában válassza a **riasztások** lehetőséget az alábbiak szerint:

   ![Riasztási szabályok kiválasztása](./media/howto-alert-on-metric/2-alert-rules.png)

3. Válassza a **metrika hozzáadása riasztás** (+ ikon) lehetőséget.

4. Megnyílik a **szabály létrehozása** lap az alább látható módon. Adja meg a kötelező adatokat:

   ![Metrikus riasztási űrlap hozzáadása](./media/howto-alert-on-metric/4-add-rule-form.png)

5. A **feltétel** szakaszban válassza a **feltétel hozzáadása**lehetőséget.

6. Válasszon ki egy mérőszámot azon jelek listájáról, amelyekről riasztást szeretne kapni. Ebben a példában válassza a "tárolási százalék" lehetőséget.
   
   ![Metrika kiválasztása](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. Konfigurálja a riasztási logikát, beleértve a **feltételt** (pl. "Nagyobb, mint"), **küszöbérték** (pl. 85 százalék), **időösszesítés**, **időtartam** a metrikai szabálynak meg kell felelnie a riasztási eseményindítók előtt (pl. "Az elmúlt 30 percben") és a **gyakoriság**.
   
   A Befejezés gombra kattintva válassza a **kész** lehetőséget.

   ![Metrika kiválasztása](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. A **műveleti csoportok** szakaszban válassza az **új létrehozása** lehetőséget egy új csoport létrehozásához, hogy értesítést kapjon a riasztásról.

9. Töltse ki a "műveleti csoport hozzáadása" űrlapot névvel, rövid névvel, előfizetéssel és erőforráscsoporthoz.

10. **E-mail/SMS/leküldéses/** hangműveletek típusának konfigurálása.
    
    Válassza az "e-mail-Azure Resource Manager szerepkör" lehetőséget az előfizetés-tulajdonosok, a közreműködők és az olvasók számára az értesítések fogadásához.
   
    Szükség esetén adjon meg egy érvényes URI-t a **webhook** mezőben, ha azt szeretné, hogy a riasztás tüzek esetén legyen meghívva.

    Ha elkészült, kattintson **az OK gombra** .

    ![Műveletcsoport](./media/howto-alert-on-metric/10-action-group-type.png)

11. Adja meg a riasztási szabály nevét, leírását és súlyosságát.

    ![Műveletcsoport](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. A riasztás létrehozásához válassza a **riasztási szabály létrehozása** lehetőséget.

    Néhány percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

## <a name="manage-your-alerts"></a>A riasztások kezelése
Miután létrehozta a riasztást, kiválaszthatja, és elvégezheti a következő műveleteket:

* Megtekintheti a metrika küszöbértékét és a riasztásra vonatkozó előző naptól számított tényleges értékeket bemutató diagramot.
* A riasztási szabály **szerkesztése** vagy **törlése** .
* **Tiltsa le** vagy **engedélyezze** a riasztást, ha átmenetileg le kívánja állítani vagy folytatni szeretné az értesítések fogadását.

## <a name="next-steps"></a>További lépések
* További információ a [webhookok riasztásokban való konfigurálásáról](../azure-monitor/platform/alerts-webhooks.md).
* [Tekintse át a metrikák gyűjteményét](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , és győződjön meg arról, hogy a szolgáltatás elérhető és rugalmas.
