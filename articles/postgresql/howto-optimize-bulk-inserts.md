---
title: Tömeges Beszúrások az Azure Database for PostgreSQL-kiszolgáló optimalizálása
description: Ez a cikk bemutatja, hogyan optimalizálhatók a tömeges beszúrási műveletek az Azure Database for PostgreSQL-kiszolgálóhoz.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 9d2bfcddc649e4fff68bdba49df0945e88067036
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545236"
---
# <a name="optimizing-bulk-inserts-and-use-of-transient-data-on-azure-database-for-postgresql-server"></a>Tömeges beszúrások és átmeneti adatok az Azure Database for PostgreSQL-kiszolgáló használatának optimalizálása 
Ez a cikk bemutatja, hogyan optimalizálhatók a tömeges beszúrási műveletek és az átmeneti adatok egy Azure database for PostgreSQL-kiszolgáló használatát.

## <a name="using-unlogged-tables"></a>Nem naplózott táblák használata
Azon ügyfeleink esetében, amelyek rendelkeznek a munkaterheléssel kapcsolatos műveletek, amely magában foglalja az átmeneti adatok, vagy egyszerre több nagy méretű adatkészletek, amely beszúrása fontolja meg, nem naplózott táblák használatát.

Nem naplózott táblák PostgreSQL funkciója használható hatékonyan tömeges Beszúrások optimalizálása érdekében. PostgreSQL használja írási előre naplózás (WAL), amely alapértelmezés szerint atomitást és az ACID tulajdonságok két tartósságot biztosít. Beszúráskor nem naplózott táblába lenne középérték PostgreSQL Beszúrások, a tranzakciós naplóba való írása nélkül kellene tennie, amely önmagában egy i/o-művelet, jelentősen gyorsabb, mint a szokásos táblák így ezek a táblák.

Az alábbiakban a beállításokat egy nem naplózott tábla létrehozásához:
- Hozzon létre egy új nem naplózott táblát a szintaxis használatával: `CREATE UNLOGGED TABLE <tableName>`
- A konvertálás egy meglévő tábla naplózza a szintaxis nem naplózott táblához: `ALTER <tableName> SET UNLOGGED`.  Ez visszafordítható a szintaxis használatával: `ALTER <tableName> SET LOGGED`

## <a name="unlogged-table-tradeoff"></a>Nem naplózott tábla ára
Nem naplózott táblázatok nem szerepelnek a összeomlási szálbiztos. Egy nem naplózott tábla a rendszer automatikus rendszerösszeomlás után vagy egy tiszta leállítás csonkolja. Egy nem naplózott tábla tartalmát is nem készenléti kiszolgálóra replikálódnak. Létrehozott egy nem naplózott táblán indexekkel, valamint automatikusan nem naplózott.  Az insert művelet befejezése után bármikor áttérhet a táblában, így a Beszúrás tartós naplózza.

Azonban az egyes ügyfél munkaterheléseinek lépett fel körülbelül 15 – 20 %-os teljesítményjavulást nem naplózott táblák használata esetén.

## <a name="next-steps"></a>További lépések
Tekintse át a számítási feladatok esetében az átmeneti adatok és a nagyméretű tömeges beszúrások.  

Tekintse át a következő PostgreSQL dokumentációja – [táblázat SQL-parancsok létrehozása](https://www.postgresql.org/docs/current/static/sql-createtable.html)