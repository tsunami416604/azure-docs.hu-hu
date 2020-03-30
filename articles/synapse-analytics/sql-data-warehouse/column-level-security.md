---
title: Mi az Azure Synapse oszlopszintű biztonsága?
description: Az Oszlopszintű biztonság lehetővé teszi az ügyfelek számára, hogy a felhasználó végrehajtási környezete vagy csoporttagsága alapján szabályozzák az adatbázistábla-oszlopokhoz való hozzáférést, egyszerűsítve az alkalmazás biztonságának tervezését és kódolását, és lehetővé teszi az oszlopra vonatkozó korlátozások végrehajtását Hozzáférés.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 24ead458232b096a5c69ffe8b45c6298a9da9f75
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349091"
---
# <a name="column-level-security"></a>Oszlopszintű biztonság

Az Oszlopszintű biztonság lehetővé teszi az ügyfelek számára a táblaoszlopokhoz való hozzáférést a felhasználó végrehajtási környezete vagy csoporttagsága alapján.


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Mivel ez a videó lett kiküldött [sor szintű biztonság](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) elérhetővé vált az Azure Synapse. 

Az oszlopszintű biztonság leegyszerűsíti az alkalmazás biztonságának tervezését és kódolását, lehetővé téve az oszlopokhoz való hozzáférés korlátozását a bizalmas adatok védelme érdekében. Például annak biztosítása, hogy bizonyos felhasználók csak a részlegükhöz tartozó tábla bizonyos oszlopait férhessenek hozzá. A hozzáférés-korlátozási logika az adatbázisrétegben található, nem pedig egy másik alkalmazásréteg adataitól. Az adatbázis minden alkalommal alkalmazza a hozzáférési korlátozásokat, amikor bármely rétegről adatelérést kísérelnek meg. Ez a korlátozás megbízhatóbbá és robusztusabbá teszi a biztonságot azáltal, hogy csökkenti az általános biztonsági rendszer felületét. Emellett az oszlopszintű biztonság is szükségtelenné teszi a nézetek bevezetését, hogy kiszűrje az oszlopokat a felhasználók hozzáférési korlátozásának bevezetéséhez.

A [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL utasítással oszlopszintű biztonságot valósíthat meg. Ezzel a mechanizmussal az SQL és az Azure Active Directory (AAD) hitelesítés e támogatott.

![Cls](./media/column-level-security/cls.png)

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
A következő példa bemutatja, hogyan `SSN` korlátozható `Membership` `TestUser` a tábla oszlopához való hozzáférés korlátozása:

Hozzon létre `Membership` táblázatot a társadalombiztosítási számok tárolására használt SSN oszloppal:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Az `TestUser` összes oszlop elérésének engedélyezése, kivéve az SSN oszlopot, amely a bizalmas adatokat tartalmaz:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

A végrehajtott `TestUser` lekérdezések sikertelenek lesznek, ha tartalmazzák az SSN oszlopot:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Használati esetek

Néhány példa arra, hogyan használják ma az oszlopszintű biztonságot:

- A pénzügyi szolgáltató cég csak a számlakezelők számára teszi lehetővé, hogy hozzáférjenek az ügyfél társadalombiztosítási számaihoz (SSN), telefonszámokhoz és egyéb személyazonosításra alkalmas adatokhoz.
- Az egészségügyi szolgáltató lehetővé teszi, hogy csak az orvosok és ápolók férhetnek hozzá az érzékeny orvosi feljegyzések, miközben megakadályozza tagjai a számlázási osztály megtekintéséhez ezeket az adatokat.
