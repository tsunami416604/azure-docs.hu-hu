---
title: Alkalmazás elrejtése a felhasználói élmény elől az Azure AD-ben
description: Hogyan rejtheti el egy alkalmazást a felhasználói élmény elől az Azure Active Directory hozzáférési paneljein vagy az Office 365-indítókban.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5718adf4fd76e2fbd0ff793dd2fa33ee08f7c0fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295048"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Alkalmazások elrejtése a végfelhasználók elől az Azure Active Directoryban

Útmutató az alkalmazások elrejtéséhez a végfelhasználók MyApps panelje vagy az Office 365 indítója elől. Ha egy alkalmazás rejtett, a felhasználók továbbra is rendelkeznek engedélyekkel az alkalmazáshoz. 

## <a name="prerequisites"></a>Előfeltételek

Az alkalmazásrendszergazdai jogosultságok szükségesek ahhoz, hogy egy alkalmazást elrejtsenek a MyApps panelről és az Office 365 indítójából.

Az összes Office 365-alkalmazás elrejtéséhez globális rendszergazdai jogosultságszükséges.


## <a name="hide-an-application-from-the-end-user"></a>Alkalmazás elrejtése a végfelhasználó elől
Az alábbi lépésekkel elrejthet egy alkalmazást a MyApps panelről és az Office 365 alkalmazásindítójából.

1.  Jelentkezzen be az [Azure Portalon](https://portal.azure.com) a címtár globális rendszergazdájaként.
2.  Válassza az **Azure Active Directory**lehetőséget.
3.  Válassza **a Vállalati alkalmazások lehetőséget**. Az **Enterprise applications – Minden alkalmazás** panel megnyílik.
4.  Az **Alkalmazástípusa**csoportban válassza a **Vállalati alkalmazások**lehetőséget, ha még nincs kijelölve.
5.  Keresse meg az elrejteni kívánt alkalmazást, és kattintson az alkalmazásra.  Megnyílik az alkalmazás áttekintése.
6.  Kattintson a **Tulajdonságok** elemre. 
7.  A **Látható a felhasználók számára kérdéshez** kattintson a **Nem**gombra.
8.  Kattintson a **Mentés** gombra.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Alkalmazás elrejtése az Azure AD PowerShell használatával

Ha el szeretne rejteni egy alkalmazást a MyApps panelről, manuálisan is hozzáadhatja a HideApp-címkét az alkalmazás egyszerű szolgáltatásához. Futtassa a következő [AzureAD PowerShell-parancsokat,](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) hogy az alkalmazás **látható a felhasználók számára tulajdonsága** **nem**legyen. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Az Office 365-alkalmazások elrejtése a MyApps panelen

Az alábbi lépésekkel elrejtheti az összes Office 365-alkalmazást a MyApps panelről. Az alkalmazások továbbra is láthatók az Office 365 portálon.

1.  Jelentkezzen be az [Azure Portalon](https://portal.azure.com) a címtár globális rendszergazdájaként.
2.  Válassza az **Azure Active Directory**lehetőséget.
3.  Válassza a **Felhasználói beállítások** elemet.
4.  A **Nagyvállalati alkalmazások**csoportban kattintson **a Végfelhasználók indításának és alkalmazásaik megtekintésének kezelése elemre.**
5.  A **felhasználók számára csak az Office 365-alkalmazások at az Office 365 portálon láthatják,** kattintson az **Igen**gombra.
6.  Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések
* [Az összes csoport megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
* [Felhasználó- vagy csoporthozzárendelés eltávolítása vállalati alkalmazásból](remove-user-or-group-access-portal.md)
* [Vállalati alkalmazás nevének vagy emblémájának módosítása](change-name-or-logo-portal.md)

