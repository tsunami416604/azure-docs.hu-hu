---
title: Azure Active Directory
description: Az Azure Active Directory sql-adatbázissal, felügyelt példányokkal és SQL Data Warehouse-ral történő hitelesítéshez való használatának megismerése
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 02/20/2019
ms.openlocfilehash: ef20c5b8babdf378b88997ae2fd7b98350c31319
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124924"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Az Azure Active Directory-hitelesítés használata az SQL-hitelesítéshez

Az Azure Active Directory-hitelesítés az Azure [SQL Database,](sql-database-technical-overview.md) [a Felügyelt példány](sql-database-managed-instance.md)és az SQL Data [Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) az Azure Active Directoryban (Azure AD) való identitások használatával történő kapcsolódás mechanizmusa. 

> [!NOTE]
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

Az Azure Active Directory-hitelesítéssel lehetővé teszi adatbázis-felhasználók és más Microsoft-szolgáltatások identitásainak központi kezelését. A központi azonosítófelügyelettel egyetlen helyen kezelheti az adatbázis-felhasználókat, így leegyszerűsítheti az engedélykezelést. Ez az alábbi előnyökkel jár:

- Az SQL Server-hitelesítés alternatívája.
- Segít megállítani a felhasználói identitások elterjedését az adatbázis-kiszolgálók között.
- Lehetővé teszi a jelszó elforgatását egyetlen helyen.
- Az ügyfelek kezelhetik az adatbázis-engedélyeket külső (Azure AD) csoportok használatával.
- Megszüntetheti a jelszavak tárolását az integrált Windows-hitelesítés és az Azure Active Directory által támogatott egyéb hitelesítési formák engedélyezésével.
- Az Azure AD-hitelesítés a tartalmazott adatbázis-felhasználók segítségével hitelesíti az identitásokat az adatbázis szintjén.
- Az Azure AD támogatja a jogkivonat-alapú hitelesítést az SQL-adatbázishoz csatlakozó alkalmazások hoz.
- Az Azure AD-hitelesítés támogatja az ADFS (tartományösszevonás) vagy a natív felhasználói/jelszó-hitelesítést a helyi Azure Active Directory tartományszinkronizálás nélkül.
- Az Azure AD támogatja az SQL Server Management Studióból érkező azon kapcsolatokat, amelyek az Active Directory univerzális hitelesítést használják, amelynek része a többtényezős hitelesítés (MFA) is.  A számos egyszerű ellenőrzési lehetőségnek (telefonhívás, SMS, intelligens kártya PIN-kóddal vagy mobilalkalmazásos értesítés) köszönhetően az MFA erős hitelesítést kínál. További információért tekintse meg az [Azure AD MFA és az SQL Database, valamint az SQL Data Warehouse együttes SSMS-támogatását](sql-database-ssms-mfa-authentication.md) ismertető cikket.
- Az Azure AD támogatja az SQL Server Data Toolsból (SSDT) érkező, az Active Directory interaktív hitelesítést használó hasonló kapcsolatokat. További információért tekintse meg [az Azure Active Directory SQL Server Data Tools (SSDT) általi támogatását](/sql/ssdt/azure-active-directory) ismertető cikket.

> [!NOTE]  
> Az Azure Virtuális gépen futó SQL Server-kiszolgálóhoz való csatlakozás nem támogatott Egy Azure Active Directory-fiók használatával. Használjon helyette tartományi Active Directory-fiókot.  

A konfigurációs lépések közé tartoznak a következő eljárások az Azure Active Directory-hitelesítés konfigurálásához és használatához.

