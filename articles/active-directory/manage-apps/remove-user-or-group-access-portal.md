---
title: Felhasználói vagy csoportos hozzárendelések eltávolítása az Azure AD-ből származó alkalmazásokból
description: Felhasználó vagy csoport hozzáférési hozzárendelésének eltávolítása egy vállalati alkalmazásból Azure Active Directory
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74275879"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Felhasználó vagy csoport hozzárendelésének eltávolítása egy vállalati alkalmazásból Azure Active Directory

Egyszerűen eltávolíthat egy felhasználót vagy csoportot a Azure Active Directory (Azure AD) egyik vállalati alkalmazásához hozzárendelt hozzáférésből. A vállalati alkalmazás felügyeletéhez szükséges engedélyek szükségesek. Emellett globális rendszergazdai jogosultsággal kell rendelkeznie a címtárhoz.

> [!NOTE]
> Microsoft-alkalmazások (például Office 365-alkalmazások) esetén a PowerShell használatával távolítsa el a felhasználókat a vállalati alkalmazásokhoz.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hogyan eltávolít egy felhasználó vagy csoport hozzárendelését egy vállalati alkalmazáshoz a Azure Portalban?

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza a **minden szolgáltatás**lehetőséget, írja be **Azure Active Directory** a szövegmezőbe, majd válassza az **ENTER billentyűt**.
1. A **Azure Active Directory- *könyvtárnév* ** lapon (azaz a kezelt címtárhoz tartozó Azure ad-oldalon) válassza a **vállalati alkalmazások**lehetőséget.
1. A **vállalati alkalmazások – minden alkalmazás** oldalon láthatja a felügyelhető alkalmazások listáját. Jelöljön ki egy alkalmazást.
1. A ***AppName*** – áttekintés oldalon (azaz a cím alatt a kiválasztott alkalmazás nevével rendelkező oldalon) válassza a **felhasználók & csoportok**lehetőséget.
1. A ***AppName*** **-felhasználó & csoport hozzárendelése** lapon válassza ki a további felhasználók vagy csoportok egyikét, majd kattintson az **Eltávolítás** parancsra. Erősítse meg döntését a parancssorban.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Hogyan a PowerShell használatával távolítson el egy felhasználói vagy csoport-hozzárendelést egy vállalati alkalmazáshoz?

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.

   > [!NOTE]
   > Telepítenie kell a AzureAD modult (használja a parancsot `Install-Module -Name AzureAD`). Ha a rendszer kéri, hogy telepítsen egy NuGet modult vagy az új Azure Active Directory v2 PowerShell-modult, írja be az Y értéket, és nyomja le az ENTER

1. Futtassa `Connect-AzureAD` a parancsot, és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
1. A következő parancsfájl használatával távolíthatja el a felhasználót és a szerepkört egy alkalmazásból:

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

- [Összes saját csoport megjelenítése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
- [Vállalati alkalmazás felhasználói bejelentkezésének letiltása](disable-user-sign-in-portal.md)
- [Vállalati alkalmazás nevének vagy emblémájának módosítása](change-name-or-logo-portal.md)
