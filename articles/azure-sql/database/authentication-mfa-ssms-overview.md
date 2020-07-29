---
title: Multi-Factor Azure Active Directory hitelesítés használata
description: A Azure SQL Database, az Azure SQL felügyelt példánya és az Azure szinapszis Analytics Active Directory univerzális hitelesítés használatával támogatja a SQL Server Management Studio (SSMS) kapcsolatait.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 07/27/2020
tags: azure-synapse
ms.openlocfilehash: b2d179121b05d7bf3493937a9ff72e302fd31f3d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281140"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Multi-Factor Azure Active Directory hitelesítés használata
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, az Azure SQL felügyelt példánya és az Azure szinapszis Analytics támogatja a [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) kapcsolatait a *Azure Active Directory-Universal és MFA* hitelesítés használatával. Ez a cikk a különböző hitelesítési lehetőségek közötti különbségeket ismerteti, valamint a Azure Active Directory (Azure AD) Azure SQL-hez való univerzális hitelesítéssel kapcsolatos korlátozásait is.

**Töltse le a legújabb SSMS** -t az ügyfélszámítógépen, töltse le a SSMS legújabb verzióját a [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)webhelyről.

Az ebben a cikkben tárgyalt összes szolgáltatáshoz használja a 17,2-es vagy újabb 2017-es verziót. A legutóbbi kapcsolódás párbeszédpanelnek az alábbi képhez hasonlóan kell kinéznie:

  ![1mfa – univerzális – kapcsolat](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png "Befejezi a Felhasználónév mezőt.")  

## <a name="authentication-options"></a>Hitelesítési lehetőségek

Két nem interaktív hitelesítési modell létezik az Azure AD-hez, amely számos különböző alkalmazásban (ADO.NET, JDCB, ODC stb.) is használható. Ez a két módszer soha nem eredményez előugró párbeszédpanelt:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Az Azure Multi-Factor Authenticationt (MFA) is támogató interaktív módszer a következő: 

- `Azure Active Directory - Universal with MFA`

Az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáférésekkel szemben, miközben a felhasználói igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát teszi lehetővé. Erős hitelesítést biztosít számos egyszerű ellenőrzési lehetőséggel (telefonhívás, szöveges üzenet, PIN-kóddal ellátott intelligens kártyák vagy mobil alkalmazások értesítése), így a felhasználók kiválaszthatják a kívánt módszert. Az interaktív MFA az Azure AD-vel az ellenőrzés előugró párbeszédpanelét eredményezheti.

