---
title: "Hozzáférés engedélyezése az Azure SQL Database-hez | Microsoft Docs"
description: "A Microsoft Azure SQL Database hozzáférésének engedélyezésére vonatkozó tudnivalók."
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 02/06/2017
ms.author: carlrab
ms.openlocfilehash: 85d3e7819ef06fd943c207e1410c95168f574a7d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-access-control"></a>Az Azure SQL Database hozzáférés-vezérlése
A biztonság garantálása érdekében az SQL Database IP-cím alapján történő kapcsolatkorlátozást érvényesítő tűzfalszabályokkal, a felhasználói identitás igazolását megkövetelő hitelesítési mechanizmusokkal, illetve csak bizonyos műveletek és adatok elérését lehetővé tévő engedélyezési mechanizmusokkal vezérli a hozzáférést. 

> [!IMPORTANT]
> Az SQL Database biztonsági szolgáltatásairól [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít további információkat. Az oktatóanyagok esetén lásd: [az Azure SQL Database biztonságos](sql-database-security-tutorial.md).

## <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok
A Microsoft Azure SQL Database egy relációs adatbázis-szolgáltatást nyújt az Azure és egyéb internetalapú alkalmazások számára. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján. További információkért lásd: [Az Azure SQL Database-tűzfalszabályok áttekintése](sql-database-firewall-configure.md).

Az Azure SQL Database szolgáltatás kizárólag a 1433-as TCP-porton keresztül érhető el. Ha a helyi számítógépről szeretné elérni az SQL Database-t, akkor ellenőrizze, hogy az ügyfélszámítógépen lévő tűzfal engedélyezi-e a kimenő kommunikációt az 1433-as TCP-porton keresztül. Ha más alkalmazásoknak nincs szüksége rá, akkor blokkolja az 1433-as TCP-port bejövő kapcsolatait. 

A kapcsolódási folyamat részeként az Azure virtuális gépektől érkező kapcsolatok az egyes feldolgozói szerepkörök egyedi IP-címeire és portjaira lesznek átirányítva. A portszám a 11000-től 11999-ig terjedő tartományban található. További információ a TCP-portok: [kívüli ADO.NET 4.5 és SQL Database2 1433-as portokon](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Hitelesítés

Az SQL Database két hitelesítési típust támogat:

* A felhasználónévvel és jelszóval végzett **SQL-hitelesítést**. Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezen hitelesítő adatokkal hitelesítheti magát a kiszolgáló minden adatbázisában az adatbázis tulajdonosaként („dbo”). 
* Az **Azure Active Directory-alapú hitelesítést**, amely az Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok is támogatják. [Amikor csak lehet](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), használja az Active Directory-hitelesítést (beépített biztonság). Ha Azure Active Directory-alapú hitelesítést kíván használni, létre kell hoznia egy másik kiszolgálói rendszergazdát „Azure AD admin” névvel, amely engedélyekkel rendelkezik az Azure AD-felhasználók és -csoportok felügyeletéhez. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja. A [Csatlakozás az SQL Database-hez Azure Active Directory-alapú hitelesítéssel](sql-database-aad-authentication.md) című cikk bemutatja, hogyan hozhat létre Azure AD-rendszergazdát az Azure Active Directory-alapú hitelesítés engedélyezéséhez.

Az adatbázismotor bontja a 30 percnél tovább tétlen kapcsolatokat. A kapcsolat használata újbóli bejelentkezés után folytatható. A folyamatosan aktív SQL Database-kapcsolatokat legalább 10 óránként újból hitelesíteni kell (ezt a feladatot az adatbázismotor végzi el). Az adatbázismotor az eredetileg megadott jelszóval kísérli meg az újrahitelesítést, felhasználói beavatkozásra nincs szükség. A teljesítmény javítása érdekében, ha az SQL Database szolgáltatás egyik jelszava vissza lett állítva, akkor a kapcsolat akkor sem lesz újrahitelesítve, ha a kapcsolatkészletezés céljából lett visszaállítva. Ez különbözik a helyszíni SQL Server működésétől. Ha a jelszó a kapcsolat első hitelesítése óta megváltozott, akkor a kapcsolatot bontani kell, és új kapcsolatot kell létrehozni új jelszóval. A `KILL DATABASE CONNECTION` engedéllyel rendelkező felhasználók a [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql) paranccsal közvetlenül bonthatnak egy SQL Database-kapcsolatot.

A felhasználói fiókok a master adatbázisban hozhatók létre, így a kiszolgáló összes adatbázisához engedélyeket kaphatnak, de létrehozhatók magában az adatbázisban is (ezeket tartalmazott felhasználóknak nevezzük). A bejelentkezések létrehozásával és kezelésével kapcsolatos információért lásd: [Bejelentkezések kezelése](sql-database-manage-logins.md). A hordozhatóság és a méretezhetőség javításához használja a tartalmazott adatbázis felhasználóit. A tartalmazott felhasználókról további információ a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), a [CREATE USER-rel (Transact-SQL utasítás)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) és a [tartalmazott adatbázisokkal](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) foglalkozó cikkekben található.

Az ajánlott eljárás az, ha az alkalmazás egy dedikált fiókot használ a hitelesítéshez. Ezzel korlátozhatja az alkalmazáshoz rendelt engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát abban az esetben, ha az alkalmazás kódja sebezhető az SQL-injektálásos támadásokkal szemben. Az ajánlott módszer a [tartalmazott adatbázis-felhasználó](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) létrehozása, amellyel az alkalmazás közvetlenül kapcsolódhat az adatbázishoz. 

## <a name="authorization"></a>Engedélyezés

Az engedélyezés az Azure SQL Database-adatbázisokban a felhasználók által végrehajtható műveletek körét jelenti, amelyeket a felhasználóifiók-adatbázis [szerepkörtagságai](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) és [objektumszintű engedélyei](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) határoznak meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel. További információk: [Bejelentkezések kezelése](sql-database-manage-logins.md).

A `master` adatbázishoz általában csak a rendszergazdáknak kell hozzáférniük. Az egyes felhasználói adatbázisokhoz való rutinszerű hozzáférést az egyes adatbázisokban létrehozott nem rendszergazdai tartalmazottadatbázis-felhasználókon keresztül végezni. Tartalmazottadatbázis-felhasználók használata esetén nem szükséges bejelentkezési adatokat létrehozni a `master` adatbázisban. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) cikkben talál.

Érdemes megismerkednie az alábbi funkciókkal, amelyek az engedélyek korlátozására vagy a szint emelésére is használhatók:   
* A [megszemélyesítés](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) és a [modulaláírás](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) az engedélyek biztonságos átmeneti növelésére használható.
* A [sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) használatával korlátozhatja, hogy a felhasználó mely sorokhoz férhessen hozzá.
* Az [adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) használatával korlátozhatja a bizalmas adatok megjelenítését.
* A [tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

## <a name="next-steps"></a>További lépések

- Az SQL Database biztonsági szolgáltatásairól [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít további információkat.
- Tűzfalszabályok kapcsolatos további információkért lásd: [tűzfal-szabályok](sql-database-firewall-configure.md).
- Felhasználókkal és bejelentkezésekkel kapcsolatos információk: [Bejelentkezések kezelése](sql-database-manage-logins.md). 
- Az előrejelzéses figyeléssel tárgyalását lásd: [Database Auditing](sql-database-auditing.md) és [SQL adatbázis Fenyegetésészlelés](sql-database-threat-detection.md).
- Az oktatóanyagok esetén lásd: [az Azure SQL Database biztonságos](sql-database-security-tutorial.md).
