---
title: "Az Azure SQL-adatbázisban található Lekérdezéstár működő"
description: "Útmutató a Lekérdezéstár az Azure SQL-adatbázis"
keywords: 
services: sql-database
documentationcenter: 
author: bonova
manager: jhubbard
editor: 
ms.assetid: 0cccf6bd-1327-44f7-a6f9-8eff0c210463
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: sqldb-performance
ms.workload: Inactive
ms.date: 11/08/2016
ms.author: bonova
ms.openlocfilehash: e57f1c51ef5c551f3b2e5d0f0a51a1f462b6c1af
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Az Azure SQL-adatbázisban található Lekérdezéstár működő
A Lekérdezéstár az Azure-ban érhető el egy teljes körűen felügyelt adatbázis-szolgáltatás, amely folyamatosan gyűjti, és minden lekérdezést előzménymodell részletes adatait jeleníti meg. Információ a Lekérdezéstárról egy repülőgép felé továbbított adatok író, amely jelentősen leegyszerűsíti a lekérdezési teljesítmény hibaelhárítási mind a felhőalapú és helyszíni felhasználók hasonló, gondolja. Ez a cikk ismerteti a Lekérdezéstár az Azure-ban működő adott aspektusait. Ez előtti gyűjtött adatait használja, gyorsan diagnosztizálhatja és teljesítménybeli problémák megoldásához és így az üzleti összpontosító több időt tölt. 

A Lekérdezéstár le lett [világszerte elérhető](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) az Azure SQL Database, 2015. November óta. A Lekérdezéstár képezi a Teljesítményelemzés és a szolgáltatások, például a hangolása alapját [SQL Database Advisor és teljesítmény irányítópult](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Ez a cikk közzétételének időpontjában a Lekérdezéstár fut a több mint 200 000 felhasználói adatbázisokat az Azure több hónapig megszakítás nélkül lekérdezéssel kapcsolatos adatok gyűjtése.

> [!IMPORTANT]
> A Microsoft a Lekérdezéstár az összes Azure SQL-adatbázisok (meglévő és új) aktiválása folyamatban van. 
> 
> 

## <a name="optimal-query-store-configuration"></a>A Lekérdezéstár optimális konfiguráció
Ez a szakasz ismerteti, amelyek működése megbízható a Lekérdezéstár és a függő szolgáltatások, például a optimális konfigurációs alapértelmezéseinek [SQL Database Advisor és teljesítmény irányítópult](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Alapértelmezett konfiguráció folyamatos az adatgyűjtés, a minimális OFF/READ_ONLY állapotban töltött ideje van optimalizálva.

| Konfiguráció | Leírás | Alapértelmezett | Megjegyzés |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Megadja a Lekérdezéstár is igénybe vehet az ügyféladatbázis belüli adatok terület korlátja |100 |Az új adatbázisokat kényszerítése |
| AZ INTERVAL_LENGTH_MINUTES |Határozza meg az időtartományt, amely során összegyűjtött futásidejű statisztikája lekérdezésterveket összesített értéket és megőrzött méretét. Minden aktív lekérdezésterv legfeljebb egy sor rendelkezik ezzel a konfigurációval meghatározott ideig |60 |Az új adatbázisokat kényszerítése |
| AZ STALE_QUERY_THRESHOLD_DAYS |A megőrzési időtartam megőrzött futásidejű statisztikák és inaktív lekérdezések vezérlő időalapú karbantartása házirend |30 |Az új adatbázisokat és adatbázisokat az előző alapértelmezetten (367) érvényes |
| A SIZE_BASED_CLEANUP_MODE BEÁLLÍTÁST |Megadja, hogy automatikus adatok karbantartása kerül sor a Lekérdezéstár adatok mérete megközelíti a korlátot |AUTOMATIKUS |Az összes adatbázisra érvényes |
| A QUERY_CAPTURE_MODE BEÁLLÍTÁST |Megadja, hogy lekérdezések csak egy részhalmazát vagy az összes lekérdezés követi |AUTOMATIKUS |Az összes adatbázisra érvényes |
| FLUSH_INTERVAL_SECONDS |Megadja, hogy rögzíteni futásidejű statisztikák lemezre kiürítése előtt a memóriában, mindig maximális időtartama |900 |Az új adatbázisokat kényszerítése |
|  | | | |

> [!IMPORTANT]
> A Lekérdezéstár aktiválási minden Azure SQL-adatbázisban (lásd az előző fontos megjegyzés) utolsó szakasza automatikusan alkalmazza ezeket az alapértelmezett értékeket. A ritka be, miután az Azure SQL Database nem módosítják, az ügyfelek által beállított konfigurációs értékeket kivéve, ha negatív hatással elsődleges munkaterhelésére vagy a Lekérdezéstár megbízható működésére.
> 
> 

Ha azt szeretné, hogy az egyéni beállításokkal maradjanak, használjon [ALTER DATABASE a Lekérdezéstár beállításokkal](https://msdn.microsoft.com/library/bb522682.aspx) konfigurációját, és a korábbi állapotának visszaállításához. Tekintse meg [gyakorlati tanácsok a Lekérdezéstár rendelkező](https://msdn.microsoft.com/library/mt604821.aspx) ahhoz, hogy megtudhatja, hogyan felső döntött, hogy optimális konfigurációs paramétereket.

## <a name="next-steps"></a>Következő lépések
[SQL adatbázis elemzéséhez](sql-database-performance.md)

## <a name="additional-resources"></a>További források
A további információkat kivétel a következő cikkeket:

* [A felhőszolgáltató közötti átviteléhez rögzítő az adatbázis számára](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 
* [A Lekérdezéstár használatával teljesítményének figyelése](https://msdn.microsoft.com/library/dn817826.aspx)
* [A Lekérdezéstár használati forgatókönyvek](https://msdn.microsoft.com/library/mt614796.aspx)
* [A Lekérdezéstár használatával teljesítményének figyelése](https://msdn.microsoft.com/library/dn817826.aspx) 

