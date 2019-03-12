---
title: Riasztások és az Azure portal használatával értesítések beállítása |} A Microsoft Docs
description: Az Azure portal használatával hozzon létre SQL Database-riasztást, amelyek is indíthat értesítések vagy az automation az Ön által megadott feltételek teljesülése esetén.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: dea9a73d7ac868e45d3abf2ee3ff8366fc7b65cd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783091"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Riasztások létrehozása az Azure SQL Database és a Data warehouse-bA az Azure portal használatával

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan állítható be az Azure portal használatával Azure SQL Database és az adatraktár riasztások. Riasztásokat is küldjön egy e-mailt vagy egy webhook hívása, amikor bizonyos mérőszám (például adatbázis mérete vagy a CPU-használat) eléri a küszöbértéket. Ez a cikk a riasztási időszakok beállítása ajánlott eljárásokat is tartalmaz.    

> [!IMPORTANT]
> Ez a funkció még nem áll rendelkezésre a felügyelt példány. Alternatív megoldásként az SQL Agent segítségével küld e-mail riasztást bizonyos metrikák alapján [dinamikus felügyeleti nézetek](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

A figyelési metrikákat, vagy eseményeket, az Azure-szolgáltatások alapuló riasztást kaphat.

* **Metrikaértékek** – a riasztást aktivál, ha egy adott mérőszám értéke átlép egy küszöbértéket, bármelyik irányba rendel. Vagyis mindkét elindítja amikor először a feltétel teljesül, és majd ezt követően, hogy a feltétel ha van már nem teljesül.    
* **Tevékenységnapló-események** -riasztást is aktiválhatja a *minden* esemény, vagy csak akkor, ha bizonyos számú események történnek.

Tegye a következőket, amikor elindítja a riasztásokat lehet beállítani:

* e-mail értesítések küldéséhez a szolgáltatás-rendszergazda és társrendszergazdák
* További e-mailek, Ön által megadott e-mail küldése.
* Webhook meghívása

Konfigurálhatja és riasztási szabályok használatával kapcsolatos információk lekérése

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Parancssori felület (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Az Azure Monitor REST API-val](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Hozzon létre egy riasztási szabályt a metrika az Azure portal használatával
1. Az a [portál](https://portal.azure.com/), keresse meg az erőforrást, amelyek figyelése érdekli, és válassza ki azt.
2. Válassza ki **riasztások (klasszikus)** a figyelés szakaszban. A szöveg és ikon kissé eltérhet a különböző erőforrásokat.  
   
     ![Figyelés](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **CSAK AZ SQL DW**: Kattintson a **DWU-felhasználás** grafikon. Válassza ki **klasszikus riasztások megtekintése**

3. Válassza ki a **metrikariasztás hozzáadása (klasszikus)** gombra, és töltse ki a mezőket.
   
    ![Riasztás beállítása](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **Név** a riasztás szabályt, majd válassza ki a **leírása**, amely értesítő e-mailek is megjeleníti.
5. Válassza ki a **metrika** figyelheti, majd kattintson a kívánt egy **feltétel** és **küszöbérték** a mérőszám értéke. Is kiválaszthatják a **időszak** , ameddig a metrikaszabály riasztási triggerek előtt teljesülniük kell. Így például, ha az időszak "PT5M" használja, és a riasztás 80 % fölötti keres CPU, a riasztás aktiválásakor mikor a **átlagos** CPU 80 % fölötti 5 perc volt. Akkor fordul elő, az első eseményindító, ha azt újra aktivál, ha az átlagos Processzorhasználat nem éri a 80 %-kal több mint 5 perc. A CPU-mérték 1 percenként történik. Tekintse át az alábbi táblázat a támogatott idő windows és az összesítés írja be, hogy minden egyes riasztás használ – nem minden riasztás átlagos értéket használja.   
6. Ellenőrizze **E-mail-tulajdonosok...**  Ha azt szeretné, hogy elküldhetők e-mailben a riasztás akkor aktiválódik, amikor a rendszergazdák és a társadminisztrátorok.
7. Ha azt szeretné, további e-mail értesítést kapnak, ha a riasztás akkor aktiválódik, adja hozzá őket a **további rendszergazdai email(s)** mező. Pontosvesszővel több e-mailek -  *email@contoso.com;email2@contoso.com*
8. Egy érvényes URI-t a PUT a **Webhook** Ha azt szeretné, hogy ez a riasztás aktiválódásakor nevű mezőt.
9. Válassza ki **OK** Ha ezzel elkészült, a riasztás létrehozásához.   

Néhány percen belül a riasztás aktív, és elindítja a fent leírtaknak megfelelően.

## <a name="managing-your-alerts"></a>A riasztások kezelése
Miután létrehozta a riasztást, is kiválaszthatja, és:

* Tekintse meg a metrika küszöbértéke és a tényleges értékek az előző nap megjelenítő grafikon.
* Szerkesztheti, és törölje azt.
* **Tiltsa le** vagy **engedélyezése** , ha azt szeretné, ideiglenesen leállíthatja, vagy folytathatja a riasztás-mailjeire.


## <a name="sql-database-alert-values"></a>Az SQL Database riasztási értékek

| Erőforrás típusa | Metrika neve | Rövid név | Aggregáció típusa | Minimális riasztási időkerete|
| --- | --- | --- | --- | --- |
| SQL-adatbázis | cpu_percent | Processzorhasználat (%) | Átlag | 5 perc |
| SQL-adatbázis | physical_data_read_percent | Adat IO kihasználtsága (%) | Átlag | 5 perc |
| SQL-adatbázis | log_write_percent | Napló i/o-százaléka | Átlag | 5 perc |
| SQL-adatbázis | dtu_consumption_percent | DTU-kihasználtság (%) | Átlag | 5 perc |
| SQL-adatbázis | tárterület | Adatbázis teljes mérete | Maximum | 30 perc |
| SQL-adatbázis | connection_successful | Sikeres kapcsolatok | Összes | 10 perc |
| SQL-adatbázis | connection_failed | Sikertelen kapcsolatok | Összes | 10 perc |
| SQL-adatbázis | blocked_by_firewall | Tűzfal által blokkolva | Összes | 10 perc |
| SQL-adatbázis | Holtpont | Holtpontok | Összes | 10 perc |
| SQL-adatbázis | storage_percent | Adatbázis méretének kihasználtsága | Maximum | 30 perc |
| SQL-adatbázis | xtp_storage_percent | Memóriabeli OLTP storage percent(Preview) | Átlag | 5 perc |
| SQL-adatbázis | workers_percent | Feldolgozók százalékos aránya | Átlag | 5 perc |
| SQL-adatbázis | sessions_percent | Munkamenetek százaléka | Átlag | 5 perc |
| SQL-adatbázis | dtu_limit | DTU-korlát | Átlag | 5 perc |
| SQL-adatbázis | dtu_used | Használt dtu-k | Átlag | 5 perc |
||||||
| Rugalmas készlet | cpu_percent | Processzorhasználat (%) | Átlag | 10 perc |
| Rugalmas készlet | physical_data_read_percent | Adat IO kihasználtsága (%) | Átlag | 10 perc |
| Rugalmas készlet | log_write_percent | Napló i/o-százaléka | Átlag | 10 perc |
| Rugalmas készlet | dtu_consumption_percent | DTU-kihasználtság (%) | Átlag | 10 perc |
| Rugalmas készlet | storage_percent | Tárolási százalékos aránya | Átlag | 10 perc |
| Rugalmas készlet | workers_percent | Feldolgozók százalékos aránya | Átlag | 10 perc |
| Rugalmas készlet | eDTU_limit | az eDTU-korlát | Átlag | 10 perc |
| Rugalmas készlet | storage_limit | Tárolási kapacitása | Átlag | 10 perc |
| Rugalmas készlet | eDTU_used | használt edtu-k | Átlag | 10 perc |
| Rugalmas készlet | storage_used | Felhasznált tárterület | Átlag | 10 perc |
||||||               
| SQL data warehouse | cpu_percent | Processzorhasználat (%) | Átlag | 10 perc |
| SQL data warehouse | physical_data_read_percent | Adat IO kihasználtsága (%) | Átlag | 10 perc |
| SQL data warehouse | connection_successful | Sikeres kapcsolatok | Összes | 10 perc |
| SQL data warehouse | connection_failed | Sikertelen kapcsolatok | Összes | 10 perc |
| SQL data warehouse | blocked_by_firewall | Tűzfal által blokkolva | Összes | 10 perc |
| SQL data warehouse | service_level_objective | Az adatbázis szolgáltatási szintjét | Összes | 10 perc |
| SQL data warehouse | dwu_limit | dwu-korlát | Maximum | 10 perc |
| SQL data warehouse | dwu_consumption_percent | Százalékos DWU | Átlag | 10 perc |
| SQL data warehouse | dwu_used | Alkalmazott DWU | Átlag | 10 perc |
||||||


## <a name="next-steps"></a>További lépések
* [Az Azure monitoring áttekintése](../monitoring-and-diagnostics/monitoring-overview.md) többek között a különböző információkat gyűjthet, és figyelheti.
* Tudjon meg többet [webhookok konfigurálásáról a riasztások](../azure-monitor/platform/alerts-webhooks.md).
* Get- [diagnosztikai naplók áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md) és részletes nagy gyakoriságú metrikákat gyűjt be a szolgáltatás.
* Get- [metrikák gyűjtési áttekintése](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , győződjön meg arról, hogy szolgáltatása elérhető és válaszkész.
