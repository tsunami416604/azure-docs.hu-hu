---
title: Az Azure SQL Data Warehouse oszlopszintű biztonsági |} A Microsoft Docs
description: Oszlopszintű biztonsági (CLS) lehetővé teszi, hogy az ügyfelek számára, hogy ki férhet hozzá a felhasználó a végrehajtási környezet vagy a csoporttagságuk alapuló tábla oszlopokat. CLS leegyszerűsíti a megtervezését és kódolását az alkalmazás. CLS lehetővé teszi korlátozások megvalósítása az oszlop hozzáférést.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: aa91bd586e064239d0e05c754427947963c9ee3a
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879182"
---
# <a name="column-level-security"></a>Oszlop-biztonság
Oszlopszintű biztonsági (CLS) lehetővé teszi, hogy az ügyfelek számára, hogy ki férhet hozzá a felhasználó a végrehajtási környezet vagy a csoporttagságuk alapuló tábla oszlopokat.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS leegyszerűsíti a megtervezését és kódolását az alkalmazás. CLS lehetővé teszi korlátozások megvalósítása az oszlop hozzáféréssel bizalmas adatok védelme érdekében. Például annak biztosítása, hogy adott felhasználó hozzáférhet-e csak bizonyos egy tábla adott oszlopait tanulóinak tárolására. A hozzáférés korlátozási logika található az adatbázisszinten, hanem helyezkedik el az adatokat egy másik alkalmazás szinten. Az adatbázis a hozzáférési korlátozásokat alkalmazza, minden alkalommal, amikor az egyik csomagunkban kísérel meg, hogy adatelérési. Ez a korlátozás lehetővé teszi a biztonsági rendszer megbízhatóbb és robusztus az átfogó biztonsági rendszer felületének csökkentése révén. Emellett CLS is kiküszöböli a nézetek bemutatása a felhasználók hozzáférési korlátozásokat az oszlopok szűréséhez.

CLS-implementálhatja a [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL-utasítással. Ez a mechanizmus az SQL és az Azure Active Directory (AAD) hitelesítés támogatott.

![CLS](./media/column-level-security/cls.png)

## <a name="syntax"></a>Szintaxis

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Példa
Az alábbi példa bemutatja, hogyan fér hozzá a "SSN" oszlop "Tagsági" tábla "TestUser" korlátozhatja, hogy:

Társadalombiztosítási számot tárolására szolgáló SSN oszlop "Tagsági" tábla létrehozása:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Lehetővé teszi a "TestUser" SSN oszlop, amely bizalmas adatok kivételével az összes oszlop el:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

A lekérdezések végre, mert "TestUser" sikertelen lesz, ha a SSN oszlop tartalmazzák:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Használati példák
Néhány példa, hogyan CLS használják még ma:
- A pénzügyi szolgáltatások vállalkozás áttekinthető egyetlen fiók rendelkezik hozzáféréssel az ügyfél társadalombiztosítási szám (SSN), a telefonszámokat, és az egyéb személyes azonosításra alkalmas adatokat (PII).
- Egészségügyi szolgáltató lehetővé teszi, hogy csak orvosok és nővérek hozzáférjenek a bizalmas orvosi a számlázási részleg tagjai nem téve az adatok megtekintésére.
