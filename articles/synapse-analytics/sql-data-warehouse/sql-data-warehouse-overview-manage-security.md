---
title: Adatbázis biztonságossá tétele
description: Tippek az adatbázis biztonságossá tételéhez és a synapse SQL-készleterőforrás megoldásainak fejlesztéséhez.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 46d32fdca615833bd602480ac182585da898ab98
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586442"
---
# <a name="secure-a-database-in-azure-synapse"></a>Adatbázis biztonságossá tétele az Azure Synapse-ban

> [!div class="op_single_selector"]
> * [Biztonság – áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Titkosítás (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Ez a cikk végigvezeti a Synapse SQL-készlet biztosításának alapjait. Ez a cikk első lépések a hozzáférés korlátozására, az adatok védelmére és az SQL-készlet használatával kiépített adatbázis tevékenységeinek figyelésére szolgáló erőforrásokkal.

## <a name="connection-security"></a>Kapcsolatbiztonság

A kapcsolatbiztonság azt jelenti, hogy hogyan korlátozza és védi az adatbázis kapcsolatait a tűzfalszabályok és a csatlakozástitkosítás használatával.

A tűzfalszabályokat a kiszolgáló és az adatbázis egyaránt használja a kifejezetten nem engedélyezési listán szereplő IP-címekcsatlakozási kísérleteinek elutasítására. Ahhoz, hogy az alkalmazás vagy az ügyfélgép nyilvános IP-címe kapcsolatokat engedélyezze, először létre kell hoznia egy kiszolgálószintű tűzfalszabályt az Azure Portal, a REST API vagy a PowerShell használatával. 

Az ajánlott eljárás a kiszolgáló tűzfalán átengedett IP-címtartományokat lehető legnagyobb mértékű korlátozása.  Az SQL-készlet helyi számítógépről történő eléréséhez győződjön meg arról, hogy a tűzfal a hálózaton és a helyi számítógépen engedélyezi a kimenő kommunikációt az 1433-as TCP-porton.  

Az Azure Synapse Analytics kiszolgálószintű IP-tűzfalszabályokat használ. Nem támogatja az adatbázisszintű IP-tűzfal szabályokat. További információ: [Azure SQL Database firewall rules](../../sql-database/sql-database-firewall-configure.md)

Az SQL-készlethez való csatlakozások alapértelmezés szerint titkosítva vannak.  A rendszer figyelmen kívül hagyja a kapcsolatbeállítások módosítását a titkosítás letiltása érdekében.

## <a name="authentication"></a>Hitelesítés

A hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való kapcsolódáskor. Az SQL-készlet jelenleg támogatja az SQL Server hitelesítést felhasználónévvel és jelszóval, valamint az Azure Active Directoryval. 

Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezekkel a hitelesítő adatokkal hitelesítheti magát a kiszolgáló bármely adatbázisában az adatbázis tulajdonosaként, vagy "dbo" az SQL Server-hitelesítés en keresztül.

Ajánlott eljárásként azonban a szervezet felhasználóinak egy másik fiókot kell használniuk a hitelesítéshez. Így korlátozhatja az alkalmazásnak adott engedélyeket, és csökkentheti a rosszindulatú tevékenységek kockázatát abban az esetben, ha az alkalmazáskód ki van téve egy SQL-injektálási támadásveszélynek. 

SQL Server hitelesített felhasználó létrehozásához csatlakozzon a kiszolgáló **fő** adatbázisához a kiszolgáló rendszergazdájának bejelentkezésével, és hozzon létre egy új kiszolgálóbejelentkezést.  Célszerű egy felhasználót is létrehozni a fő adatbázisban. Ha főnévben hoz létre felhasználót, a felhasználó az Adatbázis nevének megadása nélkül is bejelentkezhet az Olyan eszközökkel, mint az SSMS.  Azt is lehetővé teszi számukra, hogy az objektumkezelő segítségével megtekintsék az összes adatbázist egy SQL-kiszolgálón.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ezután csatlakozzon az **SQL-készlet adatbázisához** a kiszolgáló rendszergazdájának bejelentkezésével, és hozzon létre egy adatbázis-felhasználót a létrehozott kiszolgálóbejelentkezés alapján.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ha engedélyt szeretne adni a felhasználónak további műveletek végrehajtására, például bejelentkezések `Loginmanager` létrehozására vagy új adatbázisok létrehozására, rendelje hozzá a felhasználót a fő adatbázis és `dbmanager` szerepköreihez. 

Ezekről a további szerepkörökről és az SQL-adatbázishitelesítésről az [Adatbázisok és bejelentkezések kezelése az Azure SQL Database-ben](../../sql-database/sql-database-manage-logins.md)című témakörben talál további információt.  Az Azure Active Directory használatával való csatlakozásról a [Csatlakozás az Azure Active Directory-hitelesítés használatával](sql-data-warehouse-authentication.md)című témakörben talál további információt.

## <a name="authorization"></a>Engedélyezés
Az engedélyezés azt a dolgot teszi, amit az adatbázison belül a hitelesítés és a csatlakoztatás után tehet. Az engedélyezési jogosultságokat a szerepkör-tagságok és az engedélyek határozzák meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. A szerepkörök kezeléséhez a következő tárolt eljárásokat használhatja:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel.

A felhasználók az adatbázisban további annekteket tehetnek:

* A részletes [engedélyek](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15) segítségével szabályozhatja, hogy az adatbázis egyes oszlopain, tábláin, nézetein, sémájain, eljárásaiban és egyéb objektumain milyen műveleteket végezhet. A részletes engedélyekkel a legtöbb vezérlést és a szükséges minimális engedélyeket biztosíthatja. 
* A db_datareader és db_datawriter kívüli [adatbázis-szerepkörök](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15) hatékonyabb alkalmazásfelhasználói fiókok vagy kevésbé hatékony felügyeleti fiókok létrehozására használhatók. A beépített rögzített adatbázis-szerepkörök egyszerű módot biztosítanak az engedélyek megadására, de a szükségesnél több engedélyt adhatnak.
* A [tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

A következő példa olvasási hozzáférést biztosít egy felhasználó által definiált sémához.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Az adatbázisok és logikai kiszolgálók kezelése az Azure Portalon, vagy az Azure Resource Manager API-t a portál felhasználói fiók szerepkör-hozzárendelések által szabályozza. További információ: [Szerepköralapú hozzáférés-vezérlés az Azure Portalon.](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure)

## <a name="encryption"></a>Titkosítás
Az átlátszó adattitkosítás (TDE) az adatok titkosításával és visszafejtésével védelmet nyújt a rosszindulatú tevékenységek kel szemben. Az adatbázis titkosításakor a kapcsolódó biztonsági mentések és tranzakciós naplófájlok titkosítása az alkalmazások módosítása nélkül történik. A TDE egy teljes adatbázis tartalmát titkosítja egy szimmetrikus kulcs – az adatbázis-titkosítási kulcs – használatával. 

Az SQL Database rendszerben az adatbázis-titkosítási kulcsot beépített kiszolgálói tanúsítvány védi. A beépített kiszolgálói tanúsítvány minden SQL Database-kiszolgáló esetében egyedi. A Microsoft legalább 90 naponta automatikusan elforgatja ezeket a tanúsítványokat. A használt titkosítási algoritmus AES-256. A TDE általános leírását az [Átlátszó adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15).

Az [Azure Portal vagy](sql-data-warehouse-encryption-tde.md) a [T-SQL](sql-data-warehouse-encryption-tde-tsql.md)használatával titkosíthatja az adatbázist.

## <a name="next-steps"></a>További lépések
A különböző protokollokkal a raktárhoz való csatlakozással kapcsolatos részletekért és példákért a [Csatlakozás az SQL-készlethez](sql-data-warehouse-connect-overview.md)című témakörben talál.
