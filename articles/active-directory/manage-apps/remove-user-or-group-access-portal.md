---
title: Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazásokat az Azure Active Directoryban |} A Microsoft Docs
description: A hozzáférés hozzárendelése egy felhasználó vagy csoport eltávolítása a vállalati alkalmazásokat az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97759ae992ebe38aa85e9b4724edeebb5285db4b
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565699"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazásokat az Azure Active Directoryban
A vállalati alkalmazások az Azure Active Directoryban (Azure AD) egyik hozzárendelt hozzáférés egy felhasználó vagy csoport eltávolítása könnyebbé vált. A megfelelő engedélyekkel a vállalati alkalmazások kezelésére van szüksége. És a címtár globális rendszergazdának kell lennie.

> [!NOTE]
> A Microsoft Applications (például az Office 365-alkalmazások) a PowerShell használatával távolítsa el a felhasználók számára a vállalati alkalmazásokat.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hogyan távolíthatom el a felhasználó vagy csoport-hozzárendelést a vállalati alkalmazásokat az Azure Portalon?
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza ki **minden szolgáltatás**, adja meg **Azure Active Directory** a szövegmezőbe, és válassza ki a **Enter**.
1. Az a **Azure Active Directory - *directoryname***  (azaz az Azure AD-oldalról a címtár kezelése), jelölje be **vállalati alkalmazások**.
1. Az a **nagyvállalati alkalmazások – minden alkalmazás** lapon látni fogja a kezelhető alkalmazások listáját. Válasszon ki egy alkalmazást.
1. Az a ***appname*** áttekintése lapon (azt jelenti, az oldal a cím a kijelölt alkalmazás nevét), jelölje ki **felhasználók és csoportok**.
1. Az a ***appname*** **-felhasználó és csoport-hozzárendelés** oldalra, válassza ki az egyik további felhasználókat vagy csoportokat, és kattintson a **eltávolítása** parancsot. Erősítse meg a döntést, a parancssorba.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Hogyan távolíthatom el a felhasználó vagy csoport-hozzárendelés egy vállalati alkalmazáshoz a PowerShell használatával?
1. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.

    >[!NOTE] 
    > Az Azure ad-modul telepítéséhez szüksége (a parancs használata `Install-Module -Name AzureAD`). Ha kéri, telepítse a NuGet-modult vagy az új Azure Active Directory V2 PowerShell modul, írja be az Y, és nyomja le az ENTER billentyűt.

1. Futtatás `Connect-AzureAD` , és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
1. A következő parancsfájl használatával távolítsa el a felhasználó és szerepkör-alkalmazásból:

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

- [Megjelenik az összes saját csoportok](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](assign-user-or-group-access-portal.md)
- [Tiltsa le a felhasználók bejelentkezési folyamatába egy vállalati alkalmazás](disable-user-sign-in-portal.md)
- [A name vagy a vállalati alkalmazás emblémájának módosítása](change-name-or-logo-portal.md)
