---
title: Metrikák riasztások konfigurálása az Azure Database for MariaDB az Azure Portalon
description: Ez a cikk ismerteti konfigurálása és a hozzáférési metrikákhoz kapcsolódó riasztások az Azure Database for MariaDB az Azure Portalról.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/07/2019
ms.openlocfilehash: ce9804a4ecad4cd0f2e797038dc10c71b50de9bf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122715"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mariadb"></a>Riasztásokat állíthat be mérőszámok az Azure Database for MariaDB az Azure portal használatával

Ez a cikk bemutatja, hogyan állítható be az Azure Database for MariaDB riasztások az Azure portal használatával. A metrikák az Azure-szolgáltatások monitorozása alapján riasztást kaphat.

A riasztás akkor aktiválódik, ha egy adott mérőszám értéke átlép egy küszöbértéket, rendeli. A riasztási eseményindítók is, ha az első feltétele teljesül, és majd ezt követően, hogy a feltétel ha van már nem teljesül.

A következő műveleteket hajthatja végre, amikor elindítja riasztásokat lehet beállítani:
* e-mail értesítések küldéséhez a szolgáltatás-rendszergazda és társrendszergazdák
* További e-mailek, Ön által megadott e-mail küldése.
* Webhook meghívása

Konfigurálhatja és riasztási szabályok használatával kapcsolatos információk lekérése:
* [Azure Portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Az Azure Monitor REST API-val](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric"></a>Egy riasztási szabályt létrehozni egy metrika
1. Az a [az Azure portal](https://portal.azure.com/), válassza ki az Azure Database for MariaDB kiszolgáló figyelni szeretné.

2. Alatt a **figyelés** oldalsávon válassza szakaszában **riasztások** látható módon:

   ![Válassza ki a riasztási szabályok](./media/howto-alert-metric/2-alert-rules.png)

3. Válassza ki **metrikariasztás hozzáadása** (+ ikon).

4. A **létrehozás szabály** lap, melyen alább látható módon. Töltse ki a szükséges adatokat:

   ![Metrikaalapú riasztás űrlap hozzáadása](./media/howto-alert-metric/4-add-rule-form.png)

5. Belül a **feltétel** szakaszban jelölje be **feltétel hozzáadása**.

6. Válassza ki egy metrikát a listából, riasztást kap a jelek. Ebben a példában válassza a "Storage százaléka".
   
   ![Metrika kiválasztása](./media/howto-alert-metric/6-configure-signal-logic.png)

7. Konfigurálja a riasztási logika többek között a **feltétel** (például. "Nagyobb"), **küszöbérték** (például. 85 %-os), **idő összesítése**, **időszak** idő a metrikaszabály kell biztosítani a riasztási eseményindítók (például előtt "Keresztül az elmúlt 30 percben") és **gyakorisága**.
   
   Válassza ki **kész** teljes.

   ![Metrika kiválasztása](./media/howto-alert-metric/7-set-threshold-time.png)

8. Belül a **Műveletcsoportok** szakaszban jelölje be **hozzon létre új** , hozzon létre egy új csoportot a riasztás az értesítések fogadásához.

9. Adja meg a nevét, rövid név, előfizetés és erőforráscsoport "Műveleti csoport hozzáadása" űrlapján.

10. Konfigurálja egy **e-mailek és SMS és leküldéses/Hangvétel** művelet típusa.
    
    Válassza az "E-mailt az Azure Resource Manager szerepkör" jelölje be az előfizetés-tulajdonosokat, közreműködőknek és olvasóknak értesítések fogadásához.
   
    Szükség esetén adja meg az érvényes URI-t a **Webhook** Ha azt szeretné, hogy ez a riasztás aktiválódásakor nevű mezőt.

    Válassza ki **OK** befejezésekor.

    ![Műveletcsoport](./media/howto-alert-metric/10-action-group-type.png)

11. Adjon meg egy riasztási szabály neve, leírása és súlyosság.

    ![Műveletcsoport](./media/howto-alert-metric/11-name-description-severity.png) 

12. Válassza ki **riasztási szabály létrehozása** a riasztás létrehozásához.

    Néhány percen belül a riasztás aktív, és elindítja a fent leírtaknak megfelelően.

## <a name="manage-your-alerts"></a>A riasztások kezelése
Miután létrehozta a riasztást, válassza ki, és hajtsa végre a következőket:

* Tekintse meg a metrika küszöbértéke és a tényleges értékek az előző nap a riasztáshoz tartozó megjelenítő grafikon.
* **Szerkesztés** vagy **törlése** a riasztási szabályt.
* **Tiltsa le** vagy **engedélyezése** a riasztás, ha azt szeretné, ideiglenesen leállíthatja, vagy folytathatja a értesítéseket kapni.


## <a name="next-steps"></a>További lépések
* Tudjon meg többet [webhookok konfigurálásáról a riasztások](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Get- [metrikák gyűjtési áttekintése](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , győződjön meg arról, hogy szolgáltatása elérhető és válaszkész.
