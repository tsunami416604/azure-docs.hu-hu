---
title: "Az Azure Active Directory hitelesítési - Azure SQL (áttekintés) |} Microsoft Docs"
description: "További tudnivalók az Azure Active Directory használatáról az SQL-adatbázis, a felügyelt példány és az SQL Data Warehouse hitelesítéshez"
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/07/2018
ms.author: mireks
ms.openlocfilehash: cfad53a3f86450163b2e29d5e4d4ed2726ecb0bc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>Az SQL-adatbázis, a példány kezelt vagy az SQL Data Warehouse hitelesítéshez használandó Azure Active Directory-hitelesítés
Az Azure Active Directory-hitelesítés a Microsoft Azure SQL-adatbázishoz szeretne csatlakozni egy olyan mechanizmus és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) identitások az Azure Active Directory (Azure AD) segítségével. Az Azure AD-alapú hitelesítés adatbázis-felhasználók identitását, és más Microsoft-szolgáltatásokban egyetlen központi helyen központilag kezelheti. Központi azonosítófelügyeleti biztosít egy helyen adatbázis-felhasználók kezelése és egyszerűbbé teszi a jogosultság kezelése. Előnyei a következők:

* SQL Server-hitelesítés helyett biztosít.
* Segítséget nyújt a felhasználói identitások elterjedése leállítása adatbázis-kiszolgáló között.
* Lehetővé teszi, hogy a jelszó Elforgatás egyetlen helyen
* Az ügyfelek adatbázis-engedélyek külső (az Azure AD) csoportok használatával kezelheti.
* Integrált Windows-hitelesítés és egyéb Azure Active Directory által támogatott hitelesítési engedélyezésével azt megnövelésével kiküszöbölheti jelszavak tárolását.
* Az Azure AD authentication tartalmazott adatbázis-felhasználók használatával személyazonosság igazolása az adatbázis szintjén.
* Az Azure AD hitelesítési jogkivonat-alapú alkalmazások SQL-adatbázishoz szeretne csatlakozni támogatja.
* Az Azure AD authentication az ADFS (tartomány-összevonás) vagy a natív felhasználói/jelszó-hitelesítés támogat egy helyi Azure Active Directory tartományi szinkronizálás nélkül.  
* Az Azure AD-példányokhoz SQL Server Management Studio eszközt, amely az Active Directory univerzális hitelesítés használatára, amely magában foglalja a többtényezős hitelesítés (MFA).  Többtényezős hitelesítés tartalmazza, könnyen ellenőrzési lehetőségek széles erős hitelesítés – telefonhívás, szöveges üzenetet, intelligens kártyák PIN-kód és az értesítést a mobilalkalmazásban. További információkért lásd: [SSMS támogatása az Azure AD MFA az SQL-adatbázis és az SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).  

>  [!NOTE]  
>  Egy Azure virtuális Gépen futó SQL Server nem támogatott a Kapcsolódás egy Azure Active Directory-fiókkal. A tartomány Active Directory-fiókot használni.  

A konfigurálás lépéseinek végrehajtásához konfigurálhatja és használhatja az Azure Active Directory-hitelesítés a következő eljárásokat tartalmazzák.

