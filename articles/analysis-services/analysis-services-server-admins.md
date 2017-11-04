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
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 6a42baf9fd880264e4130c009f27935a4743de21
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
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

