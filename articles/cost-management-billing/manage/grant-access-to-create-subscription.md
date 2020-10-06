---
title: Hozzáférés biztosítása Azure Enterprise-előfizetés létrehozáshoz
description: Megismerheti, hogyan biztosíthatja az Azure Enterprise-előfizetés programozott létrehozásának képességét egy felhasználó vagy szolgáltatásnév számára.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.reviewer: amberb
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: banders
ms.openlocfilehash: 661b088d024a6da631fa06fbd97131091b9f650b
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371881"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Hozzáférés biztosítása Azure Enterprise-előfizetés létrehozáshoz (előzetes verzió)

[Nagyvállalati Szerződéssel (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) rendelkező Azure-ügyfeleként engedélyt adhat egy másik felhasználónak vagy szolgáltatásnévnek olyan előfizetések létrehozására, amelyek az Ön fiókjához lesznek kiszámlázva. Ez a cikk bemutatja, hogyan oszthatja meg az előfizetések létrehozásának képességét az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) segítségével, illetve hogyan naplózhatja az előfizetések létrehozását. Fiók megosztásához tulajdonosi szerepkörrel kell rendelkeznie az adott fiókra vonatkozóan.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Hozzáférés biztosítása

Ha [előfizetéseket szeretnének létrehozni egy regisztrációs fiókhoz](programmatically-create-subscription.md), a felhasználóknak Azure RBAC-alapú [Tulajdonos szerepkörrel](../../role-based-access-control/built-in-roles.md#owner) kell rendelkezniük az adott fiók esetében. A következő lépésekkel biztosíthatja az Azure RBAC-alapú Tulajdonos szerepkört egy regisztrációs fiók esetében, egy felhasználó vagy felhasználócsoport számára:

1. Kérje le annak a regisztrációs fióknak az objektumazonosítóját, amelyhez hozzáférést szeretne adni.

    Ha Azure RBAC-alapú Tulajdonos szerepkört szeretne biztosítani mások számára egy regisztrációs fiók esetében, akkor a fiók tulajdonosának vagy Azure RBAC-tulajdonosának kell lennie.

    # <a name="rest"></a>[REST](#tab/rest)

    Kérelem azon fiókok felsorolására, amelyekhez hozzáféréssel rendelkezik:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Az Azure megadja az összes olyan regisztrációs fiók listáját, amelyhez hozzáféréssel rendelkezik:

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

    Használja a `principalName` tulajdonságot a fiók azonosításához, amelyhez Azure RBAC-tulajdonosi hozzáférést kíván biztosítani. Másolja ki a fiók `name` elemét. Ha például Azure RBAC-tulajdonosi hozzáférést szeretne biztosítani a SignUpEngineering@contoso.com regisztrációs fiókhoz, a következőt kell másolnia: ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ez a regisztrációs fiók objektumazonosítója. Illessze be valahova ezt az értéket, hogy a következő lépésben használni tudja mint `enrollmentAccountObjectId`.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    A [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) parancsmaggal jelenítse meg az Ön által hozzáférhető összes regisztrációs fiókot tartalmazó listát. Válassza a **Kipróbálás** lehetőséget az [Azure Cloud Shell](https://shell.azure.com/) megnyitásához. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéjablakokra, és válassza a **Beillesztés**lehetőséget.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Az Azure megadja azon regisztrációs fiókok listáját, amelyekhez hozzáféréssel rendelkezik:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Használja a `principalName` tulajdonságot a fiók azonosításához, amelyhez Azure RBAC-tulajdonosi hozzáférést kíván biztosítani. Másolja ki a fiók `ObjectId` elemét. Ha például Azure RBAC-tulajdonosi hozzáférést szeretne biztosítani a SignUpEngineering@contoso.com regisztrációs fiókhoz, a következőt kell másolnia: ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Illessze be valahova ezt az objektumazonosítót, hogy a következő lépésben használni tudja mint `enrollmentAccountObjectId`.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    Használja az [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) parancsot azon regisztrációs fiókok listázásához, amelyekhez hozzáféréssel rendelkezik. Válassza a **Kipróbálás** lehetőséget az [Azure Cloud Shell](https://shell.azure.com/) megnyitásához. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéjablakokra, és válassza a **Beillesztés**lehetőséget.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Az Azure megadja azon regisztrációs fiókok listáját, amelyekhez hozzáféréssel rendelkezik:

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

    Használja a `principalName` tulajdonságot a fiók azonosításához, amelyhez Azure RBAC-tulajdonosi hozzáférést kíván biztosítani. Másolja ki a fiók `name` elemét. Ha például Azure RBAC-tulajdonosi hozzáférést szeretne biztosítani a SignUpEngineering@contoso.com regisztrációs fiókhoz, a következőt kell másolnia: ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ez a regisztrációs fiók objektumazonosítója. Illessze be valahova ezt az értéket, hogy a következő lépésben használni tudja mint `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Kérje le annak a felhasználónak vagy csoportnak az objektumazonosítóját, amelynek Azure RBAC-alapú Tulajdonos szerepkört szeretne adni

    1. Az Azure Portalon keressen az **Azure Active Directory** kifejezésre.
    1. Ha felhasználói hozzáférést szeretne biztosítani, a bal oldali menüben válassza a **Felhasználók** lehetőséget. Ha egy csoportnak kíván hozzáférést biztosítani, válassza a **Csoportok** lehetőséget.
    1. Válassza ki azt a felhasználót vagy csoportot, amelynek Azure RBAC-alapú Tulajdonos szerepkört szeretne adni.
    1. Ha egy felhasználót választott, az objektumazonosító a Profil oldalon található. Ha egy csoportot választott, az objektumazonosító az Áttekintés oldalon található. Másolja az **Objektumazonosítót** a szövegmező jobb oldalán található ikon kiválasztásával. Illessze be valahova, hogy a következő lépésben használni tudja mint `userObjectId`.

1. Azure RBAC-alapú Tulajdonos szerepkör biztosítása egy felhasználó vagy csoport számára a regisztrációs fiók esetében

    Az első két lépésben összegyűjtött értékek használatával adja meg a felhasználó vagy csoport számára az Azure RBAC-alapú Tulajdonos szerepkört a regisztrációs fiókra vonatkozóan.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Futtassa a következő parancsot, amelyben cserélje le az ```<enrollmentAccountObjectId>``` értéket a `name` első lépésben kimásolt értékére (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Cserélje le a ```<userObjectId>``` értéket a második lépésben kimásolt objektumazonosítóra.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Ha a tulajdonosi szerepkör sikeresen hozzá lett rendelve a regisztrációs fiók hatókörében, az Azure megadja a szerepkör-hozzárendelés információit:

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

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    Futtassa a következő [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md) parancsot, amelyben cserélje le az ```<enrollmentAccountObjectId>``` értéket a `ObjectId` első lépésben begyűjtött értékére (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Cserélje le a ```<userObjectId>``` értéket a második lépésben begyűjtött objektumazonosítóra.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    Futtassa a következő [az role assignment create](../../role-based-access-control/role-assignments-cli.md)parancsot, amelyben cserélje le az ```<enrollmentAccountObjectId>``` értéket a `name` első lépésben kimásolt értékére (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Cserélje le a ```<userObjectId>``` értéket a második lépésben begyűjtött objektumazonosítóra.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Miután a felhasználó a regisztrációs fiók Azure RBAC-tulajdonosává válik, [programozott módon hozhat létre előfizetéseket](programmatically-create-subscription.md) benne. A delegált felhasználó által létrehozott előfizetés szolgáltatás-rendszergazdája továbbra is az eredeti fióktulajdonos, de a delegált felhasználó is alapértelmezett Azure RBAC-tulajdonos lesz.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Az előfizetések létrehozóinak naplózása tevékenységnaplókkal

Ha ezzel az API-val szeretné nyomon követni a létrehozott előfizetéseket, használja a [Tenant Activity Log API-t](/rest/api/monitor/tenantactivitylogs). Az előfizetések létrehozásának nyomon követéséhez jelenleg nem használható a PowerShell, a parancssori felület vagy az Azure Portal.

1. Az Azure AD-bérlő bérlői rendszergazdájaként [emelheti a jogosultságszintet](../../role-based-access-control/elevate-access-global-admin.md), majd Olvasó szerepkört rendelhet a naplózást végző felhasználóhoz a `/providers/microsoft.insights/eventtypes/management` hatókörben. Ez a hozzáférés a [Olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör, a [figyelési közreműködő](../../role-based-access-control/built-in-roles.md#monitoring-contributor) szerepkör vagy egy [egyéni szerepkör](../../role-based-access-control/custom-roles.md) számára érhető el.
1. Naplózási felhasználóként meghívhatja a [Tenant Activity Log API-t](/rest/api/monitor/tenantactivitylogs) az előfizetés-létrehozási tevékenységek megtekintésére. Példa:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Az API-t kényelmesen meghívhatja a parancssorból az [ARMClient](https://github.com/projectkudu/ARMClient) segítségével.

## <a name="next-steps"></a>Következő lépések

* Most, hogy a felhasználó vagy a szolgáltatásnév jogosult előfizetést létrehozni, ezt az identitást [Azure Enterprise-előfizetések programozott létrehozására](programmatically-create-subscription.md) is használhatja.
* Előfizetések .NET használatával történő létrehozását bemutató példáért tekintse meg a [GitHubon elérhető mintakódot](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* További információt az Azure Resource Managerről és az API-jairól az [Azure Resource Manager áttekintésében](../../azure-resource-manager/management/overview.md) talál.
* Több előfizetés felügyeleti csoportok használatával történő kezeléséről az [erőforrások Azure-beli felügyeleti csoportokkal való rendszerezését](../../governance/management-groups/overview.md) ismertető részben talál további információt.
* A nagy szervezeteknek szóló, az előfizetés-irányításra vonatkozó ajánlott eljárásokra vonatkozó átfogó útmutatóért lásd: [ (Azure nagyvállalati struktúra – előíró előfizetés-irányítás)](/azure/architecture/cloud-adoption-guide/subscription-governance).
