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
ms.date: 04/23/2020
tags: azure-synapse
ms.openlocfilehash: 25d08e86fde47c24c134bc03b036c4f456314856
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983579"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Multi-Factor Azure Active Directory hitelesítés használata
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, az Azure felügyelt példánya és az Azure szinapszis Analytics támogatja a SQL Server Management Studio (SSMS) kapcsolatait az *MFA-hitelesítéssel rendelkező Azure Active Directory-Universal* használatával. Ez a cikk a különböző hitelesítési lehetőségek közötti különbségeket ismerteti, valamint az univerzális hitelesítéssel kapcsolatos korlátozásokat is.

**Töltse le a legújabb SSMS** -t az ügyfélszámítógépen, töltse le a SSMS legújabb verzióját a [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)webhelyről.

Az ebben a cikkben tárgyalt összes szolgáltatáshoz használja a 17,2-es vagy újabb 2017-es verziót. A legutóbbi kapcsolódás párbeszédpanelnek az alábbi képhez hasonlóan kell kinéznie:

  ![1mfa – univerzális – kapcsolat](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png "Befejezi a Felhasználónév mezőt.")  

## <a name="the-five-authentication-options"></a>Az öt hitelesítési lehetőség  

Active Directory univerzális hitelesítés támogatja a két nem interaktív hitelesítési módszert:
    - `Azure Active Directory - Password`hitelesítés
    - `Azure Active Directory - Integrated`hitelesítés

Két nem interaktív hitelesítési modell is létezik, amelyek számos különböző alkalmazásban (ADO.NET, JDCB, ODC stb.) is használhatók. Ez a két módszer soha nem eredményez előugró párbeszédpanelt:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Az Azure Multi-Factor Authenticationt (MFA) is támogató interaktív módszer a következő:`Active Directory - Universal with MFA`

Az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáférésekkel szemben, miközben a felhasználói igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát teszi lehetővé. Erős hitelesítést biztosít számos egyszerű ellenőrzési lehetőséggel (telefonhívás, szöveges üzenet, PIN-kóddal ellátott intelligens kártyák vagy mobil alkalmazások értesítése), így a felhasználók kiválaszthatják a kívánt módszert. Az interaktív MFA az Azure AD-vel az ellenőrzés előugró párbeszédpanelét eredményezheti.

