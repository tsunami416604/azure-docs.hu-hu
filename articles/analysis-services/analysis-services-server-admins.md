---
title: "Az Azure Analysis Services kiszolgálói rendszergazdák kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a kiszolgáló-rendszergazdák az Azure Analysis Services-kiszolgáló."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: a1b58125dafdf73f245b6a8cd0f4917513b22ea9
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="manage-server-administrators"></a>Kiszolgáló-rendszergazdák kezelése
Kiszolgáló-rendszergazdák, amelyek a kiszolgálón található bérlőjének kell lennie egy érvényes felhasználó vagy csoport az Azure Active Directory (Azure AD). Használhat **Analysis Services rendszergazdák** a vezérlő panelen a kiszolgáló Azure-portálon, vagy a kiszolgáló tulajdonságai szolgáltatáshoz az ssms kiszolgáló-rendszergazdák kezeléséhez. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Kiszolgáló-rendszergazdák hozzáadása az Azure portál használatával
1. A kiszolgáló a vezérlő paneljén kattintson **Analysis Services rendszergazdák**.
2. Az a  **\<kiszolgálónév >-Analysis Services rendszergazdák** panelen kattintson a **Hozzáadás**.
3. Az a **kiszolgáló-rendszergazdák hozzáadása** panelen válassza ki a felhasználói fiókokat az Azure ad- vagy meghívhatja a külső felhasználók e-mail címét.

    ![Kiszolgáló-rendszergazdák az Azure portálon](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Kiszolgáló-rendszergazdák hozzáadása az SSMS használatával
1. Kattintson a jobb gombbal a kiszolgáló > **tulajdonságok**.
2. A **elemzési kiszolgáló tulajdonságai**, kattintson a **biztonsági**.
3. Kattintson a **Hozzáadás**, és adja meg az e-mail cím egy felhasználót vagy csoportot az Azure AD-ben.
   
    ![Kiszolgáló-rendszergazdák hozzáadása a szolgáltatáshoz az ssms](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Következő lépések 
[Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md)  
[Adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md)  
[Szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-what-is.md)  

