---
title: Feltételes hozzáférés
description: Ismerje meg, hogyan konfigurálhatja az Azure SQL Database és az Azure szinapszis feltételes hozzáférését.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124908"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Feltételes hozzáférés (MFA) a Azure SQL Database és az Azure szinapszis Analytics használatával

Az Azure [SQL Database](sql-database-technical-overview.md), a [felügyelt példány](sql-database-managed-instance.md)és az [Azure szinapszis](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) támogatja a Microsoft feltételes hozzáférését. 

> [!NOTE]
> Ez a témakör az Azure SQL Serverre, valamint az Azure SQL Serveren létrehozott SQL Database és az Azure Szinapszisra is vonatkozik. Az egyszerűség kedvéért SQL Database a rendszer akkor használja, ha a SQL Database és az Azure Szinapszisra hivatkozik.

A következő lépések bemutatják, hogyan konfigurálhatja a SQL Databaset a feltételes hozzáférési szabályzat érvénybe léptetéséhez.  

## <a name="prerequisites"></a>Előfeltételek  
- A Azure Active Directory hitelesítés támogatásához konfigurálnia kell az SQL Database vagy az SQL-készletet az Azure Szinapszisban. Konkrét lépések: [Azure Active Directory hitelesítés konfigurálása és kezelése SQL Database vagy Azure szinapszis segítségével](sql-database-aad-authentication-configure.md).  
- Ha a többtényezős hitelesítés engedélyezve van, akkor a-t a támogatott eszközzel kell összekapcsolni, például a legújabb SSMS. További információ: [Azure SQL Database multi-Factor Authentication konfigurálása SQL Server Management studiohoz](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>HITELESÍTÉSSZOLGÁLTATÓ konfigurálása az Azure SQL DB/DW számára  
1. Jelentkezzen be a portálra, válassza a **Azure Active Directory**lehetőséget, majd válassza a **feltételes hozzáférés**lehetőséget. További információ: [Azure Active Directory feltételes hozzáférés technikai útmutatója](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Feltételes hozzáférés panel](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. A **feltételes hozzáférés – szabályzatok** panelen kattintson az **új házirend**elemre, adjon meg egy nevet, majd kattintson a **szabályok konfigurálása**elemre.  
3. A **hozzárendelések**területen válassza a **felhasználók és csoportok**, majd a **felhasználók és csoportok kiválasztása**lehetőséget, majd a feltételes hozzáféréshez válassza ki a felhasználót vagy a csoportot. Kattintson a **kiválasztás elemre, majd**kattintson a **kész** gombra a kijelölés elfogadásához.  
   ![felhasználók és csoportok kiválasztása](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Válassza a **Cloud apps**lehetőséget, majd kattintson az **alkalmazások kiválasztása**lehetőségre. A feltételes hozzáféréshez elérhető összes alkalmazás megjelenik. Válassza a **Azure SQL Database**lehetőséget, alul kattintson a **kiválasztás**elemre, majd kattintson a **kész**gombra.  
   ![SQL Database kiválasztása](./media/sql-database-conditional-access/select-sql-database.png)  
   Ha nem találja az alábbi harmadik képernyőképen felsorolt **Azure SQL Database** , hajtsa végre a következő lépéseket:   
   - Jelentkezzen be az Azure SQL DB/DW-példányba a SSMS használatával egy HRE-rendszergazdai fiókkal.  
   - Végrehajtás `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Jelentkezzen be a HRE, és ellenőrizze, hogy a Azure SQL Database és az Azure szinapszis szerepel-e a HRE lévő alkalmazásokban.  

5. Válassza a **hozzáférés-vezérlés**lehetőséget, válassza a **támogatás**lehetőséget, majd jelölje ki az alkalmazni kívánt szabályzatot. Ebben a példában a **többtényezős hitelesítés megkövetelése**lehetőséget választjuk.  
   ![Válassza a hozzáférés engedélyezése lehetőséget](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Összefoglalás  
A kiválasztott alkalmazás (Azure SQL Database), amely lehetővé teszi az Azure SQL DB/DW-hez való kapcsolódást a prémium szintű Azure AD használatával, most már kikényszeríti a kiválasztott feltételes hozzáférési szabályzatot, a **szükséges multi-Factor Authentication hitelesítést.**  
A többtényezős hitelesítéssel kapcsolatos Azure SQL Database és az Azure szinapszis kérdéseivel MFAforSQLDB@microsoft.comkapcsolatban forduljon a következőhöz:.  

## <a name="next-steps"></a>További lépések  

Oktatóanyagért lásd: [a Azure SQL Database biztonságossá tétele](sql-database-security-tutorial.md).