Az Azure Multi-Factor Authentication leírását itt tekintheti meg: [multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md).
A konfigurációs lépésekért lásd: [Azure SQL Database multi-Factor Authentication konfigurálása SQL Server Management studiohoz](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-tartománynév vagy bérlői azonosító paraméter

A [SSMS 17-es verziójától](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)kezdve a felhasználók, akik az aktuális Active Directoryba importálnak a vendég felhasználóként más Azure Active Directory-címtárból, megadhatják az Azure ad-tartománynevet vagy a bérlő azonosítóját a csatlakozáskor. A vendég felhasználók a más Azure-hirdetéseket, például a outlook.com, a hotmail.com, a live.com vagy más, például a gmail.com fiókokat meghívó felhasználókat is tartalmazhatnak. Ez az információ lehetővé teszi, hogy az **MFA-hitelesítéssel rendelkező Active Directory univerzálisan** azonosítsa a megfelelő hitelesítő szolgáltatót. Ez a beállítás a Microsoft-fiókok (MSA), például a outlook.com, a hotmail.com, a live.com és a nem MSA fiókok támogatásához is szükséges. Az univerzális hitelesítés használatával hitelesíteni kívánt felhasználóknak meg kell adniuk az Azure AD-tartománynevet vagy a bérlői azonosítót. Ez a paraméter az aktuális Azure AD-tartománynevet vagy-bérlő AZONOSÍTÓját jelöli, az Azure-kiszolgáló pedig a következőhöz van társítva:. Ha például az Azure Server társítva van az Azure AD-tartományhoz `contosotest.onmicrosoft.com` , ahol a felhasználó az `joe@contosodev.onmicrosoft.com` Azure ad-tartományból importált felhasználóként fut `contosodev.onmicrosoft.com` , a felhasználó hitelesítéséhez szükséges tartománynév `contosotest.onmicrosoft.com` . Ha a felhasználó az Azure AD-hez társított natív felhasználó, és nem MSA-fiók, nincs szükség tartománynévre vagy bérlői AZONOSÍTÓra. A paraméter megadásához (az SSMS 17,2-es verziójától kezdődően) a **Kapcsolódás az adatbázishoz** párbeszédpanelen fejezze be a párbeszédpanelt, és válassza a **Active Directory-Universal az MFA-** hitelesítéssel lehetőséget, kattintson a **Beállítások**elemre, fejezze be a **Felhasználónév** mezőt, majd kattintson a **kapcsolat tulajdonságai** lapra. Ellenőrizze az **ad-tartomány nevét vagy a bérlő azonosítóját** , és adja meg a hitelesítő hatóságot, például a tartománynevet (**contosotest.onmicrosoft.com**) vagy a bérlő azonosítóját.  
   ![MFA-bérlő – SSMS](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Ha a SSMS 18. x vagy újabb verzióját futtatja, az AD-tartománynév vagy a bérlői azonosító már nem szükséges a vendég felhasználói számára, mert 18. x vagy újabb automatikusan felismeri azt.

   ![MFA-bérlő – SSMS](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD Business – üzleti támogatás

Az Azure ad B2B-forgatókönyvekben támogatott Azure AD-felhasználók (lásd: [Mi az az Azure B2B-együttműködés](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) a SQL Databasehoz és az Azure szinapszishoz csak az aktuális Azure ad-ben létrehozott csoport tagjainak részeként, illetve az adott adatbázisban lévő Transact-SQL- `CREATE USER` utasítással. Ha például `steve@gmail.com` meghívja az Azure ad-t `contosotest` (az Azure ad-tartománnyal `contosotest.onmicrosoft.com` ), egy Azure ad-csoportot, például a `usergroup` tagot tartalmazó Azure ad-ben kell létrehoznia `steve@gmail.com` . Ezután ezt a csoportot egy Transact-SQL-utasítás végrehajtásával létre kell hozni egy adott adatbázishoz (azaz MyDatabase) az Azure AD SQL-rendszergazda vagy az Azure AD DBO `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . Az adatbázis-felhasználó létrehozása után a felhasználó `steve@gmail.com` bejelentkezhet a `MyDatabase` SSMS-hitelesítési lehetőség használatára `Active Directory – Universal with MFA support` . Alapértelmezés szerint a csoporthoz csak a kapcsolódási engedély és minden további adathozzáférés szükséges, amelyet a szokásos módon kell megadni. Vegye figyelembe, hogy a felhasználónak `steve@gmail.com` , mint vendég felhasználónak be kell jelölnie a jelölőnégyzetet, és fel kell vennie az ad-tartománynevet a `contosotest.onmicrosoft.com` SSMS- **kapcsolatok tulajdonságai** párbeszédpanelen. Az **Active Directory Domain Name vagy a bérlői azonosító** lehetőség csak az univerzális és MFA-kapcsolatok esetén támogatott, ellenkező esetben szürkén jelenik meg.

## <a name="universal-authentication-limitations"></a>Univerzális hitelesítési korlátozások

- Az SSMS és a SqlPackage.exe az egyetlen jelenleg engedélyezett eszköz az MFA-hoz Active Directory univerzális hitelesítéssel.
- A SSMS 17,2-es verziója támogatja a többfelhasználós hozzáférést az MFA-val való univerzális hitelesítéssel. A 17,0-es és a 17,1-es verzió a SSMS egy példányára korlátozza a bejelentkezést egyetlen Azure Active Directory fiókra, univerzális hitelesítéssel. Ha másik Azure AD-fiókkal szeretne bejelentkezni, a SSMS egy másik példányát kell használnia. (Ez a korlátozás Active Directory univerzális hitelesítésre korlátozódik; Active Directory jelszó-hitelesítéssel, Active Directory integrált hitelesítéssel vagy SQL Server hitelesítéssel bejelentkezhet a másik kiszolgálóra.
- A SSMS támogatja a Object Explorer, a lekérdezés-szerkesztő és a lekérdezés-áruház vizualizációjának Active Directory univerzális hitelesítését.
- A SSMS 17,2-es verziója a DacFx varázsló támogatását biztosítja az adatok exportálásához/kinyeréséhez/üzembe helyezéséhez. Ha egy adott felhasználó hitelesítése az univerzális hitelesítés használatával történik a kezdeti hitelesítési párbeszédpanelen, a DacFx varázsló ugyanúgy működik, mint az összes többi hitelesítési módszer.
- A SSMS Táblatervező nem támogatja az univerzális hitelesítést.
- Az univerzális hitelesítéshez nincs szükség további szoftverre Active Directory, kivéve, ha a SSMS támogatott verzióját kell használnia.  
- Az univerzális hitelesítés Active Directory-hitelesítési tár (ADAL) verziója frissítve lett a legújabb ADAL.dll 3.13.9 elérhető verzióra. Lásd: [Active Directory-hitelesítési tár 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>További lépések

- A konfigurációs lépésekért lásd: [Azure SQL Database multi-Factor Authentication konfigurálása SQL Server Management studiohoz](authentication-mfa-ssms-configure.md).
- Mások hozzáférésének biztosítása az adatbázishoz: [SQL Database hitelesítés és engedélyezés: hozzáférés biztosítása](logins-create-manage.md)  
- Győződjön meg arról, hogy mások kapcsolódhatnak a tűzfalon keresztül: [a kiszolgálói szintű tűzfalszabály konfigurálása a Azure Portal használatával](firewall-configure.md)  
- [Azure Active Directory hitelesítés konfigurálása és kezelése SQL Database vagy Azure szinapszis segítségével](authentication-aad-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [BACPAC-fájl importálása egy új adatbázisba](database-import.md)  
- [Adatbázis exportálása BACPAC-fájlba](database-export.md)  
- C# interfész [IUniversalAuthProvider interfész](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- A **Active Directory-Universal és az MFA-** hitelesítés használatakor a ADAL nyomkövetés a [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)-es verziójától kezdődően érhető el. Alapértelmezés szerint kikapcsolhatja a ADAL nyomkövetését az **eszközök**, beállítások menü, az **Azure-szolgáltatások**, az **Azure-felhő**, a **ADAL kimeneti ablak nyomkövetési szintje** **lehetőséggel** , majd a **kimenet** lehetőséget a **nézet** menüben engedélyezheti. A Nyomkövetések a kimeneti ablakban érhetők el **Azure Active Directory lehetőség**kiválasztásakor.  
