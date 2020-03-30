---
title: Kiszolgálógazdák kezelése az Azure Analysis Services ben | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan kezelheti a kiszolgálói rendszergazdák egy Azure Analysis Services-kiszolgáló az Azure Portalon, a PowerShell vagy a REST API-k használatával.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f7c57a5751f2ff34abb26b7653070ce4ee5010fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572627"
---
# <a name="manage-server-administrators"></a>A kiszolgálók rendszergazdáinak kezelése

A kiszolgáló rendszergazdáinak érvényes felhasználónak vagy biztonsági csoportnak kell lenniük az Azure Active Directoryban (Azure AD) ahhoz a bérlőhöz, amelyben a kiszolgáló található. Az **Analysis Services-rendszergazdák** az Azure Portalon, a Kiszolgáló tulajdonságai az SSMS-ben, a PowerShellben vagy a REST API-ban a kiszolgáló rendszergazdáinak kezeléséhez használható. 

**A biztonsági csoportoknak** [levelezésre alkalmasnak](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) kell lenniük, ha a `MailEnabled` tulajdonság a értékre `True`van állítva. Csoport e-mail cím szerint `obj:groupid@tenantid`történő megadásakor használja a használatát.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Kiszolgálói rendszergazdák hozzáadása az Azure Portal használatával

1. A portálon a kiszolgálóhoz kattintson az **Analysis Services Admins**elemre.
2. A ** \<kiszolgálónév> – Analysis Services Rendszergazdák ( Elemzési szolgáltatások rendszergazdái )** kattintson a **Hozzáadás**gombra.
3. A **Kiszolgálógazdák hozzáadása csoportban**válassza ki a felhasználói fiókokat az Azure AD-ből, vagy e-mail-cím útján hívja meg a külső felhasználókat.

    ![Kiszolgálógazdák az Azure Portalon](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Kiszolgálói rendszergazdák hozzáadása az SSMS használatával

1. Kattintson a jobb gombbal a kiszolgálóra > **Tulajdonságok parancsra.**
2. Az **Analysis Server tulajdonságai**párbeszédpanelen kattintson a **Biztonság**gombra.
3. Kattintson **a Hozzáadás**gombra, majd adja meg egy felhasználó vagy csoport e-mail címét az Azure AD-ben.
   
    ![Kiszolgálórendszergazdák hozzáadása az SSMS-ben](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[A New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) parancsmag segítségével adja meg a Rendszergazda paramétert új kiszolgáló létrehozásakor. <br>
A [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) parancsmag segítségével módosítsa egy meglévő kiszolgáló Rendszergazda paraméterét.

## <a name="rest-api"></a>REST API

A [Létrehozás segítségével](https://docs.microsoft.com/rest/api/analysisservices/servers/create) adja meg a rendszergazdai tulajdonságot új kiszolgáló létrehozásakor. <br>
A [Frissítés segítségével](https://docs.microsoft.com/rest/api/analysisservices/servers/update) adja meg az asAdministrator tulajdonságot egy meglévő kiszolgáló módosításakor. <br>



## <a name="next-steps"></a>További lépések 

[Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md)  
[Adatbázis-szerepkörök és felhasználók kezelése](analysis-services-database-users.md)  
[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)  

