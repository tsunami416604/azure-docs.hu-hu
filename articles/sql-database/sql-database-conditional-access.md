---
title: "Feltételes hozzáférés – az Azure SQL adatbázishoz és Adatraktárhoz |} Microsoft Doc"
description: "Megtudhatja, hogyan konfigurálja a feltételes hozzáférést az Azure SQL adatbázishoz és Adatraktárhoz."
services: sql-database
author: GithubMirek
manager: johammer
ms.custom: security
ms.service: sql-database
ms.topic: article
ms.date: 06/07/2017
ms.author: mireks
ms.openlocfilehash: f32928805275c9d797c8cb15bb731fe172d5903f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Feltételes hozzáférés (MFA) az Azure SQL adatbázishoz és Adatraktárhoz  

SQL Database és az SQL Data Warehouse támogatja a Microsoft feltételes hozzáférést. A következő lépések bemutatják, hogyan SQL-adatbázis a feltételes hozzáférési házirend konfigurálása.  

## <a name="prerequisites"></a>Előfeltételek  
- A SQL Database vagy az SQL Data Warehouse az Azure Active Directory-hitelesítés támogatásához kell konfigurálnia. Adott lépéseiért lásd: [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Ha engedélyezve van a többtényezős hitelesítést, csatlakoznia kell a támogatott eszköz, például a legújabb SSMS. További információkért lásd: [konfigurálása az Azure SQL Database multi-factor authentication szolgáltatást az SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Az Azure SQL DB/DW hitelesítésszolgáltató konfigurálása  
1.  Jelentkezzen be a portálra, válassza ki **Azure Active Directory**, majd válassza ki **feltételes hozzáférés**. További információkért lásd: [Azure Active Directory feltételes hozzáférési technikai útmutató](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
  ![feltételes hozzáférési panel](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  Az a **feltételes hozzáférés-házirendek** panelen kattintson a **új házirend**, adjon meg egy nevet, és kattintson a **szabályok konfigurálása**.  
3.  A **hozzárendelések**, jelölje be **felhasználók és csoportok**, ellenőrizze **felhasználók és csoportok kiválasztása**, majd válassza ki a felhasználó vagy csoport a feltételes hozzáférés. Kattintson **kiválasztása**, és kattintson a **végzett** a kiválasztott fogadásához.  
  ![Felhasználók és csoportok kiválasztása](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  Válassza ki **felhőalapú alkalmazásokba**, kattintson a **alkalmazásokról**. Megjelenik az elérhető, a feltételes hozzáférés az összes alkalmazást. Válassza ki **Azure SQL Database**, a lap alján kattintson **kiválasztása**, és kattintson a **végzett**.  
  ![select SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
  Ha nem talál **Azure SQL Database** szerepel a következő harmadik képernyőfelvétel, kövesse az alábbi lépéseket:   
  - Jelentkezzen be az Azure SQL adatbázis vagy Adatraktár-példányhoz SSMS használatával egy AAD-rendszergazdai fiókkal.  
  - Végrehajtás `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
  - Jelentkezzen be az aad-be, és ellenőrizze, hogy az Azure SQL Database és az adatraktár szerepel-e az alkalmazásokban az az aad-ben.  

5.  Válassza ki **hozzáférés-szabályozási**, jelölje be **Grant**, és ellenőrizze az alkalmazni kívánt házirendet. Ez a példa azt válassza **többtényezős hitelesítést**.  
  ![Válassza ki a hozzáférés biztosítása](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Összegzés  
A kijelölt alkalmazás (az Azure SQL Database), csatlakozás az Azure SQL DB/DW Azure AD prémium, így most érvénybe lépteti a feltételes hozzáférési szabályzatot, **szükséges többtényezős hitelesítést.**  
Tudnivalók Azure SQL adatbázishoz és Adatraktárhoz többtényezős hitelesítéssel kapcsolatban, forduljon a MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>További lépések  

Az oktatóanyagok esetén lásd: [az Azure SQL Database biztonságos](sql-database-security-tutorial.md).
