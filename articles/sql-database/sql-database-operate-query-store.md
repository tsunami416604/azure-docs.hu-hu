---
title: Az Azure SQL Database Query Store működtetése
description: Ismerje meg, hogyan működnek a Query Store az Azure SQL Database-ben
services: sql-database
author: bonova
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: bonova
ms.openlocfilehash: 37cb77b6738ba1354034dcf77d22a19b96c4ef23
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903098"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Az Azure SQL Database-ben a Query Store működtetése
Query Store az Azure-ban olyan teljes körűen felügyelt adatbázis-szolgáltatás, amely folyamatosan gyűjti, és az összes lekérdezés részletes történelmi adatait jeleníti meg. Egy repülőgép adatok eseményrögzítő, amely jelentősen leegyszerűsíti a lekérdezési teljesítmény hibaelhárítása, mind a felhőbeli és helyszíni ügyfelek hasonlíthatók Query Store is gondolja. Ez a cikk ismerteti az Azure-ban a Query Store működtetése adott aspektusait. Ez előtti gyűjtött adatait használja, gyorsan diagnosztizálhatja és teljesítménybeli problémák megoldásához és így több időt üzleti kellene összpontosítani. 

Query Store lett [globálisan elérhető](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) az Azure SQL Database 2015 November óta. Query Store szolgáltatás teljesítményét elemző- és finomhangolási funkciókhoz, mint például az alapja [az SQL Database Advisor és a teljesítmény irányítópult](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Ez a cikk közzététel időpontjában Query Store fut a több mint 200 000 felhasználói adatbázisok az Azure-ban több hónapig megszakítás nélkül lekérdezéssel kapcsolatos információk összegyűjtése.

> [!IMPORTANT]
> A Microsoft az összes Azure SQL-adatbázis (meglévő vagy új) Query Store aktiválása folyamatban van. 
> 
> 

## <a name="optimal-query-store-configuration"></a>Optimális Query Store konfiguráció
Ez a szakasz ismerteti, amely megbízható működését a Query Store és a függő szolgáltatások, például biztosítja, hogy optimális konfigurációt alapértelmezett [az SQL Database Advisor és a teljesítmény irányítópult](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Alapértelmezett konfiguráció folyamatos az adatgyűjtés, az OFF/READ_ONLY Államokban minimális idő van optimalizálva.

| Konfiguráció | Leírás | Alapértelmezett | Megjegyzés |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Adja meg a korlátot, az adatok terület, amely a Query Store is igénybe vehet, az ügyfél-adatbázisban |100 |Az új adatbázisokat kényszerítése |
| INTERVAL_LENGTH_MINUTES |Időtartomány, mely során összegyűjtött futásidejű statisztikája lekérdezési tervek összesített és megőrzött mérete határozza meg. Minden aktív lekérdezésterv legfeljebb egy sor egy ebben a konfigurációban meghatározott ideig rendelkezik. |60 |Az új adatbázisokat kényszerítése |
| STALE_QUERY_THRESHOLD_DAYS |Időalapú törlési szabályzattal, amely szabályozza a megőrzött futásidejének statisztikai adatait és inaktív lekérdezések megőrzési időtartama |30 |Új adatbázisokat és adatbázisok korábbi alapértelmezett (367) kényszerítése |
| SIZE_BASED_CLEANUP_MODE |Itt adhatja meg, hogy automatikus adatok törlése kerül sor, amikor Query Store adatok mérete megközelíti a korlátot |AUTOMATIKUS |Az összes adatbázis által kényszerített |
| QUERY_CAPTURE_MODE |Itt adhatja meg, akár az összes lekérdezés vagy a lekérdezések csak egy részhalmazát nyomon követi |AUTOMATIKUS |Az összes adatbázis által kényszerített |
| FLUSH_INTERVAL_SECONDS |Megadja a maximális időtartamot, amelynek során rögzített statisztikákat előtt lemezre írja a memóriában, tartják modul |900 |Az új adatbázisokat kényszerítése |
|  | | | |

> [!IMPORTANT]
> Ezeket az alapértelmezett értékeket a rendszer automatikusan alkalmazza az utolsó fáziséit Query Store aktiválás az Azure SQL-adatbázisok (lásd az előző fontos megjegyzést). Után fel a világos Azure SQL Database nem lehet módosítása ügyfelei által beállított konfigurációs értékeket, kivéve, ha negatív hatással elsődleges számítási feladat vagy a Query Store a megbízható műveleteket.
> 
> 

Ha azt szeretné, az egyéni beállításokkal rendelkező marad, [ALTER DATABASE Query Store beállításokkal](https://msdn.microsoft.com/library/bb522682.aspx) konfigurációs a korábbi állapotára visszaállítani. Tekintse meg [ajánlott eljárásokat a Query Store](https://msdn.microsoft.com/library/mt604821.aspx) annak érdekében, hogy ismerje meg, hogyan a felső választott optimális konfigurációs paramétereket.

## <a name="next-steps"></a>További lépések
[Az SQL Database Terheléselemző](sql-database-performance.md)

## <a name="additional-resources"></a>További források
További információkért tekintse meg a következő cikkeket:

* [Az adatbázishoz tartozó adatok eseményrögzítő](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 
* [Teljesítmény figyelése a Query Store használatával](https://msdn.microsoft.com/library/dn817826.aspx)
* [Query Store használati forgatókönyvek](https://msdn.microsoft.com/library/mt614796.aspx)
 

