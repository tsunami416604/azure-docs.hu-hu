---
title: Egy Azure Database for PostgreSQL-kiszolgáló a tömeges Beszúrások optimalizálása
description: Ez a cikk bemutatja, hogyan optimalizálhatók a tömeges beszúrási műveletek egy Azure database for PostgreSQL-kiszolgálóhoz.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: fba109e04369c05f98e863b7dd0fa3d51f40d0ad
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810238"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql-server"></a>Tömeges Beszúrások optimalizálása és a egy Azure Database for PostgreSQL-kiszolgálót átmeneti adatok használata 
Ez a cikk bemutatja, hogyan tömeges beszúrási műveletek optimalizálása és átmeneti adatok használata az Azure Database for PostgreSQL-kiszolgálóhoz.

## <a name="use-unlogged-tables"></a>Nem naplózott táblák használata
Ha rendelkezik, amely magában foglalja az átmeneti adatok, vagy egyszerre több nagy méretű adatkészletek, amely beszúrása munkaterheléssel kapcsolatos műveletek, fontolja meg nem naplózott táblákat.

Nem naplózott táblák PostgreSQL funkciója használható hatékonyan tömeges Beszúrások optimalizálása érdekében. PostgreSQL előre írási naplózás (WAL) használja. Alapértelmezés szerint biztosít atomitást és tartósság érdekében. Atomitást, konzisztencia, elkülönítési és tartóssági alkotják az ACID tulajdonságait. 

A PostgreSQL-nem naplózott táblázat azt jelenti, hogy beszúrása nem Beszúrások be a tranzakció kódírás nélkül jelentkezzen, amely önmagában egy i/o-művelet. Ennek eredményeképpen ezek a táblák olyan jelentősen gyorsabb, mint a szokásos táblákat.

Egy nem naplózott tábla létrehozásához használja a következő beállításokat:
- Nem naplózott új tábla létrehozása a következő szintaxis segítségével `CREATE UNLOGGED TABLE <tableName>`.
- A következő szintaxis segítségével egy meglévő Convert naplózza az nem naplózott táblához tábla `ALTER <tableName> SET UNLOGGED`.  

A folyamat visszafordítása, használja a szintaxist `ALTER <tableName> SET LOGGED`.

## <a name="unlogged-table-tradeoff"></a>Nem naplózott tábla ára
Nem naplózott táblák nem összeomlási szálbiztos. Egy nem naplózott tábla a rendszer automatikus rendszerösszeomlás után vagy egy tiszta leállítás csonkolja. Egy nem naplózott tábla tartalmát is a rendszer nem replikálja készenléti kiszolgáló. Létrehozott egy nem naplózott táblán indexekkel, valamint automatikusan nem naplózott. Művelet befejezése után a Beszúrás, konvertálja a táblában úgy, hogy a Beszúrás tartós naplózza.

Néhány ügyfél munkaterheléseinek körülbelül 15 %-os, 20 százalékos teljesítményjavulást lépett fel nem naplózott táblák használatakor is.

## <a name="next-steps"></a>További lépések
Tekintse át a számítási feladatok esetében az átmeneti adatok és a nagyméretű tömeges beszúrások. A következő PostgreSQL-dokumentációjában talál:
 
- [Hozzon létre a táblát az SQL-parancsok](https://www.postgresql.org/docs/current/static/sql-createtable.html)