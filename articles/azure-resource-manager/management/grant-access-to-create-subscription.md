---
title: Hozzáférés az Azure Enterprise-előfizetések létrehozásához
description: Ismerje meg, hogyan adhat lehetővé egy felhasználónak vagy egyszerű szolgáltatásnak az Azure Enterprise-előfizetések programozott létrehozását.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478876"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Hozzáférés megadása Azure Enterprise-előfizetések létrehozásához (előzetes verzió)

Nagyvállalati [szerződéssel (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)kapcsolatos Azure-ügyfélként engedélyt adhat egy másik felhasználónak vagy egyszerű szolgáltatásnak a fiókjába számlázott előfizetések létrehozásához. Ebben a cikkben megtudhatja, hogyan [használhatja a szerepköralapú hozzáférés-vezérlést (RBAC)](../../active-directory/role-based-access-control-configure.md) az előfizetések létrehozásának lehetővé tételéhez, és hogyan naplózhatja az előfizetések létrehozása. A megosztani kívánt fióktulajdonosi szerepkörsel kell rendelkeznie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Hozzáférés biztosítása

Ha előfizetéseket szeretne [létrehozni egy regisztrációs fiók alatt,](programmatically-create-subscription.md)a felhasználóknak [rbac-tulajdonosszerepkörsel](../../role-based-access-control/built-in-roles.md#owner) kell rendelkezniük a fiókhoz. Az alábbi lépésekkel egy felhasználónak vagy felhasználók egy csoportjának adhat meg RBAC-tulajdonosi szerepkört egy regisztrációs fiókhoz:

1. Annak a regisztrációs fióknak az objektumazonosítójának beszereznie, amelyhez hozzáférést kíván biztosítani

    Ahhoz, hogy másoknak a regisztrációs fiók RBAC-tulajdonosszerepkörét adja meg, a fiók tulajdonosának vagy a fiók RBAC-tulajdonosának kell lennie.

    # <a name="rest"></a>[Többi](#tab/rest)

    Az összes olyan regisztrációs fiók listázásának kérése, amelyhez hozzáférése van:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Az Azure válaszol az összes olyan regisztrációs fiók listájával, amelyhez hozzáférése van:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    A `principalName` tulajdonság segítségével azonosíthatja azt a fiókot, amelyhez hozzáférést kíván adni az RBAC-tulajdonosnak. Másolja `name` a fiók. Ha például az RBAC-tulajdonosnak hozzáférést SignUpEngineering@contoso.com szeretne adni a ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```regisztrációs fiókhoz, másolja a programot. Ez a regisztrációs fiók objektumazonosítója. Illessze be ezt az értéket valahova, `enrollmentAccountObjectId`hogy a következő lépésben használhassa .

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell)

    A [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) parancsmag segítségével sorolja fel az összes olyan regisztrációs fiókot, amelyhez hozzáférése van. Válassza **a Próbálja ki az** Azure Cloud [Shell](https://shell.azure.com/)megnyitásához lehetőséget. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablakaira, és válassza a **Beillesztés parancsot.**

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Az Azure a regisztrációs fiókok listájával válaszol, amelyekhez hozzáférése van:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    A `principalName` tulajdonság segítségével azonosíthatja azt a fiókot, amelyhez hozzáférést kíván adni az RBAC tulajdonosának. Másolja `ObjectId` a fiók. Ha például az RBAC-tulajdonosnak hozzáférést SignUpEngineering@contoso.com szeretne adni a ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```regisztrációs fiókhoz, másolja a programot. Illessze be az objektumazonosítót valahova, hogy a `enrollmentAccountObjectId`következő lépésben a .

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    Az [az számlázási regisztrációs fiók lista](https://aka.ms/EASubCreationPublicPreviewCLI) paranccsal listázhatja az összes olyan regisztrációs fiókot, amelyhez hozzáférése van. Válassza **a Próbálja ki az** Azure Cloud [Shell](https://shell.azure.com/)megnyitásához lehetőséget. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablakaira, és válassza a **Beillesztés parancsot.**

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Az Azure a regisztrációs fiókok listájával válaszol, amelyekhez hozzáférése van:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    A `principalName` tulajdonság segítségével azonosíthatja azt a fiókot, amelyhez hozzáférést kíván adni az RBAC-tulajdonosnak. Másolja `name` a fiók. Ha például az RBAC-tulajdonosnak hozzáférést SignUpEngineering@contoso.com szeretne adni a ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```regisztrációs fiókhoz, másolja a programot. Ez a regisztrációs fiók objektumazonosítója. Illessze be ezt az értéket valahova, `enrollmentAccountObjectId`hogy a következő lépésben használhassa .

1. <a id="userObjectId"></a>Annak a felhasználónak vagy csoportnak az objektumazonosítóját, amilyennek az RBAC-tulajdonos szerepkört adni szeretné

    1. Az Azure Portalon keressen az **Azure Active Directoryban.**
    1. Ha felhasználói hozzáférést szeretne adni, kattintson a Bal oldali menü **Felhasználók parancsára.** Ha hozzáférést szeretne adni egy csoportnak, kattintson a **Csoportok gombra.**
    1. Válassza ki azt a felhasználót vagy csoportot, amelynek az RBAC tulajdonosi szerepkört szeretné adni.
    1. Ha kiválasztott egy felhasználót, az objektumazonosítót a Profil lapon találja. Ha kijelölt egy csoportot, az objektumazonosító az Áttekintés lapon lesz. Másolja az **Objektumazonosítót** a szövegdoboz jobb oldalán található ikonra kattintva. Illessze be ezt valahova, így használhatja a következő lépésben, mint `userObjectId`.

1. A felhasználó vagy csoport rbac-tulajdonosi szerepkörének megadása a regisztrációs fiókban

    Az első két lépésben összegyűjtött értékek használatával adja meg a felhasználónak vagy csoportnak az RBAC tulajdonosszerepkört a regisztrációs fiókban.

    # <a name="rest"></a>[Többi](#tab/rest-2)

    Futtassa a ```<enrollmentAccountObjectId>``` következő `name` parancsot, és cserélje```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```le az első lépésben másolt ( ) parancsot. Cserélje ```<userObjectId>``` le a második lépésből másolt objektumazonosítóra.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Ha a tulajdonosszerepkör sikeresen hozzá van rendelve a regisztrációs fiók hatókörén, az Azure válaszol a szerepkör-hozzárendelés adataival:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell-2)

    Futtassa a következő [New-AzRoleAssignment](../../active-directory/role-based-access-control-manage-access-powershell.md) parancsot, és cserélje le ```<enrollmentAccountObjectId>``` az `ObjectId` első lépésben (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cserélje ```<userObjectId>``` le a második lépésben gyűjtött objektumazonosítóra.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    Futtassa a következő [az szerepkör-hozzárendelés létrehozási](../../active-directory/role-based-access-control-manage-access-azure-cli.md) parancsot, és cserélje le ```<enrollmentAccountObjectId>``` az `name` első lépésben```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```( . Cserélje ```<userObjectId>``` le a második lépésben gyűjtött objektumazonosítóra.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Ha egy felhasználó rbac-tulajdonossá válik a regisztrációs fiókhoz, [programozott módon hozhat létre alatta előfizetéseket.](programmatically-create-subscription.md) A delegált felhasználó által létrehozott előfizetés továbbra is rendelkezik az eredeti fióktulajdonossal szolgáltatásrendszergazdaként, de alapértelmezés szerint a delegált felhasználó is rendelkezik RBAC-tulajdonosként.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Az előfizetések létrehozásának naplózása tevékenységnaplók használatával

Az API-n keresztül létrehozott előfizetések nyomon követéséhez használja a [bérlői tevékenységnapló API-t.](/rest/api/monitor/tenantactivitylogs) Jelenleg nem használható a PowerShell, a CLI vagy az Azure Portal az előfizetések létrehozásának nyomon követéséhez.

1. Az Azure AD-bérlő bérlői rendszergazdájaként [emelheti a jogosultságszintet](../../active-directory/role-based-access-control-tenant-admin-access.md), majd Olvasó szerepkört rendelhet a naplózást végző felhasználóhoz a `/providers/microsoft.insights/eventtypes/management` hatókörben.
1. A naplózási felhasználóként hívja meg a [bérlői tevékenységnapló API-t](/rest/api/monitor/tenantactivitylogs) az előfizetés-létrehozási tevékenységek megtekintéséhez. Példa:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Az API-t kényelmesen meghívhatja a parancssorból az [ARMClient](https://github.com/projectkudu/ARMClient) segítségével.

## <a name="next-steps"></a>További lépések

* Most, hogy a felhasználó vagy az egyszerű szolgáltatás rendelkezik engedéllyel egy előfizetés létrehozásához, használhatja ezt az [identitást az Azure Enterprise-előfizetések programozott létrehozásához.](programmatically-create-subscription.md)
* A .NET használatával létrehozott előfizetések például [a GitHub mintakódját](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)című témakörben található.
* Ha többet szeretne megtudni az Azure Resource Managerről és api-jairól, olvassa el az [Azure Resource Manager áttekintése című témakört.](overview.md)
* Ha többet szeretne tudni arról, hogy hogyan kezelheti a nagyszámú előfizetést felügyeleti csoportok használatával, olvassa [el az Erőforrások rendszerezése az Azure felügyeleti csoportokkal](../../governance/management-groups/overview.md)
* Az előfizetés-szabályozással kapcsolatos átfogó gyakorlati útmutatót a nagy szervezetek számára az [Azure vállalati állványzat – előíró előfizetés-szabályozás](/azure/architecture/cloud-adoption-guide/subscription-governance)
