---
title: "Az Azure Analysis Services kiszolgálói rendszergazdák kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a kiszolgáló-rendszergazdák az Azure Analysis Services-kiszolgáló."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: d90f1e3df8f5934d5c334ec72b5726f105842ca1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="manage-server-administrators"></a>Kiszolgáló-rendszergazdák kezelése
Kiszolgáló-rendszergazdák, amelyek a kiszolgálón található bérlőjének kell lennie egy érvényes felhasználó vagy csoport az Azure Active Directory (Azure AD). Használhat **Analysis Services rendszergazdák** a kiszolgáló Azure-portálon, vagy a kiszolgáló Tulajdonságok szolgáltatáshoz az ssms kiszolgáló-rendszergazdák kezeléséhez. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Kiszolgáló-rendszergazdák hozzáadása az Azure portál használatával
1. Az a kattintson a portálon, a kiszolgáló **Analysis Services rendszergazdák**.
2. A  **\<kiszolgáló_neve >-Analysis Services rendszergazdák**, kattintson a **Hozzáadás**.
3. A **kiszolgáló-rendszergazdák hozzáadása**, válassza ki a felhasználói fiókokat az Azure ad- vagy meghívhatja a külső felhasználók e-mail címét.

    ![Kiszolgáló-rendszergazdák az Azure portálon](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Kiszolgáló-rendszergazdák hozzáadása az SSMS használatával
1. Kattintson a jobb gombbal a kiszolgáló > **tulajdonságok**.
2. A **elemzési kiszolgáló tulajdonságai**, kattintson a **biztonsági**.
3. Kattintson a **Hozzáadás**, és adja meg az e-mail cím egy felhasználót vagy csoportot az Azure AD-ben.
   
    ![Kiszolgáló-rendszergazdák hozzáadása a szolgáltatáshoz az ssms](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>További lépések 
[Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md)  
[Adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md)  
[Szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-what-is.md)  

