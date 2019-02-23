---
title: Az Azure Analysis Services kiszolgálói rendszergazdák kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a kiszolgáló-rendszergazdák számára az Azure Analysis Services-kiszolgáló.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 25eb111871bbe3b18f59b0d6c123c72f3e55c859
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737110"
---
# <a name="manage-server-administrators"></a>A kiszolgálók rendszergazdáinak kezelése

Kiszolgáló-rendszergazdák a bérlőn, amelyben a kiszolgálón található érvényes felhasználónak vagy biztonsági csoportot az Azure Active Directoryban (Azure AD) kell lennie. Használhat **Analysis Services-rendszergazdák** a kiszolgáló Azure Portalon, kiszolgáló tulajdonságai az ssms-ben, PowerShell vagy REST API-t a kiszolgálók rendszergazdáinak kezelése. 

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

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Használat [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) parancsmag használatával adja meg a rendszergazda paramétert, ha az új kiszolgáló létrehozása. <br>
Használat [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) parancsmag módosítása a rendszergazda egy meglévő kiszolgálót paramétert.

## <a name="rest-api"></a>REST API

Használat [létrehozás](https://docs.microsoft.com/rest/api/analysisservices/servers/create) asAdministrator tulajdonság megadására, amikor egy új kiszolgálót hoz létre. <br>
Használat [frissítés](https://docs.microsoft.com/rest/api/analysisservices/servers/update) asAdministrator tulajdonság megadására, ha módosítja a meglévő kiszolgáló. <br>



## <a name="next-steps"></a>További lépések 

[Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md)  
[Adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md)  
[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)  

