---
title: "Az SQL Data Warehouse adatbázis védelme |} Microsoft Docs"
description: "Tippek az Azure SQL Data Warehouse adatbázis védelme adattárházzal történő, megoldások."
services: sql-data-warehouse
documentationcenter: NA
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 8fa2f5ca-4cf5-4418-99a2-4dc745799850
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 12/14/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: efc0ca9b156bd69a39197d40083830c6c7e77647
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Az SQL Data Warehouse adatbázis védelme
> [!div class="op_single_selector"]
> * [Biztonság – áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Encryption (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Ez a cikk végigvezeti az Azure SQL Data Warehouse-adatbázis védelme alapjait. Ebben az esetben, ez a cikk lekérdezi használatba erőforrásaival korlátozza a hozzáférést, az adatok védelme és figyelési tevékenységek egy adatbázison.

## <a name="connection-security"></a>Kapcsolatbiztonság
A kapcsolatbiztonság azt jelenti, hogy hogyan korlátozza és védi az adatbázis kapcsolatait a tűzfalszabályok és a csatlakozástitkosítás használatával.

A tűzfalszabályokat a kiszolgáló és az adatbázis is felhasználja, hogy elutasítsa a csatlakozási kísérleteket a nem kifejezetten engedélyezett IP-címekről. Az alkalmazás ügyfél gép nyilvános IP- címének kapcsolódásának engedélyezéséhez először létre kell hoznia egy kiszolgálószintű tűzfalszabályt, az Azure-portálon, a REST API-t vagy a PowerShell használatával. Az ajánlott eljárás a kiszolgáló tűzfalán átengedett IP-címtartományokat lehető legnagyobb mértékű korlátozása.  Az Azure SQL Data Warehouse hozzáférést a helyi számítógépen, győződjön meg arról, a hálózat és a helyi számítógépen a tűzfal lehetővé teszi, hogy az 1433-as TCP-port kimenő kommunikációra.  

Az SQL Data Warehouse kiszolgálószintű tűzfal-szabályokat használ. Adatbázis-szintű tűzfalszabályok nem támogatja. További információkért lásd: [Azure SQL Database-tűzfal][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

Az SQL Data warehouse kapcsolatokat a rendszer alapértelmezés szerint titkosítja.  Tiltsa le a titkosítást a kapcsolódási beállítások módosítása a rendszer figyelmen kívül hagyja.

## <a name="authentication"></a>Hitelesítés
A hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való kapcsolódáskor. Az SQL Data Warehouse jelenleg támogatja az SQL Server-hitelesítés egy felhasználónévvel és jelszóval, és az Azure Active Directoryban. 

Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezeket a hitelesítő adatokat használ, elvégezheti a hitelesítést bármely adatbázis a kiszolgálón az adatbázis tulajdonosa vagy a "dbo" SQL Server-hitelesítésen keresztül.

Azonban célszerű, a szervezet felhasználói fiók használata ajánlott a különböző hitelesítéséhez. Így korlátozhatja az alkalmazás számára megadott engedélyeket és a kártékony tevékenység a kockázatok csökkentése, abban az esetben, ha az alkalmazás kódjában téve egy SQL injektálási támadásnak. 

Csatlakozás SQL-kiszolgáló hitelesített felhasználó létrehozásához a **fő** adatbázis a kiszolgálón a kiszolgáló-rendszergazdai bejelentkezés, és hozzon létre egy új bejelentkezés a kiszolgálóra.  Ezenkívül célszerű egy felhasználó létrehozása az Azure SQL Data Warehouse-felhasználók számára a fő adatbázist. A felhasználó létrehozása a fő lehetővé teszi, hogy a felhasználót, hogy jelentkezzen be például az SSMS használatával adatbázis nevének megadása nélkül.  Emellett lehetővé teszi az object explorer segítségével megtekintheti az összes adatbázis SQL-kiszolgálón.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Majd kapcsolódjon a **SQL Data Warehouse-adatbázis** az a kiszolgáló-rendszergazdai bejelentkezés, és hozzon létre egy adatbázis-felhasználó alapján létrehozott bejelentkezés a kiszolgálóra.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Egy felhasználónak engedélyt például bejelentkezések létrehozása vagy új adatbázisok létrehozásához további műveletek végrehajtásához, rendelje hozzá a felhasználót, hogy a `Loginmanager` és `dbmanager` szerepkörök a főadatbázisban. Ezek a további szerepkörök és SQL-adatbázishoz hitelesítésével kapcsolatos további információkért lásd: [adatbázisok és az Azure SQL Database bejelentkezések kezelése][Managing databases and logins in Azure SQL Database].  További információkért lásd: [kapcsolódás az SQL Data Warehouse által használata Azure Active Directory-hitelesítéssel][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Engedélyezés
Engedélyezési hivatkozik, mi mindent az Azure SQL Data Warehouse-adatbázison belül. Engedélyezési jogosultságokkal szerepkörtagságok és engedélyek határozzák meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. Szerepkörök kezelése, a következő tárolt eljárásokat használhatja:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel.

Tovább korlátozhatja a felhasználók mit tehetnek az Azure SQL Data Warehouse szolgáltatással módja van:

* A részletes [engedélyek] [ Permissions] lehetővé teszik, hogy mely műveletek, akkor az egyes oszlopok, táblák, megtekinti vezérlő, sémák, eljárások és egyéb objektumok az adatbázisban. A részletes engedélyeket használ a legtöbb vezérlő, majd adja meg a minimálisan szükséges engedélyeket. 
* [Adatbázis-szerepkörök] [ Database roles] eltérő db_datareader és db_datawriter nagyobb teljesítményű alkalmazás felhasználói fiókok vagy kevésbé hatékony felügyeleti fiókok létrehozására használható. A beépített rögzített adatbázis-szerepkörök olyan engedélyek megadásához könnyű megoldást biztosítson, de a szükségesnél több jogosultságokat eredményezhet.
* [Tárolt eljárások] [ Stored procedures] segítségével korlátozhatja a műveleteket, amelyen átvihető az adatbázishoz.

A következő példa egy felhasználó által definiált séma olvasási hozzáférést biztosít.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Az Azure portálról adatbázisok és a logikai kiszolgáló kezelése vagy az Azure Resource Manager API-val a portál felhasználói fiók szerepkör-hozzárendelések vezérli. További információkért lásd: [Azure portál szerepköralapú hozzáférés-vezérlés][Role-based access control in Azure portal].

## <a name="encryption"></a>Titkosítás
Az Azure SQL Data adatraktár átlátszó adatok titkosítás (TDE) abban a fenyegetés kártevő szándékú tevékenységek által az inaktív adatok titkosítása és visszafejtése.  Az adatbázis titkosításakor társított biztonsági másolatok és a tranzakciós naplófájlok vannak titkosítva az alkalmazások módosítása nélkül. TDE teljes adatbázis tárterülete az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával titkosítja. 

Az SQL-adatbázis az adatbázis-titkosítási kulcs védi egy beépített kiszolgálói tanúsítványt. A beépített kiszolgálói tanúsítványa nem egyedi a következő SQL-adatbázis kiszolgálónként. Microsoft legalább 90 naponta automatikusan elforgatja ezeket a tanúsítványokat. Az SQL Data Warehouse által használt titkosítási algoritmus az AES-256. TDE általános ismertetését lásd: [átlátható adattitkosítási][Transparent Data Encryption].

Az adatbázis használatával titkosíthatja az [Azure-portálon] [ Encryption with Portal] vagy [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>További lépések
Részletek és csatlakozás az SQL Data Warehouse a különböző protokollokkal példák: [csatlakozás az SQL Data Warehouse][Connect to SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
