---
title: Az Azure Analysis Services kiszolgálói rendszergazdák kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a kiszolgáló-rendszergazdák számára az Azure Analysis Services-kiszolgáló.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 00a4de5f463133054ff4821cce74b3dc859a07c2
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603566"
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

Használat [New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) parancsmag használatával adja meg a rendszergazda paramétert, ha az új kiszolgáló létrehozása. <br>
Használat [Set-azurermanalysisservicesserver parancsmagban](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver) parancsmag módosítása a rendszergazda egy meglévő kiszolgálót paramétert.

## <a name="rest-api"></a>REST API

Használat [létrehozás](https://docs.microsoft.com/rest/api/analysisservices/servers/create) asAdministrator tulajdonság megadására, amikor egy új kiszolgálót hoz létre. <br>
Használat [frissítés](https://docs.microsoft.com/rest/api/analysisservices/servers/update) asAdministrator tulajdonság megadására, ha módosítja a meglévő kiszolgáló. <br>



## <a name="next-steps"></a>További lépések 

[Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md)  
[Adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md)  
[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)  

