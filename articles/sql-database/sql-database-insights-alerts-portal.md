---
title: SQL-adatbázis riasztások létrehozásához használja az Azure portálon |} Microsoft Docs
description: Az Azure-portál használatával hozzon létre SQL adatbázis riasztást, amelyek elindítható értesítések vagy automation, ha a megadott feltételek teljesülnek.
author: aamalvea
manager: craigg
services: sql-database
ms.service: sql-database
ms.custom: monitor and tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: aamalvea
ms.openlocfilehash: 349f1bbb0bfcc737d70e32947dd217c2cd51e194
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648023"
---
# <a name="use-azure-portal-to-create-alerts-for-azure-sql-database-and-data-warehouse"></a>Az Azure portál használatával riasztások létrehozása az Azure SQL adatbázishoz és Adatraktárhoz

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan állíthatja be az Azure portál használata az Azure SQL Database és az adatraktár riasztások. Ez a cikk a riasztási időszakok beállítása ajánlott eljárásokat is tartalmaz.    

A figyelési metrikákat, vagy események, az Azure-szolgáltatások alapuló riasztást kaphat.

* **Metrika értékek** -a riasztás elindítja a megadott metrika értékét ebbe a küszöbérték mindkét irányban rendel. Ez azt jelenti, hogy elindítja a mindkét Ha először a feltétel teljesül, és majd ezt követően, hogy a feltétel mikor van már nem teljesül.    
* **Tevékenység naplóeseményeket** -riasztást aktiválhatók *minden* esemény, vagy csak akkor, ha bizonyos számú esemény történik.

Az alábbi lépések, amikor elindítja a riasztásokat lehet beállítani:

* e-mail értesítések küldéséhez a szolgáltatás-rendszergazda és a társadminisztrátorok
* e-mail küldéséhez megadott további e-maileket.
* A webhook hívása

