---
title: Az Azure szinapszis Analytics megosztott adatbázisa
description: Az Azure szinapszis Analytics olyan megosztott metaadat-modellt biztosít, amelyben az adatbázis létrehozása Apache Spark az SQL igény szerinti (előzetes verzió) és az SQL Pool Engines használatával elérhetővé teszi.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: da1bd9c812c20f60264d1a5ee1f8821128900618
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698851"
---
# <a name="azure-synapse-analytics-shared-database"></a>Az Azure szinapszis Analytics megosztott adatbázisa

Az Azure szinapszis Analytics lehetővé teszi, hogy a különböző számítási munkaterület-hajtóművek megosszák az adatbázisokat és a táblákat a Spark-készletek (előzetes verzió) és az SQL on-demand (előzetes verzió) motorja között.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

A Spark-feladatokkal létrehozott adatbázisok a munkaterületen és az SQL igény szerinti motoron is láthatóak lesznek a jelenlegi és a jövőbeli Spark-készletek (előzetes verzió) nevében.

A meghívott Spark alapértelmezett adatbázis `default` az SQL igény szerinti környezetében is látható lesz, mint egy nevű adatbázis `default` .

Mivel a rendszer aszinkron módon szinkronizálja az adatbázisokat az SQL-ben, a rendszer késést okoz, amíg meg nem jelenik.

## <a name="manage-a-spark-created-database"></a>Spark által létrehozott adatbázis kezelése

A Spark segítségével kezelheti a Spark által létrehozott adatbázisokat. Például törölheti azt egy Spark-készlet feladatokon keresztül, és létrehozhat táblákat a Sparkból.

Ha az SQL on-demand használatával hoz létre objektumokat egy Spark által létrehozott adatbázisban, vagy megpróbálja eldobni az adatbázist, a művelet sikeres lesz. Az eredeti Spark-adatbázis azonban nem módosul.

## <a name="handling-of-name-conflicts"></a>A nevek ütközésének feldolgozása

Ha egy Spark-adatbázis neve ütközik egy meglévő SQL on-demand adatbázis nevével, a rendszer az SQL igény szerinti utótagot adja hozzá a Spark-adatbázishoz. Az SQL igény szerinti utótagja a következő: `_<workspace name>-ondemand-DefaultSparkConnector` .

Ha például egy nevű Spark `mydb` -adatbázist hoz létre az Azure szinapszis munkaterületen, `myws` és már létezik ilyen nevű SQL on-demand adatbázis, akkor az SQL-ben igénybe vehető Spark-adatbázist a név használatával kell hivatkozni `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Vigyázat: ezt a viselkedést nem kell függőséget kihasználnia.

## <a name="security-model"></a>Biztonsági modell

A Spark-adatbázisok és-táblák, valamint az SQL-motorban szinkronizált ábrázolások az alapul szolgáló tárolási szinten lesznek biztosítva.

Az adatbázist létrehozó rendszerbiztonsági tag ennek az adatbázisnak a tulajdonosa, és minden jogosultsággal rendelkezik az adatbázishoz és az objektumaihoz.

Ahhoz, hogy egy rendszerbiztonsági tag, például egy felhasználó vagy egy biztonsági csoport hozzáférhessen egy adatbázishoz, adja meg a megfelelő POSIX-mappát és-engedélyeket a címtárban található alapul szolgáló mappákhoz és fájlokhoz `warehouse` . 

Ahhoz például, hogy egy rendszerbiztonsági tag beolvasson egy táblát egy adatbázisban, a címtár adatbázis mappájától kezdődő összes mappának rendelkeznie kell az `warehouse` `X` `R` adott rendszerbiztonsági tag számára, és hozzá kell rendelnie az engedélyeket. Emellett a fájloknak (például a tábla alapjául szolgáló adatfájloknak) `R` engedélyre van szükségük. 

Ha egy rendszerbiztonsági tag lehetővé teszi objektumok létrehozását vagy objektumok eldobását egy adatbázisban, további `W` engedélyekre van szükség a mappában található mappákban és fájlokban `warehouse` .

## <a name="examples"></a>Példák

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Hozzon létre & csatlakozni a Spark-adatbázishoz – SQL igény szerint

Először hozzon létre egy nevű új Spark `mytestdb` -adatbázist egy olyan Spark-fürt használatával, amelyet már létrehozott a munkaterületen. Ezt például a Spark C# jegyzetfüzet és a következő .NET for Spark-utasítás használatával érheti el:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Rövid késleltetés után megtekintheti az adatbázist az SQL igény szerinti használatával. Futtassa például az alábbi utasítást az SQL igény szerint.

```sql
SELECT * FROM sys.databases;
```

Ellenőrizze, hogy `mytestdb` szerepel-e az eredmények között.

## <a name="next-steps"></a>Következő lépések

- [További információ az Azure szinapszis Analytics megosztott metaadatairól](overview.md)
- [További információ az Azure szinapszis Analytics megosztott metaadatait tartalmazó tábláiról](table.md)
