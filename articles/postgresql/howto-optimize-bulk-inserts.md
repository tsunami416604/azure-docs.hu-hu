---
title: Tömeges beszúrások optimalizálása - Azure Database for PostgreSQL - Single Server
description: Ez a cikk bemutatja, hogyan optimalizálhatja a tömeges beszúrási műveleteket egy Azure Database for PostgreSQL - Single Server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4c4bac16917be0064ebb111328753d378d462a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770135"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Tömeges beszúrások optimalizálása és átmeneti adatok használata egy Azure-adatbázisban a PostgreSQL-hez – Egykiszolgálós 
Ez a cikk ismerteti, hogyan optimalizálhatja a tömeges beszúrási műveleteket, és hogyan használhat átmeneti adatokat egy Azure Database for PostgreSQL-kiszolgálón.

## <a name="use-unlogged-tables"></a>Nem naplózott táblák használata
Ha olyan számítási feladatok vannak, amelyek átmeneti adatokat tartalmaznak, vagy nagy adatkészleteket szúrnak be tömegesen, fontolja meg a nem naplózott táblák használatát.

A nem naplózott táblák egy PostgreSQL funkció, amely hatékonyan használható a tömeges lapkák optimalizálásához. A PostgreSQL a Write-Ahead Logging (WAL) programot használja. Ez biztosítja az atomi és a tartósság, alapértelmezés szerint. Az ACID tulajdonságait az atomiság, a konzisztencia, az izolálás és a tartósság alkotja. 

A nem naplózott táblába való beszúrás azt jelenti, hogy a PostgreSQL anélkül szúr be, hogy beírna a tranzakciós naplóba, amely maga is egy I/O-művelet. Ennek eredményeképpen ezek a táblák lényegesen gyorsabbak, mint a hagyományos táblák.

Az alábbi beállításokkal hozhat létre nem naplózott táblát:
- Hozzon létre egy új, nem `CREATE UNLOGGED TABLE <tableName>`naplózott táblát a szintaxissal.
- Meglévő naplózott tábla átalakítása nem naplózott táblává a szintaxis `ALTER TABLE <tableName> SET UNLOGGED`használatával.  

A folyamat megfordításához használja `ALTER TABLE <tableName> SET LOGGED`a szintaxist.

## <a name="unlogged-table-tradeoff"></a>Nem naplózott tábla-kompromisszum
A nem naplózott asztalok nem biztonságosak. A nem naplózott tábla automatikusan csonkul egy összeomlás után, vagy egy tisztátalan leállás nak van kitéve. A nem naplózott tábla tartalma is nem replikálódik készenléti kiszolgálókra. A nem naplózott táblában létrehozott indexek is automatikusan lekerülnek a naplódból. A beszúrási művelet befejezése után alakítsa át a táblázatot naplózottá, hogy a lapka tartós.

Egyes ügyfél-munkaterhelések körülbelül 15–20 százalékos teljesítményjavulást tapasztaltak a nem naplózott táblák használatakor.

## <a name="next-steps"></a>További lépések
Tekintse át a számítási feladatok átmeneti adatok és nagy tömeges beszúrások. Lásd a következő PostgreSQL dokumentációt:
 
- [Táblázat SQL-parancsainak létrehozása](https://www.postgresql.org/docs/current/static/sql-createtable.html)
