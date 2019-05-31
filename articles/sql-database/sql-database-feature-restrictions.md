---
title: Az Azure SQL Database szolgáltatás korlátozásai |} A Microsoft Docs
description: Az Azure SQL Database szolgáltatás korlátozásai javítja az adatbázis-biztonság korlátozhassa az adatbázisban, hogy a támadók található adatok eléréséhez.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: ac7a074e78def504a10b4daa07971f919f414a88
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259452"
---
# <a name="azure-sql-database-feature-restrictions"></a>Az Azure SQL Database szolgáltatás korlátozásai

Az SQL Server-támadások egyetlen közös forrás webes alkalmazást, hogy az adatbázis keresztül történik, ahol különféle formában előforduló SQL-injektálási támadások glean az adatbázisra vonatkozó információk használhatók.  Ideális esetben alkalmazáskód van fejlesztett, így nem vonatkozik az SQL-injektálás számára.  Azonban nagy-kódbázisaik, beleértve az örökölt és külső kód, az egyik soha nem lehet arról, hogy minden esetben történnek, így SQL-injektálásokat egy tény élet általunk elleni védelem érdekében.  Szolgáltatáskorlátozások célja, hogy egyes formái esetében SQL-injektálás kiszivárgását az adatbázisra vonatkozó információk, még ha az SQL-injektálás sikeres volt.

## <a name="enabling-feature-restrictions"></a>A szolgáltatás korlátozások engedélyezése

A szolgáltatás korlátozások engedélyezése történik használatával a `sp_add_feature_restriction` a következő tárolt eljárást:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Lehet, hogy korlátozott a következő funkciókat:

| Funkció          | Leírás |
|------------------|-------------|
| N'ErrorMessages' | Ha korlátozott, semmilyen felhasználói adatot a hibaüzenet belül maszkolva lesz. Lásd: [hibaüzenetek funkció korlátozása](#error-messages-feature-restriction) |
| N'Waitfor "       | Ha korlátozott, a parancs visszaadja azonnal késedelem nélkül. Lásd: [WAITFOR funkció korlátozása](#waitfor-feature-restriction) |

Értékét `object_class` lehet `N'User'` vagy `N'Role'` jelölésére e `object_name` egy felhasználónevet vagy a szerepkör nevét, az adatbázisban.

Az alábbi példa okoz az összes hibaüzenet a felhasználó `MyUser` való maszkolva lesz:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Szolgáltatáskorlátozások letiltása

Szolgáltatáskorlátozások letiltása történik használatával a `sp_drop_feature_restriction` a következő tárolt eljárást:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

A következő példa letiltja a felhasználó hiba üzenet maszkolási `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Megtekintés a szolgáltatás korlátozásai

A `sys.sql_feature_restrictions` nézet az összes jelenleg definiált szolgáltatáskorlátozásokra megadja az adatbázison. Rendelkezik a következő oszlopokat:

| Oszlop neve | Adattípus | Leírás |
|-------------|-----------|-------------|
| Osztály       | nvarchar(128) | Objektumosztály esetében, amelyekre a korlátozás vonatkozik. |
| objektum      | nvarchar(256) | A korlátozás vonatkozik, amelyhez objektum neve |
| A szolgáltatás     | nvarchar(128) | A szolgáltatás, amely korlátozott |

## <a name="feature-restrictions"></a>A szolgáltatás korlátozásai

### <a name="error-messages-feature-restriction"></a>Hiba üzenetek funkció korlátozása

SQL injektálási egy gyakori támadási módszer az injektovat kód, amely hibát okoz.  A hibaüzenet vizsgálatával egy támadó is meg információt a rendszer további, jobban célzott támadások engedélyezése.  A támadás különösen hasznos lehet, ha az alkalmazás nem jelenik meg a lekérdezés eredményeit, de hibaüzenetek megjelenítése.

Fontolja meg egy webalkalmazást, amely rendelkezik egy kérelem formájában:

```html
http://www.contoso.com/employee.php?id=1
```

Amely végrehajtja a következő adatbázis-lekérdezés:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Ha a függvénynek átadott a `id` cserélje le az adatbázis-lekérdezés a $EmpId másolja a webes alkalmazás kérelem paramétert, a támadó elhelyezheti a következő kérelmet:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

És a következő hiba kellene visszaadnia, lehetővé téve a támadó megtudhatja, az adatbázis neve:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Lehetővé teszi a hibaüzenetek funkcióinak korlátozása az alkalmazás felhasználója az adatbázisban, miután a hibaüzenet, hogy nem az adatbázis belső információt kiszivárgott van maszkolva:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Hasonlóképpen a támadó teheti a következő kérelmet:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

És a következő hiba kellene visszaadnia, lehetővé téve a támadó megtudhatja, az alkalmazott bér:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Az adatbázis termékazonosítóhoz hiba üzenetek funkció korlátozás használata esetén:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR funkció korlátozása

Titkos SQL-injektálás számára akkor, ha egy alkalmazás nem biztosít egy támadó az injektált SQL eredményét, vagy egy hibaüzenet, de a támadó hozhat létre, amelyben feltételes lekérdezés is kikövetkeztetni adatokat az adatbázisból a két feltételes ág más végrehajtási idő igénybe. Összehasonlítja a válaszidőt, a támadó is tudja, melyik ágat végre lett hajtva, és ezáltal ismerje meg, a rendszer adatait. A támadás legegyszerűbb változatát használja a `WAITFOR` bevezetni a késedelmet utasítás.

Fontolja meg egy webalkalmazást, amely rendelkezik egy kérelem formájában:

```html
http://www.contoso.com/employee.php?id=1
```

amely végrehajtja a következő adatbázis-lekérdezés:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Cserélje le az adatbázis-lekérdezés a $EmpId másolja az alkalmazás webes kéréseket, mint az id paraméter átadott érték, ha egy támadó kezdeményezhetik a következő kérelmet:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

És a lekérdezés egy további 5 másodperc állna, ha a `sa` használt fiók. Ha `WAITFOR` korlátozás a szolgáltatás le van tiltva, az adatbázisban a `WAITFOR` utasítás figyelmen kívül, és nem információk kiszivárgott a támadás használatával.