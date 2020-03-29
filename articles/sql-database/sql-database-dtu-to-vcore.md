---
title: Migrálás DTU-ról virtuálismag-alapú modellre
description: Áttelepítés a DTU-modellről a virtuálismag-modellre. A virtuális magra való migrálás hasonló a standard és a prémium szintű csomagok közötti frissítéshez vagy visszaminősítéshez.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945402"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Áttelepítés a DTU-alapú modellről a virtuálismag-alapú modellre

## <a name="migrate-a-database"></a>Adatbázisok migrálása

Az adatbázis áttelepítése a DTU-alapú vásárlási modella virtuálismag-alapú vásárlási modell hasonló a frissítés vagy a prémium szintű szolgáltatási szintek közötti frissítés vagy a prémium szintű szolgáltatási szintek a DTU-alapú vásárlási modell.

## <a name="migrate-databases-that-use-geo-replication"></a>Georeplikációt használó adatbázisok áttelepítése

A DTU-alapú modellről a virtuálismag-alapú vásárlási modellre való áttérés hasonló a szabványos és prémium szintű szolgáltatási szintek adatbázisai közötti georeplikációs kapcsolatok frissítéséhez vagy visszaminősítéséhez. Az áttelepítés során nem kell leállítania a georeplikációt, de be kell tartania az alábbi szekvenálási szabályokat:

- A frissítéskor először frissítenie kell a másodlagos adatbázist, majd frissítenie kell az elsődleges adatbázist.
- Visszaminősítéskor fordítsa meg a sorrendet: először az elsődleges adatbázist kell visszaminősíteni, majd a másodlagost.

Ha két rugalmas készlet között georeplikációt használ, azt javasoljuk, hogy az egyik készletet elsődlegesként, a másikat pedig másodlagosként jelölje meg. Ebben az esetben rugalmas készletek áttelepítésekor ugyanazt a szekvenálási útmutatást kell használnia. Azonban ha rugalmas készletek, amelyek tartalmazzák az elsődleges és másodlagos adatbázisok, kezelje a készlet a magasabb kihasználtság elsődleges, és ennek megfelelően kövesse a szekvenálási szabályokat.  

Az alábbi táblázat útmutatást nyújt az egyes áttelepítési forgatókönyvekhez:

|Aktuális szolgáltatási szint|Cél szolgáltatási szint|Áttelepítés típusa|Felhasználói műveletek|
|---|---|---|---|
|Standard|Általános célú|Oldalsó|Bármilyen sorrendben áttelepíthető, de biztosítania kell a megfelelő virtuálismag-méretezést*|
|Prémium|Üzleti kritikus|Oldalsó|Bármilyen sorrendben áttelepíthető, de biztosítania kell a megfelelő virtuálismag-méretezést*|
|Standard|Üzleti kritikus|Frissítés|Először másodlagost kell áttelepítenie.|
|Üzleti kritikus|Standard|Alacsonyabb szintre|Először az elsődlegest kell áttelepíteni|
|Prémium|Általános célú|Alacsonyabb szintre|Először az elsődlegest kell áttelepíteni|
|Általános célú|Prémium|Frissítés|Először másodlagost kell áttelepítenie.|
|Üzleti kritikus|Általános célú|Alacsonyabb szintre|Először az elsődlegest kell áttelepíteni|
|Általános célú|Üzleti kritikus|Frissítés|Először másodlagost kell áttelepítenie.|
||||

\*Ökölszabály ként a standard szinten minden 100 DIT legalább 1 virtuális magot igényel, és a prémium szinten minden 125 DIT legalább 1 virtuális maszükséges. További információt a [virtuálismag-alapú vásárlási modell ben](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model)talál.

## <a name="migrate-failover-groups"></a>Feladatátvételi csoportok áttelepítése

A több adatbázissal rendelkező feladatátvevő csoportok áttelepítése az elsődleges és másodlagos adatbázisok egyéni áttelepítését igényli. E folyamat során ugyanazok a szempontok és szekvenálási szabályok érvényesek. Miután az adatbázisokat virtuálismag-alapú vásárlási modellre konvertálta, a feladatátvételi csoport ugyanazzal a házirend-beállítással marad érvényben.

### <a name="create-a-geo-replication-secondary-database"></a>Georeplikációs másodlagos adatbázis létrehozása

Georeplikációs másodlagos adatbázist (geoszekundert) csak az elsődleges adatbázishoz használt szolgáltatási szint használatával hozhat létre. A magas naplógenerálási sebességű adatbázisok esetében azt javasoljuk, hogy hozza létre a geo-másodlagos azonos számítási mérete, mint az elsődleges.

Ha egy elsődleges adatbázis rugalmas készletében létrehoz egy geomásodlagos beállítást, `maxVCore` győződjön meg arról, hogy a készlet beállítása megegyezik az elsődleges adatbázis számítási méretével. Ha egy másik rugalmas készletben hoz létre egy elsődleges földrajzi másodlagos, azt `maxVCore` javasoljuk, hogy a készletek azonos beállításokkal rendelkeznek.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>DTU-alapú adatbázis konvertálása adatbázismásoló használatával virtuálismag-alapú adatbázissá

A DTU-alapú számítási méretű adatbázisokat korlátozás vagy speciális szekvenálás nélkül másolhatja egy virtuálismag-alapú számítási mérettel rendelkező adatbázisba, feltéve, hogy a célszámítási méret támogatja a forrásadatbázis maximális adatbázisméretét. Az adatbázis-másolat pillanatképet hoz létre az adatokról a másolási művelet kezdő időpontjától kezdve, és nem szinkronizálja az adatokat a forrás és a cél között.

## <a name="next-steps"></a>További lépések

- Az egyes adatbázisokhoz rendelkezésre álló speciális számítási méreteket és tárolási méreteket az [SQL Database virtuálismag-alapú erőforráskorlátok az egyes adatbázisokhoz című témakörben található.](sql-database-vcore-resource-limits-single-databases.md)
- A rugalmas készletekhez rendelkezésre álló speciális számítási méreteket és tárolási méreteket lásd: [SQL Database virtuálismag-alapú erőforráskorlátok rugalmas készletekhez.](sql-database-vcore-resource-limits-elastic-pools.md)
