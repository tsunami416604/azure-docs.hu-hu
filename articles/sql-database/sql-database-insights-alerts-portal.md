---
title: Riasztások és értesítések beállítása a Azure Portal használatával | Microsoft Docs
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
ms.openlocfilehash: 9468dbd71ee8da88cbabc3ca9f76c77d47adc221
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567928"
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
   
     ![Figyelés](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **CSAK SQL DW**: Kattintson a **DWU használati** gráfra. Válassza ki a **klasszikus riasztások megtekintése** lehetőséget.

3. Jelölje be a **metrikai riasztás hozzáadása (klasszikus)** gombot, és töltse ki a mezőket.
   
    ![Riasztás beállítása](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **Nevezze** el a riasztási szabályt, és válasszon egy **leírást**, amely az értesítő e-mailekben is megjelenik.
5. Válassza ki a figyelni kívánt metrikát, majd válasszon egy feltételt és egy **küszöbértéket** a metrikához. Adja meg azt az időtartamot is, ameddig a metrikai szabálynak meg kell felelnie a riasztási eseményindítók előtt. Így például, ha a "PT5M" időszakot használja, és a riasztás a 80%-nál nagyobb CPU-t keres, akkor a riasztás akkor aktiválódik, ha az **átlagos** CPU 5 percnél nagyobb a 80%-nál. Ha az első eseményindító bekövetkezik, a rendszer ismét elindítja, ha az átlagos CPU 5 percnél 80% alá esik. A CPU-mérés 1 percenként történik. Az alábbi táblázatban találja a támogatott időablakokat, valamint az egyes riasztások által használt összesítési típust – nem minden riasztás használja az átlagos értéket.   
6. Az **e-mail-tulajdonosok ellenőrzése...** ha azt szeretné, hogy a rendszergazdák és a társ-rendszergazdák e-mailben elküldjék a riasztást.
7. Ha további e-maileket szeretne kapni, hogy értesítést kapjon a riasztás megjelenésekor, adja hozzá őket a **további rendszergazdai e-mailek (ek)** mezőben. Több e-mail-cím elválasztása pontosvesszővel – *e-mail\@contoso. com\@; email2 contoso.com*
8. Ha azt szeretné, hogy a riasztás elinduljon, a webhook mezőben érvényes URI-t kell létrehoznia.
9. A riasztás létrehozásához kattintson **az OK gombra** .   

Néhány percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

## <a name="managing-your-alerts"></a>A riasztások kezelése
A riasztás létrehozása után kiválaszthatja a következőket:

* Megtekintheti a metrika küszöbértékét és az előző naptól számított tényleges értékeket bemutató diagramot.
* Szerkessze vagy törölje.
* **Tiltsa le** vagy **engedélyezze** , ha átmenetileg le kívánja állítani vagy folytatni szeretné az értesítések fogadását az adott riasztáshoz.


## <a name="sql-database-alert-values"></a>SQL Database riasztási értékek

| Erőforrástípus | Metrika neve | Rövid név | Aggregáció típusa | Minimális riasztási idő ablaka|
| --- | --- | --- | --- | --- |
| SQL-adatbázis | cpu_percent | Processzorhasználat (%) | Average | 5 perc |
| SQL-adatbázis | physical_data_read_percent | Adat IO százalékos aránya | Average | 5 perc |
| SQL-adatbázis | log_write_percent | Naplózási IO százalékos aránya | Average | 5 perc |
| SQL-adatbázis | dtu_consumption_percent | DTU százalékos értéke | Average | 5 perc |
| SQL-adatbázis | storage | Adatbázis teljes mérete | Maximum | 30 perc |
| SQL-adatbázis | connection_successful | Sikeres kapcsolatok | Összes | 10 perc |
| SQL-adatbázis | connection_failed | Sikertelen kapcsolatok | Összes | 10 perc |
| SQL-adatbázis | blocked_by_firewall | Tűzfal blokkolja | Összes | 10 perc |
| SQL-adatbázis | holtpont | Holtpontok | Összes | 10 perc |
| SQL-adatbázis | storage_percent | Adatbázis méretének kihasználtsága | Maximum | 30 perc |
| SQL-adatbázis | xtp_storage_percent | Memóriában tárolt OLTP tárolási százaléka (előzetes verzió) | Average | 5 perc |
| SQL-adatbázis | workers_percent | Munkavégzők százalékos aránya | Average | 5 perc |
| SQL-adatbázis | sessions_percent | Munkamenetek százaléka | Average | 5 perc |
| SQL-adatbázis | dtu_limit | DTU korlátja | Average | 5 perc |
| SQL-adatbázis | dtu_used | Használt DTU | Average | 5 perc |
||||||
| Rugalmas készlet | cpu_percent | Processzorhasználat (%) | Average | 10 perc |
| Rugalmas készlet | physical_data_read_percent | Adat IO százalékos aránya | Average | 10 perc |
| Rugalmas készlet | log_write_percent | Naplózási IO százalékos aránya | Average | 10 perc |
| Rugalmas készlet | dtu_consumption_percent | DTU százalékos értéke | Average | 10 perc |
| Rugalmas készlet | storage_percent | Tárolási százalék | Average | 10 perc |
| Rugalmas készlet | workers_percent | Munkavégzők százalékos aránya | Average | 10 perc |
| Rugalmas készlet | eDTU_limit | eDTU korlátja | Average | 10 perc |
| Rugalmas készlet | storage_limit | Tárolási korlát | Average | 10 perc |
| Rugalmas készlet | eDTU_used | használt eDTU | Average | 10 perc |
| Rugalmas készlet | storage_used | Felhasznált tároló | Average | 10 perc |
||||||               
| SQL data warehouse | cpu_percent | Processzorhasználat (%) | Average | 10 perc |
| SQL data warehouse | physical_data_read_percent | Adat IO százalékos aránya | Average | 10 perc |
| SQL data warehouse | connection_successful | Sikeres kapcsolatok | Összes | 10 perc |
| SQL data warehouse | connection_failed | Sikertelen kapcsolatok | Összes | 10 perc |
| SQL data warehouse | blocked_by_firewall | Tűzfal blokkolja | Összes | 10 perc |
| SQL data warehouse | service_level_objective | Az adatbázis szolgáltatási szintje | Összes | 10 perc |
| SQL data warehouse | dwu_limit | DWU korlátja | Maximum | 10 perc |
| SQL data warehouse | dwu_consumption_percent | DWU százalékos aránya | Average | 10 perc |
| SQL data warehouse | dwu_used | Használt DWU | Average | 10 perc |
||||||


## <a name="next-steps"></a>További lépések
* [Tekintse át az Azure monitoring áttekintését](../monitoring-and-diagnostics/monitoring-overview.md) , beleértve a gyűjtött információk típusát és a figyelést.
* További információ a [webhookok riasztásokban való konfigurálásáról](../azure-monitor/platform/alerts-webhooks.md).
* Tekintse [át a diagnosztikai naplókat](../azure-monitor/platform/diagnostic-logs-overview.md) , és gyűjtsön részletes, nagy gyakoriságú mérőszámokat a szolgáltatásban.
* Tekintse [](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) át a metrikák gyűjteményét, és győződjön meg arról, hogy a szolgáltatás elérhető és rugalmas.
