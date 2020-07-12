---
title: Felhasználói és rendszergazdai engedélyek kezelése – Azure Active Directory | Microsoft Docs
description: Ismerje meg, hogyan tekintheti át és kezelheti az alkalmazásra vonatkozó engedélyeket az Azure AD-ben. Ha például vissza kívánja vonni az alkalmazáshoz megadott összes engedélyt.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277632"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>Megteheti a műveletet a Azure Active Directory

Ismerje meg, hogyan tekintheti át és kezelheti az alkalmazás engedélyeit. A forgatókönyv alapján ez a cikk az alkalmazás biztonságossá tételéhez szükséges különböző műveleteket fogja biztosítani. Ez minden olyan alkalmazásra vonatkozik, amely felhasználói vagy rendszergazdai hozzájárulással lett hozzáadva a Azure Active Directory (Azure AD) bérlőhöz.

További információ az alkalmazásokkal való hozzájárulásról: [Azure Active Directory beleegyezési keretrendszer](../develop/consent-framework.md).

## <a name="prerequisites"></a>Előfeltételek

Az alábbi műveletek végrehajtásához globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként kell bejelentkeznie.

Az alkalmazásokhoz való hozzáférés korlátozásához felhasználói hozzárendelést kell megkövetelni, majd hozzá kell rendelnie a felhasználókat vagy csoportokat az alkalmazáshoz.  További információ: [felhasználók és csoportok hozzárendelésének módszerei](methods-for-assigning-users-and-groups.md).

Az Azure AD-portálon a műveletek végrehajtásához környezetfüggő PowerShell-parancsfájlokat érhet el.
 
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**lehetőséget.
3. Válassza ki azt az alkalmazást, amelynek a hozzáférését korlátozni szeretné.
4. Válassza az **engedélyek**lehetőséget. A parancssorban válassza az **engedélyek ellenőrzése**lehetőséget.

![Engedélyek ellenőrzése](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>Szeretném vezérelni az alkalmazásokhoz való hozzáférést

Javasoljuk, hogy a felhasználó-hozzárendelés beállítás bekapcsolásával korlátozza az alkalmazáshoz való hozzáférést.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**lehetőséget.
3. Válassza ki azt az alkalmazást, amelynek a hozzáférését korlátozni szeretné.
4. Válassza a **Tulajdonságok** lehetőséget, majd állítsa az Igen értékre a felhasználói követelmény megadása kötelező beállítást.
5. Válassza a **felhasználók és csoportok** lehetőséget, majd távolítsa el az alkalmazáshoz hozzárendelt nemkívánatos felhasználókat.
6. Rendelje hozzá a felhasználó (ka) t vagy csoportot az alkalmazáshoz.

Nem kötelező, ha a PowerShell használatával eltávolítja az alkalmazáshoz hozzárendelt összes felhasználót.

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>Egy alkalmazás összes engedélyét szeretném visszavonni

A PowerShell használatával visszavonja az alkalmazásnak biztosított összes engedélyt.

> [!NOTE]
> Az aktuálisan megadott engedély visszavonása nem állítja le a felhasználókat az alkalmazások reconseing. Ha le szeretné tiltani a felhasználók számára az alkalmazáshoz való hozzájárulást, olvassa el a [végfelhasználók beleegyezésének beállítása](configure-user-consent.md)az alkalmazásokhoz című témakört.

Nem kötelező, letilthatja az alkalmazást, hogy megakadályozza, hogy a felhasználók hozzáférjenek az alkalmazáshoz, és az alkalmazás hozzáférjen az adatokhoz.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**lehetőséget.
3. Válassza ki azt az alkalmazást, amelynek a hozzáférését korlátozni szeretné.
4. Válassza a **Tulajdonságok** lehetőséget, majd állítsa be az engedélyezve beállítást a felhasználók számára a bejelentkezéshez? a nem értékre.

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>Az alkalmazás gyanús, és meg szeretném vizsgálni

Javasoljuk, hogy az alkalmazáshoz való hozzáférés korlátozásához kapcsolja be a felhasználó-hozzárendelési beállítást, és tekintse át a felhasználók és a rendszergazdák által az alkalmazás számára biztosított engedélyeket.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
3. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**lehetőséget.
5. Válassza ki azt az alkalmazást, amelynek a hozzáférését korlátozni szeretné.
6. Válassza a **Tulajdonságok** lehetőséget, majd állítsa az Igen értékre a felhasználói követelmény megadása kötelező beállítást.
7. Válassza az **engedélyek** lehetőséget, és tekintse át a rendszergazda és a felhasználó beleegyezéses engedélyeit.

Nem kötelező:

- A PowerShell használatával távolítsa el az összes hozzárendelt felhasználót, hogy leállítsa őket az alkalmazásba való bejelentkezéshez.
- A PowerShell használatával érvénytelenítse az alkalmazáshoz hozzáféréssel rendelkező felhasználók frissítési jogkivonatait.
- Az alkalmazás összes engedélyének visszavonása a PowerShell használatával
- Tiltsa le az alkalmazást a felhasználók hozzáférésének letiltásához és az alkalmazások elérésének leállításához az adataihoz.


## <a name="application-is-malicious-and-im-compromised"></a>Az alkalmazás rosszindulatú, és biztonságban vagyok

Javasoljuk, hogy tiltsa le az alkalmazást, hogy letiltsa a felhasználók hozzáférését az alkalmazáshoz, és az alkalmazás hozzáférjen az adatokhoz. Ha ehelyett törli az alkalmazást, a végfelhasználók újra megadhatják az alkalmazást, és hozzáférést biztosíthatnak az adataihoz.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**lehetőséget.
3. Válassza ki azt az alkalmazást, amelynek a hozzáférését korlátozni szeretné.
4. Válassza a **Tulajdonságok** lehetőséget, majd másolja az objektumazonosítót.

### <a name="powershell-commands"></a>PowerShell-parancsok


Szolgáltatásnév AZONOSÍTÓjának lekérése

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**lehetőséget.
3. Válassza ki azt az alkalmazást, amelynek a hozzáférését korlátozni szeretné.
4. Válassza a **Tulajdonságok** lehetőséget, majd másolja az objektumazonosítót.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Az alkalmazáshoz hozzárendelt összes felhasználó eltávolítása.
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

Az alkalmazásnak biztosított engedélyek visszavonása

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
Frissítési tokenek érvénytelenítése
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>Következő lépések
- [Az alkalmazások beleegyezett az alkalmazásokkal és a beleegyezikés kiértékelésével](manage-consent-requests.md)
- [Felhasználói beleegyezés konfigurálása](configure-user-consent.md)
- [Rendszergazdai engedélyezési munkafolyamat konfigurálása](configure-admin-consent-workflow.md)
