---
title: "Az Azure SQL Database hozzáférés-vezérlése | Microsoft Docs"
description: "A Microsoft Azure SQL Database hozzáférésének vezérlésére vonatkozó tudnivalók."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 8e71b04c-bc38-4153-8f83-f2b14faa31d9
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/18/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 82508d01f9f1aaee11ebb5a5f936dfc71ace9cad


---
# <a name="azure-sql-database-access-control"></a>Az Azure SQL Database hozzáférés-vezérlése
A biztonság garantálása érdekében az SQL Database IP-cím alapján történő kapcsolatkorlátozást érvényesítő tűzfalszabályokkal, a felhasználói identitás igazolását megkövetelő hitelesítési mechanizmusokkal, illetve csak bizonyos műveletek és adatok elérését lehetővé tévő engedélyezési mechanizmusokkal vezérli a hozzáférést. 

> [!IMPORTANT]
> Az SQL Database biztonsági szolgáltatásairól [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít további információkat.
>

## <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok
A Microsoft Azure SQL Database egy relációs adatbázis-szolgáltatást nyújt az Azure és egyéb internetalapú alkalmazások számára. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján. További információkért lásd: [Az Azure SQL Database-tűzfalszabályok áttekintése](sql-database-firewall-configure.md).

Az Azure SQL Database szolgáltatás kizárólag a 1433-as TCP-porton keresztül érhető el. Ha a helyi számítógépről szeretné elérni az SQL Database-t, akkor ellenőrizze, hogy az ügyfélszámítógépen lévő tűzfal engedélyezi-e a kimenő kommunikációt az 1433-as TCP-porton keresztül. Ha más alkalmazásoknak nincs szüksége rá, akkor blokkolja az 1433-as TCP-port bejövő kapcsolatait. 

A kapcsolódási folyamat részeként az Azure virtuális gépektől érkező kapcsolatok az egyes feldolgozói szerepkörök egyedi IP-címeire és portjaira lesznek átirányítva. A portszám a 11000-től 11999-ig terjedő tartományban található. A TCP-portokkal kapcsolatos további információkért tekintse meg az [ADO.NET 4.5 és az SQL Database V12 1433-ason túli portjaival](sql-database-develop-direct-route-ports-adonet-v12.md) foglalkozó cikket.

## <a name="authentication"></a>Authentication

Az SQL Database két hitelesítési típust támogat:

* A felhasználónévvel és jelszóval végzett **SQL-hitelesítést**. Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezen hitelesítő adatokkal hitelesítheti magát a kiszolgáló minden adatbázisában az adatbázis tulajdonosaként („dbo”). 
* Az **Azure Active Directory-alapú hitelesítést**, amely az Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok is támogatják. [Amikor csak lehet](https://msdn.microsoft.com/library/ms144284.aspx), használja az Active Directory-hitelesítést (beépített biztonság). Ha Azure Active Directory-alapú hitelesítést kíván használni, létre kell hoznia egy másik kiszolgálói rendszergazdát „Azure AD admin” névvel, amely engedélyekkel rendelkezik az Azure AD-felhasználók és -csoportok felügyeletéhez. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja. A [Csatlakozás az SQL Database-hez Azure Active Directory-alapú hitelesítéssel](sql-database-aad-authentication.md) című cikk bemutatja, hogyan hozhat létre Azure AD-rendszergazdát az Azure Active Directory-alapú hitelesítés engedélyezéséhez.

Az adatbázismotor bontja a 30 percnél tovább tétlen kapcsolatokat. A kapcsolat használata újbóli bejelentkezés után folytatható. A folyamatosan aktív SQL Database-kapcsolatokat legalább 10 óránként újból hitelesíteni kell (ezt a feladatot az adatbázismotor végzi el). Az adatbázismotor az eredetileg megadott jelszóval kísérli meg az újrahitelesítést, felhasználói beavatkozásra nincs szükség. A teljesítmény javítása érdekében, ha az SQL Database szolgáltatás egyik jelszava vissza lett állítva, akkor a kapcsolat akkor sem lesz újrahitelesítve, ha a kapcsolatkészletezés céljából lett visszaállítva. Ez különbözik a helyszíni SQL Server működésétől. Ha a jelszó a kapcsolat első hitelesítése óta megváltozott, akkor a kapcsolatot bontani kell, és új kapcsolatot kell létrehozni új jelszóval. A KILL DATABASE CONNECTION engedéllyel rendelkező felhasználó a [KILL](https://msdn.microsoft.com/library/ms173730.aspx) parancs segítségével közvetlenül bonthat egy SQL Database-kapcsolatot.

A felhasználói fiókok a master adatbázisban hozhatók létre, így a kiszolgáló összes adatbázisához engedélyeket kaphatnak, de létrehozhatók magában az adatbázisban is (ezeket tartalmazott felhasználóknak nevezzük). A bejelentkezések létrehozásával és kezelésével kapcsolatos információért lásd: [Bejelentkezések kezelése](sql-database-manage-logins.md). A hordozhatóság és a méretezhetőség javításához használja a tartalmazott adatbázis felhasználóit. A tartalmazott felhasználókról további információ a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével](https://msdn.microsoft.com/library/ff929188.aspx), a [CREATE USER-rel (Transact-SQL utasítás)](https://technet.microsoft.com/library/ms173463.aspx) és a [tartalmazott adatbázisokkal](https://technet.microsoft.com/library/ff929071.aspx) foglalkozó cikkekben található.

Az ajánlott eljárás az, ha az alkalmazás másik fiókot használ a hitelesítéshez. Ezzel korlátozhatja az alkalmazáshoz rendelt engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát abban az esetben, ha az alkalmazás kódja sebezhető az SQL-injektálásos támadásokkal szemben. Az ajánlott módszer a [tartalmazott adatbázis-felhasználó](https://msdn.microsoft.com/library/ff929188) létrehozása, amellyel az alkalmazás közvetlenül kapcsolódhat az adatbázishoz. SQL-hitelesítést használó tartalmazott adatbázis-felhasználó létrehozásához futtassa az alábbi T-SQL-parancsot, miközben a kiszolgálói rendszergazdai bejelentkezéssel csatlakozik a felhasználói adatbázishoz:

## <a name="authorization"></a>Engedélyezés

Az engedélyezés az Azure SQL Database-adatbázisokban a felhasználók által végrehajtható műveletek körét jelenti, amelyeket a felhasználóifiók-adatbázis [szerepkörtagságai](https://msdn.microsoft.com/library/ms189121) és [objektumszintű engedélyei](https://msdn.microsoft.com/library/ms191291.aspx) határoznak meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel. További információk: [Bejelentkezések kezelése](sql-database-manage-logins.md).

A master adatbázishoz általában csak a rendszergazdáknak kell hozzáférnie. Az egyes felhasználói adatbázisokhoz való rutinszerű hozzáférést az egyes adatbázisokban létrehozott nem rendszergazdai tartalmazottadatbázis-felhasználókon keresztül végezni. Tartalmazottadatbázisok-felhasználók használata esetén nem szükséges bejelentkezési adatokat létrehozni a master adatbázisban. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://msdn.microsoft.com/library/ff929188.aspx) cikkben talál.

Ezek a szolgáltatások az engedélyek korlátozásához vagy szintjük emeléséhez is használhatók.

* A [megszemélyesítés](https://msdn.microsoft.com/library/vstudio/bb669087) és a [modulaláírás](https://msdn.microsoft.com/library/bb669102) az engedélyek biztonságos átmeneti növelésére használható.
* A [sorszintű biztonság](https://msdn.microsoft.com/library/dn765131) használatával korlátozhatja, hogy a felhasználó mely sorokhoz férhessen hozzá.
* Az [adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) használatával korlátozhatja a bizalmas adatok megjelenítését.
* A [tárolt eljárások](https://msdn.microsoft.com/library/ms190782) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

## <a name="next-steps"></a>Következő lépések

- Az SQL Database összes biztonsági szolgáltatásairól [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít további információkat.
- A tűzfalszabályokkal kapcsolatos további információk: [Azure SQL Database-tűzfal](sql-database-firewall-configure.md).
- Felhasználókkal és bejelentkezésekkel kapcsolatos információk: [Bejelentkezések kezelése](sql-database-manage-logins.md). 
- Az SQL Database adatvédelmi szolgáltatásainak használatával kapcsolatban lásd az [adatvédelemmel és biztonsággal](sql-database-protect-data.md) foglalkozó témakört.
- Információk a proaktív figyelésről: [az SQL Database naplózási szolgáltatását](sql-database-auditing-get-started.md) és [az SQL Database fenyegetésészlelési szolgáltatását](sql-database-threat-detection-get-started.md) bemutató cikkek.




<!--HONumber=Dec16_HO4-->


