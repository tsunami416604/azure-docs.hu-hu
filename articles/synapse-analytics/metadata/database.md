---
title: Az Azure Synapse Analytics megosztott adatbázisa
description: Az Azure Synapse Analytics egy megosztott metaadat-modellt biztosít, ahol az Apache Spark-beli adatbázis létrehozása elérhetővé teszi az SQL on-demand (előzetes verzió) és az SQL-készletmotorok számára.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424145"
---
# <a name="azure-synapse-analytics-shared-database"></a>Az Azure Synapse Analytics megosztott adatbázisa

Az Azure Synapse Analytics lehetővé teszi, hogy a különböző számítási munkaterületi motorok adatbázisokat és táblákat osztanak meg a Spark-készletek (előzetes verzió), az SQL on-demand (előzetes verzió) motor és az SQL-készletek között.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

A Spark-feladattal létrehozott adatbázis ugyanezzel a névvel láthatóvá válik a munkaterület összes jelenlegi és jövőbeli Spark-készleteszámára (előzetes verzió), valamint az SQL on-demand motorszámára.

Ha a munkaterületen vannak olyan SQL-készletek, amelyeken engedélyezve van a metaadat-szinkronizálás, vagy ha új SQL-készletet hoz létre, amelyen engedélyezve van a metaadat-szinkronizálás, akkor ezek a Spark által létrehozott adatbázisok automatikusan speciális sémákba lesznek leképezve az SQL-készlet adatbázisában. 

Minden séma a Spark-adatbázis neve `$` után kapta a nevét egy további előtaggal. A Spark által létrehozott adatbázis külső és felügyelt táblái is külső táblákként vannak elérhetővé téve a megfelelő speciális sémában.

A Spark alapértelmezett `default`adatbázisa , a neve is látható lesz az `default`SQL on-demand környezetben, mint egy adatbázis neve, és bármely SQL `$default`készlet adatbázisok metaadat-szinkronizálás bekapcsolva, mint a séma.

Mivel az adatbázisok at-demand-re szinkronizálódnak az SQL-fájlba, és az SQL-készletek aszinkron módon szinkronizálódnak, késleltetni fogják, amíg meg nem jelennek.

## <a name="manage-a-spark-created-database"></a>Spark által létrehozott adatbázis kezelése

A Spark segítségével kezelheti a Spark által létrehozott adatbázisokat. Például törölje egy Spark-készlet feladat, és hozzon létre táblákat a Sparkból.

Ha objektumokat hoz létre egy Spark által létrehozott adatbázisban az SQL igény szerinti használatával, vagy megpróbálja eldobni az adatbázist, a művelet sikeres lesz. De az eredeti Spark-adatbázis nem módosul.

Ha megpróbálja eldobni a szinkronizált sémát egy SQL-készletbe, vagy megpróbál létrehozni benne egy táblát, az Azure hibát ad vissza.

## <a name="handling-of-name-conflicts"></a>Névütközések kezelése

Ha egy Spark-adatbázis neve ütközik egy meglévő SQL igény szerinti adatbázis nevével, a spark-adatbázishoz igény szerint hozzáfűzi az utótagot az SQL-adatbázishoz. Az SQL on-demand utótagja `_<workspace name>-ondemand-DefaultSparkConnector`.

Ha például egy Spark-adatbázis takarásban `mydb` jön létre `myws` az Azure Synapse-munkaterületen, és már létezik ilyen nevű SQL-adatbázis, akkor `mydb_myws-ondemand-DefaultSparkConnector`az SQL-ben lévő Spark-adatbázisra a név használatával kell hivatkozni.

> [!CAUTION]
> Figyelem: Ne vegyen függőséget ettől a viselkedéstől.

## <a name="security-model"></a>Biztonsági modell

A Spark-adatbázisok és táblák, valamint a szinkronizált ábrázolások az SQL-motorok lesznek biztosítva az alapul szolgáló tárolási szinten.

Az adatbázist létrehozó rendszerbiztonsági tag az adatbázis tulajdonosának minősül, és rendelkezik az adatbázishoz és objektumaihoz való összes jogosultsággal.

Ha egy rendszerbiztonsági tagnak, például felhasználónak vagy biztonsági csoportnak hozzáférést szeretne biztosítani egy adatbázishoz, adja `warehouse` meg a megfelelő POSIX mappa- és fájlengedélyeket a címtárban lévő alapul szolgáló mappákhoz és fájlokhoz. 

Ha például egy rendszerbiztonsági tag egy adatbázisban lévő táblát olvas, a címtárban lévő `X` adatbázismappából induló összes mappának rendelkeznie kell a `R` `warehouse` rendszerbiztonsági taghoz rendelt engedélyekkel és engedélyekkel. Ezenkívül a fájloknak (például a tábla `R` alapjául szolgáló adatfájloknak) engedélyeket kell igényelni. 

Ha egy rendszerbiztonsági tagnak lehetősége van objektumok létrehozására `W` vagy objektumok ledobására az `warehouse` adatbázisban, további engedélyekre van szükség a mappában lévő mappákon és fájlokon.

## <a name="examples"></a>Példák

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Hozzon létre & csatlakozzon a Spark-adatbázishoz – SQL on-demand

Először hozzon létre `mytestdb` egy új Spark-adatbázis nevű egy Spark-fürt már létrehozott a munkaterületen. Ezt például egy Spark C# notebook használatával érheti el a következő .NET for Spark utasítással:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Rövid késleltetés után az sql-ből igény szerint láthatja az adatbázist. Például futtassa a következő utasítást az SQL igény szerinti.

```sql
SELECT * FROM sys.databases;
```

Ellenőrizze, `mytestdb` hogy szerepel-e az eredményekben.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>Spark-adatbázis felfedése SQL-készletben

Az előző példában létrehozott adatbázissal most hozzon létre `mysqlpool` egy SQL-készletet a munkaterületen, amely lehetővé teszi a metaadatok szinkronizálását.

Futtassa a `mysqlpool` következő utasítást az SQL-készleten:

```sql
SELECT * FROM sys.schema;
```

Ellenőrizze az újonnan létrehozott adatbázis sémáját az eredményekközött.

## <a name="next-steps"></a>További lépések

- [További információ az Azure Synapse Analytics megosztott metaadatairól](overview.md)
- [További információ az Azure Synapse Analytics megosztott metaadat-táblázatairól](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->
