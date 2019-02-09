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
ms.date: 02/14/2018
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 7d9f0ed56acce59832af3c02f88746869163ce42
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963696"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazásokat az Azure Active Directoryban
Egy felhasználó vagy csoport eltávolítása folyamatban van hozzárendelve a hozzáférés a vállalati alkalmazások az Azure Active Directoryban (Azure AD) egyik könnyebbé vált. A vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.

> [!NOTE]
> A Microsoft Applications (például az Office 365-alkalmazások) a PowerShell használatával távolítsa el a felhasználók számára a vállalati alkalmazásokat.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hogyan távolíthatom el a felhasználó vagy csoport-hozzárendelést a vállalati alkalmazásokat az Azure Portalon?
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **további szolgáltatások**, adja meg **Azure Active Directory** a szövegmezőbe, és válassza ki a **Enter**.
3. Az a **Azure Active Directory - *directoryname***  (azt jelenti, az Azure AD-oldalról a címtár kezeli), jelölje be **vállalati alkalmazások**.

    ![Vállalati alkalmazások megnyitása](./media/remove-user-or-group-access-portal/open-enterprise-apps.png)
4. Az a **vállalati alkalmazások** lapon jelölje be **minden alkalmazás**. Látni fogja a kezelhető alkalmazások listáját.
5. Az a **nagyvállalati alkalmazások – minden alkalmazás** lap, válasszon ki egy alkalmazást.
6. Az a ***appname*** (azaz lapon címében szerepel a kijelölt alkalmazás nevét) oldalon válassza ki, **felhasználók és csoportok**.

    ![Felhasználók vagy csoportok kiválasztása](./media/remove-user-or-group-access-portal/remove-app-users.png)
7. Az a ***appname*** **-felhasználó és csoport-hozzárendelés** oldalra, válassza ki az egyik további felhasználókat vagy csoportokat, és kattintson a **eltávolítása** parancsot. Erősítse meg a döntést, a parancssorba.

    ![A Remove-parancs kiválasztása](./media/remove-user-or-group-access-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Hogyan távolíthatom el a felhasználó vagy csoport-hozzárendelés egy vállalati alkalmazáshoz a PowerShell használatával?
1. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.

    >[!NOTE] 
    > Az Azure ad-modul telepítéséhez szüksége (a parancs használata `Install-Module -Name AzureAD`). Ha kéri, telepítse a NuGet-modult vagy az új Azure Active Directory V2 PowerShell modul, írja be az Y, és nyomja le az ENTER billentyűt.

2. Futtatás `Connect-AzureAD` , és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
3. A következő szkript használatával egy felhasználó és szerepkör hozzárendelése egy alkalmazáshoz:

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
