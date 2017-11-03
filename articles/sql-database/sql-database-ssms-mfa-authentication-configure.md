---
title: "A többtényezős hitelesítést - Azure SQL |} Microsoft Docs"
description: "Útmutató az SQL-adatbázis és az SQL Data Warehouse szolgáltatáshoz az SSMS Multi-Factored-hitelesítés használatára."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/27/2017
ms.author: rickbyh
ms.openlocfilehash: a6895f7a145c7b925703e4deb32411d51e7a3cab
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Az SQL Server Management Studio és az Azure AD többtényezős hitelesítés beállítása

Ez a témakör bemutatja, hogyan használható az Azure Active Directory többtényezős hitelesítés (MFA) az SQL Server Management Studio. Az Azure AD MFA használható SSMS vagy SqlPackage.exe Azure SQL Database és az Azure SQL Data warehouse-ba történő csatlakozás során.

Többtényezős hitelesítés az Azure SQL Database áttekintését lásd: [univerzális hitelesítés használata az SQL-adatbázis és az SQL Data Warehouse (többtényezős hitelesítés támogatása SSMS)](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Konfigurációs lépések

1. **Egy Azure Active Directory konfigurálása** – további információért lásd: [az Azure AD-címtár felügyelete](https://msdn.microsoft.com/library/azure/hh967611.aspx), [a helyszíni identitások integrálása az Azure Active Directoryval](../active-directory/active-directory-aadconnect.md), [Saját tartománynév hozzáadása az Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure mostantól támogatja a Windows Server Active Directory összevonási](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), és [kezelése az Azure AD a Windows PowerShell használatával ](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Többtényezős hitelesítés beállítása** – részletes ismertetését lásd: [Mi az Azure multi-factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md), [feltételes hozzáférést (többtényezős hitelesítés) az Azure SQL adatbázishoz és Adatraktárhoz](sql-database-conditional-access.md). (Teljes feltételes hozzáférés szükséges egy prémium szintű Azure Active Directory (Azure AD). Korlátozott MFA érhető el a szabványos Azure AD-val.)
3. **Konfigurálja az SQL Database vagy az SQL Data Warehouse az Azure AD-alapú hitelesítés** – részletes ismertetését lásd: [csatlakozás az SQL Database vagy az SQL Data Warehouse által használata Azure Active Directory-hitelesítéssel](sql-database-aad-authentication.md).
4. **Töltse le a szolgáltatáshoz az SSMS** – az ügyfélszámítógépen, a legújabb SSMS letöltését [töltse le az SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Az összes funkcióját ebben a témakörben használhat legalább július 2017, 17.2 verziója.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Csatlakozás univerzális hitelesítési szolgáltatáshoz az SSMS

A következő lépések bemutatják, hogyan csatlakozás az SQL Database vagy az SQL Data Warehouse a legújabb szolgáltatáshoz az SSMS használatával.

1. Univerzális hitelesítés használatát, a csatlakozás a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen jelölje ki **Active Directory - MFA-támogatással rendelkező univerzális**. (Ha megjelenik **Active Directory univerzális hitelesítési** SSMS legújabb verzióját a rendszer nem.)  
   ![1mfa-universal-csatlakozás][1]  
2. Fejezze be a **felhasználónév** be az Azure Active Directory hitelesítő adatokkal, formátumú `user_name@domain.com`.  
   ![1mfa-universal-csatlakozás-felhasználó](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Ha a Vendég felhasználóként csatlakozik, akkor a **beállítások**, és a a **Connection tulajdonság** párbeszédpanelen adja a **AD tartomány neve vagy a bérlői azonosító** mezőbe. További információkért lásd: [univerzális hitelesítés használata az SQL-adatbázis és az SQL Data Warehouse (többtényezős hitelesítés támogatása SSMS)](sql-database-ssms-mfa-authentication.md).
   ![MFA-bérlő-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. A szokásos módon SQL Database és az SQL Data Warehouse, meg kell nyomnia **beállítások** , és adja meg az adatbázis a **beállítások** párbeszédpanel megnyitásához. (Ha a kapcsolódó felhasználó a Vendég felhasználói (azaz joe@outlook.com), kell jelölje be a jelölőnégyzetet, és adja hozzá az aktuális Active Directory-tartomány nevét, vagy bérlői azonosító beállítások részeként. Lásd: [univerzális hitelesítés használata az SQL-adatbázis és az SQL Data Warehouse (többtényezős hitelesítés támogatása SSMS)]()(sql-adatbázis-ssms-mfa-authentication.md. Kattintson a **Connect**.  
5. Ha a **jelentkezzen be a fiókba** párbeszédpanel jelenik meg, a fiók és az Azure Active Directory-identitás jelszavát. Nem kell jelszót szükség, ha egy felhasználó egy Azure AD-val összevont tartomány része.  
   ![2mfa-bejelentkezés][2]  

   > [!NOTE]
   > Egy olyan fiókkal, többtényezős Hitelesítést nem igénylő univerzális hitelesítéshez csatlakozás ezen a ponton. A felhasználók számára a többtényezős hitelesítés megkövetelése folytassa a következő lépésekkel:
   >  
   
6. Két többtényezős hitelesítés beállítása párbeszédpanel jelenhet meg. Egyetlen most művelet MFA rendszergazda függ, és ezért nem kötelező. Egy engedélyezve van az MFA-tartomány ezt a lépést néha nem előre definiált (például a tartomány megköveteli a felhasználóktól egy intelligens kártyák és PIN-kód használatára).  
   ![3mfa-telepítő][3]  
7. A második lehetséges párbeszédpanelen jelölje be a hitelesítési módszer adatait teszi egy alkalommal. A lehetséges beállításokat a rendszergazda által.  
   ![4mfa ellenőrzése 1][4]  
8. Az Azure Active Directory a információkkal küld Önnek. Ha megkapta az ellenőrzőkódot, írja be azokat a **adja meg a megerősítési kódot** mezőbe, majd kattintson **jelentkezzen be a**.  
   ![5mfa ellenőrzése 2][5]  

Ha ellenőrzés befejeződött, az SSMS csatlakozik, általában feltéve, hogy érvényes hitelesítő adatokat és a tűzfalon a hozzáférést.

## <a name="next-steps"></a>Következő lépések

- Többtényezős hitelesítés az Azure SQL Database áttekintését lásd: az univerzális hitelesítési [SQL-adatbázis és az SQL Data Warehouse (többtényezős hitelesítés támogatása SSMS)](sql-database-ssms-mfa-authentication.md).  
- Az adatbázishoz való hozzáférés engedélyezése mások: [SQL adatbázis-hitelesítés és engedélyezés: hozzáférés biztosítása](sql-database-manage-logins.md)  
- Győződjön meg arról, hogy mások a tűzfalon keresztül csatlakozhassanak: [az Azure portál használata az Azure SQL Database kiszolgálószintű tűzfal szabály konfigurálása](sql-database-configure-firewall-settings.md)  
- Használata esetén **Active Directory - univerzális többtényezős hitelesítéssel** hitelesítés ADAL nyomkövetés elérhető kezdődő [SSMS 17.3](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). Ki alapértelmezés szerint, ha bekapcsolja a ADAL nyomkövetési használatával a **eszközök**, **beállítások** menüben, a **Azure Services**, **Azure felhőbe**,  **ADAL kimeneti ablak nyomkövetési szint**, utána pedig engedélyezése **kimeneti** a a **nézet** menü. A nyomkövetési adatokat érhetők el a kimeneti ablakban lehetőséget választva **Azure Active Directory lehetőséget**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

