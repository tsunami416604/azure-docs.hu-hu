---
title: Oszlopszintű biztonság
description: Az oszlop szintű biztonság (CLS) lehetővé teszi az ügyfelek számára, hogy a felhasználó végrehajtási környezete vagy a csoporttagság alapján szabályozzák az adatbázis-táblázat oszlopaihoz való hozzáférést. A CLS leegyszerűsíti az alkalmazás biztonságának megtervezését és kódolását. A CLS lehetővé teszi az oszlopok hozzáférésére vonatkozó korlátozások megvalósítását.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 85f705022a0ff5970d30c61206d4f2631254b7ce
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077107"
---
# <a name="column-level-security"></a>Oszlop szintű biztonság
Az oszlop szintű biztonság (CLS) lehetővé teszi az ügyfelek számára, hogy a felhasználó végrehajtási környezete vagy a csoporttagság alapján szabályozzák az adatbázis-táblázat oszlopaihoz való hozzáférést.
Frissítsen az alábbi videóra – mivel ez a videó közzé lett téve a [sor szintjének biztonsága](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) SQL Data Warehouseban is elérhető. 
> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

A CLS leegyszerűsíti az alkalmazás biztonságának megtervezését és kódolását. A CLS lehetővé teszi a bizalmas adatok védelme érdekében az oszlopok hozzáférésére vonatkozó korlátozások megvalósítását. Tegyük fel például, hogy egy adott felhasználó csak a saját részlegéhez tartozó táblázat bizonyos oszlopaihoz fér hozzá. A hozzáférés-korlátozási logika az adatbázis-szinten található, nem pedig egy másik alkalmazási szinten lévő adatoktól. Az adatbázis minden olyan alkalommal alkalmazza a hozzáférési korlátozásokat, amikor az adathozzáférés bármely szintjéről megkísérelhető. Ez a korlátozás a biztonsági rendszerek megbízhatóságát és megbízhatóságát teszi lehetővé a teljes biztonsági rendszerek felületének csökkentésével. Emellett a CLS is szükségtelenné teszi a nézetek bevezetését, hogy kiszűrje a hozzáférési korlátozásokat a felhasználók számára.

A CLS-T a [Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL-utasítással implementálhatja. Ezzel a mechanizmussal az SQL és a Azure Active Directory (HRE) hitelesítés is támogatott.

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
Az alábbi példa bemutatja, hogyan korlátozhatja a "tesztfelhasználó" a "tagság" tábla "SSN" oszlopának elérését:

A "tagság" tábla létrehozása a társadalombiztosítási számok tárolására szolgáló SSN-oszloppal:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

A "tesztfelhasználó" engedélyezése az összes oszlop eléréséhez, kivéve az SSN oszlopot, amely bizalmas adatokat tartalmaz:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

A "tesztfelhasználó" művelettel végrehajtott lekérdezések sikertelenek lesznek, ha tartalmazzák a SSN oszlopot:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Használati példák
Néhány példa arra, hogyan használják a CLS-t a mai napon:
- A pénzügyi szolgáltatások vállalata csak a fiókok kezelői számára teszi lehetővé, hogy hozzáférjenek az ügyfél társadalombiztosítási számaihoz (SSN), a telefonszámokhoz és az egyéb személyazonosításra alkalmas adatokhoz.
- Az állapotfigyelő szolgáltató csak az orvosok és ápolók számára teszi lehetővé, hogy hozzáférjenek a bizalmas orvosi adatokhoz, miközben nem engedélyezik a számlázási részleg tagjai számára az adatok megtekintését.
