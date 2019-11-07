---
title: Azure SQL Database szolgáltatásra vonatkozó korlátozások
description: Azure SQL Database a szolgáltatásra vonatkozó korlátozások javítják az adatbázis biztonságát azáltal, hogy korlátozza az adatbázis azon funkcióit, amelyeket a támadók a bennük lévő információk elérésére használhatnak.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: e9518065b2240d72698ed75f2fa8a7aed343b7bf
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690060"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL Database szolgáltatásra vonatkozó korlátozások

Az SQL Server támadások egyik gyakori forrása a webalkalmazások, amelyek hozzáférnek az adatbázishoz, ahol az SQL-injektálás különböző formáit használják az adatbázissal kapcsolatos információk begyűjtése céljából.  Ideális esetben az alkalmazás kódja úgy van kialakítva, hogy ne engedélyezze az SQL-injektálást.  Az örökölt és a külső kódokat tartalmazó nagyméretű kódokban azonban soha nem biztos benne, hogy az összes esetet megoldották, így az SQL-injektálások valójában az életnek, amelyet védeni kell.  A szolgáltatásokra vonatkozó korlátozások célja, hogy megakadályozza az SQL-injektálás bizonyos formáinak az adatbázisból való szivárgását, még akkor is, ha az SQL-injektálás sikeres.

## <a name="enabling-feature-restrictions"></a>A szolgáltatásra vonatkozó korlátozások engedélyezése

A szolgáltatás korlátozásának engedélyezése a `sp_add_feature_restriction` tárolt eljárással végezhető el az alábbiak szerint:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

A következő funkciók korlátozhatók:

| Szolgáltatás          | Leírás |
|------------------|-------------|
| N'ErrorMessages' | Ha korlátozva van, a rendszer a hibaüzenetben lévő összes felhasználói adattal maszkot fog tartalmazni. Lásd a [hibaüzenetek szolgáltatás korlátozását](#error-messages-feature-restriction) |
| N'Waitfor'       | Ha korlátozott, a parancs késedelem nélkül vissza fog térni. Lásd: [waitfor szolgáltatás korlátozása](#waitfor-feature-restriction) |

A `object_class` értéke lehet `N'User'` vagy `N'Role'`, amely azt jelzi, hogy a `object_name` Felhasználónév vagy szerepkör neve szerepel-e az adatbázisban.

A következő példa hatására a rendszer az összes hibaüzenetet a felhasználói `MyUser` maszkolásához:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Szolgáltatási korlátozások letiltása

A szolgáltatás korlátozásait a `sp_drop_feature_restriction` tárolt eljárással teheti meg a következő módon:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

A következő példa letiltja a felhasználói `MyUser`a hibaüzenetek maszkolását:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>A szolgáltatásra vonatkozó korlátozások megtekintése

A `sys.sql_feature_restrictions` nézet az adatbázis jelenleg definiált szolgáltatásainak korlátozásait mutatja be. A következő oszlopokból áll:

| Oszlop neve | Data type | Leírás |
|-------------|-----------|-------------|
| Osztály       | nvarchar (128) | Azon objektum osztálya, amelyre a korlátozás vonatkozik |
| objektum      | nvarchar (256) | Annak az objektumnak a neve, amelyre a korlátozás vonatkozik |
| Vonás     | nvarchar (128) | Korlátozott funkció |

## <a name="feature-restrictions"></a>Szolgáltatásokra vonatkozó korlátozások

### <a name="error-messages-feature-restriction"></a>Hibaüzenetek funkciójának korlátozása

Egy gyakori SQL-befecskendezéses támadási módszer egy olyan kód behelyezése, amely hibát okoz.  A hibaüzenet vizsgálatával a támadó információt kaphat a rendszerről, és további célzottabb támadásokat tesz lehetővé.  Ez a támadás különösen hasznos lehet, ha az alkalmazás nem jeleníti meg a lekérdezés eredményét, de hibaüzeneteket jelenít meg.

Vegyünk egy olyan webalkalmazást, amely a következő formában rendelkezik kéréssel:

```html
http://www.contoso.com/employee.php?id=1
```

a következő adatbázis-lekérdezést hajtja végre:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Ha a webalkalmazás-kérelemre `id` paraméterként átadott értéket másolja a rendszer az adatbázis-lekérdezésben szereplő $EmpId helyére, a támadó a következő kérelmet teheti:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

A következő hibaüzenetet adja vissza, amely lehetővé teszi, hogy a támadó megismerje az adatbázis nevét:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Miután engedélyezte a hibaüzenetek szolgáltatás korlátozását az alkalmazás felhasználója számára az adatbázisban, a visszaadott hibaüzenet maszkolása úgy történik, hogy az adatbázisra vonatkozó belső információk ne legyenek kiszivárgott:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Ehhez hasonlóan a támadó a következő kérést is elvégezheti:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

A következő hibaüzenetet adja vissza, így a támadó megtudhatja az alkalmazott fizetését:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

A hibaüzenetek szolgáltatás korlátozásával az adatbázis visszatér:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR szolgáltatás korlátozása

A vak SQL-injektálás akkor történik, ha egy alkalmazás nem biztosít támadót a befecskendezett SQL vagy hibaüzenet eredményeivel, de a támadó adatokat tud kikövetkeztetni az adatbázisból egy feltételes lekérdezés létrehozásával, amelyben a két feltételes ág végezze el a végrehajtás különböző időtartamát. A válaszadási idő összehasonlításával a támadó megtudhatja, hogy melyik ág lett végrehajtva, és így a rendszerrel kapcsolatos információkat is megismerheti. A támadás legegyszerűbb változata a `WAITFOR` utasítást használja a késleltetés bevezetéséhez.

Vegyünk egy olyan webalkalmazást, amely a következő formában rendelkezik kéréssel:

```html
http://www.contoso.com/employee.php?id=1
```

a következő adatbázis-lekérdezést hajtja végre:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Ha a webalkalmazási kérelmekhez azonosító paraméterként átadott értéket másolja a rendszer az adatbázis-lekérdezésben szereplő $EmpId helyére, a támadó a következő kérést hajthatja végre:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

Ha a `sa` fiókot használták, a lekérdezés további 5 másodpercet is igénybe vesz. Ha `WAITFOR` szolgáltatás korlátozása le van tiltva az adatbázisban, a rendszer figyelmen kívül hagyja a `WAITFOR` utasítást, és a támadás használatával nem szivárog az információ.