---
title: Hozzáférést biztosíthat a vállalati Azure-előfizetések létrehozása |} Microsoft Docs
description: Megtudhatja, hogyan adhat egy felhasználó vagy az egyszerű szolgáltatásnév programozott módon a vállalati Azure-előfizetések létrehozása a képességét.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: jlian
ms.openlocfilehash: 4c5d505f431ef684b73adc04629464883d336a5b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238272"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Hozzáférés engedélyezése az Azure vállalati előfizetések (előzetes verzió) létrehozása

Egy Azure ügyfélként a [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), egy másik felhasználó vagy szolgáltatás egyszerű engedélyt adhat a fiókjához számlázva előfizetést létrehozni. Ebben a cikkben megismerheti, hogyan használható [szerepköralapú hozzáférés-vezérlést (RBAC)](../active-directory/role-based-access-control-configure.md) képes létrehozni az előfizetések és az előfizetés és naplózása megosztásához.

Előfizetés létrehozása: [programozott módon létrehozása az Azure vállalati előfizetések (előzetes verzió)](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Egy olyan beléptetési fiókot az RBAC használata delegálása

Egy másik felhasználó vagy szolgáltatás egyszerű adhat egy adott fiók-előfizetések létrehozása olyan [számukra az RBAC tulajdonosi szerepkört, a beléptetési fiók a hatókörben](../active-directory/role-based-access-control-manage-access-rest.md). Az alábbi példában a felhasználó adja meg a bérlői `principalId` a `<userObjectId>` (a SignUpEngineering@contoso.com) egy tulajdonosi szerepkört, a beléptetési fiók. Az eszközregisztráció-fiók és résztvevő-azonosító, lásd: [programozott módon létrehozása az Azure vállalati előfizetések (előzetes verzió)](programmatically-create-subscription.md).

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
Tulajdonosi szerepkör sikeresen hozzá van rendelve, a beléptetési fiók hatókörben, amikor a szerepkör-hozzárendelés információkkal válaszol, Azure:

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Használja a [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) adhat egy másik felhasználónak tulajdonosi hozzáférés igénylés fiókjára.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja a [az szerepkör-hozzárendelés létrehozása](../active-directory/role-based-access-control-manage-access-azure-cli.md) adhat egy másik felhasználónak tulajdonosi hozzáférés igénylés fiókjára.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Miután egy felhasználó beléptetési fiókja RBAC tulajdonosát válik, annak alapján előfizetések programozott módon hozhatnak létre. Delegált felhasználó által létrehozott előfizetés még az eredeti tulajdonosa szolgáltatásadminisztrátoréval, de is rendelkezik a delegált felhasználó tulajdonos alapértelmezés szerint. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Tevékenység-naplók segítségével előfizetések létrehozó naplózási

Ez az API-n keresztül létrehozott előfizetések követheti nyomon, használja a [bérlői tevékenység napló API](/rest/api/monitor/tenantactivitylogs). Jelenleg nem lehetséges a PowerShell, a CLI vagy az Azure-portálon nyomon követéséhez előfizetés létrehozása.

1. Bérlői rendszergazdaként az Azure AD-bérlő [jogosultságszintjének emelése](../active-directory/role-based-access-control-tenant-admin-access.md) majd olvasó szerepkör hozzárendelése a naplózási felhasználói a hatókörben `/providers/microsoft.insights/eventtypes/management`.
1. A naplózási felhasználóként hívja a [bérlői tevékenység napló API](/rest/api/monitor/tenantactivitylogs) előfizetés létrehozása tevékenységek. Példa:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Kényelmesen hívja az API a parancssorból, próbálja ki a [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>További lépések

* Most, hogy a felhasználó vagy szolgáltatás egyszerű előfizetés létrehozása engedéllyel rendelkezik, használhatja az identitásukat [programozott módon a vállalati Azure-előfizetések létrehozása](programmatically-create-subscription.md).
* Például a .NET használatával előfizetések létrehozása, [mintát a Githubon code](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Azure Resource Manager és az API-k kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](resource-group-overview.md).
* A felügyeleti csoportok előfizetések nagy számú kezelésével kapcsolatos további tudnivalókért lásd: [az Azure felügyeleti csoportok-erőforrások rendszerezése](management-groups-overview.md)
* Egy átfogó bevált gyakorlatokat tartalmazó útmutatóval nagy méretű szervezeteknek előfizetés cégirányításra fókuszálnak, olvassa el [Azure enterprise scaffold - részletes utasításokkal megadott előfizetés-irányítás](/azure/architecture/cloud-adoption-guide/subscription-governance)
