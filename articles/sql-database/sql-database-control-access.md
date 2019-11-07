---
title: Hozzáférés biztosítása Azure SQL Database és SQL Data Warehouse
description: További információ a Microsoft Azure SQL Database és SQL Data Warehouse elérésének biztosításáról.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 05/08/2019
ms.openlocfilehash: c115cd7e4d531bfdc7ddbacd4f6eff2a892ea3c3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690756"
---
# <a name="azure-sql-database-and-sql-data-warehouse-access-control"></a>Azure SQL Database és SQL Data Warehouse hozzáférés-vezérlés

A biztonság biztosításához az Azure [SQL Database](sql-database-technical-overview.md) és a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) hozzáférés-vezérlést biztosít az IP-címekhez való kapcsolódást korlátozó tűzfalszabályok, hitelesítési mechanizmusok, amelyek megkövetelik a felhasználókat, hogy igazolják identitásukat és engedélyezési mechanizmusaikat a felhasználók meghatározott műveletekre és adatértékekre való korlátozása. 

> [!IMPORTANT]
> Az SQL Database biztonsági szolgáltatásairól [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít további információkat. Oktatóanyagért lásd: [a Azure SQL Database biztonságossá tétele](sql-database-security-tutorial.md). A SQL Data Warehouse biztonsági funkcióinak áttekintését lásd: [SQL Data Warehouse biztonsági áttekintés](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)

## <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok

A Microsoft Azure SQL Database egy relációs adatbázis-szolgáltatást nyújt az Azure és egyéb internetalapú alkalmazások számára. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján. További információkért lásd: [Az Azure SQL Database-tűzfalszabályok áttekintése](sql-database-firewall-configure.md).

Az Azure SQL Database szolgáltatás kizárólag a 1433-as TCP-porton keresztül érhető el. Ha a helyi számítógépről szeretné elérni az SQL Database-t, akkor ellenőrizze, hogy az ügyfélszámítógépen lévő tűzfal engedélyezi-e a kimenő kommunikációt az 1433-as TCP-porton keresztül. Ha más alkalmazásoknak nincs szüksége rá, akkor blokkolja az 1433-as TCP-port bejövő kapcsolatait. 

A kapcsolódási folyamat részeként az Azure virtuális gépektől érkező kapcsolatok az egyes feldolgozói szerepkörök egyedi IP-címeire és portjaira lesznek átirányítva. A portszám a 11000-től 11999-ig terjedő tartományban található. A TCP-portokkal kapcsolatos további információkért lásd: [1433-nál nagyobb portok a ADO.NET 4,5 és az SQL Adatbázis2](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Authentication

Az SQL Database két hitelesítési típust támogat:

- **SQL-hitelesítés**:

  Ez a hitelesítési módszer egy felhasználónevet és egy jelszót használ. Amikor létrehozta a SQL Database-kiszolgálót az adatbázishoz, a "kiszolgálói rendszergazda" bejelentkezési azonosítót adta meg felhasználónévvel és jelszóval. Ezen hitelesítő adatokkal hitelesítheti magát a kiszolgáló minden adatbázisában az adatbázis tulajdonosaként („dbo”). 
- **Azure Active Directory hitelesítés**:

  Ez a hitelesítési módszer Azure Active Directory által felügyelt identitásokat használ, és felügyelt és integrált tartományok esetén támogatott. Ha Azure Active Directory-alapú hitelesítést kíván használni, létre kell hoznia egy másik kiszolgálói rendszergazdát „Azure AD admin” névvel, amely engedélyekkel rendelkezik az Azure AD-felhasználók és -csoportok felügyeletéhez. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja. A [Csatlakozás az SQL Database-hez Azure Active Directory-alapú hitelesítéssel](sql-database-aad-authentication.md) című cikk bemutatja, hogyan hozhat létre Azure AD-rendszergazdát az Azure Active Directory-alapú hitelesítés engedélyezéséhez.

Az adatbázismotor bontja a 30 percnél tovább tétlen kapcsolatokat. A kapcsolat használata újbóli bejelentkezés után folytatható. A folyamatosan aktív SQL Database-kapcsolatokat legalább 10 óránként újból hitelesíteni kell (ezt a feladatot az adatbázismotor végzi el). Az adatbázismotor az eredetileg megadott jelszóval kísérli meg az újrahitelesítést, felhasználói beavatkozásra nincs szükség. A SQL Database a jelszó alaphelyzetbe állításakor a rendszer nem hitelesíti újra a kapcsolódást, még akkor sem, ha a Kapcsolódás a kapcsolatok készletezése miatt megtörténik. Ez különbözik a helyszíni SQL Server működésétől. Ha a jelszó a kapcsolat első hitelesítése óta megváltozott, akkor a kapcsolatot bontani kell, és új kapcsolatot kell létrehozni új jelszóval. A `KILL DATABASE CONNECTION` engedéllyel rendelkező felhasználók a [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql) paranccsal közvetlenül bonthatnak egy SQL Database-kapcsolatot.

A felhasználói fiókok a master adatbázisban hozhatók létre, így a kiszolgáló összes adatbázisához engedélyeket kaphatnak, de létrehozhatók magában az adatbázisban is (ezeket tartalmazott felhasználóknak nevezzük). A bejelentkezések létrehozásával és kezelésével kapcsolatos információért lásd: [Bejelentkezések kezelése](sql-database-manage-logins.md). A hordozhatóság és a méretezhetőség érdekében a foglalt adatbázisok használhatók. A tartalmazott felhasználókról további információ a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), a [CREATE USER-rel (Transact-SQL utasítás)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) és a [tartalmazott adatbázisokkal](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) foglalkozó cikkekben található.

Az ajánlott eljárás az, ha az alkalmazás egy dedikált fiókot használ a hitelesítéshez. Ezzel korlátozhatja az alkalmazáshoz rendelt engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát abban az esetben, ha az alkalmazás kódja sebezhető az SQL-injektálásos támadásokkal szemben. Az ajánlott módszer a [tartalmazott adatbázis-felhasználó](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) létrehozása, amellyel az alkalmazás közvetlenül kapcsolódhat az adatbázishoz. 

## <a name="authorization"></a>Engedélyezés

Az engedélyezés az Azure SQL-adatbázisokban a felhasználók által végrehajtható műveletek körét jelenti, amelyeket a felhasználóifiók-adatbázis [szerepkörtagságai](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) és [objektumszintű engedélyei](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) határoznak meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel. További információk: [Bejelentkezések kezelése](sql-database-manage-logins.md).

A `master` adatbázishoz általában csak a rendszergazdáknak kell hozzáférniük. Az egyes felhasználói adatbázisokhoz való rutinszerű hozzáférést az egyes adatbázisokban létrehozott nem rendszergazdai tartalmazottadatbázis-felhasználókon keresztül végezni. Tartalmazottadatbázis-felhasználók használata esetén nem szükséges bejelentkezési adatokat létrehozni a `master` adatbázisban. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) cikkben talál.

Érdemes megismerkednie az alábbi funkciókkal, amelyek az engedélyek korlátozására vagy a szint emelésére is használhatók:

- A [megszemélyesítés](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) és a [modulaláírás](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) az engedélyek biztonságos átmeneti növelésére használható.
- A [sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) használatával korlátozhatja, hogy a felhasználó mely sorokhoz férhessen hozzá.
- Az [adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) használatával korlátozhatja a bizalmas adatok megjelenítését.
- A [tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

## <a name="next-steps"></a>További lépések

- Az SQL Database biztonsági szolgáltatásairól [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít további információkat.
- További információ a tűzfalszabályok használatáról: [Tűzfalszabályok](sql-database-firewall-configure.md).
- Felhasználókkal és bejelentkezésekkel kapcsolatos információk: [Bejelentkezések kezelése](sql-database-manage-logins.md). 
- A proaktív figyeléssel kapcsolatban lásd: adatbázis- [naplózás](sql-database-auditing.md) és [SQL Database veszélyforrások észlelése](sql-database-threat-detection.md).
- Oktatóanyagért lásd: [a Azure SQL Database biztonságossá tétele](sql-database-security-tutorial.md).
