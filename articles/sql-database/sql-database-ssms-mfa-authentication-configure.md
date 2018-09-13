---
title: Konfigurálja a multi-factor authentication – Azure SQL |} A Microsoft Docs
description: Útmutató az ssms-ben az SQL Database és az SQL Data Warehouse Multi-Factored hitelesítést használ.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.prod_service: sql-database, sql-data-warehouse
ms.custom: security
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: mireks
ms.reviewer: vanto
ms.openlocfilehash: 504b20dfddb5984c17bba9842fbc1a08671c4175
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719035"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Az SQL Server Management Studio és az Azure AD multi-factor authentication konfigurálása

Ez a témakör bemutatja, hogyan használható az Azure Active Directory többtényezős hitelesítés (MFA) az SQL Server Management Studióval. Az Azure AD MFA használható, amikor ssms-ben vagy az SqlPackage.exe csatlakozik Azure [SQL Database](sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Azure SQL Database a multi-factor authentication szolgáltatás áttekintését lásd: [univerzális hitelesítés az SQL Database és az SQL Data warehouse-ba (többtényezős hitelesítés támogatása SSMS)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

## <a name="configuration-steps"></a>Konfigurációs lépések

1. **Egy Azure Active Directory konfigurálása** – további információkért lásd: [az Azure AD-címtár felügyelete](https://msdn.microsoft.com/library/azure/hh967611.aspx), [a helyszíni identitások integrálása az Azure Active Directory](../active-directory/active-directory-aadconnect.md), [ A saját tartománynév hozzáadása az Azure ad-ben](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonás](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), és [kezelése Windows PowerShell-lel az Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Multi-factor Authentication** – részletes útmutatásért lásd: [Mi az Azure multi-factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [feltételes hozzáférés (többtényezős hitelesítés) az Azure SQL Database és az adatraktár](sql-database-conditional-access.md). (Teljes feltételes hozzáférés szükséges egy prémium szintű Azure Active Directory (Azure AD). Korlátozott MFA érhető el egy standard szintű Azure AD-vel.)
3. **Az SQL Database vagy az SQL Data Warehouse az Azure AD-hitelesítés konfigurálása** – részletes útmutatásért lásd: [SQL Database vagy az SQL Data Warehouse által használatával az Azure Active Directory-hitelesítéssel csatlakozik](sql-database-aad-authentication.md).
4. **Töltse le az SSMS** – az ügyfélszámítógépen, az ssms legújabb verziójának letöltése [töltse le az SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Az összes funkciót ebben a témakörben használja legalább 17.2 verziója 2017. július.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Csatlakozás szolgáltatáshoz az ssms-ben univerzális hitelesítéssel

A következő lépések bemutatják, hogyan csatlakozhat az SQL Database vagy az SQL Data Warehouse az ssms legújabb verziójának használatával.

1. Univerzális hitelesítéssel, érdemesebb a a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen jelölje ki **Active Directory - MFA-támogatással rendelkező univerzális**. (Ha **Active Directory univerzális hitelesítéssel** Ön nem az SSMS legújabb verziója található.)  
   ![1mfa-universal-connect][1]  
2. Végezze el a **felhasználónév** be az Azure Active Directory hitelesítő adatokkal, a következő formátumban `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Ha meg vendégként kapcsolódik, akkor a **beállítások**, majd a a **kapcsolati tulajdonság** párbeszédpanelen adja a **AD tartomány felhasználónév vagy a bérlői azonosító** mezőbe. További információkért lásd: [univerzális hitelesítés az SQL Database és az SQL Data warehouse-ba (többtényezős hitelesítés támogatása SSMS)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. A szokásos módon az SQL Database és SQL Data warehouse-ba, kattintson **beállítások** és adja meg az adatbázis a **beállítások** párbeszédpanel bezárásához. (Ha a csatlakoztatott felhasználó vendégfelhasználó (azaz joe@outlook.com), kell jelölje be a jelölőnégyzetet, és adja hozzá a jelenlegi AD-tartomány nevét, vagy bérlőazonosító beállítások részeként. Lásd: [univerzális hitelesítés az SQL Database és az SQL Data warehouse-ba (többtényezős hitelesítés támogatása SSMS)](sql-database-ssms-mfa-authentication.md). Ezután kattintson a **Csatlakozás** gombra.  
5. Ha a **bejelentkezni a fiókjába** párbeszédpanel jelenik meg, adja meg a fiók és jelszó az Azure Active Directory-identitás. Jelszó nélküli nem szükséges, ha egy felhasználó része az Azure ad-vel összevont tartománynak.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Egy olyan fiókkal, amely nem szükséges az MFA univerzális hitelesítéssel, a csatlakozás ezen a ponton. A felhasználóknak a többtényezős hitelesítés megkövetelése folytassa a következő lépéseket:
   >  
   
6. Két MFA beállítása párbeszédpanel jelenhet meg. Ezúttal egy művelet függ, hogy a beállítás az MFA-rendszergazda, és ezért előfordulhat, hogy ne legyen kötelező. Tartomány MFA engedélyezve van ez a lépés néha nem előre meghatározott (például a tartomány felhasználóinak egy intelligens kártyák és PIN-kód szükséges).  
   ![3mfa-telepítés][3]  
7. A második lehetséges párbeszédpanel részletei a hitelesítési módszer kiválasztását teszi egy alkalommal. A lehetőségeket vannak konfigurálva, a rendszergazda által.  
   ![4mfa-verify-1][4]  
8. Az Azure Active Directory, a jóváhagyási adatokat küld. Ha megkapta az ellenőrzőkódot, írja be azt a **adja meg az ellenőrző kód** mezőbe, majd kattintson a **jelentkezzen be a**.  
   ![5mfa-verify-2][5]  

Ellenőrzési befejeződése után az SSMS kapcsolódik, általában feltéve, hogy érvényes hitelesítő adatokat és a tűzfal hozzáférés.

## <a name="next-steps"></a>További lépések

- Azure SQL Database a multi-factor authentication szolgáltatás áttekintését lásd: univerzális hitelesítéssel rendelkező [SQL Database és az SQL Data warehouse-ba (többtényezős hitelesítés támogatása SSMS)](sql-database-ssms-mfa-authentication.md).  
- Az adatbázishoz való hozzáférés biztosítása más: [SQL Database hitelesítése és engedélyezése: hozzáférés biztosítása](sql-database-manage-logins.md)  
- Ellenőrizze, hogy mások csatlakozhatnak a tűzfalon: [konfigurálni egy Azure SQL Database kiszolgálószintű tűzfalszabályt az Azure portal használatával](sql-database-configure-firewall-settings.md)  
- Használata esetén **Active Directory - univerzális az MFA** hitelesítés ADAL nyomkövetés kiadásától kezdve érhetőek el [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Ki alapértelmezés szerint bekapcsolása ADAL nyomkövetésének használatával az **eszközök**, **beállítások** menü alatt **Azure-szolgáltatások**, **Azure felhő**,  **Nyomkövetési szint ADAL kimeneti ablak**, amely lehetővé teszi majd **kimeneti** a a **nézet** menü. A nyomkövetések érhetők el a kimeneti ablakban kiválasztásakor **Azure Active Directoryval opciót**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

