---
title: Többtényezős hitelesítés konfigurálása
description: Ismerje meg, hogyan használható a többtényezős hitelesítés a SSMS for SQL Database és az Azure szinapszis Analytics használatával
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
ms.openlocfilehash: 38d8eba5dd451c8e8709ce4d43aba107e5346bfc
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627364"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Többtényezős hitelesítés konfigurálása SQL Server Management Studio és az Azure AD-hez

Ez a témakör bemutatja, hogyan használható Azure Active Directory multi-Factor Authentication (MFA) a SQL Server Management Studio használatával. Az Azure AD MFA a SSMS vagy a SqlPackage. exe az Azure [SQL Database](sql-database-technical-overview.md) és az [Azure szinapszis analyticshez](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)való csatlakoztatásakor használható. A többtényezős hitelesítés Azure SQL Database áttekintését lásd: az [univerzális hitelesítés az SQL Database és az Azure szinapszis (SSMS-támogatás az MFA-hoz)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Ez a témakör az Azure SQL Serverre, valamint az Azure SQL Serveren létrehozott SQL Database és Azure szinapszis-adatbázisokra is vonatkozik. Az egyszerűség kedvéért SQL Database a rendszer akkor használja, ha a SQL Database és az Azure Szinapszisra hivatkozik.

## <a name="configuration-steps"></a>Konfigurációs lépések

1. **Azure Active Directory konfigurálása** – további információ: [Az Azure ad-címtár felügyelete](https://msdn.microsoft.com/library/azure/hh967611.aspx), [a helyszíni identitások integrálása a Azure Active Directoryekkel](../active-directory/hybrid/whatis-hybrid-identity.md), [saját tartománynév hozzáadása az Azure ad](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)-hez, [Microsoft Azure mostantól támogatja a Windows Server Active Directory összevonását](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), és a [Windows PowerShell használatával felügyelheti az Azure ad](https://msdn.microsoft.com/library/azure/jj151815.aspx)-t.
2. Az **MFA konfigurálása** – részletes útmutatásért lásd: [mi az Azure multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [feltételes hozzáférés (MFA) a Azure SQL Database és az Azure szinapszis](sql-database-conditional-access.md)használatával. (A teljes feltételes hozzáféréshez prémium szintű Azure Active Directory (Azure AD) szükséges. A korlátozott MFA standard szintű Azure AD-vel érhető el.)
3. **SQL Database vagy Azure szinapszis konfigurálása az Azure ad-hitelesítéshez** – lépésenkénti útmutatásért lásd: [Csatlakozás a SQL Databasehoz vagy az azure Szinapszishoz Azure Active Directory hitelesítés használatával](sql-database-aad-authentication.md).
4. **Töltse le a SSMS** -t az ügyfélszámítógépen, töltse le a legújabb SSMS a [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)webhelyről. Az ebben a témakörben található összes szolgáltatáshoz használja a 17,2-es vagy újabb 2017-es verziót.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Csatlakozás a SSMS-sel való univerzális hitelesítéssel

A következő lépések bemutatják, hogyan csatlakozhat SQL Database vagy SAzure Szinapszishoz a legújabb SSMS használatával.

1. Az univerzális hitelesítés használatával történő kapcsolódáshoz a **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen válassza a **Active Directory-Universal lehetőséget MFA-támogatással**. (Ha **Active Directory univerzális hitelesítést** látja, akkor nem a SSMS legújabb verziójával rendelkezik.)  
   ![1mfa – univerzális – kapcsolat][1]  
2. Töltse ki a **Felhasználónév** mezőt a Azure Active Directory hitelesítő adataival a (z `user_name@domain.com`) formátumban.  
   ![1mfa – univerzális – kapcsolat – felhasználó](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Ha vendég felhasználóként csatlakozik, már nem kell végrehajtania az AD-tartománynév vagy a bérlői azonosító mezőt a vendég felhasználók számára, mert a SSMS 18. x vagy újabb automatikusan felismeri. További információkért lásd: az [univerzális hitelesítés SQL Database és az Azure szinapszis (SSMS-támogatás az MFA-hoz)](sql-database-ssms-mfa-authentication.md).
   ![MFA-No-bérlő-SSMS](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   Ha azonban vendég felhasználóként csatlakozik a 17. x vagy régebbi SSMS használatával, kattintson a **Beállítások**elemre, majd a **kapcsolat tulajdonságai** párbeszédpanelen adja meg az **ad-tartománynév vagy a bérlői azonosító mező nevét** .
   ![MFA-bérlő – SSMS](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. A SQL Database és az Azure szinapszis esetében általában a **Beállítások** **párbeszédpanelen kell** megadnia az adatbázist. (Ha a csatlakoztatott felhasználó egy vendég felhasználó (azaz joe@outlook.com), jelölje be a jelölőnégyzetet, és adja hozzá az aktuális ad-tartománynevet vagy a bérlő azonosítóját a beállítások részeként. Lásd: [univerzális hitelesítés SQL Database és Azure szinapszis (SSMS-támogatás az MFA-hoz)](sql-database-ssms-mfa-authentication.md). Ezután kattintson a **Csatlakozás** gombra.  
5. Amikor megjelenik a **Bejelentkezés a fiókba** párbeszédpanel, adja meg a Azure Active Directory identitásának fiókját és jelszavát. Nincs szükség jelszóra, ha a felhasználó az Azure AD-vel összevont tartomány tagja.  
   ![2mfa – bejelentkezés][2]  

   > [!NOTE]
   > Ha az univerzális hitelesítéshez olyan fiókra van szükség, amely nem igényel MFA-t, akkor ezen a ponton kapcsolódhat. Az MFA-t igénylő felhasználók esetében folytassa a következő lépésekkel:
   >  
   
6. Előfordulhat, hogy két MFA beállítási párbeszédpanel jelenik meg. Ez az egyszeri művelet az MFA rendszergazdai beállításától függ, ezért opcionális lehet. Az MFA-kompatibilis tartományokban ez a lépés néha előre definiálva van (például a tartomány megköveteli, hogy a felhasználók intelligens kártyát és PIN-kódot használjanak).  
   ![3mfa – telepítés][3]  
7. A második lehetőség egy alkalommal párbeszédpanel segítségével kiválaszthatja a hitelesítési módszer részleteit. A rendszergazda konfigurálja a lehetséges beállításokat.  
   ![4mfa – ellenőrzés – 1][4]  
8. A Azure Active Directory elküldi a megerősítési adatokat. Amikor megkapja az ellenőrző kódot, adja meg az **ENTER ellenőrző kód megadása** mezőbe, és kattintson a **Bejelentkezés**elemre.  
   ![5mfa – ellenőrzés – 2][5]  

Az ellenőrzés befejezésekor a SSMS általában feltételezi az érvényes hitelesítő adatokat és a tűzfal elérését.

## <a name="next-steps"></a>További lépések

- A többtényezős hitelesítés Azure SQL Database áttekintését lásd: az univerzális hitelesítés az [SQL Database és az Azure szinapszis (SSMS-támogatás az MFA-hoz)](sql-database-ssms-mfa-authentication.md).  
- Mások hozzáférésének biztosítása az adatbázishoz: [SQL Database hitelesítés és engedélyezés: hozzáférés biztosítása](sql-database-manage-logins.md)  
- Győződjön meg arról, hogy mások kapcsolódhatnak a tűzfalon keresztül: [Azure SQL Database kiszolgáló szintű tűzfalszabály konfigurálása a Azure Portal használatával](sql-database-configure-firewall-settings.md)  
- A **Active Directory-Universal és az MFA-** hitelesítés használatakor a ADAL nyomkövetés a [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)-es verziójától kezdődően érhető el. Alapértelmezés szerint kikapcsolhatja a ADAL nyomkövetését az **eszközök**, beállítások menü, az **Azure-szolgáltatások**, az **Azure-felhő**, a **ADAL kimeneti ablak nyomkövetési szintje** **lehetőséggel** , majd a **kimenet** lehetőséget a **nézet** menüben engedélyezheti. A Nyomkövetések a kimeneti ablakban érhetők el **Azure Active Directory lehetőség**kiválasztásakor.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

