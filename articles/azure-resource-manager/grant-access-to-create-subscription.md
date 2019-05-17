---
title: Hozzáférést biztosít az Azure Enterprise-előfizetések létrehozása |} A Microsoft Docs
description: Megtudhatja, hogyan biztosíthat a felhasználónév vagy szolgáltatásnév az Azure Enterprise-előfizetések létrehozása programozott módon.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2019
ms.author: jureid
ms.openlocfilehash: 742658e36da956c46bd932b59903e68786c65b93
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794566"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Hozzáférés engedélyezése az Azure Enterprise-előfizetések (előzetes verzió) létrehozása

Az Azure ügyfeleként [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), engedélyt adhat egy másik felhasználó vagy szolgáltatás egyszerű hozhatnak létre előfizetéseket a fiók számítunk fel. Ebben a cikkben megismerheti, hogyan használható [szerepköralapú hozzáférés-vezérlés (RBAC)](../active-directory/role-based-access-control-configure.md) képes létrehozni az előfizetések és előfizetés-létrehozás naplózása megosztásához. A tulajdonosi szerepkörrel kell rendelkeznie a megosztani kívánt fiókhoz.

Előfizetés létrehozásával kapcsolatban lásd: [(előzetes verzió) az Azure Enterprise-előfizetések létrehozása programozott módon](programmatically-create-subscription.md).

## <a name="grant-subscription-creation-access-to-a-user-or-group"></a>Előfizetés létrehozása hozzáférést egy felhasználó vagy csoport

