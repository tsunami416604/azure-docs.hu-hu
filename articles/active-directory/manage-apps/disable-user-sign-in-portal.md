---
title: Felhasználói bejelentkezések letiltása egy vállalati alkalmazáshoz az Azure AD-ben
description: Vállalati alkalmazások letiltása, hogy a felhasználók ne jelentkezzenek be Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6fc9a4716f1427257b3bbf18e5fa653567e141
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763380"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Vállalati alkalmazásokhoz tartozó felhasználói bejelentkezések letiltása Azure Active Directory

A vállalati alkalmazások egyszerűen letilthatók, így egyetlen felhasználó sem tud bejelentkezni Azure Active Directory (Azure AD) szolgáltatásba. A vállalati alkalmazás felügyeletéhez szükséges engedélyek szükségesek. Emellett globális rendszergazdai jogosultsággal kell rendelkeznie a címtárhoz.

## <a name="how-do-i-disable-user-sign-ins"></a>Hogyan letiltani a felhasználói bejelentkezéseket?

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza a **minden szolgáltatás**lehetőséget, írja be **Azure Active Directory** a szövegmezőbe, majd válassza az **ENTER billentyűt**.
1. A **Azure Active Directory**  -   ***könyvtárnév*** ablaktáblán (azaz a kezelt címtárhoz tartozó Azure ad-ablaktáblán) válassza a **vállalati alkalmazások**lehetőséget.
1. A **vállalati alkalmazások – minden alkalmazás** panelen láthatja a felügyelhető alkalmazások listáját. Jelöljön ki egy alkalmazást.
1. A ***AppName*** panelen (azaz a cím alatt a kiválasztott alkalmazás nevét tartalmazó ablaktáblán) válassza a **Tulajdonságok**lehetőséget.
1. A ***AppName***  -  **Tulajdonságok** ablaktáblán válassza a **nem** lehetőséget, ha **a felhasználók bejelentkeznek?**.
1. Kattintson a **Save (Mentés** ) parancsra.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Lista nélküli alkalmazás letiltása az Azure AD PowerShell használatával

Ha ismeri egy olyan alkalmazás AppId, amely nem jelenik meg a vállalati alkalmazások listáján (például azért, mert törölte az alkalmazást vagy a szolgáltatást még nem hozták létre a Microsoft által előre felhatalmazott alkalmazás miatt), manuálisan is létrehozhatja az alkalmazást, majd letilthatja a [AzureAD PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0)használatával.

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

* [Összes saját csoport megjelenítése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
* [Felhasználó vagy csoport hozzárendelésének eltávolítása vállalati alkalmazásból](remove-user-or-group-access-portal.md)
* [Vállalati alkalmazás nevének vagy emblémájának módosítása](change-name-or-logo-portal.md)
