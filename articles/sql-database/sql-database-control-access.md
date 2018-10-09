---
title: Hozzáférés engedélyezése az Azure SQL Database és az SQL Data Warehouse |} A Microsoft Docs
description: A Microsoft Azure SQL Database és SQL Data warehouse-ba való hozzáférésének engedélyezésére vonatkozó tudnivalók.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 08854c2f31d86eefa1645269f47bb88659d7e1cb
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868887"
---
# <a name="azure-sql-database-and-sql-data-warehouse-access-control"></a>Az Azure SQL Database és az SQL Data Warehouse hozzáférés-vezérlés

Biztonság, az Azure [SQL Database](sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) hozzáférést tűzfalszabályokkal IP-címe, igazolja, hogy a felhasználók hitelesítési mechanizmusokkal rendelkező azok identitáskezelési és engedélyezési mechanizmusokkal korlátozza a felhasználók számára bizonyos műveletek és adatok. 

> [!IMPORTANT]
> Az SQL Database biztonsági szolgáltatásairól [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít további információkat. Foglalkozó oktatóanyagért lásd: [biztonságossá tétele az Azure SQL Database](sql-database-security-tutorial.md). Az SQL Data Warehouse biztonsági szolgáltatások áttekintését lásd: [SQL Data Warehouse biztonsági áttekintése](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)

## <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok

A Microsoft Azure SQL Database egy relációs adatbázis-szolgáltatást nyújt az Azure és egyéb internetalapú alkalmazások számára. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján. További információkért lásd: [Az Azure SQL Database-tűzfalszabályok áttekintése](sql-database-firewall-configure.md).

Az Azure SQL Database szolgáltatás kizárólag a 1433-as TCP-porton keresztül érhető el. Ha a helyi számítógépről szeretné elérni az SQL Database-t, akkor ellenőrizze, hogy az ügyfélszámítógépen lévő tűzfal engedélyezi-e a kimenő kommunikációt az 1433-as TCP-porton keresztül. Ha más alkalmazásoknak nincs szüksége rá, akkor blokkolja az 1433-as TCP-port bejövő kapcsolatait. 

A kapcsolódási folyamat részeként az Azure virtuális gépektől érkező kapcsolatok az egyes feldolgozói szerepkörök egyedi IP-címeire és portjaira lesznek átirányítva. A portszám a 11000-től 11999-ig terjedő tartományban található. TCP-portokkal kapcsolatos további információkért lásd: [ADO.NET 4.5 és a 2. a SQL adatbázis 1433-Ason túli](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Hitelesítés

Az SQL Database két hitelesítési típust támogat:

- **SQL-hitelesítés**:

  Ezt a hitelesítési módszert használ, egy felhasználónevet és jelszót. Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezen hitelesítő adatokkal hitelesítheti magát a kiszolgáló minden adatbázisában az adatbázis tulajdonosaként („dbo”). 
- **Az Azure Active Directory-hitelesítés**:

  Ez a hitelesítési módszer az Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok támogatott. [Amikor csak lehet](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), használja az Active Directory-hitelesítést (beépített biztonság). Ha Azure Active Directory-alapú hitelesítést kíván használni, létre kell hoznia egy másik kiszolgálói rendszergazdát „Azure AD admin” névvel, amely engedélyekkel rendelkezik az Azure AD-felhasználók és -csoportok felügyeletéhez. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja. A [Csatlakozás az SQL Database-hez Azure Active Directory-alapú hitelesítéssel](sql-database-aad-authentication.md) című cikk bemutatja, hogyan hozhat létre Azure AD-rendszergazdát az Azure Active Directory-alapú hitelesítés engedélyezéséhez.

Az adatbázismotor bontja a 30 percnél tovább tétlen kapcsolatokat. A kapcsolat használata újbóli bejelentkezés után folytatható. A folyamatosan aktív SQL Database-kapcsolatokat legalább 10 óránként újból hitelesíteni kell (ezt a feladatot az adatbázismotor végzi el). Az adatbázismotor az eredetileg megadott jelszóval kísérli meg az újrahitelesítést, felhasználói beavatkozásra nincs szükség. Teljesítménybeli megfontolások miatt a jelszó alaphelyzetbe áll, az SQL Database, ha a kapcsolat nem újrahitelesítése, még akkor is, ha a kapcsolat alaphelyzetbe állítása kapcsolatkészletezést miatt. Ez különbözik a helyszíni SQL Server működésétől. Ha a jelszó a kapcsolat első hitelesítése óta megváltozott, akkor a kapcsolatot bontani kell, és új kapcsolatot kell létrehozni új jelszóval. A `KILL DATABASE CONNECTION` engedéllyel rendelkező felhasználók a [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql) paranccsal közvetlenül bonthatnak egy SQL Database-kapcsolatot.

A felhasználói fiókok a master adatbázisban hozhatók létre, így a kiszolgáló összes adatbázisához engedélyeket kaphatnak, de létrehozhatók magában az adatbázisban is (ezeket tartalmazott felhasználóknak nevezzük). A bejelentkezések létrehozásával és kezelésével kapcsolatos információért lásd: [Bejelentkezések kezelése](sql-database-manage-logins.md). A hordozhatóság és a méretezhetőség javításához használja tartalmazott adatbázisok. A tartalmazott felhasználókról további információ a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), a [CREATE USER-rel (Transact-SQL utasítás)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) és a [tartalmazott adatbázisokkal](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) foglalkozó cikkekben található.

Az ajánlott eljárás az, ha az alkalmazás egy dedikált fiókot használ a hitelesítéshez. Ezzel korlátozhatja az alkalmazáshoz rendelt engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát abban az esetben, ha az alkalmazás kódja sebezhető az SQL-injektálásos támadásokkal szemben. Az ajánlott módszer a [tartalmazott adatbázis-felhasználó](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) létrehozása, amellyel az alkalmazás közvetlenül kapcsolódhat az adatbázishoz. 

## <a name="authorization"></a>Engedélyezés

Az engedélyezés az Azure SQL Database-adatbázisokban a felhasználók által végrehajtható műveletek körét jelenti, amelyeket a felhasználóifiók-adatbázis [szerepkörtagságai](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) és [objektumszintű engedélyei](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) határoznak meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel. További információk: [Bejelentkezések kezelése](sql-database-manage-logins.md).

A `master` adatbázishoz általában csak a rendszergazdáknak kell hozzáférniük. Az egyes felhasználói adatbázisokhoz való rutinszerű hozzáférést az egyes adatbázisokban létrehozott nem rendszergazdai tartalmazottadatbázis-felhasználókon keresztül végezni. Tartalmazottadatbázis-felhasználók használata esetén nem szükséges bejelentkezési adatokat létrehozni a `master` adatbázisban. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) cikkben talál.

Érdemes megismerkednie az alábbi funkciókkal, amelyek az engedélyek korlátozására vagy a szint emelésére is használhatók:

- A [megszemélyesítés](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) és a [modulaláírás](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) az engedélyek biztonságos átmeneti növelésére használható.
- A [sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) használatával korlátozhatja, hogy a felhasználó mely sorokhoz férhessen hozzá.
- Az [adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) használatával korlátozhatja a bizalmas adatok megjelenítését.
- A [tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

## <a name="next-steps"></a>További lépések

- Az SQL Database biztonsági szolgáltatásairól [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít további információkat.
- A tűzfalszabályokkal kapcsolatos további tudnivalókért lásd: [tűzfalszabályok](sql-database-firewall-configure.md).
- Felhasználókkal és bejelentkezésekkel kapcsolatos információk: [Bejelentkezések kezelése](sql-database-manage-logins.md). 
- A proaktív, lásd: [Database Auditing](sql-database-auditing.md) és [SQL Database Threat Detection](sql-database-threat-detection.md).
- Foglalkozó oktatóanyagért lásd: [biztonságossá tétele az Azure SQL Database](sql-database-security-tutorial.md).
