---
title: Riasztások konfigurálása – Azure Portal-Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Ez a cikk bemutatja, hogyan konfigurálhat és érhet el metrikai riasztásokat Azure Database for PostgreSQL rugalmas kiszolgálókhoz a Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d3d5ced5860c14e9e4d522c42ffd0bc71341a9c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935776"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL-rugalmas kiszolgáló metrikáinak beállítása a Azure Portal használatával

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ez a cikk bemutatja, hogyan állíthat be Azure Database for PostgreSQL riasztásokat a Azure Portal használatával. Riasztást az Azure-szolgáltatások metrikáinak monitorozása alapján kaphat.

A riasztás akkor aktiválódik, ha egy megadott metrika értéke átlépi a hozzárendelt küszöbértéket. A riasztás akkor is aktiválódik, ha a feltétel először teljesül, majd ezt követően, ha a feltétel már nem teljesül.

A következő műveletek elvégzéséhez beállíthatja a riasztást:

* E-mail-értesítések küldése a szolgáltatás-rendszergazdának és a társ-rendszergazdáknak.
* Küldjön e-mailt a megadott további e-mailekre.
* Hívja meg a webhookot.

A riasztási szabályokkal kapcsolatos információkat a használatával konfigurálhatja és kérheti le:

* [Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#create-with-azure-portal)
* [Azure CLI](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Riasztási szabály létrehozása metrika alapján a Azure Portal

1. A [Azure Portal](https://portal.azure.com/)válassza ki a figyelni kívánt Azure Database for PostgreSQL-kiszolgálót.

2. Az oldalsáv **figyelés** szakaszában válassza a **riasztások** lehetőséget az alábbiak szerint:

   :::image type="content" source="./media/howto-alert-on-metrics/2-alert-rules.png" alt-text="Riasztási szabályok kiválasztása":::

3. Válassza a **metrika hozzáadása riasztás** (+ ikon) lehetőséget.

4. Megnyílik a **szabály létrehozása** lap az alább látható módon. Adja meg a kötelező adatokat:

   :::image type="content" source="./media/howto-alert-on-metrics/4-add-rule-form.png" alt-text="Riasztási szabályok kiválasztása" lehetőséget.

   :::image type="content" source="./media/howto-alert-on-metrics/6-configure-signal-logic.png" alt-text="Riasztási szabályok kiválasztása":::

7. Konfigurálja a riasztási logikát, beleértve a **feltételt** (pl. "Nagyobb, mint"), **küszöbérték** (pl. 85 százalék), **időösszesítés**, **időtartam** a metrikai szabálynak meg kell felelnie a riasztási eseményindítók előtt (pl. "Az elmúlt 30 percben") és a **gyakoriság**.

   A Befejezés gombra kattintva válassza a **kész** lehetőséget.

   :::image type="content" source="./media/howto-alert-on-metrics/7-set-threshold-time.png" alt-text="Riasztási szabályok kiválasztása" lehetőséget az előfizetés-tulajdonosok, a közreműködők és az olvasók számára az értesítések fogadásához.

    2. Szükség esetén adjon meg egy érvényes URI-t a **webhook** mezőben, ha azt szeretné, hogy a riasztás tüzek esetén legyen meghívva.

    3. Ha elkészült, kattintson **az OK gombra** .

    :::image type="content" source="./media/howto-alert-on-metrics/10-action-group-type.png" alt-text="Riasztási szabályok kiválasztása":::

11. Adja meg a riasztási szabály nevét, leírását és súlyosságát.

    :::image type="content" source="./media/howto-alert-on-metrics/11-name-description-severity.png" alt-text="Riasztási szabályok kiválasztása"::: 

12. A riasztás létrehozásához válassza a **riasztási szabály létrehozása** lehetőséget.

    Néhány percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

## <a name="manage-your-alerts"></a>A riasztások kezelése

Miután létrehozta a riasztást, kiválaszthatja, és elvégezheti a következő műveleteket:

* Megtekintheti a metrika küszöbértékét és a riasztásra vonatkozó előző naptól számított tényleges értékeket bemutató diagramot.
* A riasztási szabály **szerkesztése** vagy **törlése** .
* **Tiltsa le** vagy **engedélyezze** a riasztást, ha átmenetileg le kívánja állítani vagy folytatni szeretné az értesítések fogadását.

## <a name="next-steps"></a>Következő lépések

* További információ a [webhookok riasztásokban való konfigurálásáról](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-webhooks).
* [Tekintse át a metrikák gyűjteményét](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-how-to-customize-monitoring) , és győződjön meg arról, hogy a szolgáltatás elérhető és rugalmas.
