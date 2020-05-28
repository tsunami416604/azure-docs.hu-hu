---
title: Feltételes hozzáférés
description: Megtudhatja, hogyan konfigurálhatja a feltételes hozzáférést Azure SQL Database, az Azure SQL felügyelt példányához és az Azure szinapszis Analytics szolgáltatáshoz.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: cc4857c32eca924051ba72fb716e29231327f543
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043526"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Feltételes hozzáférés (MFA) a Azure SQL Database és az Azure szinapszis Analytics használatával
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md), az [Azure SQL felügyelt példánya](../managed-instance/sql-managed-instance-paas-overview.md)és az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) támogatja a Microsoft feltételes hozzáférését.

A következő lépések bemutatják, hogyan konfigurálhatja az Azure SQL Database, az SQL felügyelt példányát vagy az Azure Szinapszisot a feltételes hozzáférési (CA) szabályzat érvényesítéséhez.  

## <a name="prerequisites"></a>Előfeltételek

- A Azure Active Directory hitelesítés támogatásához konfigurálnia kell az SQL Database, az SQL felügyelt példányát vagy az SQL-készletet az Azure Szinapszisban. Konkrét lépések: [Azure Active Directory hitelesítés konfigurálása és kezelése SQL Database vagy Azure szinapszis segítségével](authentication-aad-configure.md).  
- Ha a többtényezős hitelesítés engedélyezve van, akkor a-t a támogatott eszközzel kell összekapcsolni, például a legújabb SQL Server Management Studio (SSMS). További információ: [Azure SQL Database multi-Factor Authentication konfigurálása SQL Server Management studiohoz](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Feltételes hozzáférés konfigurálása

> [!NOTE]
> Az alábbi példa Azure SQL Database használ, de ki kell választania a megfelelő terméket, amelyhez feltételes hozzáférést kíván konfigurálni.

1. Jelentkezzen be a portálra, válassza a **Azure Active Directory**lehetőséget, majd válassza a **feltételes hozzáférés**lehetőséget. További információ: [Azure Active Directory feltételes hozzáférés technikai útmutatója](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Feltételes hozzáférés panel](./media/conditional-access-configure/conditional-access-blade.png)

2. A **feltételes hozzáférés – szabályzatok** panelen kattintson az **új házirend**elemre, adjon meg egy nevet, majd kattintson a **szabályok konfigurálása**elemre.  
3. A **hozzárendelések**területen válassza a **felhasználók és csoportok**, majd a **felhasználók és csoportok kiválasztása**lehetőséget, majd a feltételes hozzáféréshez válassza ki a felhasználót vagy a csoportot. Kattintson a **kiválasztás elemre, majd**kattintson a **kész** gombra a kijelölés elfogadásához.  
   ![felhasználók és csoportok kiválasztása](./media/conditional-access-configure/select-users-and-groups.png)  

4. Válassza a **Cloud apps**lehetőséget, majd kattintson az **alkalmazások kiválasztása**lehetőségre. A feltételes hozzáféréshez elérhető összes alkalmazás megjelenik. Válassza a **Azure SQL Database**lehetőséget, alul kattintson a **kiválasztás**elemre, majd kattintson a **kész**gombra.  
   ![SQL Database kiválasztása](./media/conditional-access-configure/select-sql-database.png)  
   Ha nem találja az alábbi harmadik képernyőképen felsorolt **Azure SQL Database** , hajtsa végre a következő lépéseket:
   - Jelentkezzen be Azure SQL Database a SSMS használatával egy Azure AD-rendszergazdai fiókkal.  
   - Végrehajtás `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` .  
   - Jelentkezzen be az Azure AD-be, és ellenőrizze, hogy a Azure SQL Database, az SQL felügyelt példánya vagy az Azure szinapszis szerepel-e a HRE lévő alkalmazásokban.  

5. Válassza a **hozzáférés-vezérlés**lehetőséget, válassza a **támogatás**lehetőséget, majd jelölje ki az alkalmazni kívánt szabályzatot. Ebben a példában a **többtényezős hitelesítés megkövetelése**lehetőséget választjuk.  
   ![Válassza a hozzáférés engedélyezése lehetőséget](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Összefoglalás

A kiválasztott alkalmazás (Azure SQL Database) prémium szintű Azure AD használatával mostantól kikényszeríti a kiválasztott feltételes hozzáférési szabályzatot, a **szükséges multi-Factor Authentication hitelesítést.**

A többtényezős hitelesítéssel kapcsolatos Azure SQL Database és az Azure szinapszis kérdéseivel kapcsolatban forduljon a következőhöz: <MFAforSQLDB@microsoft.com> .  

## <a name="next-steps"></a>Következő lépések  

Oktatóanyagért lásd: [a Azure SQL Database biztonságossá tétele](secure-database-tutorial.md).
