---
title: Feltételes hozzáférés
description: Ismerje meg, hogyan konfigurálhatja a feltételes hozzáférést az Azure SQL Database és az Azure Synapse számára.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124908"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Feltételes hozzáférés (MFA) az Azure SQL Database és az Azure Synapse Analytics szolgáltatással

Az Azure [SQL Database](sql-database-technical-overview.md), a Felügyelt példány és [az](sql-database-managed-instance.md) [Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) támogatja a Microsoft feltételes hozzáférést. 

> [!NOTE]
> Ez a témakör az Azure SQL-kiszolgálóra, valamint az Azure SQL-kiszolgálón létrehozott SQL Database és Azure Synapse rendszerre is vonatkozik. Az egyszerűség kedvéért az SQL Database az SQL Database és az Azure Synapse hivatkozva használatos.

A következő lépések bemutatják, hogyan konfigurálható az SQL Database feltételes hozzáférési házirend ek kényszerítése.  

## <a name="prerequisites"></a>Előfeltételek  
- Konfigurálnia kell az SQL-adatbázist vagy az SQL-készletet az Azure Synapse-ban az Azure Active Directory-hitelesítés támogatásához. A konkrét lépéseket az [Azure Active Directory-hitelesítés konfigurálása és kezelése SQL Database vagy Azure Synapse használatával](sql-database-aad-authentication-configure.md)című témakörben t.For specific steps, see Configure and manage Azure Active Directory authentication with SQL Database or Azure Synapse .  
- Ha a többtényezős hitelesítés engedélyezve van, csatlakoznia kell a támogatott eszközhöz, például a legújabb SSMS-hez. További információt az [Azure SQL Database többtényezős hitelesítéskonfigurálása az SQL Server Management Studio számára című témakörben talál.](sql-database-ssms-mfa-authentication-configure.md)  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Hitelesítéscsomag konfigurálása az Azure SQL DB/DW-hez  
1. Jelentkezzen be a portálra, válassza az **Azure Active Directory**t, majd a Feltételes **hozzáférés**lehetőséget. További információ: [Azure Active Directory feltételes hozzáférés technikai útmutató.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)  
   ![Feltételes hozzáférés panel](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. A **Feltételes hozzáférés-házirendek** panelen kattintson az **Új házirend gombra,** adja meg a nevet, majd kattintson **a Szabályok konfigurálása**gombra.  
3. A **Hozzárendelések**csoportban jelölje be a **Felhasználók és csoportok**jelölőnégyzetet, jelölje be a Felhasználók és csoportok **kijelölése**jelölőnégyzetet, majd jelölje ki a felhasználót vagy csoportot a Feltételes hozzáférés hez. Kattintson **a Kijelölés**gombra, majd a **Kész** gombra a kijelölés elfogadásához.  
   ![felhasználók és csoportok kijelölése](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Válassza a **Felhőalapú alkalmazások**lehetőséget, kattintson **az Alkalmazások kiválasztása**gombra. A feltételes hozzáféréshez elérhető összes alkalmazás megjelenik. Válassza az **Azure SQL Database (Azure SQL Database)** lehetőséget, kattintson a **Kijelölés gombra,** majd a **Kész**gombra.  
   ![SQL-adatbázis kiválasztása](./media/sql-database-conditional-access/select-sql-database.png)  
   Ha nem találja az **Azure SQL Database-t** a következő harmadik képernyőképen, hajtsa végre az alábbi lépéseket:   
   - Jelentkezzen be az Azure SQL DB/DW példányába Az SSMS használatával Egy AAD-rendszergazdai fiókkal.  
   - Végrehajtás `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Jelentkezzen be az AAD-be, és ellenőrizze, hogy az Azure SQL Database és az Azure Synapse szerepel-e az AAD-ben szereplő alkalmazásokban.  

5. Válassza **a Hozzáférés-vezérlők ,** majd a **Grant**lehetőséget, majd ellenőrizze az alkalmazni kívánt házirendet. Ebben a példában a **Többtényezős hitelesítés megkövetelése**lehetőséget választjuk.  
   ![támogatási hozzáférés kiválasztása](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Összefoglalás  
A kiválasztott alkalmazás (Azure SQL Database), amely lehetővé teszi, hogy csatlakozzon az Azure SQL DB/DW az Azure AD Premium használatával, most kényszeríti a kiválasztott feltételes hozzáférési szabályzat, **szükséges többtényezős hitelesítés.**  
Az Azure SQL Database és az Azure Synapse MFAforSQLDB@microsoft.comtöbbtényezős hitelesítéssel kapcsolatos kérdéseiért lépjen kapcsolatba a kapcsolattal.  

## <a name="next-steps"></a>További lépések  

Az oktatóanyagról az [Azure SQL-adatbázis biztonságossá tétele című témakörben található.](sql-database-security-tutorial.md)
