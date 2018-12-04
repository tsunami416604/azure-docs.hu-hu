---
title: Azure Active Directory-hitelesítés – Azure SQL |} A Microsoft Docs
description: További tudnivalók az Azure Active Directory használata a hitelesítés az SQL Database felügyelt példány és az SQL Data warehouse-bA
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: ff9011dda4a94f323b430a3860eadc8d970a23f7
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838616"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>SQL-hitelesítéshez használandó Azure Active Directory-hitelesítéssel

Az Azure Active Directory-hitelesítés olyan mechanizmus csatlakozás az Azure-bA [SQL Database](sql-database-technical-overview.md), [felügyelt példány](sql-database-managed-instance.md), és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) identitások használatával az Azure-ban Az Active Directory (Azure AD). 

> [!NOTE]
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

Az Azure AD-hitelesítés az identitások, az adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen központilag kezelheti. Központi azonosítófelügyeleti biztosít egy helyen adatbázis-felhasználók kezelése és egyszerűsíti az engedélyek kezelését. Előnyei a következők:

- SQL Server-hitelesítés helyett biztosít.
- Segít állítsa le a felhasználói identitások elterjedése adatbázis-kiszolgáló között.
- Lehetővé teszi, hogy a jelszóváltoztatás egyetlen helyen.
- Ügyfelek adatbázis-engedélyek külső (az Azure AD-) csoportok használata kezelheti.
- Integrált Windows-hitelesítés és egyéb Azure Active Directory által támogatott hitelesítési engedélyezésével jelszavak tárolását, nyilvánvaló.
- Azure AD-hitelesítés használja a tartalmazott adatbázis felhasználóit az adatbázis szintjén identitások hitelesítésére.
- Az Azure AD hitelesítési jogkivonat-alapú támogatja az alkalmazások SQL Database csatlakoztatásához.
- Az Azure AD-hitelesítés egy helyi Azure Active Directory tartományi szinkronizálás nélkül támogatja az ADFS (tartomány-összevonási) vagy a natív felhasználó/jelszó-hitelesítést.  
- Az Azure AD-példányokhoz SQL Server Management Studio által használt Active Directory univerzális hitelesítéssel, amely tartalmazza a multi-factor Authentication (MFA).  MFA magában foglalja a több egyszerű ellenőrzési lehetőség erős hitelesítés – telefonhívás, szöveges üzenet, intelligens kártyák PIN-kód vagy mobilalkalmazásbeli értesítés. További információkért lásd: [SSMS-támogatás az Azure AD MFA és az SQL Database és az SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).  

> [!NOTE]  
> Egy Azure virtuális gépeken futó SQL Serverhez való csatlakozáshoz nem támogatott az Azure Active Directory-fiók használatával. Használja helyette a tartomány Active Directory-fiókot.  

A konfigurálás lépéseinek végrehajtásához konfigurálhatja és használhatja az Azure Active Directory-hitelesítést a következő eljárásokat tartalmazzák.

