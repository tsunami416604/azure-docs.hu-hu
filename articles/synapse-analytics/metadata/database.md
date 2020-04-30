---
title: Az Azure szinapszis Analytics megosztott adatbázisa
description: Az Azure szinapszis Analytics olyan megosztott metaadat-modellt biztosít, amelyben az adatbázis létrehozása Apache Spark az SQL igény szerinti (előzetes verzió) és az SQL Pool Engines használatával elérhetővé teszi.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424145"
---
# <a name="azure-synapse-analytics-shared-database"></a>Az Azure szinapszis Analytics megosztott adatbázisa

Az Azure szinapszis Analytics lehetővé teszi, hogy a különböző számítási munkaterület-hajtóművek adatbázisokat és táblákat osszanak meg a Spark-készletek (előzetes verzió), az SQL on-demand (előzetes verzió) motorja és az SQL-készletek között.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

A Spark-feladatokkal létrehozott adatbázisok a munkaterületen és az SQL igény szerinti motoron is láthatóak lesznek a jelenlegi és a jövőbeli Spark-készletek (előzetes verzió) nevében.

Ha vannak olyan SQL-készletek a munkaterületen, amelyeken engedélyezve van a metaadatok szinkronizálása, vagy ha új SQL-készletet hoz létre, amelyen engedélyezve van a metaadatok szinkronizálása, akkor ezek a Spark által létrehozott adatbázisok automatikusan speciális sémába vannak rendelve az SQL-készlet adatbázisában. 

Minden séma neve a Spark-adatbázis neve után egy további `$` előtaggal. A Spark által generált adatbázisban a külső és a felügyelt táblák is elérhetők külső táblákként a megfelelő speciális sémában.

A meghívott `default`Spark alapértelmezett adatbázis az SQL igény szerinti környezetében is látható lesz, mint egy nevű `default`adatbázis, valamint a metaadatok szinkronizálását végző SQL Pool-adatbázisokban a séma `$default`részeként.

Mivel az adatbázisok szinkronizálva vannak az SQL igény szerinti és az SQL-készletekkel, aszinkron módon, amíg meg nem jelennek.

## <a name="manage-a-spark-created-database"></a>Spark által létrehozott adatbázis kezelése

A Spark segítségével kezelheti a Spark által létrehozott adatbázisokat. Például törölheti azt egy Spark-készlet feladatokon keresztül, és létrehozhat táblákat a Sparkból.

Ha az SQL on-demand használatával hoz létre objektumokat egy Spark által létrehozott adatbázisban, vagy megpróbálja eldobni az adatbázist, a művelet sikeres lesz. Az eredeti Spark-adatbázis azonban nem módosul.

Ha egy SQL-készletben megpróbálja eldobni a szinkronizált sémát, vagy megpróbál létrehozni egy táblát, az Azure hibát jelez.

## <a name="handling-of-name-conflicts"></a>A nevek ütközésének feldolgozása

Ha egy Spark-adatbázis neve ütközik egy meglévő SQL on-demand adatbázis nevével, a rendszer az SQL igény szerinti utótagot adja hozzá a Spark-adatbázishoz. Az SQL igény szerinti utótagja a következő `_<workspace name>-ondemand-DefaultSparkConnector`:.

Ha például egy nevű `mydb` Spark-adatbázist hoz létre az Azure szinapszis munkaterületen `myws` , és már létezik ilyen nevű SQL on-demand adatbázis, akkor az SQL-ben igénybe vehető Spark-adatbázist a név `mydb_myws-ondemand-DefaultSparkConnector`használatával kell hivatkozni.

> [!CAUTION]
> Vigyázat: ezt a viselkedést nem kell függőséget kihasználnia.

## <a name="security-model"></a>Biztonsági modell

A Spark-adatbázisok és-táblák, valamint az SQL-motorokban lévő szinkronizált ábrázolások a mögöttes tárolási szinten lesznek biztosítva.

Az adatbázist létrehozó rendszerbiztonsági tag ennek az adatbázisnak a tulajdonosa, és minden jogosultsággal rendelkezik az adatbázishoz és az objektumaihoz.

Ahhoz, hogy egy rendszerbiztonsági tag, például egy felhasználó vagy egy biztonsági csoport hozzáférhessen egy adatbázishoz, adja meg a megfelelő POSIX-mappát és-engedélyeket a `warehouse` címtárban található alapul szolgáló mappákhoz és fájlokhoz. 

Ahhoz például, hogy egy rendszerbiztonsági tag beolvasson egy táblát egy adatbázisban, a `warehouse` címtár adatbázis mappájától kezdődő összes mappának rendelkeznie `X` kell az adott rendszerbiztonsági tag `R` számára, és hozzá kell rendelnie az engedélyeket. Emellett a fájloknak (például a tábla alapjául szolgáló adatfájloknak) `R` engedélyre van szükségük. 

Ha egy rendszerbiztonsági tag lehetővé teszi objektumok létrehozását vagy objektumok eldobását egy adatbázisban, további `W` engedélyekre van szükség a `warehouse` mappában található mappákban és fájlokban.

## <a name="examples"></a>Példák

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Hozzon létre & csatlakozni a Spark-adatbázishoz – SQL igény szerint

Először hozzon létre egy nevű `mytestdb` új Spark-adatbázist egy olyan Spark-fürt használatával, amelyet már létrehozott a munkaterületen. Ezt például a Spark C# jegyzetfüzet és a következő .NET for Spark-utasítás használatával érheti el:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Rövid késleltetés után megtekintheti az adatbázist az SQL igény szerinti használatával. Futtassa például az alábbi utasítást az SQL igény szerint.

```sql
SELECT * FROM sys.databases;
```

Ellenőrizze, `mytestdb` hogy szerepel-e az eredmények között.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>Spark-adatbázis kimutatása SQL-készletben

Az előző példában létrehozott adatbázissal mostantól létrehozhat egy SQL-készletet a nevű `mysqlpool` munkaterületen, amely lehetővé teszi a metaadatok szinkronizálását.

Futtassa az alábbi utasítást az `mysqlpool` SQL-készleten:

```sql
SELECT * FROM sys.schema;
```

Ellenőrizze az újonnan létrehozott adatbázis sémáját az eredmények között.

## <a name="next-steps"></a>További lépések

- [További információ az Azure szinapszis Analytics megosztott metaadatairól](overview.md)
- [További információ az Azure szinapszis Analytics megosztott metaadatait tartalmazó tábláiról](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->
