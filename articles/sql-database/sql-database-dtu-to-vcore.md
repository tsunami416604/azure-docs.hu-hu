---
title: Migrálás a DTU-ből a virtuális mag-be
description: Migrálás a DTU modellből a virtuális mag modellbe. A virtuális mag-re való Migrálás hasonló a standard és a prémium szint közötti verziófrissítéshez vagy visszalépéshez.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/08/2019
ms.openlocfilehash: f34439b7750ca1858e71d4a36121eb65001fff50
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811273"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrálás a DTU-alapú modellből a virtuális mag-alapú modellbe

## <a name="migrate-a-database"></a>Adatbázisok migrálása

A DTU-alapú vásárlási modellből a virtuális mag-alapú vásárlási modellbe való Migrálás hasonló a standard és prémium szintű szolgáltatási szintek a DTU-alapú vásárlási modellben való frissítéséhez vagy visszalépéséhez.

## <a name="migrate-databases-that-use-geo-replication"></a>Geo-replikálást használó adatbázisok migrálása

A DTU-alapú modellről a virtuális mag-alapú vásárlási modellre való Migrálás hasonló a standard és prémium szintű szolgáltatási szinten lévő adatbázisok közötti földrajzi replikálási kapcsolatok frissítéséhez vagy visszaminősítéséhez. Az áttelepítés során nem kell leállítania a Geo-replikálást, de a következő sorrendi szabályokat kell követnie:

- A frissítéskor először frissítenie kell a másodlagos adatbázist, majd frissítenie kell az elsődlegest.
- A visszalépéskor a sorrend megfordítása: először az elsődleges adatbázist kell visszaadnia, majd vissza kell állítani a másodlagosat.

Ha a Geo-replikációt két rugalmas készlet között használja, javasoljuk, hogy jelöljön ki egy készletet elsődlegesként, a másikat pedig másodlagosként. Ebben az esetben, ha rugalmas készleteket telepít át, ugyanazt a sorrendi útmutatást kell használnia. Ha azonban olyan rugalmas készletekkel rendelkezik, amelyek elsődleges és másodlagos adatbázisokat is tartalmaznak, akkor a készletet a magasabb kihasználtsággal kell kezelni elsődlegesként, és ennek megfelelően kövesse az előkészítési szabályokat.  

Az alábbi táblázat a speciális áttelepítési forgatókönyvekhez nyújt útmutatást:

|Aktuális szolgáltatási szintek|Cél szolgáltatási szintje|Áttelepítés típusa|Felhasználói műveletek|
|---|---|---|---|
|Standard|Általános célú|Oldalirányú|Az áttelepíthető bármilyen sorrendben, de biztosítani kell a megfelelő virtuális mag-méretezést *|
|Prémium|Üzletileg kritikus|Oldalirányú|Az áttelepíthető bármilyen sorrendben, de biztosítani kell a megfelelő virtuális mag-méretezést *|
|Standard|Üzletileg kritikus|Frissítés|Először át kell telepítenie a másodlagost|
|Üzletileg kritikus|Standard|Alacsonyabb szintre|Először át kell telepítenie az elsődlegest|
|Prémium|Általános célú|Alacsonyabb szintre|Először át kell telepítenie az elsődlegest|
|Általános célú|Prémium|Frissítés|Először át kell telepítenie a másodlagost|
|Üzletileg kritikus|Általános célú|Alacsonyabb szintre|Először át kell telepítenie az elsődlegest|
|Általános célú|Üzletileg kritikus|Frissítés|Először át kell telepítenie a másodlagost|
||||

\* a standard szinten minden 100 DTU legalább 1 virtuális mag kell lennie, és a prémium szintű 125 DTU legalább 1 virtuális mag kell lennie.

## <a name="migrate-failover-groups"></a>Feladatátvételi csoportok áttelepíthetők

A több adatbázissal rendelkező feladatátvételi csoportok áttelepítése az elsődleges és a másodlagos adatbázisok egyedi áttelepítését igényli. A folyamat során ugyanazok a megfontolások és az előkészítési szabályok érvényesek. Miután az adatbázisok át lettek konvertálva a virtuális mag-alapú vásárlási modellre, a feladatátvételi csoport ugyanazokkal a házirend-beállításokkal marad érvényben.

### <a name="create-a-geo-replication-secondary-database"></a>Geo-replikációs másodlagos adatbázis létrehozása

A Geo-replikációs másodlagos adatbázist (a Geo-másodlagost) csak az elsődleges adatbázishoz használt szolgáltatási réteg használatával lehet létrehozni. A nagy log-generálási arányú adatbázisok esetében javasoljuk, hogy a Geo-másodlagost ugyanazzal a számítási mérettel hozza létre, mint az elsődleges.

Ha egyetlen elsődleges adatbázishoz hoz létre egy geo-másodlagost a rugalmas készletben, akkor győződjön meg arról, hogy a készlet `maxVCore` beállítása megfelel az elsődleges adatbázis számítási méretének. Ha egy másik rugalmas készletben elsődlegesként hoz létre egy geo-másodlagost, azt javasoljuk, hogy a készletek azonos `maxVCore` beállításokkal rendelkezzenek.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Adatbázis-másolat használata DTU-alapú adatbázis virtuális mag-alapú adatbázisba való átalakításához

A DTU-alapú számítási mérettel rendelkező adatbázisok bármely adatbázisba másolhatók egy virtuális mag-alapú számítási mérettel korlátozás vagy speciális sorrendbe rendezés nélkül, feltéve, hogy a célként megadott számítási méret támogatja a forrásadatbázis maximális adatbázis-méretét. Az adatbázis-másolat létrehoz egy pillanatképet az adatokról a másolási művelet kezdési időpontjáról, és nem szinkronizálja az adatokat a forrás és a cél között.

## <a name="next-steps"></a>További lépések

- Az önálló adatbázisok számára elérhető számítási méretek és a tárolási méretek tekintetében lásd: [SQL Database virtuális mag-alapú erőforrás-korlátok az önálló adatbázisokhoz](sql-database-vcore-resource-limits-single-databases.md).
- A rugalmas készletekhez rendelkezésre álló számítási méretek és a tárhelyek méretére vonatkozó választási lehetőségekért tekintse meg a [rugalmas készletek SQL Database virtuális mag-alapú erőforrás-korlátozásait](sql-database-vcore-resource-limits-elastic-pools.md).