Konfigurálhatja, és a riasztási szabályok használatával adatainak beolvasása

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Parancssori felület (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Az Azure figyelő REST API-n](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Riasztási szabályt létrehozni a metrika az Azure portállal
1. Az a [portal](https://portal.azure.com/), keresse meg az erőforrás figyelési érdekli, és válassza ki azt.
2. Ez a lépés nem egyezik az SQL-adatbázis és a rugalmas készletek és SQL DW: 

   - **SQL-adatbázis & csak a rugalmas készletek**: válasszon **riasztások** vagy **riasztási szabályok** a figyelés szakaszban. A szöveg és ikon eltérő lehet attól függően némileg különböző erőforrások.  
   
     ![Figyelés](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButton.png)
  
   - **CSAK SQL DW**: válasszon **figyelés** a GYAKORI feladatok szakaszban. Kattintson a **DWU használati** grafikon.

     ![GYAKORI FELADATOK](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButtonDW.png)

3. Válassza ki a **riasztás hozzáadása** parancsot, és töltse ki a mezőket.
   
    ![Riasztás beállítása](../monitoring-and-diagnostics/media/insights-alerts-portal/AddDBAlertPage.png)
4. **Név** a riasztás szabályt, majd válassza ki a **leírása**, amely értesítési e-mailt is mutatja.
5. Válassza ki a **metrika** szeretne figyelni, majd kattintson egy **feltétel** és **küszöbérték** a mérték értékét. Is választhatja, a **időszak** idő a metrika szabály a riasztási eseményindítók előtt kell biztosítani. Így például, ha az időszakban "PT5M" használja, és 80 % fölötti CPU keresi a riasztás, a riasztás akkor váltja ki mikor a **átlagos** CPU 5 percig 80 % felett volt. Akkor következik be, az első eseményindító, amennyiben azt újra váltja ki, ha a átlagos CPU nem éri el 80 %-kal több mint 5 perc. A CPU-mérési 1 percenként történik. Tekintse át az alábbi táblázatban a windows támogatott indításakor, és az összesítés írja be, hogy minden egyes riasztás használ – nem minden riasztás átlagos érték legyen használva.   
6. Ellenőrizze **E-mail-tulajdonosok...**  Ha azt szeretné, hogy a rendszergazdák és a társadminisztrátorok e-mailben a riasztás aktiválódásakor.
7. Ha azt szeretné, hogy további az e-maileket kap értesítést, a riasztás aktiválódásakor, adja hozzá a a **további rendszergazda email(s)** mező. Több e-mailek külön és pontosvesszővel kell elválasztani -  *email@contoso.com;email2@contoso.com*
8. Be egy érvényes URI-azonosító található a **Webhook** mezőben, ha azt szeretné, hogy a riasztás aktiválódásakor meghívta.
9. Válassza ki **OK** végzett a riasztás létrehozása.   

Néhány percen belül a riasztás aktív, és elindítja a leírt módon.

## <a name="managing-your-alerts"></a>A riasztások kezelése
Miután létrehozott egy riasztást, kijelölheti azt és:

* A metrika küszöbérték és a tényleges értékek az előző nap egy grafikonon megtekintése.
* Szerkesztheti és törölheti azt.
* **Tiltsa le a** vagy **engedélyezése** , ha azt szeretné, ideiglenesen leállítani, vagy folytassa a riasztás-mailjeire.


## <a name="sql-database-alert-values"></a>SQL-adatbázis riasztási értékek

| Erőforrás típusa | Metrika neve | Rövid név | Aggregáció típusa | Minimális riasztási időkerete|
| --- | --- | --- | --- | --- |
| SQL-adatbázis | cpu_percent | Processzorhasználat (%) | Átlag | 5 perc |
| SQL-adatbázis | physical_data_read_percent | Adat IO kihasználtsága (%) | Átlag | 5 perc |
| SQL-adatbázis | log_write_percent | Napló IO százalékos aránya | Átlag | 5 perc |
| SQL-adatbázis | dtu_consumption_percent | DTU-kihasználtság (%) | Átlag | 5 perc |
| SQL-adatbázis | tárterület | Adatbázis teljes mérete | Maximum | 30 perc |
| SQL-adatbázis | connection_successful | Sikeres kapcsolatok | Összes | 10 perc |
| SQL-adatbázis | connection_failed | Sikertelen kapcsolatok | Összes | 10 perc |
| SQL-adatbázis | blocked_by_firewall | Tiltsa le tűzfal | Összes | 10 perc |
| SQL-adatbázis | Holtpont | Holtpontok | Összes | 10 perc |
| SQL-adatbázis | storage_percent | Adatbázis méretének kihasználtsága | Maximum | 30 perc |
| SQL-adatbázis | xtp_storage_percent | A memórián belüli online Tranzakciófeldolgozási tárolási percent(Preview) | Átlag | 5 perc |
| SQL-adatbázis | workers_percent | Feldolgozók százalékos aránya | Átlag | 5 perc |
| SQL-adatbázis | sessions_percent | Munkamenetek százaléka | Átlag | 5 perc |
| SQL-adatbázis | dtu_limit | DTU korlátot | Átlag | 5 perc |
| SQL-adatbázis | dtu_used | Felhasznált DTU | Átlag | 5 perc |
||||||
| Rugalmas készlet | cpu_percent | Processzorhasználat (%) | Átlag | 10 perc |
| Rugalmas készlet | physical_data_read_percent | Adat IO kihasználtsága (%) | Átlag | 10 perc |
| Rugalmas készlet | log_write_percent | Napló IO százalékos aránya | Átlag | 10 perc |
| Rugalmas készlet | dtu_consumption_percent | DTU-kihasználtság (%) | Átlag | 10 perc |
| Rugalmas készlet | storage_percent | Tárolási százalékos aránya | Átlag | 10 perc |
| Rugalmas készlet | workers_percent | Feldolgozók százalékos aránya | Átlag | 10 perc |
| Rugalmas készlet | eDTU_limit | eDTU korlátot | Átlag | 10 perc |
| Rugalmas készlet | storage_limit | Tárolási kapacitása | Átlag | 10 perc |
| Rugalmas készlet | eDTU_used | felhasznált edtu-ra | Átlag | 10 perc |
| Rugalmas készlet | storage_used | Felhasznált tárterület | Átlag | 10 perc |
||||||               
| SQL data warehouse | cpu_percent | Processzorhasználat (%) | Átlag | 10 perc |
| SQL data warehouse | physical_data_read_percent | Adat IO kihasználtsága (%) | Átlag | 10 perc |
| SQL data warehouse | tárterület | Adatbázis teljes mérete | Maximum | 10 perc |
| SQL data warehouse | connection_successful | Sikeres kapcsolatok | Összes | 10 perc |
| SQL data warehouse | connection_failed | Sikertelen kapcsolatok | Összes | 10 perc |
| SQL data warehouse | blocked_by_firewall | Tiltsa le tűzfal | Összes | 10 perc |
| SQL data warehouse | service_level_objective | Szolgáltatási szint célkitűzésének az adatbázis | Összes | 10 perc |
| SQL data warehouse | dwu_limit | dwu korlátot | Maximum | 10 perc |
| SQL data warehouse | dwu_consumption_percent | DWU százalékos aránya | Átlag | 10 perc |
| SQL data warehouse | dwu_used | A DWU használt | Átlag | 10 perc |
||||||


## <a name="next-steps"></a>További lépések
* [Az Azure Figyelés áttekintése](../monitoring-and-diagnostics/monitoring-overview.md) többek között a adattípusok összegyűjtheti, és figyelje.
* További információ [konfigurálása webhookokkal a riasztások](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Első egy [diagnosztikai naplók áttekintése](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) és begyűjtése részletes nagyon gyakori a szolgáltatásban.
* Első egy [metrikák gyűjtemény áttekintése](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) ellenőrizze, hogy a szolgáltatás elérhető, és a gyors.
