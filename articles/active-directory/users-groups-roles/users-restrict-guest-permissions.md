---
title: Vendég felhasználói hozzáférési engedélyek korlátozása – Azure Active Directory | Microsoft Docs
description: A vendég felhasználói hozzáférési engedélyek korlátozása a Azure Portal, a PowerShell vagy a Microsoft Graph használatával Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 08/20/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e8cba89abd65844d238120726572481457f27cb
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88706156"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>A vendég hozzáférési engedélyei (előzetes verzió) korlátozása Azure Active Directory

Azure Active Directory (Azure AD) segítségével korlátozhatja, hogy a külső vendég felhasználók hogyan láthatják a szervezetében az Azure AD-ben. Az Azure AD alapértelmezés szerint korlátozott jogosultsági szintre állítja a vendég felhasználókat, míg a felhasználók alapértelmezés szerint az alapértelmezett felhasználói engedélyek teljes készletét használják. Ez az Azure AD-szervezet külső együttműködési beállításaiban található új vendég felhasználói jogosultsági szint előzetes verziója, amely még korlátozottabb hozzáférést biztosít, így a vendég hozzáférési lehetőségei mostantól a következők:

Engedélyszint         | Hozzáférési szint
----------------         | ------------
Ugyanaz, mint a tag felhasználói     | A vendégek ugyanolyan hozzáféréssel rendelkeznek az Azure AD-erőforrásokhoz, mint a tag felhasználói
Korlátozott hozzáférés (alapértelmezett) | A vendégek az összes nem rejtett csoport tagságát láthatják
**Korlátozott hozzáférés (új)**  | **A vendégek nem láthatják a csoportok tagságát**