Olyan regisztrációs fiók-előfizetések létrehozása, felhasználók kell rendelkeznie a [RBAC tulajdonosi szerepkör](../role-based-access-control/built-in-roles.md#owner) a fiókban. Biztosíthat egy felhasználó vagy csoport számára az RBAC tulajdonosi szerepkör a regisztrációs fiókhoz az alábbi lépéseket:

### <a name="1-get-the-object-id-of-the-enrollment-account-you-want-to-grant-access-to"></a>1. A regisztrációs fiók hozzáférést biztosítani kívánt objektum Azonosítójának lekéréséhez

Az RBAC tulajdonosi szerepkör a regisztrációs fiókhoz adni más, a fiók tulajdonosa vagy a fiók az RBAC vagy kell lennie.

### <a name="resttabrest"></a>[REST](#tab/rest)

A kérelem hozzáfér az összes regisztrációs fiókok listáját:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure válaszol az hozzáfér az összes regisztrációs fiókok listáját:

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

Használja a `principalName` tulajdonság, amely RBAC tulajdonosi hozzáféréssel biztosítani a fiók azonosításához. Másolás a `name` -fiók. Például, ha az RBAC tulajdonosi hozzáférést szeretne a SignUpEngineering@contoso.com eszközregisztráció-fiókot, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ez a regisztrációs fiók az objektum Azonosítóját. Illessze be ezt az értéket, valahol, hogy használhatja azt, a következő lépésben `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Nyissa meg [Azure Cloud Shell](https://shell.azure.com/) , és válassza ki a PowerShell.

Használja a [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) parancsmag használatával listázhatja az összes regisztrációs fiókok hozzáfér.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure fűzi hozzá van hozzáférése a regisztrációs fiókok listáját:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

Használja a `principalName` tulajdonság RBAC tulajdonosi hozzáféréssel biztosítani a fiók azonosításához. Másolás a `ObjectId` -fiók. Például, ha az RBAC tulajdonosi hozzáférést szeretne a SignUpEngineering@contoso.com eszközregisztráció-fiókot, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Illessze be az Objektumazonosító valahol, hogy a következő lépésben, használhatja a `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja a [az számlázási regisztrációs-fióklista](https://aka.ms/EASubCreationPublicPreviewCLI) paranccsal listát készíthet az összes regisztrációs fiókok hozzáfér.

```azurecli-interactive 
az billing enrollment-account list
```

Azure fűzi hozzá van hozzáférése a regisztrációs fiókok listáját:

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

Használja a `principalName` tulajdonság, amely RBAC tulajdonosi hozzáféréssel biztosítani a fiók azonosításához. Másolás a `name` -fiók. Például, ha az RBAC tulajdonosi hozzáférést szeretne a SignUpEngineering@contoso.com eszközregisztráció-fiókot, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ez a regisztrációs fiók az objektum Azonosítóját. Illessze be ezt az értéket, valahol, hogy használhatja azt, a következő lépésben `enrollmentAccountObjectId`.

<a id="userObjectId"></a>

### <a name="2-get-object-id-of-the-user-or-group-you-want-to-give-the-rbac-owner-role-to"></a>2. A felhasználó vagy csoport kíván adni a RBAC tulajdonosi szerepkör objektum Azonosítójának lekéréséhez

1. Az Azure Portalon, keressen a **Azure Active Directory**.
1. Ha egy felhasználó hozzáférést biztosítani szeretné, kattintson a **felhasználók** a bal oldali menüben. Ha szeretne hozzáférést biztosítani egy csoporthoz, kattintson a **csoportok**.
1. Válassza ki a felhasználót vagy csoportot, szeretne adni az RBAC tulajdonosi szerepkört.
1. Ha egy felhasználó, akkor az objektum azonosítója a profil oldalon találja. Ha egy csoportot, akkor az objektum azonosítója lesz az Áttekintés lapon. Másolás a **ObjectID** a szövegdoboz jobb ikonra kattintva. Illessze be a valahol, hogy használhatja azt, a következő lépésben `userObjectId`.

### <a name="3-grant-the-user-or-group-the-rbac-owner-role-on-the-enrollment-account"></a>3. Adja meg a felhasználónak vagy a csoport az RBAC tulajdonosi szerepkör a regisztrációs fiókhoz

Az első két lépésben összegyűjtött értékeivel, adja meg a felhasználónak, vagy az RBAC tulajdonosi szerepkör a regisztrációs fiókhoz csoportnak.

### <a name="resttabrest-2"></a>[REST](#tab/rest-2)

Futtassa a következő parancsot, és cserélje le ```<enrollmentAccountObjectId>``` együtt a `name` az első lépésben kimásolt (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Cserélje le ```<userObjectId>``` a második lépésben kimásolt objektumazonosítójú.

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```

A tulajdonosi szerepkör hozzárendelése sikeres volt a regisztrációs fiók hatókörben, amikor Azure fűzi hozzá a szerepkör-hozzárendelés információkat:

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

### <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Futtassa a következő [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) parancsot, és cserélje le ```<enrollmentAccountObjectId>``` együtt a `ObjectId` gyűjti az első lépésben (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Cserélje le ```<userObjectId>``` az objektum azonosítója a második lépésben gyűjtött.

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
```

### <a name="azure-clitabazure-cli-2"></a>[Azure CLI](#tab/azure-cli-2)

Futtassa a következő [az szerepkör-hozzárendelés létrehozása](../active-directory/role-based-access-control-manage-access-azure-cli.md) parancsot, és cserélje le ```<enrollmentAccountObjectId>``` együtt a `name` az első lépésben kimásolt (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Cserélje le ```<userObjectId>``` az objektum azonosítója a második lépésben gyűjtött.

```azurecli-interactive
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
```

----

Miután egy felhasználó a regisztrációs fiókhoz tartozó RBAC tulajdonosa válik, akkor is [előfizetések létrehozása programozott módon](programmatically-create-subscription.md) alatta. Delegált felhasználó által létrehozott előfizetés még az eredeti fiók tulajdonosa, szolgáltatás-rendszergazda, de azt is a delegált felhasználó RBAC tulajdonosként alapértelmezés szerint.

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Naplózási, aki létrehozta az előfizetéseket Tevékenységnaplók segítségével

Az API-n keresztül létrehozott előfizetések nyomon követéséhez használhatja a [bérlői tevékenység Log API](/rest/api/monitor/tenantactivitylogs). Ez jelenleg nem lehetséges a PowerShell, CLI vagy az Azure portal segítségével nyomon követheti az előfizetés létrehozása.

1. Az Azure AD-bérlő bérlői rendszergazdájaként [emelheti a jogosultságszintet](../active-directory/role-based-access-control-tenant-admin-access.md), majd Olvasó szerepkört rendelhet a naplózást végző felhasználóhoz a `/providers/microsoft.insights/eventtypes/management` hatókörben.
1. A naplózási felhasználóként hívja a [bérlői tevékenység Log API](/rest/api/monitor/tenantactivitylogs) előfizetés létrehozása tevékenységek megtekintéséhez. Példa:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> Az API-t kényelmesen meghívhatja a parancssorból az [ARMClient](https://github.com/projectkudu/ARMClient) segítségével.

## <a name="next-steps"></a>További lépések

* Most, hogy a felhasználó vagy szolgáltatás-előfizetés létrehozása engedéllyel rendelkezik, használhatja az identitás [Azure Enterprise-előfizetések létrehozása programozott módon](programmatically-create-subscription.md).
* A .NET használatával előfizetések létrehozása egy példa: [mintát a githubon](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Azure Resource Manager és az API-kkal kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](resource-group-overview.md).
* Nagyszámú felügyeleti csoportok használatával előfizetések kezelésével kapcsolatos további tudnivalókért lásd: [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](management-groups-overview.md)
* Egy átfogó ajánlott eljárásokkal kapcsolatos útmutatás a nagy szervezetek is használnak az előfizetés-irányítás, olvassa el [Azure enterprise scaffold - előíró előfizetés-irányítás](/azure/architecture/cloud-adoption-guide/subscription-governance)
