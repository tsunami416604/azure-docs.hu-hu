---
title: A multi-factor authentication - Azure SQL |} Microsoft Docs
description: "Az Azure SQL Database és az Azure SQL Data Warehouse támogatja az SQL Server Management Studio (SSMS) kapcsolatok hitelesítéssel Active Directory univerzális."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/29/2017
ms.author: rickbyh
ms.openlocfilehash: cdf78a3c64933f5f01642d5ef8fe1fca64cb1fbb
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Univerzális hitelesítés használata az SQL-adatbázis és az SQL Data Warehouse (többtényezős hitelesítés támogatása SSMS)
Az Azure SQL Database és az Azure SQL Data Warehouse támogatja az SQL Server Management Studio (SSMS) használatával kapcsolatok *Active Directory univerzális hitelesítési*. 
**Töltse le a legfrissebb SSMS** – az ügyfélszámítógépen SSMS, legfrissebb verziójának letöltését [töltse le az SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Az összes funkcióját ebben a témakörben használhat legalább július 2017, 17.2 verziója.  A legutóbbi kapcsolat párbeszédpanel néz ki: ![1mfa-universal-csatlakozás](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "befejezi a felhasználónév mező.")  

## <a name="the-five-authentication-options"></a>Az öt hitelesítési beállítások  
- Az Active Directory univerzális hitelesítése a két nem interaktív hitelesítési módszereket támogatja (`Active Directory - Password` hitelesítési és `Active Directory - Integrated` hitelesítés). Nem interaktív `Active Directory - Password` és `Active Directory - Integrated` hitelesítési módszereket a különböző alkalmazások (ADO.NET JDBC, ODBC, stb.) használható. A két módszerről soha nem eredményez előugró párbeszédpanelen.

- `Active Directory - Universal with MFA`a hitelesítés egy interaktív módszerhez is támogatja az *Azure multi-factor Authentication* (MFA). Az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáférésekkel szemben, miközben a felhasználói igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát teszi lehetővé. Egyszerű hitelesítési beállítások (telefonhívás, szöveges üzenetet, intelligens kártyák PIN-kód és az értesítést a mobilalkalmazásban), számos erős hitelesítés biztosítja lehetővé teszi a felhasználók a módszerek közötti választást inkább. Az Azure ad-val interaktív MFA érvényesítéshez előugró párbeszédpanelen eredményezhet.

A multi-factor Authentication ismertetését lásd: [multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
A konfigurálás lépéseinek végrehajtásához tekintse meg a [konfigurálása az Azure SQL Database multi-factor authentication szolgáltatást az SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Az Azure AD tartományi nevét vagy a bérlői azonosító paraméter   

Kezdve [SSMS verzió 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), felhasználókat más címtárakból Azure Active vendégfelhasználók, mint az aktuális Active Directory szolgáltatásba importált is az Azure Active Directory-tartomány nevét, vagy bérlői azonosító csatlakozáshoz. Vendégfelhasználók más Azure ADs, Microsoft-fiókok például outlook.com, hotmail.com, live.com vagy más hasonló gmail.com fiókok meghívott felhasználók tartalmazza. Ezt az információt, lehetővé teszi, hogy **Active Directory univerzális MFA hitelesítéssel** a helyes hitelesítő hatóság azonosításához. Ezt a beállítást is szükség, hogy a Microsoft-fiókok (msa-t) például az Outlook.com-os, hotmail.com, live.com vagy nem MSA-fiókok. Ezek az univerzális hitelesítéssel hitelesíteni kívánó felhasználóknak meg kell adnia az Azure AD-tartomány nevét, vagy bérlői azonosítóját. A paraméter jelöli az aktuális Azure AD tartományi neve/bérlő azonosítója az Azure-kiszolgáló kapcsolódik. Például, ha Azure-kiszolgáló Azure AD-tartomány tartozik `contosotest.onmicrosoft.com` ahol felhasználói `joe@contosodev.onmicrosoft.com` importált az Azure AD tartományi felhasználóként van-e tárolva `contosodev.onmicrosoft.com`, a tartománynév, a felhasználó hitelesítéséhez szükséges `contosotest.onmicrosoft.com`. Amikor a felhasználó egy natív Azure Server csatolva az Azure AD-felhasználó, és egy nem Felügyelt fiók, nem tartományi nevét vagy a bérlői azonosító megadása kötelező. Adja meg a paraméter (SSMS 17.2 verziójával kezdődően), hogy a **adatbázis kapcsolódás** párbeszédpanel a párbeszédpanelen adja kiválasztása **Active Directory - többtényezős hitelesítéssel univerzális** hitelesítési, kattintson **Beállítások**befejeződött, a **felhasználónév** mezőbe, és kattintson a **kapcsolat tulajdonságai** fülre. Ellenőrizze a **AD tartomány neve vagy a bérlői azonosító** mezőbe, majd adja meg a hitelesítő hatóság, például a tartomány nevét (**contosotest.onmicrosoft.com**) vagy GUID azonosítója a bérlő azonosítója.  
   ![MFA-bérlő-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Az Azure AD a vállalatok támogatás   
Az Azure Active Directory-felhasználók támogatott az Azure AD B2B forgatókönyvek vendégként (lásd: [Mi az az Azure B2B együttműködés](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) csatlakozni tud-e az SQL-adatbázis és az SQL Data Warehouse hozott létre az aktuális az Azure AD és a csatlakoztatott manuálisan egy csoport részeként a Transact-SQL használatával `CREATE USER` utasításban megadott adatbázisban. Például ha `steve@gmail.com` felkérik, hogy az Azure AD `contosotest` (az Azure Ad-tartomány `contosotest.onmicrosoft.com`), az Azure AD-csoport, például a `usergroup` kell létrehozni, amely tartalmazza az Azure AD-ben a `steve@gmail.com` tag. Ezután ezt a csoportot kell létrehoznia egy adott adatbázis (azaz adatbázis) Azure AD az SQL-rendszergazdák vagy az Azure AD DBO hajtja végre a Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` utasítást. Az adatbázis-felhasználó, majd a felhasználó létrehozása után `steve@gmail.com` bejelentkezhet `MyDatabase` az SSMS hitelesítés beállításával `Active Directory – Universal with MFA support`. A felhasználói csoport alapértelmezés szerint csak a connect engedély és rendelkezik, amely a szokásos módon biztosítani kell további adatok elérését. Vegye figyelembe, hogy a felhasználó `steve@gmail.com` a Vendég felhasználó kell jelölje be a jelölőnégyzetet, és adja hozzá az Active Directory-tartomány nevét `contosotest.onmicrosoft.com` a szolgáltatáshoz az ssms **Connection tulajdonság** párbeszédpanel megnyitásához. A **AD tartomány neve vagy a bérlői azonosító** csak esetében lehetőség az univerzális MFA kapcsolati beállításokkal, ellenkező esetben szürkén jelenik meg.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>SQL-adatbázis és az SQL Data Warehouse univerzális hitelesítési korlátozásai
- SSMS és SqlPackage.exe a csak eszközök jelenleg engedélyezve van a többtényezős hitelesítés az Active Directory univerzális hitelesítésen keresztül.
- SSMS verzió 17.2, többfelhasználós egyidejű hozzáférés univerzális hitelesítéssel az MFA Használatát támogatja. 17.0 és 17.1, a napló korlátozza az SSMS egy egyetlen Azure Active Directory-fiókot univerzális hitelesítéssel-példányok esetében. Jelentkezzen be egy másik Azure AD-fiókot, az SSMS egy másik példányt kell használnia. (Ez a korlátozás az Active Directory univerzális hitelesítése korlátozódik; is jelentkezik be a különböző kiszolgálókra az Active Directory jelszavas hitelesítést, Active Directory integrált hitelesítést vagy SQL Server-hitelesítés).
- Szolgáltatáshoz az SSMS Object Explorer, a lekérdezés-szerkesztő és a Lekérdezéstár a képi megjelenítéshez tartozó Active Directory univerzális hitelesítést támogatja.
- SSMS verzió 17.2 DacFx varázsló támogatást nyújt exportálási/kivonat/központi telepítése az adatbázist. Ha egy adott felhasználó univerzális hitelesítéssel, a varázsló DacFx funkciók a mint minden más hitelesítési módszerek kezdeti hitelesítési párbeszédpaneléről hitelesítése megtörtént.
- Az SSMS tábla Tervező nem támogatja a univerzális hitelesítést.
- Nincsenek további szoftvereket az Active Directory univerzális hitelesítést azzal a különbséggel, hogy az SSMS támogatott verzióját kell használnia.  
- A Active Directory Authentication Library (ADAL) verzióját az univerzális hitelesítéshez frissült a legújabb ADAL.dll 3.13.9 elérhető hivatalosan kiadott verzió. Lásd: [Active Directory hitelesítési könyvtár 3.14.1](http://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Következő lépések

- A konfigurálás lépéseinek végrehajtásához tekintse meg a [konfigurálása az Azure SQL Database multi-factor authentication szolgáltatást az SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Az adatbázishoz való hozzáférés engedélyezése mások: [SQL adatbázis-hitelesítés és engedélyezés: hozzáférés biztosítása](sql-database-manage-logins.md)  
- Győződjön meg arról, hogy mások a tűzfalon keresztül csatlakozhassanak: [az Azure portál használata az Azure SQL Database kiszolgálószintű tűzfal szabály konfigurálása](sql-database-configure-firewall-settings.md)  
- [Konfigurálhatja és kezelheti az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Adatrétegbeli alkalmazási keretrendszer (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)  
- [Új Azure SQL-adatbázis BACPAC fájl importálása](../sql-database/sql-database-import.md)  
- [Azure SQL-adatbázis BACPAC fájlba exportálása](../sql-database/sql-database-export.md)  
- C# felület [IUniversalAuthProvider felület](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Használata esetén **Active Directory - univerzális többtényezős hitelesítéssel** hitelesítés ADAL nyomkövetés elérhető kezdődő [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Ki alapértelmezés szerint, ha bekapcsolja a ADAL nyomkövetési használatával a **eszközök**, **beállítások** menüben, a **Azure Services**, **Azure felhőbe**,  **ADAL kimeneti ablak nyomkövetési szint**, utána pedig engedélyezése **kimeneti** a a **nézet** menü. A nyomkövetési adatokat érhetők el a kimeneti ablakban lehetőséget választva **Azure Active Directory lehetőséget**.  
