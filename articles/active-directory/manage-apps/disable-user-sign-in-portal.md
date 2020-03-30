---
title: Vállalati alkalmazások felhasználói bejelentkezésének letiltása az Azure AD-ben
description: Vállalati alkalmazások letiltása annak érdekében, hogy egyetlen felhasználó se jelentkezhessen be az Azure Active Directoryban
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
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274089"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Vállalati alkalmazások felhasználói bejelentkezésének letiltása az Azure Active Directoryban

Egy vállalati alkalmazás letiltása, így egyetlen felhasználó sem jelentkezhet be az Azure Active Directoryban (Azure AD). A vállalati alkalmazás kezeléséhez megfelelő engedélyekre van szükség. És, meg kell lennie a globális admin a könyvtárba.

## <a name="how-do-i-disable-user-sign-ins"></a>Hogyan tilthatja le a felhasználói bejelentkezéseket?

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza a **Minden szolgáltatás**lehetőséget, írja be az Azure **Active Directoryt** a szövegmezőbe, majd válassza az **Enter**lehetőséget.
1. Az **Azure Active Directory** -  ***könyvtárnév*** ablaktábláján (azaz a kezelendő könyvtár Azure AD-ablaktábláján) válassza a **Nagyvállalati alkalmazások**lehetőséget.
1. A **Vállalati alkalmazások – Minden alkalmazás** ablaktáblán megjelenik a kezelhető alkalmazások listája. Jelöljön ki egy alkalmazást.
1. Az ***alkalmazásnév*** ablaktáblán (azaz a címben a kijelölt alkalmazás nevét tartalmazó ablaktáblán) válassza a **Tulajdonságok lehetőséget.**
1. Az ***alkalmazásnév*** - **tulajdonságai** ablaktáblán válassza a **Nem** lehetőséget **az Engedélyezve lévő felhasználók számára a bejelentkezéshez?**
1. Válassza a **Mentés** parancsot.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Nem listázott alkalmazás letiltása az Azure AD PowerShell használatával

Ha ismeri egy alkalmazás AppId-ját, amely nem jelenik meg az Enterprise apps listán (például azért, mert törölte az alkalmazást, vagy az egyszerű szolgáltatás még nem jött létre, mivel az alkalmazás a Microsoft által előzetesen engedélyezett), manuálisan létrehozhatja az alkalmazás egyszerű szolgáltatását, majd letilthatja az [AzureAD PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0)használatával.

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>További lépések

* [Az összes csoport megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
* [Felhasználó- vagy csoporthozzárendelés eltávolítása vállalati alkalmazásból](remove-user-or-group-access-portal.md)
* [Vállalati alkalmazás nevének vagy emblémájának módosítása](change-name-or-logo-portal.md)