1. Létrehozása és feltöltése az Azure AD.
2. Választható lehetőség: Hozzárendelése, vagy módosítsa az active directory jelenleg az Azure-előfizetéshez társított.
3. Azure Active Directory-rendszergazda az Azure SQL Database-kiszolgáló esetén a felügyelt példány létrehozása vagy a [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Állítsa be az ügyfélszámítógépen.
5. Tartalmazott adatbázis-felhasználók létrehozása az Azure AD-identitások leképezve az adatbázisban.
6. Az adatbázis csatlakozás az Azure AD-identitások segítségével.

> [!NOTE]
> Létrehozása és feltöltése az Azure AD és az Azure AD majd konfigurálása az Azure SQL Database, a felügyelt példány és az SQL Data Warehouse kapcsolatban [konfigurálása az Azure SQL Database az Azure AD](sql-database-aad-authentication-configure.md).
>

## <a name="trust-architecture"></a>Bizalmi kapcsolat architektúrája
A következő magas szintű diagramját a megoldásarchitektúra Azure AD-alapú hitelesítés használata az Azure SQL Database foglalja össze. Az SQL Data Warehouse koncepciók vonatkoznak. Az Azure AD-saját felhasználói jelszó támogatása érdekében csak a felhő része, és az Azure AD vagy az Azure SQL Database tekinthető. Külső hitelesítés (vagy/jelszó a Windows rendszerbeli hitelesítő adatokat), a kommunikáció az AD FS blokk szükség. A nyilak kommunikációs útvonallal jelzi.

![az aad-hitelesítés diagramja][1]

A következő diagram azt jelzi, az összevonási megbízhatósági és üzemeltetési kapcsolat van, amelyek lehetővé teszik az adatbázishoz való kapcsolódáshoz a elküldése a token által ügyfél. A jogkivonat az Azure AD hitelesíti, és megbízható-e az adatbázis. Ügyfél 1 natív felhasználóival az Azure Active Directory vagy egy Azure AD az összevont felhasználók jelenthet. Ügyfél 2 jelöli egy lehetséges megoldás, beleértve az importált felhasználók; Ebben a példában egy összevont Azure Active Directory és az AD FS az Azure Active Directoryval szinkronizált származik. Fontos megérteni, hogy egy adatbázist az Azure AD-alapú hitelesítés használatához, hogy az Azure AD társítva-e az üzemeltetési előfizetés. Az SQL Server kiszolgálót az Azure SQL Database vagy az SQL Data Warehouse létrehozásához ugyanahhoz az előfizetéshez kell használni.

![előfizetés kapcsolat][2]

## <a name="administrator-structure"></a>Rendszergazda-struktúra
Az Azure AD-alapú hitelesítés használata esetén vannak-e az SQL Database-kiszolgáló és a felügyelt példány; két rendszergazdai fiókok az eredeti SQL Server rendszergazdájának és az Azure AD-rendszergazda. Az SQL Data Warehouse koncepciók vonatkoznak. Csak a rendszergazda az Azure AD-fiókot alapján hozhat létre az első az Azure AD tartalmazott adatbázis felhasználó a felhasználói adatbázisban. Az Azure AD rendszergazdai bejelentkezés egy Azure AD-felhasználó vagy egy Azure AD-csoport lehet. Ha a rendszergazda csoportfiók, azt csoporttagot, lehetővé téve az SQL Server-példány több Azure AD-rendszergazdák által használható. Rendszergazda csoport fiókkal fokozza a kezelhetőségi azáltal, hogy központilag hozzáadása és eltávolítása a csoport tagjai az Azure AD a felhasználóknak vagy az SQL-adatbázis engedélyek módosítása nélkül. Csak egy Azure AD-rendszergazda (egy felhasználó vagy csoport) bármikor konfigurálhatja.

![rendszergazda-struktúra][3]

## <a name="permissions"></a>Engedélyek
Új felhasználók létrehozása, rendelkeznie kell a `ALTER ANY USER` engedéllyel az adatbázisban. A `ALTER ANY USER` engedélyt minden adatbázis-felhasználó engedélyezhetők. A `ALTER ANY USER` engedély a kiszolgáló rendszergazdai fiókok, és az adatbázis-felhasználók is tartja a `CONTROL ON DATABASE` vagy `ALTER ON DATABASE` engedéllyel az adatbázishoz tartozó, és a tagjai a `db_owner` adatbázis-szerepkör.

Tartalmazott adatbázis-felhasználó létrehozása az Azure SQL Database, a példány kezelt vagy az SQL Data Warehouse, csatlakoznia kell az adatbázis vagy a példány egy Azure AD identity használatával. Az első tartalmazott adatbázis-felhasználó létrehozása, csatlakoznia kell az adatbázis az Azure AD a rendszergazda (az adatbázis tulajdonosa van) használatával. Ezt mutatják be [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse](sql-database-aad-authentication-configure.md). Minden Azure AD-alapú hitelesítés csak akkor lehetséges, ha az Azure AD admin készült Azure SQL Database vagy az SQL Data Warehouse-kiszolgálóhoz. Ha az Azure Active Directory-rendszergazda el lett távolítva a kiszolgálóról, létrehozott meglévő Azure Active Directory-felhasználók korábban SQL-kiszolgálón belül már nem kapcsolódhatnak az adatbázist az Azure Active Directory hitelesítő adatok használatával.

## <a name="azure-ad-features-and-limitations"></a>Az Azure AD-funkciókat és korlátozások
Az alábbi tagokat az Azure AD az Azure SQL server vagy az SQL Data Warehouse kiépítése:

- Natív tagok: tagja hozott létre az Azure AD, a felügyelt tartományra vagy egy felhasználói tartományban. További információkért lásd: [saját tartománynév hozzáadása az Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
- Összevont tartományhoz: hozott létre az Azure AD egy összevont tartomány tagja. További információkért lásd: [Microsoft Azure mostantól támogatja a Windows Server Active Directory összevonási](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).
- Importált tagjai más Azure AD-ből natív vagy összevont tartománytagok.
- Active Directory csoport biztonsági csoportok jönnek létre.

Az Azure AD korlátozásokról felügyelt példány:
- Csak az Azure AD felügyeleti adatbázisok hozhatnak létre, az Azure Active Directory-felhasználók egyetlen DB hatóköre, és nem rendelkezik ezzel az engedéllyel
- Adatbázis-tulajdonosi:
  - Az Azure AD rendszerbiztonsági tag nem módosítható az adatbázis (a engedélyezési ON adatbázis módosítása) tulajdonjogát, és nem állítható be, mint a tulajdonos.
  - Az Azure AD-rendszergazda által létrehozott adatbázisokat a nem tulajdonosi van beállítva (0x1 található sys.sysdatabases owner_sid mező)
- SQL-ügynök nem tudja felügyelni, ha jelentkezett be az Azure AD rendszerbiztonsági tagok használatával. 
- Az Azure AD admin nem lehet megszemélyesíteni, EXECUTE AS használatával
- DAC-kapcsolatról az Azure AD rendszerbiztonsági tagok nem támogatott. 

A rendszer függvények visszatérési NULL értékek alatt az Azure AD rendszerbiztonsági tagok végrehajtásakor:
- `SUSER_ID()`
- `SUSER_NAME(<admin ID>)`
- `SUSER_SNAME(<admin SID>)`
- `SUSER_ID(<admin name>)`
- `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Csatlakozás az Azure AD-azonosítók használatával

Az Azure Active Directory authentication az identitásokat az Azure AD-adatbázishoz szeretne csatlakozni a következő módszereket támogatja:

* Integrált Windows-hitelesítés használatával
* Az Azure AD egyszerű felhasználónév és jelszó használatával
* Alkalmazás-tokent használó hitelesítés használatával

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

* Kezelhetőség javítása érdekében, ajánlott az egy dedikált az Azure AD kiépítése csoport rendszergazdaként.   
* Csak egy Azure AD-rendszergazda (egy felhasználó vagy csoport) bármikor konfigurálhatja egy Azure SQL Database server, a példány kezelt vagy az Azure SQL Data Warehouse számára.   
* Csak az SQL Server az Azure AD rendszergazdája kezdetben csatlakozhat a Azure SQL Database-kiszolgálóhoz, a példány kezelt vagy az Azure SQL Data Warehouse egy Azure Active Directory-fiókkal. Az Active Directory-rendszergazda úgy is konfigurálhatja a további Azure AD adatbázis-felhasználók.   
* Javasolt a kapcsolat időtúllépése beállítást 30 másodperc.   
* SQL Server 2016 Management Studio és az SQL Server Data Tools a Visual Studio 2015 (2016 vagy újabb verzió 14.0.60311.1April) támogatja az Azure Active Directory-hitelesítés. (Az azure AD-alapú hitelesítés támogatja a **.NET Framework Data Provider – SqlServer**; legalább a .NET-keretrendszer 4.6 verzióra). Ezért a legújabb ezekkel az eszközökkel és adatrétegbeli alkalmazások (DAC és. BACPAC) az Azure AD-alapú hitelesítés használható.   
* [ODBC verziója 13.1](https://www.microsoft.com/download/details.aspx?id=53339) azonban támogatja az Azure Active Directory hitelesítési `bcp.exe` nem tud csatlakozni az Azure Active Directory-hitelesítés használatával, mert azt egy régebbi ODBC-szolgáltatóját használja.   
* `sqlcmd` Azure Active Directory hitelesítési-tól kezdődően elérhető 13.1 verziója támogatja a [letöltőközpontból](http://go.microsoft.com/fwlink/?LinkID=825643).   
* SQL Server Data Tools a Visual Studio 2015 legalább a Data Tools (verzió: 14.0.60311.1) 2016. április verziója szükséges. Az Azure Active Directory-felhasználók jelenleg nem láthatók az SSDT Object Explorerben. Megoldás a felhasználók megtekintése [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
* [Microsoft JDBC illesztőprogram 6.0 az SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) támogatja az Azure AD hitelesítési. Lásd még [kapcsolat tulajdonságainak](https://msdn.microsoft.com/library/ms378988.aspx).   
* A PolyBase nem tudják hitelesíteni magukat az Azure AD-hitelesítés használatával.   
* Az Azure AD-alapú hitelesítés van támogatja az SQL Database az Azure-portálon **adatbázis importálása** és **adatbázis exportálása** paneleken. Importálás és exportálás az Azure AD-alapú hitelesítés használata is támogatott a PowerShell-paranccsal.   
* Az Azure AD-alapú hitelesítés esetén támogatott SQL-adatbázis, a felügyelt példány és az SQL Data Warehouse parancssori felület használatával. További információkért lásd: [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse](sql-database-aad-authentication-configure.md) és [SQL Server - az sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>További lépések
- Létrehozása és feltöltése az Azure AD és az Azure AD majd konfigurálása az Azure SQL Database vagy az Azure SQL Data Warehouse kapcsolatban [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL-adatbázis, a példány kezelt vagy az SQL Data Warehouse ](sql-database-aad-authentication-configure.md).
- Az SQL Database hozzáféréseinek és felügyeletének áttekintéséről az [SQL Database-hozzáférés és -felügyelet](sql-database-control-access.md) részben olvashat.
- Az SQL Database bejelentkezéseinek, felhasználóinak és adatbázis-szerepköreinek áttekintését a [Bejelentkezések, felhasználók és adatbázis-szerepkörök](sql-database-manage-logins.md) részben találja.
- További információ az adatbázis résztvevőivel kapcsolatban: [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx).
- További információ az adatbázis-szerepkörökkel kapcsolatban: [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx).
- További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