1. Hozzon létre, és töltse fel az Azure ad-ben.
2. Választható lehetőség: Hozzárendelése, vagy módosítsa az active directory jelenleg társított Azure-előfizetésében.
3. Azure Active Directory-rendszergazda az Azure SQL Database-kiszolgáló, a felügyelt példány létrehozása vagy a [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Konfigurálja az ügyfélszámítógépeken.
5. Hozzon létre tartalmazottadatbázis-felhasználók az Azure AD-identitások leképezett adatbázis.
6. Az Azure AD-identitások kapcsolódni az adatbázishoz.

> [!NOTE]
> Ismerje meg, hogyan hozzon létre és töltse fel az Azure ad-ben, és adja meg a Azure ad-ben az Azure SQL Database felügyelt példány és az SQL Data Warehouse, lásd: [konfigurálása az Azure SQL Database az Azure AD](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Bizalmi kapcsolat architektúrája

A következő magas szintű ábra összefoglalja az Azure AD-hitelesítés használata az Azure SQL Database megoldás architektúrája. Ugyanezek a fogalmak alkalmazhatók az SQL Data Warehouse. Az Azure AD natív felhasználói jelszó támogatása érdekében csak a felhő része és az Azure AD vagy az Azure SQL Database számít. Támogatja az összevont hitelesítés (vagy felhasználó/jelszó a Windows hitelesítő adatok), a kommunikáció az AD FS-blokk megadása kötelező. A nyilak jelzik a kommunikációs utak.

![aad-hitelesítés diagramja][1]

A következő diagram azt jelzi, az összevonási megbízhatósági és birtokosi kapcsolat, amelyek lehetővé teszik, hogy az ügyfél csatlakozik egy adatbázishoz, ha elküldi egy token. A jogkivonatot az Azure AD által hitelesített, és az adatbázis által megbízhatónak tartott. 1-es ügyfél hozhat létre egy Azure Active Directory natív felhasználókkal vagy egy Azure AD összevont felhasználókkal. 2. ügyfél jelöli egy lehetséges megoldást, beleértve az importált felhasználók; Ebben a példában egy összevont Azure Active Directory az AD FS az Azure Active Directoryval szinkronizált származik. Fontos megérteni, hogy egy adatbázist az Azure AD-hitelesítés használatához, hogy a szolgáltatási előfizetés társítva az Azure ad-ben. Az SQL Server kiszolgálót az Azure SQL Database vagy az SQL Data Warehouse létrehozása az ugyanahhoz az előfizetéshez kell használni.

![előfizetés-kapcsolat][2]

## <a name="administrator-structure"></a>Rendszergazda-struktúra

Az Azure AD-hitelesítés használatakor a vannak-e az SQL Database-kiszolgáló és a felügyelt példány; a két rendszergazdai fiók az eredeti SQL Server-rendszergazda és az Azure AD-rendszergazda. Ugyanezek a fogalmak alkalmazhatók az SQL Data Warehouse. Csak a rendszergazdák Azure AD-fiók alapján hozhat létre az első Azure ad-ben tárolt adatbázis-felhasználót egy felhasználói adatbázist. Az Azure AD-rendszergazdai bejelentkezés az Azure AD-felhasználó vagy egy Azure AD-csoport is lehet. Ha a rendszergazda egy fiók, használat szerint bármilyen csoport tagja, így több Azure ad-ben a rendszergazdák számára az SQL Server-példány. Rendszergazdai fiók használatával javítja a kezelhetőségi azáltal, hogy központilag hozzáadása és a csoport tagjainak eltávolítása az Azure AD a felhasználóknak vagy az SQL Database engedélyekkel módosítása nélkül. Csak egy Azure AD-rendszergazda (felhasználó vagy csoport) bármikor konfigurálhatja.

![rendszergazdai struktúra][3]

## <a name="permissions"></a>Engedélyek

Új felhasználók létrehozásához rendelkeznie kell a `ALTER ANY USER` engedélyt az adatbázisban. A `ALTER ANY USER` engedély adható bármilyen adatbázis-felhasználót. A `ALTER ANY USER` engedéllyel a kiszolgáló-rendszergazdai fiókok, és az adatbázis-felhasználók is birtokában a `CONTROL ON DATABASE` vagy `ALTER ON DATABASE` engedélye az adatbázishoz, és a tagjai a `db_owner` adatbázis-szerepkörhöz.

Egy tartalmazottadatbázis-felhasználó létrehozása az Azure SQL Database felügyelt példányába vagy az SQL Data warehouse-ba, csatlakoznia kell az adatbázis vagy az Azure AD identitás-példányt. Az első tartalmazottadatbázis-felhasználó létrehozásához, csatlakoznia kell az adatbázist az Azure AD-rendszergazda (aki az adatbázis tulajdonosa). Ezt mutatják be [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse](sql-database-aad-authentication-configure.md). Minden olyan Azure AD-hitelesítés csak akkor lehetséges, ha az Azure AD-rendszergazda az Azure SQL Database vagy az SQL Data Warehouse-kiszolgáló lett létrehozva. Ha az Azure Active Directory-rendszergazda el lett távolítva a kiszolgálóról, meglévő Azure Active Directory-felhasználók létrehozott korábban belül az SQL Server már nem csatlakozhat az Azure Active Directory hitelesítő adataikkal adatbázis.

## <a name="azure-ad-features-and-limitations"></a>Az Azure AD-funkciók és korlátozások

Az alábbi tagokat az Azure AD az Azure SQL-kiszolgáló vagy az SQL Data Warehouse kiépítése:

- Natív tag: az Azure ad-ben létrehozott vagy a felhasználói tartományban található a felügyelt tartomány tagja. További információkért lásd: [saját tartománynév hozzáadása az Azure ad-ben](../active-directory/active-directory-domains-add-azure-portal.md).
- Összevont tartomány tagjai: az összevont tartományt az Azure AD-ben létrehozott tagja. További információkért lásd: [Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonás](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).
- Importált más Azure AD-ből tartózkodó tagok natív vagy összevont tartomány tagjai.
- Active Directory-csoportok biztonsági csoportokat létrehozni.

Az Azure AD-bejelentkezések és felhasználók támogatottak az előzetes verziójú funkcióként [felügyelt példányok](sql-database-managed-instance.md)

Ezek a rendszer függvények végrehajtása alatt az Azure AD rendszerbiztonsági tagok NULL értéket adja vissza:

- `SUSER_ID()`
- `SUSER_NAME(<admin ID>)`
- `SUSER_SNAME(<admin SID>)`
- `SUSER_ID(<admin name>)`
- `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Csatlakozás az Azure AD-identitások használatával

Az Azure Active Directory-hitelesítés használata az Azure AD-identitások-adatbázishoz szeretne csatlakozni, a következő módszereket támogatja:

- Integrált Windows-hitelesítés használatával
- Az Azure AD egyszerű felhasználónév és jelszó használatával
- Alkalmazástoken-hitelesítésének használata

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

- Kezelhetőség javítása érdekében javasoljuk, hogy üzembe helyez egy dedikált Azure AD rendszergazdai csoporthoz.   
- Csak egy Azure AD-rendszergazda (felhasználó vagy csoport) bármikor konfigurálhatja egy Azure SQL Database server, a felügyelt példány vagy az Azure SQL Data Warehouse számára.
- Csak Azure AD-rendszergazda az SQL Server kezdetben csatlakozhat az Azure SQL Database-kiszolgálóhoz, a felügyelt példányába vagy az Azure SQL Data Warehouse egy Azure Active Directory-fiókkal. Az Active Directory-rendszergazda konfigurálhatja az Azure AD további adatbázis-felhasználók.   
- Azt javasoljuk, hogy a kapcsolat időtúllépése beállítás 30 másodperc.   
- SQL Server 2016 Management Studio és az SQL Server Data Tools for Visual Studio 2015 (2016-os vagy újabb verzió 14.0.60311.1April) támogatja az Azure Active Directory-hitelesítés. (Azure AD-hitelesítést támogatja a **.NET Framework Data Provider Pro SqlServer**; legalább .NET Framework 4.6-os verzió). Ezért a legújabb verzióra a következő eszközök és -adatrétegbeli alkalmazások (DAC és. BACPAC) használhatja az Azure AD-hitelesítés.   
- [ODBC 13.1 verzióját](https://www.microsoft.com/download/details.aspx?id=53339) azonban támogatja az Azure Active Directory-hitelesítés `bcp.exe` nem tud csatlakozni az Azure Active Directory-hitelesítés használatát, mert azt egy régebbi ODBC-szolgáltatóját használja.   
- `sqlcmd` Azure Active Directory hitelesítési kiadásától kezdve elérhető 13.1 verzióját támogatja a [letöltőközpontból](https://go.microsoft.com/fwlink/?LinkID=825643).
- Az SQL Server Data Tools for Visual Studio 2015 vagy újabb a Data Tools (verzió 14.0.60311.1) 2016. április verziója szükséges. Jelenleg az Azure AD-felhasználók nem jelennek meg az SSDT Object Explorerben. Áthidaló megoldásként a felhasználók megtekintéséhez [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Microsoft JDBC-illesztőprogram 6.0 az SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) támogatja az Azure AD-hitelesítés. Lásd még [beállítást a kapcsolat tulajdonságai](https://msdn.microsoft.com/library/ms378988.aspx).   
- A PolyBase nem tudja hitelesíteni az Azure AD-hitelesítés használatával.   
- Azure AD-hitelesítés az Azure portál által támogatott az SQL Database- **adatbázis importálása** és **adatbázis exportálása** paneljein. Importálása és exportálása az Azure AD-hitelesítés használata is támogatott, a PowerShell-parancsot.   
- Az Azure AD-hitelesítés az SQL Database felügyelt példány és az SQL Data Warehouse támogatott parancssori felület használatával. További információkért lásd: [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse](sql-database-aad-authentication-configure.md) és [SQL Server - az sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan hozzon létre és töltse fel az Azure ad-ben, és adja meg a Azure ad-ben az Azure SQL Database vagy Azure SQL Data Warehouse, lásd: [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database felügyelt példányába vagy az SQL Data warehouse-bA ](sql-database-aad-authentication-configure.md).
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
