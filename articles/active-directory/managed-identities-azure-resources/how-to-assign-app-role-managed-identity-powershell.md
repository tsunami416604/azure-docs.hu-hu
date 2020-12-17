---
title: Felügyelt identitás kiosztása egy alkalmazás-szerepkörhöz a PowerShell használatával – Azure AD
description: Részletes útmutató a felügyelt identitások egy másik alkalmazás szerepkörhöz való hozzárendeléséhez a PowerShell használatával.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8890eb76e3f9521aa5070789f969ffeb8f3e4ec6
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618930"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Felügyelt identitás-hozzáférés kiosztása egy alkalmazás-szerepkörhöz a PowerShell használatával

Az Azure-erőforrások felügyelt identitásai olyan Azure-szolgáltatásokat biztosítanak, mint a Azure Active Directory identitása. A kódban nem szükséges hitelesítő adatok nélkül működniük. Az Azure-szolgáltatások ezt az identitást használják az Azure AD-hitelesítést támogató szolgáltatások hitelesítésére. Az alkalmazás szerepkörei szerepköralapú hozzáférés-vezérlést biztosítanak, és lehetővé teszik a szolgáltatások számára az engedélyezési szabályok megvalósítását.

Ebből a cikkből megtudhatja, hogyan rendelhet hozzá felügyelt identitást egy másik alkalmazás által az Azure AD PowerShell használatával közzétett alkalmazási szerepkörhöz.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#managed-identity-types)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A példaként szolgáló szkriptek futtatásához két lehetőség közül választhat:
    - Használja a [Azure Cloud shellt](../../cloud-shell/overview.md), amelyet a kódrészletek jobb felső sarkában található **kipróbálás** gomb használatával nyithat meg.
    - Futtassa helyileg a parancsfájlokat az [Azure ad PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)legújabb verziójának telepítésével.

## <a name="use-azure-ad-to-assign-a-managed-identity-access-to-another-applications-app-role"></a>Felügyelt identitás-hozzáférés kiosztása az Azure AD-vel egy másik alkalmazás-szerepkörhöz

1. Felügyelt identitás engedélyezése Azure-erőforrásokon, [például egy Azure-beli virtuális gépen](qs-configure-powershell-windows-vm.md).

1. A felügyelt identitás szolgáltatásnév AZONOSÍTÓjának megkeresése.

   A **rendszer által hozzárendelt felügyelt identitások esetében** a Azure Portal az erőforrás **identitása** lapon található objektum azonosítóját. Az objektumazonosító megkereséséhez a következő PowerShell-parancsfájlt is használhatja. Szüksége lesz az 1. lépésben létrehozott erőforrás-AZONOSÍTÓra, amely az erőforrás **Tulajdonságok** lapjának Azure Portal érhető el.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **Felhasználó által hozzárendelt felügyelt identitás** esetén a felügyelt identitás OBJEKTUMának azonosítóját a Azure Portal az erőforrás **áttekintő** oldalán találja. Az objektumazonosító megkereséséhez a következő PowerShell-parancsfájlt is használhatja. Szüksége lesz a felhasználó által hozzárendelt felügyelt identitás erőforrás-AZONOSÍTÓJÁRA.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Hozzon létre egy új alkalmazás-regisztrációt annak a szolgáltatásnak a jelölésére, amelynek a felügyelt identitása kérelmet küld. Ha a felügyelt identitás számára elérhető API vagy szolgáltatás már rendelkezik egy egyszerű szolgáltatással az Azure AD-bérlőben, hagyja ki ezt a lépést. Ha például a felügyelt identitás elérését szeretné biztosítani a Microsoft Graph API számára, kihagyhatja ezt a lépést.

1. Keresse meg a szolgáltatásalkalmazás egyszerű szolgáltatásnév-AZONOSÍTÓját. Ezt a Azure Portal használatával találja meg. Nyissa meg Azure Active Directory a **vállalati alkalmazások** lapot, majd keresse meg az alkalmazást, és keresse meg az **objektumazonosítót**. Az egyszerű szolgáltatásnév AZONOSÍTÓját az alábbi PowerShell-parancsfájl használatával is megkeresheti a megjelenítendő névvel:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Az alkalmazások megjelenítendő nevei nem egyediek, ezért győződjön meg arról, hogy a megfelelő alkalmazás egyszerű szolgáltatásnév beszerzése megtörténik.

1. Vegyen fel egy alkalmazás- [szerepkört](../develop/howto-add-app-roles-in-azure-ad-apps.md) a 3. lépésben létrehozott alkalmazáshoz. A szerepkört a Azure Portal használatával vagy a Microsoft Graph használatával hozhatja létre. Hozzáadhat például egy olyan alkalmazás-szerepkört, amely a következőhöz hasonló:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Rendelje hozzá az alkalmazás szerepkört a felügyelt identitáshoz. Az alkalmazás szerepkörének hozzárendeléséhez a következő információkra lesz szüksége:
    * `managedIdentityObjectId`: a felügyelt identitás egyszerű szolgáltatásnév, amelyet a 2. lépésben talált.
    * `serverApplicationObjectId`: a kiszolgálóalkalmazás azon szolgáltatásnév, amelyet a 4. lépésben talált.
    * `appRoleId`: a kiszolgálói alkalmazás által az 5. lépésben létrehozott alkalmazás-szerepkör azonosítója, amely a példában az alkalmazás szerepkör-azonosítója `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   A szerepkör-hozzárendelés hozzáadásához hajtsa végre a következő PowerShell-parancsfájlt:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverApplicationObjectId
    ```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitás az Azure-erőforrásokhoz – áttekintés](overview.md)
- A felügyelt identitás Azure-beli virtuális gépen való engedélyezésével kapcsolatban lásd: [felügyelt identitások konfigurálása](qs-configure-powershell-windows-vm.md)Azure-beli virtuális gépeken a PowerShell használatával.
