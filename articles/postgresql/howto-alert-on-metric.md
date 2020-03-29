---
title: Riasztások konfigurálása - Azure portal - Azure Database for PostgreSQL - Single Server
description: Ez a cikk ismerteti, hogyan konfigurálhatja és érheti el a metrikariasztásokat az Azure Database for PostgreSQL - Single Server az Azure Portalról.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: fe099dcb49d176d27466c08749a5873904d1ae2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74766837"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>Az Azure Portal használatával riasztásokat állíthat be a PostgreSQL Azure Database – Single Server metrikákra vonatkozóan

Ez a cikk bemutatja, hogyan állíthatja be az Azure Database for PostgreSQL riasztások az Azure Portalhasználatával. Az Azure-szolgáltatások figyelési metrikák alapján kaphat riasztást.

A riasztás akkor aktiválódik, ha egy megadott metrika értéke átlépi a hozzárendelt küszöbértéket. A riasztás akkor is aktiválódik, amikor a feltétel először teljesül, majd utána, amikor ez a feltétel már nem teljesül. 

A riasztást úgy konfigurálhatja, hogy az aktiválások során a következő műveleteket végezze el:
* E-mail értesítések küldése a szolgáltatás rendszergazdájának és társrendszergazdának.
* E-mail küldése a megadott további e-mailekbe.
* Hívjon egy webhookot.

A riasztási szabályok konfigurálása és az okkal kapcsolatos információk az:
* [Azure-portál](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Riasztási szabály létrehozása az Azure Portalon lévő metrikán
1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a figyelni kívánt Azure Database for PostgreSQL-kiszolgálót.

2. Az oldalsáv **Figyelés** szakaszában válassza a **Riasztások** lehetőséget az alábbi módon:

   ![Figyelmeztetési szabályok kiválasztása](./media/howto-alert-on-metric/2-alert-rules.png)

3. Válassza **a Metrikariasztás hozzáadása** (+ ikon) lehetőséget.

4. A **Szabály létrehozása** lap az alábbiak szerint nyílik meg. Töltse ki a szükséges adatokat:

   ![Metrikariasztási űrlap hozzáadása](./media/howto-alert-on-metric/4-add-rule-form.png)

5. A **Feltétel csoportban** válassza a **Feltétel hozzáadása lehetőséget.**

6. Válasszon ki egy metrikát a figyelmeztetendő jelek listájából. Ebben a példában válassza a "Tárolási százalék" lehetőséget.
   
   ![Metrika kiválasztása](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. Konfigurálja a riasztási logikát, beleértve a **Feltétel** (pl. "Nagyobb, mint"), **Threshold** (pl. 85 százalék), **Időösszesítés**, **A** metrikaszabálynak meg kell felelnie a riasztási eseményindítók előtt (pl. "Az elmúlt 30 percben"), és **a Gyakoriság**.
   
   Válassza a **Kész lehetőséget,** ha befejeződött.

   ![Metrika kiválasztása](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. A **Műveletcsoportok** csoportban válassza az **Új létrehozása lehetőséget,** ha új csoportot szeretne létrehozni a riasztással kapcsolatos értesítések fogadásához.

9. Töltse ki a "Műveletcsoport hozzáadása" űrlapot névvel, rövid névvel, előfizetéssel és erőforráscsoporttal.

10. **E-mail/SMS/Push/Voice** művelettípus konfigurálása.
    
    Válassza az "E-mail Azure Resource Manager szerepkör" lehetőséget az előfizetés tulajdonosainak, közreműködőknek és olvasóknak az értesítések fogadásához.
   
    Szükség esetén adjon meg egy érvényes URI-t a **Webhook** mezőben, ha azt szeretné, hogy a riasztás esetén hívják meg.

    Ha befejeződött, válassza **az OK** gombot.

    ![Műveletcsoport](./media/howto-alert-on-metric/10-action-group-type.png)

11. Adja meg a riasztási szabály nevét, leírását és súlyosságát.

    ![Műveletcsoport](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. A riasztás létrehozásához válassza a **Figyelmeztetési szabály létrehozása** lehetőséget.

    Néhány percen belül a riasztás aktív, és a korábban leírt eseményindítók.

## <a name="manage-your-alerts"></a>A riasztások kezelése
Miután létrehozott egy riasztást, kiválaszthatja azt, és a következő műveleteket teheti:

* Tekintse meg a mérőszám küszöbértéket és az előző napi aktuális értékeket bemutató grafikont, amely a riasztásra vonatkozik.
* A figyelmeztetési szabály **szerkesztése** vagy **törlése.**
* **Tiltsa le** vagy **engedélyezze** a riasztást, ha ideiglenesen le szeretné állítani vagy folytatni szeretné az értesítések fogadását.

## <a name="next-steps"></a>További lépések
* További információ [a webhookok riasztásokban való konfigurálásáról.](../azure-monitor/platform/alerts-webhooks.md)
* Áttekintést [kaphat a metrikák gyűjteményéről,](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) hogy a szolgáltatás elérhető és válaszkész legyen.
