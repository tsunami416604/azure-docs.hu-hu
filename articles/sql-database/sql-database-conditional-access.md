---
title: Feltételes hozzáférés – Azure SQL Database és az adatraktár |} A Microsoft Doc
description: Megtudhatja, hogyan konfigurálható a feltételes hozzáférés az Azure SQL Database és a Data warehouse-bA.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: fa681198088cd4ae13668193939474d0d501b6f7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098509"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Feltételes hozzáférés (MFA) az Azure SQL Database és a Data warehouse-bA  

Mindkét Azure [SQL Database](sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) támogatják a feltételes hozzáférést a Microsoft. 

> [!NOTE]
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

A következő lépések bemutatják, hogyan konfigurálja az SQL Database egy feltételes hozzáférési szabályzat kényszerítése.  

## <a name="prerequisites"></a>Előfeltételek  
- Az SQL Database vagy az SQL Data Warehouse, Azure Active Directory-hitelesítés támogatásához kell konfigurálnia. Adott lépéseiért lásd: [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Ha a multi-factor authentication szolgáltatás engedélyezve van, csatlakoztatnia kell a jelenleg támogatott eszközt, például az ssms legújabb verziójának. További információkért lásd: [konfigurálása az Azure SQL Database többtényezős hitelesítés az SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Az Azure SQL DB/DW hitelesítésszolgáltató konfigurálása  
1. Jelentkezzen be a portálra, válassza ki **Azure Active Directory**, majd válassza ki **feltételes hozzáférési**. További információkért lásd: [technikai útmutató az Azure Active Directory feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![feltételes hozzáférés paneljén](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Az a **feltételes szabályzataihoz** panelen kattintson a **új szabályzat**, adjon meg egy nevet, és kattintson **szabályok konfigurálása**.  
3. Alatt **hozzárendelések**, jelölje be **felhasználók és csoportok**, ellenőrizze **válassza ki a felhasználók és csoportok**, majd válassza ki a felhasználó vagy csoport feltételes hozzáférés. Kattintson a **kiválasztása**, és kattintson a **kész** , fogadja el a kívánt beállítást.  
   ![Felhasználók és csoportok kiválasztása](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Válassza ki **Felhőalkalmazások**, kattintson a **alkalmazások kiválasztása**. Láthatja, hogy minden alkalmazás elérhető feltételes hozzáférés. Válassza ki **Azure SQL Database**, kattintson az alul **válassza**, és kattintson a **kész**.  
   ![select SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Ha nem találja a **Azure SQL Database** harmadik következőképen látható, a következő lépéseket:   
   - Jelentkezzen be az Azure SQL DB/DW-példány AAD-rendszergazdai fiók az SSMS használatával.  
   - Hajtsa végre `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Jelentkezzen be az aad-be, és győződjön meg arról, hogy az Azure SQL Database és az adatraktár szerepel az alkalmazásokban az AAD-ben.  

5. Válassza ki **hozzáférés-vezérlés**válassza **Grant**, majd ellenőrizze a alkalmazni kívánt házirendet. Ebben a példában kiválasztjuk **többtényezős hitelesítés megkövetelése**.  
   ![Válassza ki a hozzáférés engedélyezése](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Összegzés  
A kijelölt alkalmazás (az Azure SQL Database) való csatlakozáshoz az Azure SQL DB/DW használata az Azure AD Premium, így most már érvénybe lépteti a kiválasztott feltételes hozzáférési szabályzat **szükséges a multi-factor authentication.**  
Azure SQL Database és a Data warehouse-bA a multi-factor authentication kapcsolatos kérdése van, lépjen kapcsolatba MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>További lépések  

Foglalkozó oktatóanyagért lásd: [biztonságossá tétele az Azure SQL Database](sql-database-security-tutorial.md).