Az Azure Multi-Factor Authentication leírását itt tekintheti meg: [multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md).
A konfigurációs lépésekért lásd: [Azure SQL Database multi-Factor Authentication konfigurálása SQL Server Management studiohoz](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-tartománynév vagy bérlői azonosító paraméter

A [SSMS 17-es verziójától](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)kezdve a felhasználók, akik az aktuális Azure ad-be lesznek importálva, a vendég felhasználóként az Azure ad-tartománynevet, vagy a bérlő azonosítóját a csatlakozáskor. A vendég felhasználók a más Azure-hirdetéseket, például a outlook.com, a hotmail.com, a live.com vagy más, például a gmail.com fiókokat meghívó felhasználókat is tartalmazhatnak. Ez az információ lehetővé teszi `Azure Active Directory - Universal with MFA` a hitelesítés számára a megfelelő hitelesítő hatóság azonosítását. Ez a beállítás a Microsoft-fiókok (MSA), például a outlook.com, a hotmail.com, a live.com és a nem MSA fiókok támogatásához is szükséges. 

Az univerzális hitelesítéssel hitelesíteni kívánt összes vendég felhasználónak meg kell adnia az Azure AD-tartománynevet vagy a bérlő AZONOSÍTÓját. Ez a paraméter az aktuális Azure AD-tartománynevet vagy a bérlő AZONOSÍTÓját jelöli, amelyhez az Azure SQL logikai kiszolgáló társítva van. Ha például az SQL logikai kiszolgáló társítva van az Azure AD-tartományhoz `contosotest.onmicrosoft.com` , ahol a felhasználó az `joe@contosodev.onmicrosoft.com` Azure ad-tartományból importált felhasználóként fut `contosodev.onmicrosoft.com` , a felhasználó hitelesítéséhez szükséges tartománynév `contosotest.onmicrosoft.com` . Ha a felhasználó az SQL logikai kiszolgálóhoz társított Azure AD natív felhasználója, és nem MSA-fiók, nincs szükség tartománynévre vagy bérlői AZONOSÍTÓra. A paraméter megadása (a 17,2-es SSMS-verziótól kezdődően):


1. Nyisson meg egy kapcsolatokat a SSMS-ben. Adja meg a kiszolgáló nevét, és válassza a **Azure Active Directory-Universal beállítást MFA-** hitelesítéssel. Adja meg azt a **felhasználónevet** , amelyhez be szeretné jelentkezni.
1. Válassza a **Beállítások** mezőt, és lépjen át a **kapcsolat tulajdonságai** lapra. A **Kapcsolódás az adatbázishoz** párbeszédpanelen végezze el az adatbázis párbeszédpaneljét. Ellenőrizze az **ad-tartomány nevét vagy a bérlő azonosítóját** , és adja meg a hitelesítő hatóságot, például a tartománynevet (**contosotest.onmicrosoft.com**) vagy a bérlő azonosítójának GUID azonosítóját. 

   ![MFA-bérlő – SSMS](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Ha a SSMS 18. x vagy újabb verzióját futtatja, az AD-tartománynév vagy a bérlői azonosító már nem szükséges a vendég felhasználói számára, mert 18. x vagy újabb automatikusan felismeri azt.

   ![MFA-bérlő – SSMS](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD Business – üzleti támogatás

> [!IMPORTANT]
> Támogatás a vendég felhasználók számára a Azure SQL Databasehoz, az SQL felügyelt példányához és az Azure Szinapszishoz való kapcsolódáshoz anélkül, hogy egy csoportnak kellene részt vennie, jelenleg **nyilvános előzetes**verzióban érhető el. További információ: [Azure ad Guest Users létrehozása és beállítása Azure ad-rendszergazdaként](authentication-aad-guest-users.md).

Az Azure ad B2B-forgatókönyvekhez támogatott Azure AD-felhasználók (lásd: [Mi az az Azure B2B-együttműködés](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) a SQL Databasehoz és az Azure szinapszishoz csak a társított Azure ad-ben létrehozott csoport tagjainak részeként, illetve az adott adatbázisban a [create User (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) utasítással. Ha például `steve@gmail.com` meghívja az Azure ad-t `contosotest` (az Azure ad-tartománnyal `contosotest.onmicrosoft.com` ), egy Azure ad-csoportot, például a `usergroup` tagot tartalmazó Azure ad-ben kell létrehoznia `steve@gmail.com` . Ezt a csoportot a Transact-SQL-utasítás végrehajtásával létre kell hozni egy adott adatbázishoz (például: `MyDatabase` ) egy Azure ad SQL-rendszergazda vagy egy Azure ad-dbo használatával `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

Az adatbázis-felhasználó létrehozása után a felhasználó `steve@gmail.com` bejelentkezhet `MyDatabase` a SSMS hitelesítési lehetőséggel `Azure Active Directory – Universal with MFA` . Alapértelmezés szerint a `usergroup` csak a csatlakozási engedéllyel rendelkezik. A további adathozzáférést az adatbázisban egy megfelelő jogosultsággal rendelkező felhasználónak kell [megadnia](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) . 

> [!NOTE]
> Ha a SSMS 17. x-et használja `steve@gmail.com` vendég felhasználóként, akkor ellenőriznie kell az **ad-tartomány nevét vagy a bérlő azonosítóját** , és fel kell vennie az ad-tartománynevet `contosotest.onmicrosoft.com` a **kapcsolatok tulajdonságai** párbeszédpanelen. Az **Active Directory Domain Name vagy a bérlői azonosító** beállítás csak az **MFA-hitelesítéssel rendelkező Azure Active Directory-Universal** esetében támogatott. Ellenkező esetben a jelölőnégyzet szürkén jelenik meg.

## <a name="universal-authentication-limitations"></a>Univerzális hitelesítési korlátozások

- Az SSMS és a SqlPackage.exe az egyetlen jelenleg engedélyezett eszköz az MFA-hoz Active Directory univerzális hitelesítéssel.
- A SSMS 17,2-es verziója támogatja a többfelhasználós adathozzáférést az MFA-val való univerzális hitelesítés használatával. A 17,0-es és 17,1-es SSMS-es verzió esetében az eszköz univerzális hitelesítéssel egy Azure Active Directory-fiókra korlátozza a bejelentkezést egy SSMS-példányra. Ha másik Azure AD-fiókkal szeretne bejelentkezni, a SSMS egy másik példányát kell használnia. Ez a korlátozás Active Directory univerzális hitelesítésre korlátozódik. egy másik kiszolgálóra is bejelentkezhet `Azure Active Directory - Password` hitelesítés, `Azure Active Directory - Integrated` hitelesítés vagy a használatával `SQL Server Authentication` .
- A SSMS támogatja a Object Explorer, a lekérdezés-szerkesztő és a lekérdezés-áruház vizualizációjának Active Directory univerzális hitelesítését.
- A SSMS 17,2-es verziója a DacFx varázsló támogatását biztosítja az adatok exportálásához/kinyeréséhez/üzembe helyezéséhez. Ha egy adott felhasználó hitelesítése az univerzális hitelesítés használatával történik a kezdeti hitelesítési párbeszédpanelen, a DacFx varázsló ugyanúgy működik, mint az összes többi hitelesítési módszer.
- A SSMS Táblatervező nem támogatja az univerzális hitelesítést.
- Az univerzális hitelesítéshez nincs szükség további szoftverre Active Directory, kivéve, ha a SSMS támogatott verzióját kell használnia.  
- Tekintse meg a következő hivatkozást az univerzális hitelesítés legújabb Active Directory-hitelesítési tár (ADAL) verziójához: [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>További lépések

- A konfigurációs lépésekért lásd: [Azure SQL Database multi-Factor Authentication konfigurálása SQL Server Management studiohoz](authentication-mfa-ssms-configure.md).
- Mások hozzáférésének biztosítása az adatbázishoz: [SQL Database hitelesítés és engedélyezés: hozzáférés biztosítása](logins-create-manage.md)  
- Győződjön meg arról, hogy mások kapcsolódhatnak a tűzfalon keresztül: [a kiszolgálói szintű tűzfalszabály konfigurálása a Azure Portal használatával](firewall-configure.md)  
- [Azure Active Directory hitelesítés konfigurálása és kezelése SQL Database vagy Azure szinapszis segítségével](authentication-aad-configure.md)
- [Azure AD-vendégfelhasználók létrehozása és beállítása Azure AD-rendszergazdaként](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [BACPAC-fájl importálása egy új adatbázisba](database-import.md)  
- [Adatbázis exportálása BACPAC-fájlba](database-export.md)  
- C# interfész [IUniversalAuthProvider interfész](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- A **Azure Active Directory-Universal és az MFA-** hitelesítés használatakor a ADAL nyomkövetés a [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)-es verziójától kezdődően érhető el. Alapértelmezés szerint kikapcsolhatja a ADAL nyomkövetését az **eszközök**, beállítások menü, az **Azure-szolgáltatások**, az **Azure-felhő**, a **ADAL kimeneti ablak nyomkövetési szintje** **lehetőséggel** , majd a **kimenet** lehetőséget a **nézet** menüben engedélyezheti. A Nyomkövetések a kimeneti ablakban érhetők el **Azure Active Directory lehetőség**kiválasztásakor.  
