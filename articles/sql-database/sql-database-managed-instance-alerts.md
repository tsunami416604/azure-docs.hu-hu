---
title: Riasztások és értesítések beállítása felügyelt példányhoz (Azure Portal)
description: Az Azure Portal használatával SQL felügyelt példányriasztásokat hozhat létre, amelyek értesítéseket vagy automatizálást indíthatnak el, ha a megadott feltételek teljesülnek.
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
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639197"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Riasztások létrehozása az Azure SQL felügyelt példányához az Azure Portal használatával

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan állíthat be riasztásokat az adatbázisokhoz az Azure SQL felügyelt példányadatbázisában az Azure Portalon. Riasztások küldhet egy e-mailt, vagy hívja a web hook, ha néhány metrika (például a tárolási méret vagy a CPU-használat) eléri a küszöbértéket. Ez a cikk a riasztási időszakok beállításával kapcsolatos gyakorlati tanácsokat is tartalmaz.

Az Azure-szolgáltatások figyelési metrikái vagy eseményei alapján riasztást kaphat.

* **Metrikaértékek** – A riasztás akkor aktiválódik, ha egy megadott metrika értéke átlépi a küszöbértéket, amelyet mindkét irányban hozzárendel. Ez azt, hogy elindítja mind a feltétel első teljesült, majd utána, amikor ez a feltétel már nem teljesül.
* **Tevékenységnapló-események** – riasztás térhet el *minden* esemény, vagy csak akkor, ha bizonyos számú esemény fordul elő.

A riasztást beállíthatja úgy, hogy az aktiválások során a következőket tegye:

* E-mailes értesítések küldése a szolgáltatás rendszergazdájának és társrendszergazdának
* E-mail küldése a megadott további e-mailekbe.
* Webhook meghívása

A riasztási szabályok konfigurálása és az azokkal kapcsolatos információk beszerezhető a

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [parancssori illesztő (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Riasztási szabály létrehozása metrikán az Azure Portalon

1. A [portálon](https://portal.azure.com/)keresse meg a figyelés iránt érdeklődő erőforrást, és válassza ki.
2. Válassza a **Figyelés** szakasz Riasztások lehetőséget. A szöveg és az ikon némileg eltérhet a különböző erőforrásoktól.  

   ![Figyelés](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. A **Szabály létrehozása** lap megnyitásához kattintson az **Új riasztási szabály** gombra.
   ![Szabály létrehozása](media/sql-database-insights-alerts-portal/create-rule.png)

4. A **Feltétel csoportban** kattintson a **Hozzáadás**gombra.
   ![Feltétel definiálása](media/sql-database-insights-alerts-portal/create-rule.png)
5. A **Jellogika konfigurálása** lapon jelöljön ki egy jelet.
   ![Jel kiválasztása](media/sql-database-insights-alerts-portal/select-signal.png)
6. A jel , például a **CPU százalékos értékének**kiválasztása után megjelenik a **Jellogika konfigurálása** lap.
   ![Jellogika konfigurálása](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Ezen a lapon konfigurálja ezt a küszöbértéktípust, operátort, összesítési típust, küszöbértéket, összesítési részletességet és a kiértékelés gyakoriságát. Ezután kattintson **a Kész gombra.**
8. A **Szabály létrehozása területen**jelöljön ki egy meglévő **műveletcsoportot,** vagy hozzon létre egy új csoportot. Egy műveletcsoport lehetővé teszi, hogy meghatározza a riasztási feltétel bekövetkeztekor végrehajtandó műveletet.
  ![Műveletcsoport definiálása](media/sql-database-insights-alerts-portal/action-group.png)

9. Adja meg a szabály nevét, adjon meg egy nem kötelező leírást, válassza ki a szabály súlyossági szintjét, válassza ki, hogy engedélyezi-e a szabályt a szabály létrehozásakor, majd kattintson a **Szabályriasztás létrehozása** a metrikaszabály-riasztás létrehozásához.

10 percen belül a riasztás aktív, és a korábban leírt eseményindítók.

## <a name="next-steps"></a>További lépések

* További információ [a webhookok riasztásokban való konfigurálásáról.](../azure-monitor/platform/alerts-webhooks.md)
