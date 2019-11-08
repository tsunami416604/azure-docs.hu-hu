---
title: Lekérdezés-tároló üzemeltetése
description: Ismerje meg, hogyan működik a lekérdezési tároló a Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: fa60992c85e69143bfd65cc1a1f420ed85c8fd93
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802775"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>A lekérdezési tároló üzemeltetése Azure SQL Database

Az Azure-beli lekérdezési tároló egy teljes körűen felügyelt adatbázis-szolgáltatás, amely folyamatosan gyűjti és részletesen ismerteti az összes lekérdezéssel kapcsolatos korábbi információkat. A lekérdezési tárolót hasonló módon tekintheti meg egy repülőgép repülési adatrögzítője számára, amely jelentősen leegyszerűsíti a lekérdezési teljesítmény hibaelhárítását mind a Felhőbeli, mind a helyszíni ügyfelek számára. Ez a cikk az Azure-beli operációs lekérdezés-tároló konkrét szempontjait ismerteti. Az előre összegyűjtött lekérdezési adatok használatával gyorsan diagnosztizálhatja és megoldhatja a teljesítménnyel kapcsolatos problémákat, így több időt is igénybe vehet a vállalatnál. 

A Query Store-t a 2015-as november óta [világszerte elérhető](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) Azure SQL Database. A lekérdezési tároló a teljesítmény-és hangolási funkciók, például a [SQL Database Advisor és a teljesítmény irányítópultjának](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)alapja. A cikk közzétételének pillanatában a Query Store több mint 200 000 felhasználói adatbázisban fut az Azure-ban, a lekérdezésekkel kapcsolatos információk több hónapig való összegyűjtése megszakítás nélkül.

> [!IMPORTANT]
> A Microsoft az összes Azure SQL Database-adatbázishoz (meglévő és új) tartozó lekérdezési tároló aktiválásának folyamata. 

## <a name="optimal-query-store-configuration"></a>A lekérdezési tároló optimális konfigurációja

Ez a szakasz a lekérdezési tár és a függő szolgáltatások, például a [SQL Database Advisor és a teljesítmény irányítópultjának](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)megbízható működésének biztosítására tervezett optimális konfigurációs beállításokat ismerteti. Az alapértelmezett konfiguráció folyamatos adatgyűjtésre van optimalizálva, amely minimálisan eltelt idő/READ_ONLY állapotú.

| Konfiguráció | Leírás | Alapértelmezett | Megjegyzés |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Megadja a lekérdezési tároló által az ügyfél-adatbázisban elvégezhető adatterületre vonatkozó korlátot. |100 |Új adatbázisokra kényszerítve |
| INTERVAL_LENGTH_MINUTES |Meghatározza azt az időtartományt, amely alatt a rendszer összesíti és megőrzi a lekérdezési csomagok gyűjtési futásidejű statisztikáit. Minden aktív lekérdezési terv legfeljebb egy sorral rendelkezik az ezzel a konfigurációval definiált időszakra vonatkozóan |60 |Új adatbázisokra kényszerítve |
| STALE_QUERY_THRESHOLD_DAYS |Időalapú törlési szabályzat, amely a megőrzött futtatókörnyezeti statisztikák és az inaktív lekérdezések megőrzési idejét vezérli |30 |Új adatbázisok és adatbázisok kikényszerítve az előző alapértelmezett értékkel (367) |
| SIZE_BASED_CLEANUP_MODE |Meghatározza, hogy az automatikus Adattisztítás akkor zajlik-e, amikor a lekérdezési tároló adatmérete közelíti a korlátot |AUTOMATIKUS |Minden adatbázisra kényszerítve |
| QUERY_CAPTURE_MODE |Meghatározza, hogy az összes lekérdezés vagy csak a lekérdezések egy részhalmaza legyen nyomon követve |AUTOMATIKUS |Minden adatbázisra kényszerítve |
| FLUSH_INTERVAL_SECONDS |Azt a maximális időtartamot határozza meg, amely alatt a rendszer a rögzített futtatókörnyezeti statisztikákat a lemezre Ürítés előtt megőrzi a memóriában. |900 |Új adatbázisokra kényszerítve |
|  | | | |

> [!IMPORTANT]
> Ezeket az alapértelmezett beállításokat a rendszer automatikusan alkalmazza a lekérdezés-tároló aktiválásának utolsó szakaszában az összes Azure SQL-adatbázisban (lásd az előző fontos megjegyzést). Ezt követően a Azure SQL Database nem változtatja meg az ügyfelek által beállított konfigurációs értékeket, hacsak nem negatív hatással vannak a lekérdezési tároló elsődleges munkaterhelésére vagy megbízható műveleteire.

Ha az egyéni beállításokat szeretné használni, az [Alter Database with Query Store Options](https://msdn.microsoft.com/library/bb522682.aspx) paranccsal állítsa vissza a konfigurációt az előző állapotba. Tekintse meg [az ajánlott eljárásokat a lekérdezési tárolóval](https://msdn.microsoft.com/library/mt604821.aspx) , hogy megtudja, hogyan döntött a legjobban az optimális konfigurációs paraméterek közül.

## <a name="next-steps"></a>További lépések

[SQL Database teljesítménybeli betekintés](sql-database-performance.md)

## <a name="additional-resources"></a>További források

További információkért tekintse meg a következő cikkeket:

- [Az adatbázishoz tartozó repülési adatrögzítő](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [Teljesítmény figyelése a lekérdezési tároló használatával](https://msdn.microsoft.com/library/dn817826.aspx)
- [Lekérdezéstár – Használati forgatókönyvek](https://msdn.microsoft.com/library/mt614796.aspx)
