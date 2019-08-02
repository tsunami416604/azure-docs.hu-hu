---
title: Kiszolgáló-rendszergazdák kezelése a Azure Analysis Servicesban | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure-beli Analysis Services kiszolgáló rendszergazdáit.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: eb5248d374fa6212398ad652b4db1496833473ae
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696312"
---
# <a name="manage-server-administrators"></a>A kiszolgálók rendszergazdáinak kezelése

A kiszolgálói rendszergazdáknak érvényes felhasználói vagy biztonsági csoportnak kell lenniük a Azure Active Directoryban (Azure AD) ahhoz a bérlőhöz, amelyben a kiszolgáló található. A kiszolgálói rendszergazdák kezeléséhez **Analysis Services rendszergazdákat** használhat a Azure Portal, a SSMS, a PowerShellben vagy a REST API kiszolgálói tulajdonságaiban. 

A **biztonsági csoportoknak** e- `True` [mail-kompatibilisnek](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) kell lenniük a `MailEnabled` tulajdonsághoz beállított tulajdonsággal. A csoportok e-mail-cím `obj:groupid@tenantid`szerinti megadásakor.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Kiszolgáló-rendszergazdák hozzáadása a Azure Portal használatával

1. A portálon, a-kiszolgálónál kattintson a **Analysis Services rendszergazdák**elemre.
2. A  **\<servername > – Analysis Services rendszergazdák**területen kattintson a **Hozzáadás**gombra.
3. A **kiszolgáló-rendszergazdák hozzáadása**lapon válassza ki az Azure ad-beli felhasználói fiókokat, vagy hívja meg a külső felhasználókat e-mail-cím alapján.

    ![Kiszolgálói rendszergazdák a Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Kiszolgáló-rendszergazdák hozzáadása a SSMS használatával

1. Kattintson a jobb gombbal a kiszolgáló > **tulajdonságai**elemre.
2. A **Analysis Server tulajdonságok**területen kattintson a **Biztonság**elemre.
3. Kattintson a **Hozzáadás**gombra, majd adja meg az Azure ad-beli felhasználó vagy csoport e-mail-címét.
   
    ![Kiszolgáló-rendszergazdák hozzáadása a SSMS-ben](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Új kiszolgáló létrehozásakor a [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) parancsmag használatával adhatja meg a rendszergazda paramétert. <br>
A [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) parancsmag használatával módosíthatja egy meglévő kiszolgáló rendszergazdai paraméterét.

## <a name="rest-api"></a>REST API

Új kiszolgáló létrehozásakor a [create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) paranccsal adhatja meg a asAdministrator tulajdonságot. <br>
Egy meglévő kiszolgáló módosításakor a [frissítés](https://docs.microsoft.com/rest/api/analysisservices/servers/update) használatával adhatja meg a asAdministrator tulajdonságot. <br>



## <a name="next-steps"></a>További lépések 

[Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md)  
[Adatbázis-szerepkörök és-felhasználók kezelése](analysis-services-database-users.md)  
[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)  

