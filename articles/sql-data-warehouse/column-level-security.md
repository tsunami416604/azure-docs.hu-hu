---
title: Mi az az Azure Szinapszishoz tartozó oszlop szintű biztonság?
description: Az oszlop szintű biztonság lehetővé teszi az ügyfelek számára, hogy az adatbázis-táblázat oszlopaihoz való hozzáférést a felhasználó végrehajtási környezete vagy csoporttagság alapján szabályozzák, leegyszerűsítve az alkalmazás biztonságának megtervezését és kódolását, és lehetővé teszik az oszlopokra vonatkozó korlátozások megvalósítását. hozzáférés.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: aa9791f019436cc5c7effc9bce197d89131a6557
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199971"
---
# <a name="column-level-security"></a>Oszlop szintű biztonság

Az oszlop szintű biztonság lehetővé teszi, hogy az ügyfelek a felhasználó végrehajtási környezete vagy a csoporttagság alapján szabályozzák a Table oszlopokhoz való hozzáférést.


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Mivel ez a videó közzé lett téve, az Azure szinapszis számára elérhetővé vált a [sor szintjének biztonsága](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) . 

Az oszlop szintű biztonság leegyszerűsíti az alkalmazás biztonságának megtervezését és kódolását, ami lehetővé teszi az oszlopokhoz való hozzáférés korlátozását a bizalmas adatok védelme érdekében. Tegyük fel például, hogy egy adott felhasználó csak a saját részlegéhez tartozó táblázat bizonyos oszlopaihoz fér hozzá. A hozzáférés-korlátozási logika az adatbázis-szinten található, nem pedig egy másik alkalmazási szinten lévő adatoktól. Az adatbázis minden alkalommal alkalmazza a hozzáférési korlátozásokat, amikor az adathozzáférés bármely szintjéről megkísérelhető. Ez a korlátozás megbízhatóbb és robusztus biztonságot tesz lehetővé azáltal, hogy csökkenti az általános biztonsági rendszerek felületi területét. Emellett az oszlop szintű biztonság is szükségtelenné teszi a nézetek bevezetését, hogy kiszűrje a hozzáférési korlátozásokat a felhasználók számára.

Az oszlop szintű biztonságot a [Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL-utasítással implementálhatja. Ezzel a mechanizmussal az SQL és a Azure Active Directory (HRE) hitelesítés is támogatott.

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
Az alábbi példa bemutatja, hogyan korlátozható `TestUser` a `Membership` tábla `SSN` oszlopának elérésére:

Hozzon létre `Membership` táblát a társadalombiztosítási számok tárolására szolgáló SSN oszlop használatával:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

A bizalmas adatokkal rendelkező SSN oszlop kivételével az összes oszlop elérésének engedélyezése a `TestUser` számára:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

A `TestUser`ként végrehajtott lekérdezések sikertelenek lesznek, ha tartalmazzák a SSN oszlopot:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Használati esetek

Néhány példa arra, hogyan használják az oszlop szintű biztonságot a mai napon:

- A pénzügyi szolgáltatások vállalata csak a fiókok kezelői számára teszi lehetővé, hogy hozzáférjenek az ügyfél társadalombiztosítási számaihoz (SSN), a telefonszámokhoz és az egyéb személyazonosításra alkalmas adatokhoz.
- Az egészségügyi szolgáltató lehetővé teszi, hogy csak az orvosok és ápolók férhessenek hozzá bizalmas egészségügyi adatokhoz, miközben a számlázási részleg tagjai nem tekintik meg ezeket az adatokat.
