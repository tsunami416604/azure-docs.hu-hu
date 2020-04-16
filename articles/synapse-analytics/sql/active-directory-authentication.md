---
title: Azure Active Directory
description: Az Azure Active Directory használata az SQL Database, a Felügyelt példány és a Synapse SQL használatával történő hitelesítéshez
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 5808f892f189bd6cb2cc39bd157be1d61c966763
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424754"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Az Azure Active Directory-hitelesítés használata a Synapse SQL-nel való hitelesítéshez

Az Azure Active Directory-hitelesítés az [Azure Synapse Analytics-hez](../overview-faq.md) való csatlakozás mechanizmusa az Azure Active Directoryban (Azure AD) lévő identitások használatával.

Az Azure AD-hitelesítéssel központilag kezelheti az Azure Synapse-hoz hozzáféréssel rendelkező felhasználók identitásait az engedélykezelés egyszerűsítése érdekében. Ez az alábbi előnyökkel jár:

- Alternatívát kínál a rendszeres felhasználónév- és jelszó-hitelesítéssel.
- Segít megállítani a felhasználói identitások elterjedését az adatbázis-kiszolgálók között.
- Lehetővé teszi a jelszó elforgatását egyetlen helyen.
- Az ügyfelek külső (Azure AD) csoportok használatával kezelhetik az engedélyeket.
- Megszüntetheti a jelszavak tárolását az integrált Windows-hitelesítés és az Azure Active Directory által támogatott egyéb hitelesítési formák engedélyezésével.
- Az Azure AD támogatja a jogkivonat-alapú hitelesítést az Azure Synapse-hoz csatlakozó alkalmazásokhoz.
- Az Azure AD-hitelesítés támogatja az ADFS (tartományösszevonás) vagy a natív felhasználói/jelszó-hitelesítést a helyi Azure Active Directory tartományszinkronizálás nélkül.
- Az Azure AD támogatja az SQL Server Management Studióból érkező azon kapcsolatokat, amelyek az Active Directory univerzális hitelesítést használják, amelynek része a többtényezős hitelesítés (MFA) is.  A számos egyszerű ellenőrzési lehetőségnek (telefonhívás, SMS, intelligens kártya PIN-kóddal vagy mobilalkalmazásos értesítés) köszönhetően az MFA erős hitelesítést kínál. További információ: [SSMS támogatása az Azure AD MFA a Synapse SQL.](mfa-authentication.md)
- Az Azure AD támogatja az SQL Server Data Toolsból (SSDT) érkező, az Active Directory interaktív hitelesítést használó hasonló kapcsolatokat. További információért tekintse meg [az Azure Active Directory SQL Server Data Tools (SSDT) általi támogatását](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ismertető cikket.

A konfigurációs lépések közé tartoznak a következő eljárások az Azure Active Directory-hitelesítés konfigurálásához és használatához.

1. Hozzon létre és feltöltse az Azure AD-t.
2. Azure Active Directory-identitás létrehozása
3. Szerepkör hozzárendelése a létrehozott Azure Active Directory-identitáshoz a Synapse-munkaterületen (előzetes verzió)
4. Csatlakozzon a Synapse Studio-hoz az Azure AD-identitások használatával.

## <a name="aad-pass-through-in-azure-synapse-analytics"></a>AAD-áthaladás az Azure Synapse Analytics szolgáltatásban

Az Azure Synapse Analytics lehetővé teszi, hogy az Azure Active Directory-identitás használatával hozzáférjen a data lake-ben lévő adatokhoz.

A különböző adatmotorokban tiszteletben tartott fájlok és adatok hozzáférési jogainak meghatározása lehetővé teszi, hogy egyszerűsítse a data lake megoldásokat azáltal, hogy egyetlen hely, ahol az engedélyek vannak definiálva, ahelyett, hogy több helyen kellene definiálniőket.

## <a name="trust-architecture"></a>Megbízhatósági architektúra

Az alábbi magas szintű diagram összefoglalja az Azure AD-hitelesítés synapse SQL használatával történő megoldásarchitektúráját. Az Azure AD natív felhasználói jelszavának támogatása érdekében csak a felhő része és az Azure AD/Synapse Synapse SQL tekinthető. Az összevont hitelesítés (vagy a Windows hitelesítő adatok felhasználói/jelszava) támogatásához az ADFS-blokkgal való kommunikáció szükséges. A nyilak kommunikációs útvonalakat jeleznek.

![aad hitelesítési diagram][1]

Az alábbi ábra azt az összevonási, megbízhatósági és üzemeltetési kapcsolatokat mutatja be, amelyek lehetővé teszik az ügyfél számára, hogy egy jogkivonat elküldésével csatlakozzon az adatbázishoz. A jogkivonatot egy Azure AD hitelesíti, és az adatbázis megbízható. Az 1-es ügyfél képviselhet egy Azure Active Directoryt natív felhasználókkal, vagy egy Azure AD-t összevont felhasználókkal. A 2. ebben a példában egy összevont Azure Active Directory ból érkezik, és az ADFS szinkronizálódik az Azure Active Directoryval. Fontos megérteni, hogy az Azure AD-hitelesítés használatával az adatbázishoz való hozzáférés megköveteli, hogy az üzemeltetési előfizetés az Azure AD-hez legyen társítva. Ugyanazt az előfizetést kell használni az Azure SQL Database vagy SQL-készletet üzemeltető SQL Server létrehozásához.

![előfizetési kapcsolat][2]

## <a name="administrator-structure"></a>Rendszergazdai struktúra

Az Azure AD-hitelesítés használatakor két rendszergazdai fiók van a Synapse SQL számára; az eredeti SQL Server-rendszergazda és az Azure AD-rendszergazda. Csak az Azure AD-fiókon alapuló rendszergazda hozhatja létre az első Azure AD-ben szereplő adatbázis-felhasználót egy felhasználói adatbázisban. Az Azure AD rendszergazdai bejelentkezés lehet egy Azure AD-felhasználó vagy egy Azure AD-csoport. 

Ha a rendszergazda egy csoportfiók, bármely csoporttag használhatja, amely lehetővé teszi több Azure AD-rendszergazda a Synapse SQL-példány. A csoportfiók rendszergazdaként való használata növeli a kezelhetőséget azáltal, hogy lehetővé teszi a csoporttagok központi hozzáadását és eltávolítását az Azure AD-ben anélkül, hogy a Felhasználókat vagy az engedélyeket a Synapse Analytics-munkaterületen módosítaná. Csak egy Azure AD-rendszergazda (egy felhasználó vagy csoport) konfigurálható bármikor.

![rendszergazdai struktúra][3]

## <a name="permissions"></a>Engedélyek

Új felhasználók létrehozásához engedéllyel `ALTER ANY USER` kell rendelkeznie az adatbázisban. Az `ALTER ANY USER` engedély bármely adatbázis-felhasználó számára megadható. Az `ALTER ANY USER` engedélyt a kiszolgálórendszergazdai fiókok, valamint az `CONTROL ON DATABASE` `ALTER ON DATABASE` adatbázishoz tartozó vagy engedéllyel `db_owner` rendelkező adatbázis-felhasználók, valamint az adatbázis-szerepkör tagjai is birtokolják.

Hozzon létre egy tartalmazott adatbázis-felhasználó a Synapse SQL-ben, csatlakoznia kell az adatbázishoz vagy példányhoz egy Azure AD-identitás használatával. Az első tartalmazott adatbázis-felhasználó létrehozásához csatlakoznia kell az adatbázishoz egy Azure AD-rendszergazda (aki az adatbázis tulajdonosa). Bármilyen Azure AD-hitelesítés csak akkor lehetséges, ha az Azure AD-rendszergazda jött létre a Synapse SQL. Ha az Azure Active Directory-rendszergazda lett távolítva a kiszolgálóról, a korábban a Synapse SQL-ben létrehozott meglévő Azure Active Directory-felhasználók már nem tudnak csatlakozni az adatbázishoz az Azure Active Directory hitelesítő adataikkal.

## <a name="azure-ad-features-and-limitations"></a>Az Azure AD funkciói és korlátai

- Az Azure AD következő tagjai a Synapse SQL-ben építhetők ki:

  - Natív tagok: Az Azure AD-ben a felügyelt tartományban vagy egy ügyféltartományban létrehozott tag. További információ: [Saját tartománynév hozzáadása az Azure AD-hez](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)című témakörben talál.
  - Összevont tartományi tagok: Az Azure AD-ben létrehozott tag összevont tartománnyal. További információt a [Microsoft Azure now támogatja a Windows Server Active Directory összevonása](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)című témakörben talál.
  - Importált tagok más Azure AD-k, akik natív vagy összevont tartomány tagok.
  - Biztonsági csoportként létrehozott Active Directory-csoportok.

- A kiszolgálói szerepkörrel beeső `db_owner` csoport részét beálló Azure AD-felhasználók nem használhatják a CREATE DATABASE **[SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** szintaxist a Szinapszis SQL-vel szemben. A következő hiba jelenik meg:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Adja `db_owner` meg a szerepkört közvetlenül az egyes Azure AD-felhasználóknak a **CREATE DATABASE SCOPED CREDENTIAL** probléma csökkentése érdekében.

- Ezek a rendszerfüggvények NULL értékeket adnak vissza, ha az Azure AD-tagok alatt hajtják végre:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

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
- Csak egy Azure AD-rendszergazda (egy felhasználó vagy csoport) konfigurálható a Synapse SQL-készlet hez bármikor.
  - Az Azure AD-kiszolgálói egyszerű ségének (bejelentkezési adatok) hozzáadása az SQL igény szerinti (előzetes verzióhoz) lehetővé teszi több `sysadmin` Azure AD-kiszolgálói egyszerű ségének (bejelentkezési nevének) létrehozását, amelyek hozzáadhatók a szerepkörhöz.
- Kezdetben csak a Synapse SQL Azure AD-rendszergazdája csatlakozhat a Synapse SQL-hez egy Azure Active Directory-fiók használatával. Az Active Directory rendszergazdája konfigurálhatja a további Azure AD-adatbázis-felhasználók.
- Javasoljuk, hogy a kapcsolat időtúllépési idejét 30 másodpercre kell alapozni.
- Az SQL Server 2016 Management Studio és az SQL Server Data Tools for Visual Studio 2015 (2016. április 1. (Az Azure AD-hitelesítést az **SqlServer .** Ezért ezen eszközök és adatszintű alkalmazások legújabb verziói (DAC és . BACPAC) azure AD-hitelesítést használhat.
- A 15.0.1-es verziótól kezdve [az sqlcmd segédprogram](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és [a bcp segédprogram](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) támogatja az Active Directory Interactive hitelesítést az MFA-val.
- Az SQL Server Data Tools for Visual Studio 2015 használatához legalább az Adateszközök 2016 áprilisi verziójára van szükség (14.0.60311.1 verzió). Jelenleg az Azure AD-felhasználók nem jelennek meg az SSDT Object Explorerben. Kerülő megoldásként tekintse meg a felhasználókat a [sys.database_principals.](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [A Microsoft JDBC Driver 6.0 az SQL Server hez](https://www.microsoft.com/download/details.aspx?id=11774) támogatja az Azure AD-hitelesítést. További a [Kapcsolat tulajdonságainak beállítása](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)című témakörben is szerepel.

## <a name="next-steps"></a>További lépések

A Synapse SQL hozzáférésének és vezérlésének áttekintését a [Synapse SQL hozzáférés-vezérlés című](../sql/access-control.md)témakörben találja. Ha többet szeretne megtudni az adatbázis-biztonsági tagokról, olvassa el [a Rendszerbiztonsági tagok](https://msdn.microsoft.com/library/ms181127.aspx). Az adatbázis-szerepkörökkel kapcsolatos további információk az [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx) cikkben találhatók.
 

<!--Image references-->

[1]: ./media/aad-authentication/1-active-directory-authentication-diagram.png
[2]: ./media/aad-authentication/2-subscription-relationship.png
[3]: ./media/aad-authentication/3-admin-structure.png
[4]: ./media/aad-authentication/4-select-subscription.png
[5]: ./media/aad-authentication/5-active-directory-settings-portal.png
[6]: ./media/aad-authentication/6-edit-directory-select.png
[7]: ./media/aad-authentication/7-edit-directory-confirm.png
[8]: ./media/aad-authentication/8-choose-active-directory.png
[9]: ./media/aad-authentication/9-active-directory-settings.png
[10]: ./media/aad-authentication/10-choose-admin.png
[11]: ./media/aad-authentication/11-connect-using-integrated-authentication.png
[12]: ./media/aad-authentication/12-connect-using-password-authentication.png
[13]: ./media/aad-authentication/13-connect-to-db.png
