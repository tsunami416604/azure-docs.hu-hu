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
ms.date: 05/04/2020
ms.openlocfilehash: 0e7c4cde684f393fd98ada46393948c5a62efa2f
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82800840"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Riasztások létrehozása az Azure SQL felügyelt példányaihoz a Azure Portal használatával

## <a name="overview"></a>Áttekintés

Ebből a cikkből megtudhatja, hogyan állíthatja be a riasztásokat az Azure SQL felügyelt példány-adatbázisában lévő adatbázisokhoz a Azure Portal használatával. A riasztások e-mailt küldhetnek, meghívhatnak egy webhookot, végrehajtanak egy Azure-függvényt, runbook, külső ITSM kompatibilis jegyrendszer használatát, meghívhatják a telefont, vagy szöveges üzenetet küldhetnek, ha valamilyen metrika, például a példány tárolási mérete vagy a CPU-használat, egy előre meghatározott küszöbértéket ér el. Ez a cikk a riasztási időszakok beállításának ajánlott eljárásait is tartalmazza.

Riasztást kaphat az Azure-szolgáltatásokra vonatkozó metrikák vagy események figyelése alapján.

* **Metrikai értékek** – a riasztás akkor aktiválódik, ha egy adott metrika értéke az egyik irányban hozzárendelt küszöbértéket keresztezi. Ez azt is elindítja, hogy a feltétel első teljesülése esetén, majd később, amikor az adott feltétel már nem teljesül.

A riasztást úgy is beállíthatja, hogy a következő esetekben hajtsa végre az eseményindítókat:

* E-mail-értesítések küldése a szolgáltatás-rendszergazdának és a rendszergazdáknak
* Küldjön e-mailt a megadott további e-mailekre.
* Telefonszám meghívása hangüzenettel
* Szöveges üzenet küldése telefonszámra
* Webhook meghívása
* Azure-függvény meghívása
* Az Azure runbook meghívása
* Külső jegyű ITSM-kompatibilis rendszer hívása

