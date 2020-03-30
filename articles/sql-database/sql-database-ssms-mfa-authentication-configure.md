---
title: Többtényezős hitelesítés konfigurálása
description: Megtudhatja, hogyan használható a többtényezős hitelesítés az SSMS-rel az SQL Database és az SQL Data Warehouse számára.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 5d4d410f6fca566dab14e601972952b5996c331a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124885"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Többtényezős hitelesítés konfigurálása az SQL Server Management Studio és az Azure AD számára

Ez a témakör bemutatja, hogyan használhatja az Azure Active Directory többtényezős hitelesítés (MFA) az SQL Server Management Studio használatával. Az Azure AD MFA használható az SSMS vagy az SqlPackage.exe Azure [SQL Database](sql-database-technical-overview.md) és SQL Data Warehouse szolgáltatáshoz való [csatlakoztatásakor.](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Az Azure SQL Database többtényezős hitelesítésének áttekintését az [Univerzális hitelesítés SQL-adatbázissal és az SQL Data Warehouse -val (SSMS támogatása az MFA-hoz)](sql-database-ssms-mfa-authentication.md)című témakörben találja.

> [!NOTE]
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

## <a name="configuration-steps"></a>Konfigurációs lépések

1. **Azure Active Directory konfigurálása** – További információ: [Az Azure AD-címtár felügyelete](https://msdn.microsoft.com/library/azure/hh967611.aspx), A helyszíni [identitások integrálása az Azure Active Directoryval](../active-directory/hybrid/whatis-hybrid-identity.md)című témakörben, [saját tartománynév hozzáadása az Azure AD-hez](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [a Microsoft Azure mostantól támogatja a Windows Server Active Directoryösszevonást,](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)valamint [az Azure AD kezelése a Windows PowerShell használatával című témakört.](https://msdn.microsoft.com/library/azure/jj151815.aspx)
2. **Az MFA konfigurálása** – Részletes utasítások: Mi az Azure [Conditional Access (MFA) with Azure SQL Database and Data Warehouse](sql-database-conditional-access.md) [többtényezős hitelesítése?](../active-directory/authentication/multi-factor-authentication.md) (A teljes feltételes hozzáféréshez prémium szintű Azure Active Directory (Azure AD) szükséges. Korlátozott Többszintű szolgáltatás érhető el egy szabványos Azure AD.)
3. **Sql Database vagy SQL Data Warehouse konfigurálása az Azure AD-hitelesítéshez** – Részletes útmutatásért olvassa el [az Sql Database vagy az SQL Data Warehouse csatlakoztatása az Azure Active Directory-hitelesítés használatával című](sql-database-aad-authentication.md)témakört.
4. **Letöltés SSMS** - Az ügyfélszámítógépen, töltse le a legújabb SSMS, [a Letöltés SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). A témakör összes szolgáltatása esetén legalább 2017 júliusa, 17.2-es verzió használható.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Csatlakozás univerzális hitelesítéssel az SSMS-rel

A következő lépések bemutatják, hogyan csatlakozhat az SQL Database vagy az SQL Data Warehouse a legújabb SSMS használatával.

1. Ha univerzális hitelesítéssel szeretne csatlakozni, a **Csatlakozás kiszolgálóhoz** párbeszédpanelen válassza az **Active Directory – Univerzális mfa-támogatás lehetőséget.** (Ha az **Active Directory univerzális hitelesítés** nem az SSMS legújabb verzióját használja.)  
   ![1mfa-univerzális csatlakozás][1]  
2. Töltse ki a **Felhasználónév** mezőt az Azure `user_name@domain.com`Active Directory hitelesítő adataival a formátumban.  
   ![1mfa-univerzális-connect-felhasználó](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Ha vendégfelhasználóként csatlakozik, a továbbiakban nem kell végrehajtania az AD tartománynév vagy bérlői azonosító mezőt a vendégfelhasználók számára, mert az SSMS 18.x vagy újabb verzió automatikusan felismeri azt. További információ: [Univerzális hitelesítés sql adatbázissal és SQL Data Warehouse (SSMS támogatása MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-no-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   Ha azonban vendégfelhasználóként csatlakozik az SSMS 17.x vagy régebbi használatával, kattintson a **Beállítások**gombra, majd a **Kapcsolat tulajdonság** párbeszédpanelen, és töltse ki az **AD tartománynevet vagy a bérlőazonosítót.**
   ![mfa-bérlő-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. Az SQL Database és az SQL Data Warehouse szokásos módon a **Beállítások** gombra kell kattintania, és meg kell adnia az **adatbázist** a Beállítások párbeszédpanelen. (Ha a csatlakoztatott felhasználó vendégfelhasználó joe@outlook.com(azaz), akkor be kell jelölnie a jelölőnégyzetet, és hozzá kell adnia az aktuális AD tartománynevet vagy bérlői azonosítót a Beállítások részeként. Lásd: [Univerzális hitelesítés SQL Database és SQL Data Warehouse (SSMS támogatása MFA)](sql-database-ssms-mfa-authentication.md). Ezután kattintson a **Csatlakozás** gombra.  
5. Amikor megjelenik a **Bejelentkezés a fiókba** párbeszédpanel, adja meg az Azure Active Directory-identitás fiókját és jelszavát. Nincs szükség jelszóra, ha a felhasználó az Azure AD-vel összevont tartomány része.  
   ![2mfa-bejelentkezés][2]  

   > [!NOTE]
   > Az univerzális hitelesítés olyan fiókkal, amely nem igényel Többfa-hitelesítést, ezen a ponton csatlakozik. Az MFA-t igénylő felhasználók esetében folytassa az alábbi lépésekkel:
   >  
   
6. Két MFA-beállítási párbeszédpanel jelenhet meg. Ez az egyszeri művelet az MFA-rendszergazdai beállítástól függ, ezért előfordulhat, hogy nem kötelező. Egy MFA-kompatibilis tartomány esetében ez a lépés néha előre meghatározott (például a tartomány megköveteli a felhasználóktól, hogy intelligens kártyát és pin-kódot használjanak).  
   ![3mfa-beállítás][3]  
7. A második lehetséges egyszeri párbeszédpanellehetővé teszi a hitelesítési módszer részleteinek kiválasztását. A lehetséges beállításokat a rendszergazda konfigurálja.  
   ![4mfa-ellenőrzés-1][4]  
8. Az Azure Active Directory elküldi a megerősítő információkat. Amikor megkapja az ellenőrző kódot, írja be azt az **Ellenőrző kód megadása** mezőbe, és kattintson a **Bejelentkezés**gombra.  
   ![5mfa-ellenőrzés-2][5]  

Ha az ellenőrzés befejeződött, az SSMS általában érvényes hitelesítő adatokat és tűzfal-hozzáférést feltételez.

## <a name="next-steps"></a>További lépések

- Az Azure SQL Database többtényezős hitelesítésének áttekintését az Univerzális hitelesítés [SQL-adatbázissal és az SQL Data Warehouse -val (SSMS támogatása az MFA-hoz)](sql-database-ssms-mfa-authentication.md)című témakörben találja.  
- Mások hozzáférésének engedélyezése az adatbázishoz: [SQL-adatbázis hitelesítése és engedélyezése: Hozzáférés megadása](sql-database-manage-logins.md)  
- Győződjön meg arról, hogy mások is csatlakozhatnak a tűzfalon keresztül: [Konfiguráljon egy Azure SQL Database-kiszolgálószintű tűzfalszabályt az Azure Portal használatával](sql-database-configure-firewall-settings.md)  
- Ha **Az Active Directory-Universal-t MFA-hitelesítéssel** használja, az ADAL nyomkövetés az [SSMS 17.3-as](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)verziótól kezdődik. Alapértelmezés szerint az ADAL-nyomkövetést az **Eszközök**, **Beállítások** menü, az **Azure Services**, az **Azure Cloud**, az **ADAL kimeneti ablak nyomkövetési szintje**, majd a **Nézet** menü **kimenetengedélyezése** segítségével kapcsolhatja be. A nyomkövetések az **Azure Active Directory beállítás**kiválasztásakor érhetők el a kimeneti ablakban.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

