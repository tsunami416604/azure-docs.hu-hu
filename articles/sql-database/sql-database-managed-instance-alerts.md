---
title: Riasztások és értesítések beállítása a felügyelt példányhoz (Azure Portal)
description: A Azure Portal használatával SQL felügyelt példányokra vonatkozó riasztásokat hozhat létre, amelyek az Ön által megadott feltételek teljesülése esetén aktiválják az értesítéseket vagy az automatizálást.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/02/2020
ms.openlocfilehash: a332627d149a36ba5d5beb2626023e58a221f0d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80639197"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Riasztások létrehozása az Azure SQL felügyelt példányaihoz a Azure Portal használatával

## <a name="overview"></a>Áttekintés

Ebből a cikkből megtudhatja, hogyan állíthatja be a riasztásokat az Azure SQL felügyelt példány-adatbázisában lévő adatbázisokhoz a Azure Portal használatával. A riasztások e-mailt küldhetnek, vagy meghívhatnak egy webhookot, ha valamilyen metrika (például a példány tárolási mérete vagy a CPU-használat) eléri a küszöbértéket. Ez a cikk a riasztási időszakok beállításának ajánlott eljárásait is tartalmazza.

Riasztást kaphat az Azure-szolgáltatásokra vonatkozó metrikák vagy események figyelése alapján.

* **Metrikai értékek** – a riasztás akkor aktiválódik, ha egy adott metrika értéke az egyik irányban hozzárendelt küszöbértéket keresztezi. Ez azt is elindítja, hogy a feltétel első teljesülése esetén, majd később, amikor az adott feltétel már nem teljesül.
* **Műveletnapló eseményei** – a riasztás *minden* eseménynél aktiválható, vagy csak bizonyos számú esemény bekövetkeztekor.

A riasztást úgy is beállíthatja, hogy a következő esetekben hajtsa végre az eseményindítókat:

* E-mail-értesítések küldése a szolgáltatás-rendszergazdának és a társ-rendszergazdáknak
* Küldjön e-mailt a megadott további e-mailekre.
* Webhook meghívása

A riasztási szabályokkal kapcsolatos információkat a

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [parancssori felület (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Riasztási szabály létrehozása metrika esetén a Azure Portal

1. A [portálon](https://portal.azure.com/)keresse meg a figyelni kívánt erőforrást, és jelölje ki.
2. A figyelés szakaszban válassza a **riasztások** lehetőséget. A szöveg és az ikon némileg eltérő lehet a különböző erőforrások esetében.  

   ![Figyelés](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Kattintson az **új riasztási szabály** gombra a **szabály létrehozása** lap megnyitásához.
   ![Szabály létrehozása](media/sql-database-insights-alerts-portal/create-rule.png)

4. A **feltétel** szakaszban kattintson a **Hozzáadás**gombra.
   ![Feltétel meghatározása](media/sql-database-insights-alerts-portal/create-rule.png)
5. A **jel logikai beállítása** lapon válasszon ki egy jelet.
   ![Jel kiválasztása](media/sql-database-insights-alerts-portal/select-signal.png)
6. Ha kiválasztott egy jelet, például a **CPU-százalékot**, megjelenik a **jel logikai beállítása** lap.
   ![Jellogika konfigurálása](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Ezen a lapon konfigurálhatja a küszöbértéket, az operátort, az összesítési típust, a küszöbértéket, az Összesítés részletességét és a kiértékelés gyakoriságát. Ezután kattintson a **kész**gombra.
8. A **szabály létrehozása**lapon válasszon ki egy meglévő **műveleti csoportot** , vagy hozzon létre egy új csoportot. A műveleti csoport lehetővé teszi, hogy meghatározza a riasztási feltétel bekövetkezésekor végrehajtandó műveletet.
  ![Műveleti csoport definiálása](media/sql-database-insights-alerts-portal/action-group.png)

9. Adja meg a szabály nevét, adjon meg egy opcionális leírást, válassza ki a szabály súlyossági szintjét, adja meg, hogy engedélyezi-e a szabályt a szabályok létrehozásakor, majd kattintson a **szabály riasztás létrehozása** elemre a metrikai szabály riasztásának létrehozásához.

10 percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

## <a name="next-steps"></a>További lépések

* További információ a [webhookok riasztásokban való konfigurálásáról](../azure-monitor/platform/alerts-webhooks.md).
