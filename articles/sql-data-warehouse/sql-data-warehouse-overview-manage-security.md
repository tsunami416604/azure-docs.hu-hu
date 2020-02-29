---
title: Adatbázis biztonságossá tétele
description: Tippek az adatbázisok biztonságossá tételéhez és az SQL Analytics SQL-készlet erőforrásában lévő megoldások fejlesztéséhez.
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
tags: azure-synapse
ms.openlocfilehash: 89ec405a348e3ace851fd5f5e17283a8036692a5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199410"
---
# <a name="secure-a-database-in-azure-synapse"></a>Adatbázis biztonságossá tétele az Azure Szinapszisban
> [!div class="op_single_selector"]
> * [Biztonsági áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Titkosítás (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Ez a cikk végigvezeti az SQL-készlet SQL-elemzésen belüli biztonságossá tételének alapjain. Ez a cikk bemutatja, hogyan használhatja az erőforrásokat a hozzáférés korlátozásához, az adatok védelméhez és a figyelési tevékenységekhez egy SQL-készlet használatával kiépített adatbázisban.

## <a name="connection-security"></a>Kapcsolatbiztonság
A kapcsolatbiztonság azt jelenti, hogy hogyan korlátozza és védi az adatbázis kapcsolatait a tűzfalszabályok és a csatlakozástitkosítás használatával.

A tűzfalszabályok a-kiszolgáló és az adatbázis használatával is elutasítja a kapcsolódási kísérleteket az olyan IP-címekről, amelyek nincsenek explicit módon engedélyezve. Ha engedélyezni szeretné az alkalmazás vagy az ügyfélszámítógép nyilvános IP-címének kapcsolatait, először létre kell hoznia egy kiszolgálói szintű tűzfalszabály-szabályt a Azure Portal, REST API vagy a PowerShell használatával. 

Az ajánlott eljárás a kiszolgáló tűzfalán átengedett IP-címtartományokat lehető legnagyobb mértékű korlátozása.  Ha a helyi számítógépről szeretné elérni az SQL-készletet, győződjön meg arról, hogy a tűzfal a hálózaton és a helyi számítógépen engedélyezi a kimenő kommunikációt az 1433-as TCP-porton  

Az Azure szinapszis Analytics kiszolgálói szintű IP-tűzfalszabályok használatával működik. Az adatbázis-szintű IP-tűzfalszabályok nem támogatottak. További információ: [Azure SQL Database tűzfalszabályok](../sql-database/sql-database-firewall-configure.md)

Az SQL-készlet kapcsolatai alapértelmezés szerint titkosítva vannak.  A rendszer figyelmen kívül hagyja a kapcsolódási beállítások módosítását a titkosítás letiltásához.

## <a name="authentication"></a>Hitelesítés
A hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való kapcsolódáskor. Az SQL-készlet jelenleg támogatja a felhasználónévvel és jelszóval SQL Server hitelesítést, valamint a Azure Active Directory. 

Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezeknek a hitelesítő adatoknak a használatával a hitelesítő adatokat az adott kiszolgálón található adatbázis-tulajdonosként vagy a "dbo" SQL Server hitelesítésen keresztül végezheti el.

Az ajánlott eljárás szerint azonban a szervezet felhasználóinak egy másik fiókot kell használniuk a hitelesítéshez. Így korlátozhatja az alkalmazás számára biztosított engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát abban az esetben, ha az alkalmazás kódja sebezhető az SQL-injektálási támadásokkal szemben. 

SQL Server hitelesített felhasználó létrehozásához kapcsolódjon a kiszolgálón található **Master** adatbázishoz a kiszolgáló-rendszergazdai bejelentkezéssel, és hozzon létre egy új kiszolgálói bejelentkezést.  Érdemes létrehozni egy felhasználót is a Master adatbázisban. A felhasználók a Master szolgáltatásban való létrehozása lehetővé teszi, hogy a felhasználó egy adatbázis nevének megadása nélkül jelentkezzen be a SSMS-hez hasonló eszközökkel.  Azt is lehetővé teszi, hogy az Object Explorer használatával megtekintsék az SQL Server összes adatbázisát.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ezután kapcsolódjon az **SQL-készlet adatbázisához** a kiszolgáló-rendszergazdai bejelentkezéssel, és hozzon létre egy adatbázis-felhasználót a létrehozott kiszolgálói bejelentkezés alapján.

```sql
-- Connect to the database and create a database user
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

Lehetőség van arra, hogy tovább korlátozza, hogy a felhasználók milyen műveleteket végezhetnek el az adatbázison belül:

* A részletes [engedélyek](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15) segítségével szabályozhatja, hogy mely műveleteket végezheti el az egyes oszlopokon, táblákon, nézeteken, sémán, eljárásokon és más objektumokon az adatbázisban. Használjon részletes engedélyeket a leghatékonyabb szabályozáshoz, és adja meg a szükséges minimális engedélyeket. 
* A db_datareader és db_datawritertól eltérő [adatbázis-szerepköröket](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15) is felhasználhat nagyobb teljesítményű alkalmazás-felhasználói fiókok vagy kevésbé hatékony felügyeleti fiókok létrehozásához. A beépített rögzített adatbázis-szerepkörök egyszerű módot biztosítanak az engedélyek megadására, de a szükségesnél több engedélyt is biztosítanak.
* A [tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

A következő példa olvasási hozzáférést biztosít egy felhasználó által definiált sémához.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Az adatbázisok és logikai kiszolgálók Azure Portal vagy a Azure Resource Manager API használatával történő kezeléséhez a portál felhasználói fiókjának szerepkör-hozzárendelései vannak vezérelve. További információ: [szerepköralapú hozzáférés-vezérlés Azure Portalban](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure).

## <a name="encryption"></a>Encryption
A transzparens adattitkosítás (TDE) segít megvédeni a kártékony tevékenységek fenyegetését azáltal, hogy titkosítja és visszafejti az adatait a nyugalmi állapotban. Az adatbázis titkosításakor a társított biztonsági másolatok és a tranzakciós naplófájlok az alkalmazások módosítása nélkül titkosítva lesznek. A TDE titkosítja egy teljes adatbázis tárterületét az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával. 

SQL Database az adatbázis-titkosítási kulcsot egy beépített kiszolgálótanúsítvány védi. A beépített kiszolgálói tanúsítvány minden SQL Database-kiszolgáló esetében egyedi. A Microsoft automatikusan elforgatja ezeket a tanúsítványokat legalább 90 naponta. A használt titkosítási algoritmus az AES-256. A TDE általános ismertetését lásd: [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15).

Az adatbázis titkosítása a [Azure Portal](sql-data-warehouse-encryption-tde.md) vagy a [T-SQL](sql-data-warehouse-encryption-tde-tsql.md)használatával végezhető el.

## <a name="next-steps"></a>További lépések
További részletek és példák a Warehouse-hoz való csatlakozásra különböző protokollokkal: [Csatlakozás az SQL-készlethez](sql-data-warehouse-connect-overview.md).
