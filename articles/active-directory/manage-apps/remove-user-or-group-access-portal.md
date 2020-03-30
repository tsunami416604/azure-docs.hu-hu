---
title: Felhasználói vagy csoport-hozzárendelések eltávolítása egy alkalmazásból az Azure AD-ben
description: Felhasználó vagy csoport hozzáférés-hozzárendelésének eltávolítása vállalati alkalmazásból az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: edf918b57212cf2adfbffb358a0257d9dbea85e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275879"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Felhasználó- vagy csoporthozzárendelés eltávolítása vállalati alkalmazásból az Azure Active Directoryban

Az Azure Active Directoryban (Azure AD) könnyen eltávolíthatja a felhasználót vagy egy csoportot az egyik vállalati alkalmazáshoz rendelt hozzáférésből. A vállalati alkalmazás kezeléséhez megfelelő engedélyekre van szükség. És, meg kell lennie a globális admin a könyvtárba.

> [!NOTE]
> Microsoft-alkalmazások (például Office 365-alkalmazások) esetén a PowerShell segítségével távolítsa el a felhasználókat egy vállalati alkalmazásból.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hogyan távolíthatok el egy felhasználói vagy csoportos hozzárendelést egy vállalati alkalmazáshoz az Azure Portalon?

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza a **Minden szolgáltatás**lehetőséget, írja be az Azure **Active Directoryt** a szövegmezőbe, majd válassza az **Enter**lehetőséget.
1. Az **Azure Active Directory – *könyvtárnév* ** lapon (azaz a kezelendő könyvtár Azure AD-lapján) válassza a **Nagyvállalati alkalmazások**lehetőséget.
1. A **Vállalati alkalmazások – Minden alkalmazás** lapon megjelenik a kezelhető alkalmazások listája. Jelöljön ki egy alkalmazást.
1. Az ***alkalmazásnév*** áttekintése lapon (azaz a címben a kijelölt alkalmazás nevét tartalmazó lapon) válassza a **Felhasználók & csoportok**lehetőséget.
1. Az ***alkalmazásnév*** **– Felhasználó & Csoporthozzárendelés** lapon jelöljön ki egy további felhasználót vagy csoportot, majd válassza az **Eltávolítás** parancsot. Erősítse meg a döntést a kérdésben.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Hogyan távolíthatok el egy felhasználói vagy csoportos hozzárendelést egy vállalati alkalmazáshoz a PowerShell használatával?

1. Nyisson meg egy emelt szintű Windows PowerShell parancssort.

   > [!NOTE]
   > Telepítenie kell az AzureAD modult `Install-Module -Name AzureAD`(használja a parancsot). Ha a rendszer kéri egy NuGet modul vagy az új Azure Active Directory V2 PowerShell-modul telepítését, írja be az Y értéket, és nyomja le az ENTER billentyűt.

1. Globális `Connect-AzureAD` rendszergazdai felhasználói fiókkal futtasson és jelentkezzen be.
1. A következő parancsfájl segítségével távolítson el egy felhasználót és szerepkört az alkalmazásból:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```

## <a name="next-steps"></a>További lépések

- [Az összes csoport megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
- [Vállalati alkalmazás felhasználói bejelentkezésének letiltása](disable-user-sign-in-portal.md)
- [Vállalati alkalmazás nevének vagy emblémájának módosítása](change-name-or-logo-portal.md)
