---
title: Riasztások és értesítések beállítása (Azure Portal)
description: A Azure Portal használatával SQL Database riasztásokat hozhat létre, amelyek az Ön által megadott feltételek teljesülése esetén aktiválják az értesítéseket vagy az automatizálást.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 11/02/2018
ms.openlocfilehash: c2b889d4013abb60c9ad7bb4bcdc4e6546cfa37c
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745953"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Riasztások létrehozása a Azure SQL Database és az adatraktárhoz a Azure Portal használatával

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan állíthat be Azure SQL Database és adatraktár-riasztásokat a Azure Portal használatával. A riasztások e-mailt küldhetnek, vagy meghívhatnak egy webhookot, ha valamilyen metrika (például az adatbázis mérete vagy a CPU-használat) eléri a küszöbértéket. Ez a cikk a riasztási időszakok beállításának ajánlott eljárásait is tartalmazza.    

> [!IMPORTANT]
> Ez a funkció még nem érhető el a felügyelt példányban. Alternatív megoldásként az SQL Agent használatával e-mail-riasztásokat küldhet a [dinamikus felügyeleti nézeteken](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)alapuló mérőszámokhoz.

Riasztást kaphat az Azure-szolgáltatásokra vonatkozó metrikák vagy események figyelése alapján.

* **Metrikai értékek** – a riasztás akkor aktiválódik, ha egy adott metrika értéke az egyik irányban hozzárendelt küszöbértéket keresztezi. Ez azt is elindítja, hogy a feltétel első teljesülése esetén, majd később, amikor az adott feltétel már nem teljesül.    
* **Műveletnapló eseményei** – a riasztás *minden* eseménynél aktiválható, vagy csak bizonyos számú esemény bekövetkeztekor.

A riasztást úgy is beállíthatja, hogy a következő esetekben hajtsa végre az eseményindítókat:

* e-mail-értesítések küldése a szolgáltatás-rendszergazdának és a társ-rendszergazdáknak
* Küldjön e-mailt a megadott további e-mailekre.
* webhook meghívása

