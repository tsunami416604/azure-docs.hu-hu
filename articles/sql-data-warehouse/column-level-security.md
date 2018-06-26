---
title: Az Azure SQL Data Warehouse oszlopszintű biztonsági |} Microsoft Docs
description: Oszlopszintű biztonsági (CLS) lehetővé teszi az ügyfelek adatbázis tábla oszlopait a felhasználó a végrehajtási környezet vagy a csoporttagság alapján való hozzáférés vezérlése érdekében. CLS egyszerűbbé teszi a terv és az alkalmazás biztonsági kódolása. CLS korlátozások megvalósítása oszlop hozzáférést teszi lehetővé.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 06/15/2018
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: 5a916132f705f3c517ee6789b61a3972b2445b62
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938423"
---
# <a name="column-level-security"></a>Oszlop-biztonság 
Oszlopszintű biztonsági (CLS) lehetővé teszi az ügyfelek adatbázis tábla oszlopait a felhasználó a végrehajtási környezet vagy a csoporttagság alapján való hozzáférés vezérlése érdekében.  

CLS egyszerűbbé teszi a terv és az alkalmazás biztonsági kódolása. CLS lehetővé teszi a bizalmas adatok védelmének oszlop hozzáférési korlátozások megvalósítása. Például győződjön meg arról, hogy adott felhasználó hozzáférhet-e a megfelelő, az osztálynak egy tábla csak bizonyos oszlopok. A hozzáférés korlátozási logika található az adatbázis-rétegből, hanem helyezkedik el az adatokat egy másik alkalmazás réteg. Az adatbázis a hozzáférési korlátozásokat alkalmazza, minden alkalommal, amikor az adott adatok próbál meg elérni a réteg alapján. Így a biztonsági rendszer megbízhatóbb és robusztusabbá alkalmazását az átfogó biztonsági rendszer felületének csökkentése révén. Ezenkívül ez is szükségtelenné teszi a nézetek bemutatása a felhasználók hozzáférési korlátozásokat az oszlopok szűréséhez. 

Sikerült valósítja meg a CLS a [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL-utasításban. A mechanizmus SQL, mind az Azure Active Directory (AAD) hitelesítési támogatottak.

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
A következő példa bemutatja, hogyan tesztfelhasználó "néven" "Tagsági" tábla "SSN" oszlopában hozzáférését korlátozza: 

Hozzon létre "Tagsági" tábla SSN oszlop társadalombiztosítási számot tárolására:

```sql
CREATE TABLE Membership   
  (MemberID int IDENTITY,   
   FirstName varchar(100) NULL,   
   SSN char(9) NOT NULL, 
   LastName varchar(100) NOT NULL,   
   Phone varchar(12) NULL,   
   Email varchar(100) NULL);  
```

Lehetővé teszi a tesztfelhasználó "néven" SSN oszlop bizalmas adatokat tartalmazó kivételével az összes oszlop eléréséhez: 

```sql  
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;   
``` 

A lekérdezések végre, mert a tesztfelhasználó "néven" sikertelen lesz, ha a társadalombiztosítási szám oszlop tartoznak:

```sql  
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'. 
``` 

## <a name="use-cases"></a>Használati esetek
Néhány példa a CLS használatának ma: 
- A pénzügyi szolgáltatásokat vállalkozás lehetővé teszi, hogy egyetlen fiók kezelők hozzáférjenek a felhasználói azonosítókat (SSN), a telefonszámokat és a más személyes azonosításra alkalmas adatokat (PII).
- Egy egészségügyi szolgáltató lehetővé teszi, hogy csak orvosi és ápolók hozzáférjenek a bizalmas orvosi rögzíti a számlázási osztály tagjai nem téve az adatok megtekintéséhez.
