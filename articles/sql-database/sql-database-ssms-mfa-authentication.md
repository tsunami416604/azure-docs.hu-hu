---
title: Multi-factor Authentication AAD-hitelesítés használata Azure SQL Database és az Azure SQL Data Warehouse |} A Microsoft Docs
description: Az Azure SQL Database és Azure SQL Data Warehouse támogatja a kapcsolatot az SQL Server Management Studio (SSMS) Active Directory univerzális hitelesítéssel.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 10/08/2018
ms.openlocfilehash: ccb78e201b90dfc27f52523348e76da57087bcc8
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494901"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>Multi-factor Authentication AAD-hitelesítés használata Azure SQL Database és az Azure SQL Data Warehouse (többtényezős hitelesítés támogatása ssms-ben)
Az Azure SQL Database és Azure SQL Data Warehouse támogatja az SQL Server Management Studio (SSMS) használatával érkező kapcsolatokat *Active Directory univerzális hitelesítéssel*. Ez a cikk ismerteti a különböző hitelesítési beállítások, valamint a társított univerzális hitelesítéssel korlátozások közötti különbségeket. 

**Az ssms legújabb verziójának letöltése** – az ügyfélszámítógép az SSMS, legfrissebb verziójának letöltését [töltse le az SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Ebben a cikkben tárgyalt összes a funkciót használja legalább 2017. július 17.2 verzió.  A legutóbbi kapcsolat párbeszédpanel, az alábbi képhez hasonlóan kell kinéznie:
 
  ![1mfa-universal-csatlakozás](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "befejezi a felhasználónév mezőben.")  

## <a name="the-five-authentication-options"></a>Az öt hitelesítési beállítások  

Active Directory univerzális hitelesítéssel a két nem interaktív hitelesítési módszereket támogatja:
    - `Active Directory - Password` hitelesítés
    - `Active Directory - Integrated` hitelesítés

Nincsenek két nem interaktív hitelesítés modell, amely (ADO.NET, JDCB, ODC, stb.) számos különböző alkalmazásokban használható. Két módszer közül a felugró párbeszédpanel soha nem eredményez: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Az interaktív metódus egyben, amely támogatja az Azure multi-factor Authentication (MFA): 
- `Active Directory - Universal with MFA` 


Az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáférésekkel szemben, miközben a felhasználói igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát teszi lehetővé. Kínál az erős hitelesítés több egyszerű ellenőrzési lehetőség (telefonhívás, szöveges üzenet, intelligens kártyák PIN-kód vagy mobilalkalmazásbeli értesítés), amelyek közül a felhasználók tetszőlegesen választhatnak. Interaktív, az Azure AD MFA egy felugró párbeszédpanel érvényesítéshez eredményezhet.

A multi-factor Authentication szolgáltatás leírását lásd: [multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md).
A konfigurálás lépéseinek végrehajtásához tekintse meg a [konfigurálása az Azure SQL Database többtényezős hitelesítés az SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Az Azure AD tartományi neve vagy a bérlői azonosító paraméter   

Kezdve [SSMS verzió 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), felhasználókat más címtárakból az Azure Active vendégfelhasználók, mint az aktuális Active Directory-ba importált az Azure AD-tartomány nevét adja meg, vagy bérlői Azonosítót, amikor csatlakoznak. Vendégfelhasználók más Azure-hirdetések, Microsoft-fiókok, például az outlook.com, hotmail.com, live.com vagy más fiókokon, például gmail.com meghívott felhasználók közé tartozik. Ezt az információt, lehetővé teszi, hogy **Active Directory univerzális MFA hitelesítéssel** a helyes hitelesítő hatóság azonosításához. Ez a beállítás akkor is szükség, hogy a Microsoft-fiókok (MSA) például az outlook.com, hotmail.com, live.com vagy nem MSA-fiókok. Ezek az univerzális hitelesítéssel hitelesíteni kívánó felhasználóknak meg kell adnia az Azure AD tartománynevet, vagy bérlői azonosító. A paraméter jelöli az aktuális Azure AD tartományi neve/bérlő azonosítója az Azure-kiszolgáló van csatolva. Például, ha az Azure-kiszolgálóval társítva az Azure AD-tartomány `contosotest.onmicrosoft.com` ahol felhasználói `joe@contosodev.onmicrosoft.com` üzemel az Azure AD-tartomány az importált felhasználók `contosodev.onmicrosoft.com`, a tartománynév, a felhasználó hitelesítéséhez szükséges `contosotest.onmicrosoft.com`. Amikor a felhasználó egy natív Azure-kiszolgálóhoz társított Azure AD-felhasználó, és nem MSA-fiókkal, nem tartományi felhasználónév vagy a bérlői azonosító nem szükséges. Adja meg a paramétert (az ssms-ben verzió 17.2 kezdete), hogy a **csatlakozhat az adatbázishoz** párbeszédpanelen töltse ki a párbeszédpanelt, kiválasztásával **Active Directory - Universal az MFA** hitelesítést, kattintson **Beállítások**befejeződött, a **felhasználónév** mezőbe, és kattintson a **kapcsolat tulajdonságai** lapon. Ellenőrizze a **AD tartomány felhasználónév vagy a bérlői azonosító** mezőbe, majd adja meg a található hitelesítő hatóság, például a tartomány nevét (**contosotest.onmicrosoft.com**) vagy GUID azonosítóját a bérlő azonosítója.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Az Azure AD vállalatközi-támogatás   
Azure AD-felhasználók Azure AD B2B-forgatókönyvekhez vendégként támogatott (lásd: [Mi az Azure B2B-együttműködés](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) kapcsolódhatnak az SQL Database és SQL Data Warehouse egy jelenlegi Azure AD-ben létrehozott és a csatlakoztatott manuálisan csoport tagjai részeként a Transact-SQL használatával `CREATE USER` utasítás egy adott adatbázisban. Például ha `steve@gmail.com` meghívtak az Azure AD `contosotest` (az Azure Ad-tartományt a `contosotest.onmicrosoft.com`), az Azure AD-csoportnak, például `usergroup` kell létrehozni, amely tartalmazza az Azure AD-ben a `steve@gmail.com` tag. Ezután ezt a csoportot létre kell hozni egy adott adatbázishoz (azaz MyDatabase) az Azure AD-SQL-rendszergazda vagy az Azure AD DBO úgy, hogy végrehajtja a Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` utasítást. Az adatbázis-felhasználót, majd a felhasználó létrehozása után `steve@gmail.com` jelentkezhet be `MyDatabase` az SSMS hitelesítési lehetőséggel `Active Directory – Universal with MFA support`. A felhasználói csoport alapértelmezés szerint csak a connect engedély és rendelkezik minden olyan további adatokhoz való hozzáférést, amelyeket a megszokott módon biztosítani kell. Vegye figyelembe, hogy a felhasználó `steve@gmail.com` vendégfelhasználó kell jelölje be a jelölőnégyzetet, és adja hozzá az AD tartománynevet `contosotest.onmicrosoft.com` az az ssms-ben **kapcsolati tulajdonság** párbeszédpanel bezárásához. A **AD tartomány felhasználónév vagy a bérlői azonosító** a beállítás csak a támogatott az univerzális MFA kapcsolati beállításokkal, ellenkező esetben szürkén jelenik meg.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Az SQL Database és az SQL Data Warehouse univerzális hitelesítés korlátozások
- SSMS és az SqlPackage.exe az egyetlen eszközök jelenleg engedélyezve van az Active Directory univerzális hitelesítéssel keresztül MFA-hoz.
- SSMS verzió 17.2, többfelhasználós egyidejű hozzáférés univerzális hitelesítéssel az MFA támogatja. Verzió 17.0 és 17.1, bejelentkezési korlátozva az SSMS egy Azure Active Directory-fiókhoz univerzális hitelesítéssel-példányok esetében. Jelentkezzen be egy másik Azure AD-fiókot, egy másik példánya ssms-t kell használnia. (Ez a korlátozás az Active Directory univerzális hitelesítéssel korlátozódik, különböző kiszolgálókra az Active Directory jelszavas hitelesítés, Active Directory integrált hitelesítést vagy SQL Server-hitelesítés használatával bejelentkezhet).
- SSMS támogatja az Active Directory univerzális hitelesítéssel az Object Explorer, Query-szerkesztő és Query Store képi megjelenítését.
- SSMS verzió 17.2 DacFx varázsló támogatást nyújt exportálási/kivonat/üzembe helyezés adatok adatbázis. Miután egy adott felhasználó hitelesítését univerzális hitelesítéssel, a DacFx varázsló funkciókat hajtja végre az összes hitelesítési módszert ugyanúgy kezdeti hitelesítési párbeszédpanelen keresztül.
- Az SSMS Táblatervező nem támogatja az univerzális hitelesítéssel.
- Nem vonatkoznak további szoftverfrissítési követelmények az Active Directory univerzális hitelesítéssel azzal a különbséggel, hogy az ssms-ben támogatott verzióját kell használnia.  
- A univerzális hitelesítéssel az Active Directory Authentication Library (ADAL) verzióra frissült a legújabb ADAL.dll 3.13.9 elérhető kiadott verzióját. Lásd: [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>További lépések

- A konfigurálás lépéseinek végrehajtásához tekintse meg a [konfigurálása az Azure SQL Database többtényezős hitelesítés az SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Az adatbázishoz való hozzáférés biztosítása más: [Az SQL Database hitelesítése és engedélyezése: Hozzáférés biztosítása](sql-database-manage-logins.md)  
- Győződjön meg arról, hogy mások csatlakozhatnak a tűzfalon keresztül: [Konfigurálhatja egy Azure SQL Database kiszolgálószintű tűzfalszabályt az Azure portal használatával](sql-database-configure-firewall-settings.md)  
- [Konfigurálhatja és kezelheti az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [A Microsoft SQL Server Adatrétegbeli alkalmazási keretrendszer (17.0.0 a végleges verzió)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Az új Azure SQL-adatbázis BACPAC-fájl importálása](../sql-database/sql-database-import.md)  
- [Azure SQL-adatbázis exportálása BACPAC-fájlba](../sql-database/sql-database-export.md)  
- C# felület [IUniversalAuthProvider felület](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Használata esetén **Active Directory - univerzális az MFA** hitelesítés ADAL nyomkövetés kiadásától kezdve érhetőek el [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Ki alapértelmezés szerint bekapcsolása ADAL nyomkövetésének használatával az **eszközök**, **beállítások** menü alatt **Azure-szolgáltatások**, **Azure felhő**,  **Nyomkövetési szint ADAL kimeneti ablak**, amely lehetővé teszi majd **kimeneti** a a **nézet** menü. A nyomkövetések érhetők el a kimeneti ablakban kiválasztásakor **Azure Active Directoryval opciót**.  