1. Hozzon létre és feltöltse az Azure AD-t.
2. Nem kötelező: Társítsa vagy módosítsa az Azure-előfizetéshez jelenleg társított active directoryt.
3. Hozzon létre egy Azure Active Directory-rendszergazdát az Azure SQL Database-kiszolgálóhoz, a felügyelt példányhoz vagy az [Azure SQL Data Warehousehoz.](https://azure.microsoft.com/services/sql-data-warehouse/)
4. Konfigurálja az ügyfélszámítógépeket.
5. Hozzon létre egyben foglalt adatbázis-felhasználókat az adatbázisban az Azure AD-identitások hoz létre.
6. Csatlakozzon az adatbázishoz az Azure AD-identitások használatával.

> [!NOTE]
> Az Azure AD létrehozásáról és feltöltéséről, majd az Azure AD azure-beli SQL Database, felügyelt példány és SQL Data Warehouse szolgáltatással való konfigurálásáról az [Azure AD konfigurálása az Azure SQL Database szolgáltatással](sql-database-aad-authentication-configure.md)című témakörben olvashat.

## <a name="trust-architecture"></a>Megbízhatósági architektúra

Az alábbi magas szintű diagram összefoglalja az Azure AD-hitelesítés Azure SQL Database használatával kapcsolatos megoldásarchitektúrát. Ugyanezek a fogalmak vonatkoznak az SQL Data Warehouse. Az Azure AD natív felhasználói jelszavának támogatása érdekében csak a felhő része és az Azure AD/Azure SQL Database tekinthető. Az összevont hitelesítés (vagy a Windows hitelesítő adatok felhasználói/jelszava) támogatásához az ADFS-blokkgal való kommunikáció szükséges. A nyilak kommunikációs útvonalakat jeleznek.

![aad hitelesítési diagram][1]

Az alábbi ábra azt az összevonási, megbízhatósági és üzemeltetési kapcsolatokat mutatja be, amelyek lehetővé teszik az ügyfél számára, hogy egy jogkivonat elküldésével csatlakozzon az adatbázishoz. A jogkivonatot egy Azure AD hitelesíti, és az adatbázis megbízható. Az 1-es ügyfél képviselhet egy Azure Active Directoryt natív felhasználókkal, vagy egy Azure AD-t összevont felhasználókkal. A 2. ebben a példában egy összevont Azure Active Directory ból érkezik, és az ADFS szinkronizálódik az Azure Active Directoryval. Fontos megérteni, hogy az Azure AD-hitelesítés használatával az adatbázishoz való hozzáférés megköveteli, hogy az üzemeltetési előfizetés az Azure AD-hez legyen társítva. Ugyanazt az előfizetést kell használni az Azure SQL Database vagy az SQL Data Warehouse üzemeltetőinek létrehozásához.

![előfizetési kapcsolat][2]

## <a name="administrator-structure"></a>Rendszergazdai struktúra

Az Azure AD-hitelesítés használatakor két rendszergazdai fiók van az SQL Database-kiszolgálóhoz és a Felügyelt példányhoz; az eredeti SQL Server-rendszergazda és az Azure AD-rendszergazda. Ugyanezek a fogalmak vonatkoznak az SQL Data Warehouse. Csak az Azure AD-fiókon alapuló rendszergazda hozhatja létre az első Azure AD-ben szereplő adatbázis-felhasználót egy felhasználói adatbázisban. Az Azure AD rendszergazdai bejelentkezés lehet egy Azure AD-felhasználó vagy egy Azure AD-csoport. Ha a rendszergazda egy csoportfiók, bármely csoporttag használhatja, amely lehetővé teszi több Azure AD-rendszergazda az SQL Server-példány. A csoportfiók rendszergazdaként való használata növeli a kezelhetőséget azáltal, hogy lehetővé teszi a csoporttagok központi hozzáadását és eltávolítását az Azure AD-ben a felhasználók vagy az ENGEDÉLYEK módosítása nélkül az SQL Database-ben. Csak egy Azure AD-rendszergazda (egy felhasználó vagy csoport) konfigurálható bármikor.

![rendszergazdai struktúra][3]

## <a name="permissions"></a>Engedélyek

Új felhasználók létrehozásához engedéllyel `ALTER ANY USER` kell rendelkeznie az adatbázisban. Az `ALTER ANY USER` engedély bármely adatbázis-felhasználó számára megadható. Az `ALTER ANY USER` engedélyt a kiszolgálórendszergazdai fiókok, valamint az `CONTROL ON DATABASE` `ALTER ON DATABASE` adatbázishoz tartozó vagy engedéllyel `db_owner` rendelkező adatbázis-felhasználók, valamint az adatbázis-szerepkör tagjai is birtokolják.

Ha egy tartalmazott adatbázis-felhasználó t szeretne létrehozni az Azure SQL Database, a Felügyelt példány vagy az SQL Data Warehouse szolgáltatásban, az azure-beli AD-identitás használatával kell csatlakoznia az adatbázishoz vagy példányhoz. Az első tartalmazott adatbázis-felhasználó létrehozásához csatlakoznia kell az adatbázishoz egy Azure AD-rendszergazda (aki az adatbázis tulajdonosa). Ezt az [Azure Active Directory-hitelesítés konfigurálása és kezelése sql adatbázissal vagy SQL Data Warehouse rendszerrel](sql-database-aad-authentication-configure.md)című dokumentum mutatja be. Bármilyen Azure AD-hitelesítés csak akkor lehetséges, ha az Azure AD-rendszergazda jött létre az Azure SQL Database vagy az SQL Data Warehouse-kiszolgáló. Ha az Azure Active Directory-rendszergazda lett távolítva a kiszolgálóról, a korábban az SQL Serverben létrehozott meglévő Azure Active Directory-felhasználók már nem tudnak csatlakozni az adatbázishoz az Azure Active Directory hitelesítő adataikkal.

## <a name="azure-ad-features-and-limitations"></a>Az Azure AD funkciói és korlátai

- Az Azure AD következő tagjai kiépíthetők az Azure SQL server vagy az SQL Data Warehouse szolgáltatásban:

  - Natív tagok: Az Azure AD-ben a felügyelt tartományban vagy egy ügyféltartományban létrehozott tag. További információ: [Saját tartománynév hozzáadása az Azure AD-hez](../active-directory/active-directory-domains-add-azure-portal.md)című témakörben talál.
  - Összevont tartományi tagok: Az Azure AD-ben létrehozott tag összevont tartománnyal. További információt a [Microsoft Azure now támogatja a Windows Server Active Directory összevonása](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)című témakörben talál.
  - Importált tagok más Azure AD-k, akik natív vagy összevont tartomány tagok.
  - Biztonsági csoportként létrehozott Active Directory-csoportok.

- A kiszolgálói szerepkörrel beeső `db_owner` csoport részét beálló Azure AD-felhasználók nem használhatják a CREATE DATABASE **[SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** szintaxist az Azure SQL Database és az Azure SQL Data Warehouse ellen. A következő hiba jelenik meg:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Adja `db_owner` meg a szerepkört közvetlenül az egyes Azure AD-felhasználóknak a **CREATE DATABASE SCOPED CREDENTIAL** probléma csökkentése érdekében.

- Ezek a rendszerfüggvények NULL értékeket adnak vissza, ha az Azure AD-tagok alatt hajtják végre:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Felügyelt példányok

- Az Azure AD-kiszolgálóinak egyszerű (bejelentkezési) és felhasználói a felügyelt példányok előzetes verziója szolgáltatásként [támogatottak.](sql-database-managed-instance.md)
- Az Azure AD-kiszolgáló egyszerű tagjainak (bejelentkezési adatoknak) beállítása, amelyek adatbázis-tulajdonosként vannak leképezve, nem támogatottak a [felügyelt példányok.](sql-database-managed-instance.md)
    - Ennek egyik kiterjesztése, hogy ha egy csoportot `dbcreator` a kiszolgálói szerepkör részeként ad hozzá, a csoport felhasználói csatlakozhatnak a felügyelt példányhoz, és új adatbázisokat hozhatnak létre, de nem férhetnek hozzá az adatbázishoz. Ennek az az oka, hogy az új adatbázis tulajdonosa sa, és nem az Azure AD-felhasználó. Ez a probléma nem jelentkezik, ha `dbcreator` az adott felhasználó hozzá lett adva a kiszolgálói szerepkörhöz.
- AZ SQL-ügynök-kezelés és a feladatok végrehajtása az Azure AD-kiszolgálói egyszerű (bejelentkezési adatok) támogatott.
- Az adatbázis biztonsági mentési és visszaállítási műveleteit az Azure AD-kiszolgálói egyszerű (bejelentkezési adatok) hajthatja végre.
- Az Azure AD-kiszolgálói rendszertagokhoz (bejelentkezések) és a hitelesítési eseményekhez kapcsolódó összes utasítás naplózása támogatott.
- Dedikált rendszergazdai kapcsolat az Azure AD-kiszolgáló iszt.
    - Az SQLCMD Segédprogram és az SQL Server Management Studio támogatja.
- A bejelentkezési eseményindítók az Azure AD-kiszolgálói egyszerű (bejelentkezési) bejelentkezési események esetén támogatottak.
- A Service Broker és az DB mail azure AD-kiszolgálóegyszerű (bejelentkezés) használatával is beállítható.


## <a name="connecting-using-azure-ad-identities"></a>Csatlakozás azure AD-identitások használatával

Az Azure Active Directory-hitelesítés a következő módszereket támogatja az azure AD-identitások használatával az adatbázishoz való csatlakozáshoz:

- Az Azure Active Directory jelszava
- Azure Active Directory integrált
- Azure Active Directory univerzális az MFA-val
- Alkalmazástoken-hitelesítés használata

A következő hitelesítési módszerek támogatottak az Azure AD-kiszolgálói egyszerű (bejelentkezési adatok)**(nyilvános előzetes verzió)** számára:

- Az Azure Active Directory jelszava
- Azure Active Directory integrált
- Azure Active Directory univerzális az MFA-val


### <a name="additional-considerations"></a>Néhány fontos megjegyzés

- A kezelhetőség növelése érdekében azt javasoljuk, hogy egy dedikált Azure AD-csoportot rendszergazdaként létesítsen.   
- Egyszerre csak egy Azure AD-rendszergazda (felhasználó vagy csoport) konfigurálható egy Azure SQL Database-kiszolgálóhoz vagy az Azure SQL Data Warehousehoz.
  - Az Azure AD-kiszolgálói egyszerű ségének (bejelentkezési adatok) hozzáadása a felügyelt példányok **(nyilvános előzetes verzió)** lehetővé teszi, `sysadmin` hogy több Azure AD-kiszolgáló iszmens (bejelentkezési rendszer), amely hozzáadható a szerepkörhöz.
- Kezdetben csak egy Azure AD-rendszergazda csatlakozhat az Azure SQL Database-kiszolgálóhoz, a felügyelt példányhoz vagy az Azure SQL Data Warehousehoz egy Azure Active Directory-fiók használatával. Az Active Directory rendszergazdája konfigurálhatja a további Azure AD-adatbázis-felhasználók.   
- Javasoljuk, hogy a kapcsolat időtúllépési idejét 30 másodpercre kell alapozni.   
- Az SQL Server 2016 Management Studio és az SQL Server Data Tools for Visual Studio 2015 (2016. április 1. (Az Azure AD-hitelesítést az **SqlServer .** Ezért ezen eszközök és adatszintű alkalmazások legújabb verziói (DAC és . BACPAC) azure AD-hitelesítést használhat.   
- A 15.0.1-es verziótól kezdve [az sqlcmd segédprogram](/sql/tools/sqlcmd-utility) és [a bcp segédprogram](/sql/tools/bcp-utility) támogatja az Active Directory Interactive hitelesítést az MFA-val.
- Az SQL Server Data Tools for Visual Studio 2015 használatához legalább az Adateszközök 2016 áprilisi verziójára van szükség (14.0.60311.1 verzió). Jelenleg az Azure AD-felhasználók nem jelennek meg az SSDT Object Explorerben. Kerülő megoldásként tekintse meg a felhasználókat a [sys.database_principals.](https://msdn.microsoft.com/library/ms187328.aspx)   
- [A Microsoft JDBC Driver 6.0 az SQL Server hez](https://www.microsoft.com/download/details.aspx?id=11774) támogatja az Azure AD-hitelesítést. További a [Kapcsolat tulajdonságainak beállítása](https://msdn.microsoft.com/library/ms378988.aspx)című témakörben is szerepel.   
- A PolyBase nem hitelesíthető az Azure AD-hitelesítés használatával.   
- Az Azure AD-hitelesítést az Azure Portal **Import Database** és **export Database** blades támogatja az SQL Database számára. Az Azure AD-hitelesítés használatával történő importálás és exportálás a PowerShell parancsból is támogatott.   
- Az Azure AD-hitelesítés támogatott SQL Database, felügyelt példány és SQL Data Warehouse cli használatával. További információt az [Azure Active Directory-hitelesítés konfigurálása és kezelése SQL Database vagy SQL Data Warehouse és](sql-database-aad-authentication-configure.md) SQL Server - AZ SQL Server használatával című [témakörben](https://docs.microsoft.com/cli/azure/sql/server)talál.

## <a name="next-steps"></a>További lépések

- Az Azure AD létrehozásáról és feltöltéséről, majd az Azure AD Azure SQL Database vagy Az Azure SQL Data Warehouse használatával való konfigurálásáról az [Azure Active Directory-hitelesítés konfigurálása és kezelése SQL Database, Felügyelt példány vagy SQL Data Warehouse szolgáltatással](sql-database-aad-authentication-configure.md)című témakörben olvashat.
- Az Azure AD-kiszolgálói egyszerű staféták (bejelentkezések) felügyelt példányokkal való használatának oktatóanyaga: [Azure AD-kiszolgálói egyszerű (bejelentkezési adatok) felügyelt példányokkal](sql-database-managed-instance-aad-security-tutorial.md)
- A bejelentkezések, a felhasználók, az adatbázis-szerepkörök és az SQL-adatbázis engedélyei áttekintését a [Bejelentkezések, a felhasználók, az adatbázis-szerepkörök és az engedélyek](sql-database-manage-logins.md)című témakörben találja.
- További információ az adatbázis résztvevőivel kapcsolatban: [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx).
- További információ az adatbázis-szerepkörökkel kapcsolatban: [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx).
- Az Azure AD-kiszolgáló rendszerbiztonsági rendszerében a felügyelt példányok hozásának szintaxisát a CREATE LOGIN című témakörben [tetszetős szintaxisról.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)
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
