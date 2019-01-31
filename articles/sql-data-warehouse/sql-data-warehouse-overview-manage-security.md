---
title: Egy SQL Data warehouse-adatbázis védelme |} A Microsoft Docs
description: Tippek az Azure SQL Data Warehouse-adatbázis védelme használható megoldások fejlesztéséhez.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: c3844d378b44d292b9a7eb631fa896d5f6e61dbe
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472184"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Egy SQL Data warehouse-adatbázis védelme
> [!div class="op_single_selector"]
> * [Biztonsági áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Encryption (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Ez a cikk végigvezeti az Azure SQL Data Warehouse-adatbázis védelmét biztosító alapjait. Ebben az esetben, ebben a cikkben megismerkedhet a hozzáférés korlátozásához szükséges erőforrások adatok védelme és a tevékenységek monitorozásához az adatbázisban.

## <a name="connection-security"></a>Kapcsolatbiztonság
A kapcsolatbiztonság azt jelenti, hogy hogyan korlátozza és védi az adatbázis kapcsolatait a tűzfalszabályok és a csatlakozástitkosítás használatával.

A tűzfalszabályokat a kiszolgáló és az adatbázis is felhasználja, hogy elutasítsa a csatlakozási kísérleteket a nem kifejezetten engedélyezett IP-címekről. Ahhoz, hogy az alkalmazás vagy az ügyfél gépének nyilvános IP-cím érkező kapcsolatokat, először létre kell hoznia egy kiszolgálószintű tűzfalszabályt az Azure Portalon, a REST API vagy a PowerShell használatával. Az ajánlott eljárás a kiszolgáló tűzfalán átengedett IP-címtartományokat lehető legnagyobb mértékű korlátozása.  Azure SQL Data Warehouse eléréséhez a helyi számítógépről, győződjön meg arról, a hálózat és a helyi számítógép tűzfala lehetővé teszi, hogy a kimenő kommunikációt az 1433-as TCP-porton.  

Az SQL Data Warehouse használ a kiszolgálószintű tűzfalszabályokat. Adatbázisszintű tűzfalszabályok nem támogatja. További információkért lásd: [Azure SQL Database-tűzfal][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

Az SQL Data Warehouse kapcsolatokat a rendszer alapértelmezés szerint titkosítja.  Kapcsolati beállítások módosítása letiltja a titkosítást a rendszer figyelmen kívül hagyja.

## <a name="authentication"></a>Hitelesítés
A hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való kapcsolódáskor. Az SQL Data Warehouse jelenleg támogatja az SQL Server-hitelesítést a felhasználónévvel és jelszóval, és az Azure Active Directoryval. 

Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezen hitelesítő adatokkal hitelesítheti magát az adatbázis tulajdonosa vagy a "dbo" SQL Server-hitelesítés segítségével a kiszolgáló bármely olyan adatbázisába.

Azonban ajánlott eljárásként a szervezet felhasználói használjon egy másik fiókot hitelesítéséhez. Ezzel a módszerrel korlátozhatja az alkalmazáshoz rendelt engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát abban az esetben, ha az alkalmazás kódja sebezhető az SQL-injektálásos támadásokkal szemben. 

Csatlakozás az SQL-kiszolgáló hitelesített felhasználó létrehozásához a **fő** adatbázist a kiszolgálón a kiszolgálói rendszergazdai bejelentkezéssel, és hozzon létre egy új kiszolgáló bejelentkezést.  Emellett tanácsos hozzon létre egy felhasználót a master adatbázisban, Azure SQL Data Warehouse számára. Felhasználó létrehozása a főadatbázisban lehetővé teszi, hogy a felhasználót, hogy jelentkezzen be az ssms-ben más eszközök használatával adatbázisnév megadása nélkül.  Azt is lehetővé teszi, hogy azokat az object explorer használatával megtekintheti az összes adatbázis SQL Server-kiszolgálón.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ezután csatlakozhat a **SQL Data Warehouse-adatbázis** a kiszolgálói rendszergazdai bejelentkezéssel, és hozzon létre egy adatbázis-felhasználót a létrehozott kiszolgálói bejelentkezésen alapuló.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Például a bejelentkezések létrehozása vagy új adatbázisok létrehozásához további műveletek végrehajtása felhasználói engedélyt adni, rendelje hozzá a felhasználót a `Loginmanager` és `dbmanager` szerepkörök a master adatbázisban. Ezek a további szerepkörök és a egy SQL Database hitelesítése további információkért lásd: [adatbázisok és bejelentkezések Azure SQL Database kezelése][Managing databases and logins in Azure SQL Database].  További információkért lásd: [SQL Data Warehouse által használatával az Azure Active Directory-hitelesítéssel csatlakozik][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Engedélyezés
Engedélyezési hivatkozik, mi mindent az Azure SQL Data Warehouse-adatbázison belül. Engedélyezési jogosultságok szerepkörtagságai és engedélyei határozza meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. A szerepkörök kezeléséhez használhatja a következő tárolt eljárást:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel.

Többféleképpen korlátozhatja a felhasználók mire használhatják az Azure SQL Data Warehouse:

* A részletes [engedélyek] [ Permissions] lehetővé teszik, hogy mely műveleteket végezheti az egyes oszlopain, tábláin, megtekintheti a vezérlő, sémák, eljárások és más objektumok, az adatbázis. A részletes engedélyeket használ a legtöbb vezérlés, és adja meg a minimálisan szükséges engedélyeket. 
* [Adatbázis-szerepkörök] [ Database roles] eltérő db_datareader és a db_datawritertől is lehet használni, nagyobb teljesítményű alkalmazásfelhasználói fiókokat, illetve kevésbé sokoldalú felügyeleti fiókokat létrehozni. A beépített rögzített adatbázis-szerepkörök engedélyeket egyszerű módot biztosítanak, de eredményezhet, mint amennyi szükséges további engedélyeket.
* [Tárolt eljárások] [ Stored procedures] segítségével korlátozhatja az adatbázisban végezhető műveleteket.

Az alábbi példa egy felhasználó által definiált séma olvasási hozzáférést biztosít.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Adatbázisok és logikai kiszolgálók kezelése az Azure Portalról, vagy az Azure Resource Manager API-val a portál felhasználói fiókjának szerepkör-hozzárendelések vezérli. További információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure Portalon][Role-based access control in Azure portal].

## <a name="encryption"></a>Titkosítás
Az Azure SQL Data Warehouse transzparens adattitkosítási (TDE) segítségével elleni védelemhez kártevő szándékú tevékenységek által az inaktív adatok titkosítása és visszafejtése.  Ha az adatbázis titkosításához azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra titkosítottak az alkalmazások módosítása nélkül. TDE titkosítja a tárolót a teljes adatbázisra az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával. 

Az SQL Database az adatbázis-titkosítási kulcs védi egy beépített kiszolgálói tanúsítványt. A beépített kiszolgálói tanúsítvány egy egyedülálló megoldás minden egyes SQL Database-kiszolgáló. A Microsoft legalább 90 naponként automatikusan elforgatja ezeket a tanúsítványokat. Az SQL Data Warehouse által használt titkosítási algoritmus az AES-256. TDE általános ismertetését lásd: [transzparens adattitkosítás][Transparent Data Encryption].

Az adatbázis használatával titkosíthatja az [az Azure portal] [ Encryption with Portal] vagy [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>További lépések
Részletek és példák az SQL Data Warehouse különböző protokollokkal való csatlakozásról: [csatlakozhat az SQL Data Warehouse][Connect to SQL Data Warehouse].

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