Ha a vendég hozzáférése korlátozott, a vendégek csak a saját felhasználói profilt tekinthetik meg. A többi felhasználó megtekintésére vonatkozó engedély nem engedélyezett, még akkor sem, ha a vendég egyszerű felhasználónevet vagy objectId keres. A korlátozott hozzáférés azt is korlátozza, hogy a vendég felhasználók láthassák a csoportok tagságát. További információ az alapértelmezett felhasználói engedélyekről, beleértve a vendég felhasználói engedélyeket is: [Mik az alapértelmezett felhasználói engedélyek a Azure Active Directory-ben?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Engedélyek és licencek

A külső együttműködési beállítások konfigurálásához globális rendszergazdai szerepkörrel kell rendelkeznie. A vendég-hozzáférés korlátozására nincsenek további licencelési követelmények.

## <a name="update-in-the-azure-portal"></a>Frissítés a Azure Portal

Módosítottuk a vendég felhasználói engedélyeinek meglévő Azure Portal vezérlőit.

> [!IMPORTANT]
> Rövid idő alatt az új portál a vendég felhasználói engedélyekhez tartozó vezérlőit csak az URL-cím használatával látja el [https://aka.ms/AADRestrictedGuestAccess](https://aka.ms/AADRestrictedGuestAccess) . A PowerShell és a Microsoft Graph továbbra is használható a vezérlők és a módosítások megadására a portálon.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aka.ms/AADRestrictedGuestAccess) globális rendszergazdai engedélyekkel.
1. A szervezet **Azure Active Directory** áttekintés lapján válassza a **felhasználói beállítások**lehetőséget.
1. A **külső felhasználók**területen válassza a **külső együttműködési beállítások kezelése**lehetőséget.
1. A **külső együttműködési beállítások** lapon válassza a **vendég felhasználói hozzáférés lehetőséget a saját címtár-objektumok tulajdonságai és tagsága** lehetőségre.

    ![Az Azure AD külső együttműködési beállítások lapja](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Kattintson a **Mentés** gombra. A módosítások akár 15 percet is igénybe vehetnek a vendég felhasználóinak érvénybe léptetéséhez.

## <a name="update-with-the-microsoft-graph-api"></a>Frissítés a Microsoft Graph API-val

Új Microsoft Graph API-t adtunk hozzá a vendég engedélyeinek konfigurálásához az Azure AD-szervezetben. A következő API-hívások bármelyik jogosultsági szint hozzárendelésére használhatók. Az itt használt guestUserRoleId értéke a legtöbb korlátozott vendég felhasználói beállítás szemléltetése. További információ a Microsoft Graph használatáról a vendég engedélyeinek beállításához: [authorizationPolicy erőforrástípus](https://docs.microsoft.com/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Konfigurálás első alkalommal

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

A válasznak 204 sikeresnek kell lennie.

### <a name="updating-the-existing-value"></a>A meglévő érték frissítése

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

A válasznak 204 sikeresnek kell lennie.

### <a name="view-the-current-value"></a>Az aktuális érték megtekintése

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Példaválasz:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Frissítés PowerShell-parancsmagokkal

Ezzel a funkcióval lehetőség van a korlátozott engedélyek konfigurálására a PowerShell V2-parancsmagok használatával. A PowerShell-parancsmagok beolvasása és beállítása a 2.0.2.85 verzióban történt.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Parancs beolvasása: Get-AzureADMSAuthorizationPolicy

Példa:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set parancs: set-AzureADMSAuthorizationPolicy

Példa:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> A authorizationPolicy megadásakor AZONOSÍTÓként kell megadnia a kérést.

## <a name="supported-microsoft-365-services"></a>Támogatott Microsoft 365 szolgáltatások

### <a name="supported-services"></a>Támogatott szolgáltatások

Támogatott módon azt értjük, hogy a tapasztalatok a várt módon működnek. pontosabban, hogy ugyanaz, mint az aktuális vendég élmény.

- Teams
- Outlook (OWA)
- SharePoint

### <a name="services-currently-not-supported"></a>A szolgáltatások jelenleg nem támogatottak

Az aktuális támogatás nélküli szolgáltatás kompatibilitási problémákba ütközhet az új vendég korlátozási beállítással.

- Űrlapok
- Planner a Teams szolgáltatásban
- Planner alkalmazás
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

Kérdés | Válasz
-------- | ------
Hová kell alkalmazni ezeket az engedélyeket? | Ezek a címtár-szintű engedélyek az Azure AD-szolgáltatások és-portálok esetében érvényesek, beleértve a Microsoft Graph, a PowerShell V2, a Azure Portal és a saját alkalmazások portált. Az Office 365-csoportokat az együttműködési forgatókönyvek kihasználása mellett az Outlook, a Microsoft Teams és a SharePoint is érinti. Microsoft 365
A My apps portál mely részeit érinti ez a funkció? | A saját alkalmazások portál csoportok funkciója tiszteletben tartja ezeket az új engedélyeket. Ide tartozik a csoportok listájának és a csoporttagságok a saját alkalmazásokban való megtekintéséhez szükséges összes elérési út. Nem történt változás a csoport csempe rendelkezésre állása során. A csoport csempe rendelkezésre állását továbbra is az Azure felügyeleti portál meglévő csoport beállítása szabályozza.
Felülbírálják ezeket az engedélyeket a SharePoint vagy a Microsoft Teams vendég beállításainak? | Nem. Ezek a meglévő beállítások továbbra is szabályozzák az alkalmazások élményét és elérését. Ha például problémát tapasztal a SharePointban, ellenőrizze a külső megosztási beállításokat.
Mik a Planner és a Yammer ismert kompatibilitási problémái? | <li>Ha az engedélyek "korlátozott" értékre vannak beállítva, a Planner alkalmazásba bejelentkezett, vagy a Microsoft Teams-tervezőhöz való hozzáférés nem fog tudni hozzáférni a terveihez vagy a feladatokhoz.<li>A "korlátozott" értékre beállított engedélyek esetén a Yammer bejelentkezett vendégek nem hagyhatják el a csoportot.
Módosulnak a meglévő vendég engedélyei a bérlőn? | Nem történt változás az aktuális beállításokban. A meglévő beállításokkal visszamenőleges kompatibilitást biztosítunk. Ön dönti el, hogy szeretne-e módosításokat végezni.
A rendszer alapértelmezés szerint beállítja ezeket az engedélyeket? | Nem. A meglévő alapértelmezett engedélyek változatlanok maradnak. Igény szerint beállíthatja, hogy az engedélyek szigorúbbak legyenek.
Vannak a szolgáltatásra vonatkozó licencfeltételek? | Nem, a szolgáltatáshoz nem tartozik új licencelési követelmény.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a meglévő vendég engedélyekről az Azure AD-ben, tekintse meg [a mi az alapértelmezett felhasználói engedélyek a Azure Active Directory?](../fundamentals/users-default-permissions.md)című témakört.
- A vendég hozzáférésének korlátozására szolgáló Microsoft Graph API-módszerek megtekintéséhez lásd: [authorizationPolicy erőforrástípus](https://docs.microsoft.com/graph/api/resources/authorizationpolicy).
- A felhasználók összes hozzáférésének visszavonásához lásd: [felhasználói hozzáférés visszavonása az Azure ad-ben](users-revoke-access.md).