A riasztási szabályokkal kapcsolatos információkat a

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [parancssori felület (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Riasztási szabály létrehozása metrika esetén a Azure Portal
1. A [portálon](https://portal.azure.com/)keresse meg a figyelni kívánt erőforrást, és jelölje ki.
2. A figyelés szakaszban válassza a **riasztások (klasszikus)** lehetőséget. A szöveg és az ikon némileg eltérő lehet a különböző erőforrások esetében.  
   
     ![Monitoring](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **Csak SQL DW esetén**: kattintson a **DWU használati** gráfra. Válassza ki a **klasszikus riasztások megtekintése** lehetőséget.

3. Jelölje be a **metrikai riasztás hozzáadása (klasszikus)** gombot, és töltse ki a mezőket.
   
    ![Riasztás hozzáadása](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **Nevezze** el a riasztási szabályt, és válasszon egy **leírást**, amely az értesítő e-mailekben is megjelenik.
5. Válassza ki a figyelni kívánt **metrikát** , majd válasszon egy **feltételt** és egy **küszöbértéket** a metrikához. Adja meg azt az **időtartamot** is, ameddig a metrikai szabálynak meg kell felelnie a riasztási eseményindítók előtt. Így például, ha a "PT5M" időszakot használja, és a riasztás a 80%-nál nagyobb CPU-t keres, akkor a riasztás akkor aktiválódik, ha az **átlagos** CPU 5 percnél nagyobb a 80%-nál. Ha az első eseményindító bekövetkezik, a rendszer ismét elindítja, ha az átlagos CPU 5 percnél 80% alá esik. A CPU-mérés 1 percenként történik. Az alábbi táblázatban találja a támogatott időablakokat, valamint az egyes riasztások által használt összesítési típust – nem minden riasztás használja az átlagos értéket.   
6. Az **e-mail-tulajdonosok ellenőrzése...** ha azt szeretné, hogy a rendszergazdák és a társ-rendszergazdák e-mailben elküldjék a riasztást.
7. Ha további e-maileket szeretne kapni, hogy értesítést kapjon a riasztás megjelenésekor, adja hozzá őket a **további rendszergazdai e-mailek (ek)** mezőben. Több e-mail-cím elválasztása pontosvesszővel – *e-mail-\@contoso. com; email2\@contoso.com*
8. Ha azt szeretné, hogy a riasztás elinduljon, a **webhook** mezőben érvényes URI-t kell létrehoznia.
9. A riasztás létrehozásához kattintson **az OK gombra** .   

Néhány percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

## <a name="managing-your-alerts"></a>A riasztások kezelése
A riasztás létrehozása után kiválaszthatja a következőket:

* Megtekintheti a metrika küszöbértékét és az előző naptól számított tényleges értékeket bemutató diagramot.
* Szerkessze vagy törölje.
* **Tiltsa le** vagy **engedélyezze** , ha átmenetileg le kívánja állítani vagy folytatni szeretné az értesítések fogadását az adott riasztáshoz.


## <a name="sql-database-alert-values"></a>SQL Database riasztási értékek

| Erőforrás típusa | Metrika neve | Felhasználóbarát név | Aggregáció típusa | Minimális riasztási idő ablaka|
| --- | --- | --- | --- | --- |
| SQL-adatbázis | cpu_percent | Processzorhasználat (%) | Átlag | 5 perc |
| SQL-adatbázis | physical_data_read_percent | Adat IO kihasználtsága (%) | Átlag | 5 perc |
| SQL-adatbázis | log_write_percent | Naplózási IO-százalék | Átlag | 5 perc |
| SQL-adatbázis | dtu_consumption_percent | DTU-kihasználtság (%) | Átlag | 5 perc |
| SQL-adatbázis | tárterület | Adatbázis teljes mérete | Maximum | 30 perc |
| SQL-adatbázis | connection_successful | Sikeres kapcsolatok | Összes | 10 perc |
| SQL-adatbázis | connection_failed | Sikertelen kapcsolatok | Összes | 10 perc |
| SQL-adatbázis | blocked_by_firewall | Tűzfal blokkolja | Összes | 10 perc |
| SQL-adatbázis | holtpont | Holtpontok | Összes | 10 perc |
| SQL-adatbázis | storage_percent | Adatbázis méretének kihasználtsága | Maximum | 30 perc |
| SQL-adatbázis | xtp_storage_percent | Memóriában tárolt OLTP tárolási százaléka (előzetes verzió) | Átlag | 5 perc |
| SQL-adatbázis | workers_percent | Munkavégzők százalékos aránya | Átlag | 5 perc |
| SQL-adatbázis | sessions_percent | Munkamenetek százaléka | Átlag | 5 perc |
| SQL-adatbázis | dtu_limit | DTU korlátja | Átlag | 5 perc |
| SQL-adatbázis | dtu_used | Használt DTU | Átlag | 5 perc |
||||||
| Rugalmas készlet | cpu_percent | Processzorhasználat (%) | Átlag | 10 perc |
| Rugalmas készlet | physical_data_read_percent | Adat IO kihasználtsága (%) | Átlag | 10 perc |
| Rugalmas készlet | log_write_percent | Naplózási IO-százalék | Átlag | 10 perc |
| Rugalmas készlet | dtu_consumption_percent | DTU-kihasználtság (%) | Átlag | 10 perc |
| Rugalmas készlet | storage_percent | Tárolási százalék | Átlag | 10 perc |
| Rugalmas készlet | workers_percent | Munkavégzők százalékos aránya | Átlag | 10 perc |
| Rugalmas készlet | eDTU_limit | eDTU korlátja | Átlag | 10 perc |
| Rugalmas készlet | storage_limit | Tárolási korlát | Átlag | 10 perc |
| Rugalmas készlet | eDTU_used | használt eDTU | Átlag | 10 perc |
| Rugalmas készlet | storage_used | Felhasznált tárterület | Átlag | 10 perc |
||||||               
| SQL Data Warehouse | cpu_percent | Processzorhasználat (%) | Átlag | 10 perc |
| SQL Data Warehouse | physical_data_read_percent | Adat IO kihasználtsága (%) | Átlag | 10 perc |
| SQL Data Warehouse | connection_successful | Sikeres kapcsolatok | Összes | 10 perc |
| SQL Data Warehouse | connection_failed | Sikertelen kapcsolatok | Összes | 10 perc |
| SQL Data Warehouse | blocked_by_firewall | Tűzfal blokkolja | Összes | 10 perc |
| SQL Data Warehouse | service_level_objective | Az adatbázis szolgáltatási szintje | Összes | 10 perc |
| SQL Data Warehouse | dwu_limit | DWU korlátja | Maximum | 10 perc |
| SQL Data Warehouse | dwu_consumption_percent | DWU százalékos aránya | Átlag | 10 perc |
| SQL Data Warehouse | dwu_used | Használt DWU | Átlag | 10 perc |
||||||


## <a name="next-steps"></a>Következő lépések
* [Tekintse át az Azure monitoring áttekintését](../monitoring-and-diagnostics/monitoring-overview.md) , beleértve a gyűjtött információk típusát és a figyelést.
* További információ a [webhookok riasztásokban való konfigurálásáról](../azure-monitor/platform/alerts-webhooks.md).
* [Tekintse át a diagnosztikai naplókat](../azure-monitor/platform/platform-logs-overview.md) , és gyűjtsön részletes, nagy gyakoriságú mérőszámokat a szolgáltatásban.
* [Tekintse át a metrikák gyűjteményét](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , és győződjön meg arról, hogy a szolgáltatás elérhető és rugalmas.
