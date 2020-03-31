---
title: Többtényezős AAD-hitelesítés használata
description: Az Azure SQL Database és az Azure Synapse támogatja az SQL Server Management Studio (SSMS) kapcsolatait az Active Directory univerzális hitelesítés használatával.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synapse
ms.openlocfilehash: e9a4aa5b54cf7ed48daf1899bb5801c609dfbf32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255872"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-synapse-analytics-ssms-support-for-mfa"></a>Többtényezős AAD-hitelesítés használata az Azure SQL Database és az Azure Synapse Analytics szolgáltatással (SSMS-támogatás az MFA-hoz)
Az Azure SQL Database és az Azure Synapse támogatja az SQL Server Management Studio (SSMS) kapcsolatait az *Active Directory univerzális hitelesítés használatával.* Ez a cikk ismerteti a különböző hitelesítési beállítások közötti különbségeket, valamint az univerzális hitelesítés használatával kapcsolatos korlátozásokat. 

**Töltse le a legújabb SSMS** - Az ügyfélszámítógépen, töltse le a legújabb verzióját SSMS, [a Letöltés SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


A cikkben tárgyalt összes funkcióhoz legalább 2017 júliusa, 17.2-es verzió használható.  A legutóbbi kapcsolat párbeszédpanelnek az alábbi képhez hasonlóan kell kinéznie:
 
  ![1mfa-univerzális csatlakozás](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "A Felhasználónév mező befejezése.")  

## <a name="the-five-authentication-options"></a>Az öt hitelesítési lehetőség  

Az Active Directory univerzális hitelesítés két nem interaktív hitelesítési módszert támogatja:
    - `Active Directory - Password`Hitelesítés
    - `Active Directory - Integrated`Hitelesítés

Két nem interaktív hitelesítési modell is létezik, amelyek számos különböző alkalmazásban használhatók (ADO.NET, JDCB, ODC stb.). Ez a két módszer soha nem eredményez előugró párbeszédpaneleket: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Az interaktív módszer az, amely támogatja az Azure többtényezős hitelesítés (MFA) is: 
- `Active Directory - Universal with MFA` 


Az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáférésekkel szemben, miközben a felhasználói igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát teszi lehetővé. Erős hitelesítést biztosít számos egyszerű ellenőrzési lehetőséggel (telefonhívás, szöveges üzenet, tűs intelligens kártyák vagy mobilalkalmazás-értesítés), amely lehetővé teszi a felhasználók számára, hogy kiválasszák az általuk preferált módszert. Interaktív MFA az Azure AD eredményezhet egy előugró párbeszédpanel érvényesítése.

A többtényezős hitelesítés leírását a Többtényezős hitelesítés című témakörben [tésszet.](../active-directory/authentication/multi-factor-authentication.md)
A konfigurációs lépéseket az [Azure SQL Database többtényezős hitelesítéskonfigurálása az SQL Server Management Studio rendszerhez című témakörben tartalmazza.](sql-database-ssms-mfa-authentication-configure.md)

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD tartománynév vagy bérlőazonosító paraméter   

Az [SSMS 17-es verziójától](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)kezdve a más Azure Active-könyvtárakból az aktuális Active Directoryba vendégfelhasználóként importált felhasználók megadhatják az Azure AD tartománynevet vagy a bérlői azonosítót, amikor csatlakoznak. Vendég felhasználók közé tartoznak a más Azure-AD-k, Microsoft-fiókok, például outlook.com, hotmail.com, live.com vagy más fiókok, például a gmail.com meghívott felhasználók. Ez az információ lehetővé teszi, hogy az **Active Directory univerzális, MFA-hitelesítéssel** azonosítsa a megfelelő hitelesítő jogosultságot. Ez a beállítás a Microsoft-fiókok (MSA) (outlook.com, hotmail.com, live.com vagy nem MSA-fiókok) támogatásához is szükséges. Az univerzális hitelesítéssel hitelesíteni kívánt összes felhasználónak meg kell adnia az Azure AD-tartománynevét vagy bérlői azonosítóját. Ez a paraméter az Aktuális Azure AD-tartománynevet/-bérlőazonosítót jelöli, amelyhez az Azure Server kapcsolódik. Ha például az Azure Server olyan `contosotest.onmicrosoft.com` Azure `joe@contosodev.onmicrosoft.com` AD-tartományhoz van társítva, `contosodev.onmicrosoft.com`ahol a felhasználó az Azure `contosotest.onmicrosoft.com`AD-tartományból importált felhasználóként van elrendezve, akkor a felhasználó hitelesítéséhez szükséges tartománynév a . Ha a felhasználó az Azure Serverhez kapcsolódó Azure AD natív felhasználója, és nem MSA-fiók, nincs szükség tartománynévre vagy bérlői azonosítóra. A paraméter beírásához (az SSMS 17.2-es verziójától kezdve) a **Csatlakozás az adatbázishoz** párbeszédpanelen töltse ki a párbeszédpanelt, válassza az Active Directory **AD domain name or tenant ID** **– Univerzális MFA-hitelesítéssel** lehetőséget, kattintson a **Beállítások**gombra, töltse ki a **Felhasználónév** párbeszédpanelt, majd kattintson a Kapcsolat **tulajdonságai** fülre**contosotest.onmicrosoft.com.**  
   ![mfa-bérlő-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

Ha SSMS 18.x vagy újabb verziót futtat, akkor az AD tartománynév vagy a bérlői azonosító már nincs szükség a vendégfelhasználók számára, mert a 18.x vagy újabb verzió automatikusan felismeri azt.

   ![mfa-bérlő-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD üzleti támogatás   
Az Azure AD-felhasználók által támogatott Azure AD B2B forgatókönyvek vendégfelhasználók (lásd: [Mi az Azure B2B együttműködés)](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)csatlakozhat az SQL Database és az Azure Synapse `CREATE USER` csak a tagok egy csoport tagjai nak a jelenlegi Azure AD-ben létrehozott és manuálisan leképezve a Transact-SQL utasítás egy adott adatbázisban. Például ha `steve@gmail.com` meghívást kap az `contosotest` Azure AD -be (az Azure Ad-tartománnyal), `contosotest.onmicrosoft.com`egy Azure AD-csoportot, például `usergroup` létre kell hozni az Azure AD-ben, amely tartalmazza a `steve@gmail.com` tagot. Ezután ezt a csoportot létre kell hozni egy adott adatbázishoz (azaz a MyDatabase-hez) az Azure `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` AD SQL-rendszergazda vagy az Azure AD DBO által egy Transact-SQL utasítás végrehajtásával. Az adatbázis-felhasználó létrehozása után `steve@gmail.com` a felhasználó `MyDatabase` bejelentkezhet az `Active Directory – Universal with MFA support`SSMS hitelesítési lehetőséghasználatával. A felhasználói csoport alapértelmezés szerint csak a csatlakozási engedéllyel és minden további adathozzáféréssel rendelkezik, amelyet a szokásos módon kell megadni. Ne feledje, hogy a felhasználónak `steve@gmail.com` vendégfelhasználóként be `contosotest.onmicrosoft.com` kell jelölnie a jelölőnégyzetet, és hozzá kell adnia az AD tartománynevet az SSMS-kapcsolat **tulajdonsága** párbeszédpanelen. Az **AD tartománynév vagy a bérlői azonosító** beállítás csak az MFA-kapcsolat beállításaival rendelkező univerzális beállítás esetén támogatott, ellenkező esetben a szürke lesz.

## <a name="universal-authentication-limitations-for-sql-database-and-azure-synapse"></a>Univerzális hitelesítési korlátozások az SQL Database és az Azure Synapse számára
- Az SSMS és az SqlPackage.exe az egyetlen olyan eszköz, amely jelenleg engedélyezve van az MFA számára az Active Directory univerzális hitelesítésen keresztül.
- SSMS 17.2-es verziója, támogatja a többfelhasználós egyidejű hozzáférést az univerzális hitelesítés sel az MFA-val. A 17.0-s és 17.1-es verzió egyetlen Azure Active Directory-fiókra korlátozta az SSMS univerzális hitelesítést használó példányának bejelentkezését. A bejelentkezéshez egy másik Azure AD-fiókként, az SSMS egy másik példányát kell használnia. (Ez a korlátozás az Active Directory univerzális hitelesítésre korlátozódik; különböző kiszolgálókra jelentkezhet be active directory jelszó-hitelesítéssel, Active Directory integrált hitelesítéssel vagy SQL Server-hitelesítéssel).
- Az SSMS támogatja az Active Directory univerzális hitelesítést az Objektumkezelő, a Lekérdezésszerkesztő és a Lekérdezéstároló megjelenítéshez.
- Az SSMS 17.2-es verziója a DacFx Wizard számára támogatja az adatok exportálását/kinyerését/telepítését. Miután egy adott felhasználó hitelesítése az univerzális hitelesítést használó kezdeti hitelesítési párbeszédpanelen keresztül történik, a DacFx varázsló ugyanúgy működik, mint az összes többi hitelesítési módszer esetében.
- Az SSMS táblatervező nem támogatja az univerzális hitelesítést.
- Az Active Directory univerzális hitelesítéshez nincsenek további szoftverkövetelmények, kivéve, hogy az SSMS támogatott verzióját kell használnia.  
- Az Active Directory hitelesítési könyvtár (ADAL) verziója az univerzális hitelesítéshez a legújabb ADAL.dll 3.13.9 elérhető verzióra frissült. Lásd: [Active Directory hitelesítési könyvtár 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>További lépések

- A konfigurációs lépéseket az [Azure SQL Database többtényezős hitelesítéskonfigurálása az SQL Server Management Studio rendszerhez című témakörben tartalmazza.](sql-database-ssms-mfa-authentication-configure.md)
- Mások hozzáférésének engedélyezése az adatbázishoz: [SQL-adatbázis hitelesítése és engedélyezése: Hozzáférés megadása](sql-database-manage-logins.md)  
- Győződjön meg arról, hogy mások is csatlakozhatnak a tűzfalon keresztül: [Konfiguráljon egy Azure SQL Database-kiszolgálószintű tűzfalszabályt az Azure Portal használatával](sql-database-configure-firewall-settings.md)  
- [Az Azure Active Directory-hitelesítés konfigurálása és kezelése SQL Database vagy Azure Synapse segítségével](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server adatréteg-alkalmazáskeretrendszer (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [BACPAC-fájl importálása új Azure SQL-adatbázisba](../sql-database/sql-database-import.md)  
- [Azure SQL-adatbázis exportálása BACPAC-fájlba](../sql-database/sql-database-export.md)  
- C# felület [IUniversalAuthProvider felület](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Ha **Az Active Directory-Universal-t MFA-hitelesítéssel** használja, az ADAL nyomkövetés az [SSMS 17.3-as](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)verziótól kezdődik. Alapértelmezés szerint az ADAL-nyomkövetést az **Eszközök**, **Beállítások** menü, az **Azure Services**, az **Azure Cloud**, az **ADAL kimeneti ablak nyomkövetési szintje**, majd a **Nézet** menü **kimenetengedélyezése** segítségével kapcsolhatja be. A nyomkövetések az **Azure Active Directory beállítás**kiválasztásakor érhetők el a kimeneti ablakban.  
