---
title: Az Azure Analysis Services kiszolgálói rendszergazdák kezelése |} Microsoft Docs
description: Megtudhatja, hogyan kezelheti a kiszolgáló-rendszergazdák az Azure Analysis Services-kiszolgáló.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ec1630f4de70f77c13e335c68aff16180e524c12
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307808"
---
# <a name="manage-server-administrators"></a>Kiszolgáló-rendszergazdák kezelése
Kiszolgáló-rendszergazdák a bérlői, ahol a kiszolgáló van kell lennie egy érvényes felhasználót vagy biztonsági csoportot az Azure Active Directory (Azure AD). Használhat **Analysis Services rendszergazdák** a kiszolgáló Azure-portálon, vagy a kiszolgáló Tulajdonságok szolgáltatáshoz az ssms kiszolgáló-rendszergazdák kezeléséhez. 

> [!NOTE]
> Biztonsági csoportok kell rendelkeznie a `MailEnabled` tulajdonsága `True`.

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
[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)  

