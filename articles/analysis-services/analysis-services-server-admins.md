---
title: Az Azure Analysis Services kiszolgálói rendszergazdák kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a kiszolgáló-rendszergazdák számára az Azure Analysis Services-kiszolgáló.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9d8f74bd66fc7c980c4fc5f83492aad7d8a4aa5c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866967"
---
# <a name="manage-server-administrators"></a>A kiszolgálók rendszergazdáinak kezelése
Kiszolgáló-rendszergazdák a bérlőn, amelyben a kiszolgálón található érvényes felhasználónak vagy biztonsági csoportot az Azure Active Directoryban (Azure AD) kell lennie. Használhat **Analysis Services-rendszergazdák** a kiszolgálóhoz az Azure Portalon vagy a kiszolgáló tulajdonságai az ssms-ben, a kiszolgálók rendszergazdáinak kezelése. 

> [!NOTE]
> Biztonsági csoportok kell rendelkeznie a `MailEnabled` tulajdonság `True`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Kiszolgáló-rendszergazdák hozzáadása az Azure portal használatával
1. Kattintson a portál, a kiszolgáló **Analysis Services-rendszergazdák**.
2. A  **\<kiszolgáló_neve >-Analysis Services-rendszergazdák**, kattintson a **Hozzáadás**.
3. A **kiszolgáló-rendszergazdák hozzáadása**, válassza ki a felhasználói fiókokat az Azure AD-ből vagy a külső felhasználók meghívása az e-mail-cím alapján.

    ![Kiszolgáló-rendszergazdák az Azure Portalon](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Kiszolgáló-rendszergazdák hozzáadása az SSMS használatával
1. Kattintson a jobb gombbal a kiszolgáló > **tulajdonságok**.
2. A **Analysis Server tulajdonságai**, kattintson a **biztonsági**.
3. Kattintson a **Hozzáadás**, majd adja meg az e-mail-cím egy felhasználó vagy csoport számára az Azure AD-ben.
   
    ![Kiszolgáló-rendszergazdák hozzáadása az ssms-ben](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>További lépések 
[Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md)  
[Adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md)  
[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)  

