---
title: Adatbázis biztonságossá tétele
description: Tippek a Azure SQL Data Warehouse-adatbázis biztonságossá tételéhez a megoldások fejlesztéséhez.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5eeb1c25264c36909774ec689b7410765881c8e2
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064733"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Adatbázis biztonságossá tétele SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Biztonsági áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Titkosítás (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Ez a cikk végigvezeti a Azure SQL Data Warehouse-adatbázis biztonságossá tételének alapjain. Ez a cikk a hozzáférés korlátozására, az adatok védelmére és a figyelési tevékenységekre vonatkozó erőforrásokkal való ismerkedést ismerteti.

## <a name="connection-security"></a>Kapcsolatbiztonság
A kapcsolatbiztonság azt jelenti, hogy hogyan korlátozza és védi az adatbázis kapcsolatait a tűzfalszabályok és a csatlakozástitkosítás használatával.

A tűzfalszabályok a-kiszolgáló és az adatbázis használatával is elutasítja a kapcsolódási kísérleteket az olyan IP-címekről, amelyek nincsenek explicit módon engedélyezve. Ha engedélyezni szeretné az alkalmazás vagy az ügyfélszámítógép nyilvános IP-címének kapcsolatait, először létre kell hoznia egy kiszolgálói szintű tűzfalszabály-szabályt a Azure Portal, REST API vagy a PowerShell használatával. 

Az ajánlott eljárás a kiszolgáló tűzfalán átengedett IP-címtartományokat lehető legnagyobb mértékű korlátozása.  A helyi számítógép Azure SQL Data Warehouseának eléréséhez győződjön meg arról, hogy a tűzfal a hálózaton és a helyi számítógépen lehetővé teszi a kimenő kommunikációt a 1433-es TCP-porton.  

Az Azure szinapszis a kiszolgálói szintű IP-tűzfalszabályok használatát alkalmazza. Az adatbázis-szintű IP-tűzfalszabályok nem támogatottak. További információ: [Azure SQL Database tűzfalszabályok](../sql-database/sql-database-firewall-configure.md)

A SQL Data Warehouse létesített kapcsolatok alapértelmezés szerint titkosítva vannak.  A rendszer figyelmen kívül hagyja a kapcsolódási beállítások módosítását a titkosítás letiltásához.

## <a name="authentication"></a>Hitelesítés
A hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való kapcsolódáskor. A SQL Data Warehouse jelenleg a felhasználónévvel és a jelszóval, valamint a Azure Active Directoryával támogatja SQL Server hitelesítés használatát. 

Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezeknek a hitelesítő adatoknak a használatával a hitelesítő adatokat az adott kiszolgálón található adatbázis-tulajdonosként vagy a "dbo" SQL Server hitelesítésen keresztül végezheti el.

Az ajánlott eljárás szerint azonban a szervezet felhasználóinak egy másik fiókot kell használniuk a hitelesítéshez. Így korlátozhatja az alkalmazás számára biztosított engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát abban az esetben, ha az alkalmazás kódja sebezhető az SQL-injektálási támadásokkal szemben. 

SQL Server hitelesített felhasználó létrehozásához kapcsolódjon a kiszolgálón található **Master** adatbázishoz a kiszolgáló-rendszergazdai bejelentkezéssel, és hozzon létre egy új kiszolgálói bejelentkezést.  Érdemes létrehozni egy felhasználót is a Master adatbázisban az Azure szinapszis-felhasználók számára. A felhasználók a Master szolgáltatásban való létrehozása lehetővé teszi, hogy a felhasználó egy adatbázis nevének megadása nélkül jelentkezzen be a SSMS-hez hasonló eszközökkel.  Azt is lehetővé teszi, hogy az Object Explorer használatával megtekintsék az SQL Server összes adatbázisát.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ezután kapcsolódjon a **SQL Data Warehouse-adatbázishoz** a kiszolgáló-rendszergazdai bejelentkezéssel, és hozzon létre egy adatbázis-felhasználót a létrehozott kiszolgálói bejelentkezés alapján.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ahhoz, hogy a felhasználó engedélyt nyújtson további műveletek elvégzésére, például bejelentkezések létrehozására vagy új adatbázisok létrehozására, rendelje hozzá a felhasználót a `Loginmanager` és `dbmanager` szerepkörökhöz a Master adatbázisban. 

További információ ezekről a további szerepkörökről és a SQL Database való hitelesítésről: [adatbázisok és bejelentkezések kezelése Azure SQL Databaseokban](../sql-database/sql-database-manage-logins.md).  A Azure Active Directory használatával való csatlakozással kapcsolatos további információkért lásd: [csatlakozás Azure Active Directory-hitelesítés használatával](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Engedélyezés
Az engedélyezés arra utal, hogy a hitelesítés és a csatlakozás után egy adatbázison belül mit tehet. Az engedélyezési jogosultságokat a szerepkör-tagságok és engedélyek határozzák meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. A szerepkörök kezeléséhez használhatja a következő tárolt eljárásokat:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel.

A felhasználók egy adatraktáron belüli végrehajtásának további korlátozására van lehetőség:

* A részletes [engedélyek](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15) segítségével szabályozhatja, hogy mely műveleteket végezheti el az egyes oszlopokon, táblákon, nézeteken, sémán, eljárásokon és más objektumokon az adatbázisban. Használjon részletes engedélyeket a leghatékonyabb szabályozáshoz, és adja meg a szükséges minimális engedélyeket. 
* A db_datareader és db_datawritertól eltérő [adatbázis-szerepköröket](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15) is felhasználhat nagyobb teljesítményű alkalmazás-felhasználói fiókok vagy kevésbé hatékony felügyeleti fiókok létrehozásához. A beépített rögzített adatbázis-szerepkörök egyszerű módot biztosítanak az engedélyek megadására, de a szükségesnél több engedélyt is biztosítanak.
* A [tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

A következő példa olvasási hozzáférést biztosít egy felhasználó által definiált sémához.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Az adatbázisok és logikai kiszolgálók Azure Portal vagy a Azure Resource Manager API használatával történő kezeléséhez a portál felhasználói fiókjának szerepkör-hozzárendelései vannak vezérelve. További információ: [szerepköralapú hozzáférés-vezérlés Azure Portalban](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure).

## <a name="encryption"></a>Titkosítás
A transzparens adattitkosítás (TDE) segít megvédeni a kártékony tevékenységek fenyegetését azáltal, hogy titkosítja és visszafejti az adatait a nyugalmi állapotban. Az adatbázis titkosításakor a társított biztonsági másolatok és a tranzakciós naplófájlok az alkalmazások módosítása nélkül titkosítva lesznek. A TDE titkosítja egy teljes adatbázis tárterületét az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával. 

SQL Database az adatbázis-titkosítási kulcsot egy beépített kiszolgálótanúsítvány védi. A beépített kiszolgálói tanúsítvány minden SQL Database-kiszolgáló esetében egyedi. A Microsoft automatikusan elforgatja ezeket a tanúsítványokat legalább 90 naponta. A használt titkosítási algoritmus az AES-256. A TDE általános ismertetését lásd: [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15).

Az adatbázis titkosítása a [Azure Portal](sql-data-warehouse-encryption-tde.md) vagy a [T-SQL](sql-data-warehouse-encryption-tde-tsql.md)használatával végezhető el.

## <a name="next-steps"></a>Következő lépések
A különböző protokollokkal történő, a tárházhoz való csatlakozással kapcsolatos részletekért és Példákért lásd: [csatlakozás SQL Data Warehousehoz](sql-data-warehouse-connect-overview.md).