A riasztási szabályokkal kapcsolatos információkat a következő felületek használatával konfigurálhatja és kérheti le:

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Parancssori felület (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="alerting-metrics-available-for-managed-instance"></a>Felügyelt példányok számára elérhető riasztási mérőszámok

> [!IMPORTANT]
> A riasztási metrikák csak felügyelt példányok esetén érhetők el. A felügyelt példányokban lévő egyes adatbázisok esetében nem érhetők el riasztási mérőszámok. Az adatbázis-diagnosztika telemetria a [diagnosztikai naplók](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)formájában érhető el. A diagnosztikai naplókban lévő riasztások az [SQL Analytics](../azure-monitor/insights/azure-sql.md) terméken belül állíthatók be a felügyelt példányhoz tartozó [log riasztási parancsfájlok](../azure-monitor/insights/azure-sql.md#creating-alerts-for-managed-instances) használatával.

A következő felügyelt példány metrikái elérhetők riasztási konfigurációhoz:

| Metrika | Description | Mértékegység \ lehetséges értékek |
| :--------- | --------------------- | ----------- |
| Átlagos CPU-százalék | A CPU-kihasználtság átlagos százaléka a kiválasztott időszakban. | 0-100 (százalék) |
| I/o-bájtok olvasása | Az i/o-bájtok beolvasása a kiválasztott időszakban. | Bájt |
| I/o-bájtok írása | A kiválasztott időszakban írt IO-bájtok. | Bájt |
| IO-kérelmek száma | Az i/o-kérések száma a kiválasztott időszakban. | Numerikus |
| Tárterület fenntartva | Jelenlegi max. a felügyelt példány számára fenntartott tárterület. Változások az erőforrás-skálázási művelettel. | MB (megabájt) |
| Felhasznált tárterület | A kiválasztott időszakban használt tárterület. Az adatbázisok és a példányok tárterület-felhasználásának változásai. | MB (megabájt) |
| Virtuális mag száma | a felügyelt példány virtuális mag kiépítve. Változások az erőforrás-skálázási művelettel. | 4-80 (virtuális mag) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Riasztási szabály létrehozása metrika esetén a Azure Portal

1. Az Azure [Portalon](https://portal.azure.com/)keresse meg a figyelni kívánt SQL felügyelt példányt, majd válassza ki.

2. Válassza a **metrikák** menüpontot a figyelés szakaszban.

   ![Figyelés](media/sql-database-managed-instance-alerts/mi-alerting-menu-annotated.png)
  
3. A legördülő menüben válassza ki az egyik mérőszámot, amelyhez be szeretné állítani a riasztást (a példában a felhasznált tárterület látható).

4. Válassza ki az összesítési időszakot – átlagos, minimális vagy maximális érték a megadott időszakban (AVG, min vagy max). 

5. **Új riasztási szabály** kiválasztása

6. A riasztási szabály létrehozása panelen kattintson a **feltétel nevére** (a példában a felhasznált tárolóhely látható).

   ![Feltétel meghatározása](media/sql-database-managed-instance-alerts/mi-create-metrics-alert-smaller-annotated.png)

7. A jel logikájának konfigurálása panelen adja meg az operátort, az Összesítés típusát és a küszöbértéket.

   * Az operátor típusa beállítás értéke nagyobb, mint, egyenlő és kisebb, mint (a küszöbérték)
   * Az összesítési típus beállításai a következők: min, Max vagy Average (az összesítési részletességi időszakban)
   * A küszöbérték a riasztási érték, amely az operátori és az összesítési feltételek alapján lesz kiértékelve.
   
   ![Configure_signal_logic](media/sql-database-managed-instance-alerts/mi-configure-signal-logic-annotated.png)
   
   A képernyőképen látható példában a 1840876 MB értékű érték a 1,8 TB küszöbértéket jelképezi. Mivel a példában szereplő operátor értéke nagyobb, mint a, a rendszer akkor hozza létre a riasztást, ha a felügyelt példányon a tárterület-felhasználás 1,8 TB fölé kerül. Vegye figyelembe, hogy a tárolóhelyek metrikáinak küszöbértékét MEGABÁJTban kell megadni.

8. Állítsa be a próbaidőszak összesítési részletességét percben és a kiértékelés gyakoriságát. A kiértékelés gyakorisága azt jelzi, hogy a riasztási rendszer rendszeres időközönként ellenőrzi, hogy teljesülnek-e a küszöbérték-feltétel.

9. Válasszon műveleti csoportot. A műveleti csoport ablaktábla megjeleníti a meglévőket, vagy létrehozhat egy új műveletet. Ez a művelet határozza meg, hogy a rendszer a riasztások kiváltásakor fog történni (például e-mailek küldése, telefon hívása, webhook, Azure-függvény vagy runbook).

   ![Select_action_group](media/sql-database-managed-instance-alerts/mi-select-action-group-smaller-annotated.png)

   * Új műveleti csoport létrehozásához válassza a **+ műveleti csoport létrehozása** lehetőséget.

      ![Create_action_group_alerts](media/sql-database-managed-instance-alerts/mi-create-alert-action-group-smaller-annotated.png)
   
   * Adja meg, hogyan kívánja riasztani a riasztást: adja meg a műveleti csoport nevét, a rövid nevet, a művelet nevét, és válassza a művelet típusa lehetőséget. A Művelettípus határozza meg, hogy e-mailben, szöveges üzenetben, hanghívásban vagy esetleg webhookban, Azure-függvényben, runbook vagy ITSM-jegyben lesz-e létrehozva a kompatibilis rendszerben.

      ![Define_how_to_be_alerted](media/sql-database-managed-instance-alerts/mi-add-alerts-action-group-annotated.png)

10. Adja meg a rekordok riasztási szabályának részleteit, és válassza ki a súlyosság típusát.

      ![Rule_description](media/sql-database-managed-instance-alerts/mi-rule-details-complete-smaller-annotated.png)

   * A riasztási szabály létrehozásához kattintson a **riasztási szabály létrehozása** gombra.

Az új riasztási szabály néhány percen belül aktív lesz, és a beállítások alapján aktiválódik.

## <a name="verifying-alerts"></a>Riasztások ellenőrzése

> [!NOTE]
> A zajos riasztások elnyomásával kapcsolatban lásd: riasztások leállítása [műveleti szabályok használatával](../azure-monitor/platform/alerts-action-rules.md#suppression-of-alerts).

Riasztási szabály beállításakor ellenőrizze, hogy elégedett-e a riasztási triggerrel és annak gyakoriságával. Az ezen a lapon látható, a használatban lévő tárolóhelyre vonatkozó riasztás beállításához, ha a riasztási lehetőség e-mailben szerepel, a következőt láthatja: e-mail-cím.

   ![alert_example](media/sql-database-managed-instance-alerts/mi-email-alert-example-smaller-annotated.png)

Az e-mail megjeleníti a riasztás nevét, a küszöbérték részleteit, valamint azt, hogy a riasztás miért lett aktiválva a riasztás ellenőrzéséhez és hibakereséséhez. A **Azure Portal** gombra kattintva megtekintheti a Azure Portal e-mailben fogadott riasztásokat. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Meglévő riasztási szabályok megtekintése, felfüggesztése, aktiválása, módosítása és törlése

> [!NOTE]
> A meglévő riasztásokat Azure Portal irányítópult riasztások menüjéből kell felügyelni. A meglévő riasztások nem módosíthatók a felügyelt példányok erőforrásának paneljéről.

Meglévő riasztások megtekintése, felfüggesztése, aktiválása, módosítása és törlése:

1. Riasztások keresése Azure Portal keresés használatával. Kattintson a riasztások elemre.

   ![find_alerts](media/sql-database-managed-instance-alerts/mi-manage-alerts-browse-smaller-annotated.png)

   Azt is megteheti, hogy a riasztások elemre kattint az Azure navigációs sávján, ha konfigurálva van.

2. A riasztások panelen kattintson a riasztási szabályok kezelése lehetőségre.

   ![modify_alerts](media/sql-database-managed-instance-alerts/mi-manage-alert-rules-smaller-annotated.png)

   Megjelenik a meglévő riasztások listája. Válasszon ki egy egyéni meglévő riasztási szabályt a kezeléséhez. A meglévő aktív szabályok módosíthatók és beállíthatók az Ön igényei szerint. Az aktív szabályok felfüggesztés nélkül is felmenthetők. 

## <a name="next-steps"></a>További lépések

* Tudnivalók Azure Monitor riasztási rendszerről: [a riasztások áttekintése Microsoft Azure](../azure-monitor/platform/alerts-overview.md)
* További információ a metrikus riasztásokról: [a metrikai riasztások működésének ismertetése Azure monitor](../azure-monitor/platform/alerts-metric-overview.md)
* További tudnivalók a webhookok riasztásokban való konfigurálásáról: [webhook meghívása klasszikus metrikus riasztással](../azure-monitor/platform/alerts-webhooks.md)
* Tudnivalók a riasztások PowerShell használatával történő konfigurálásáról és kezeléséről: [műveleti szabályok](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)
* További információ a riasztások API-val történő konfigurálásáról és kezeléséről: [Azure Monitor REST API referenciája](https://docs.microsoft.com/rest/api/monitor/) 
