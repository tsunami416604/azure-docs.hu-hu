---
title: Hozzáférést biztosít az Azure Enterprise-előfizetések létrehozása |} A Microsoft Docs
description: Megtudhatja, hogyan biztosíthat a felhasználónév vagy szolgáltatásnév az Azure Enterprise-előfizetések létrehozása programozott módon.
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 7a2397328f715dbf63246e8d4aaa789b5986b3b4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112563"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Hozzáférés engedélyezése az Azure Enterprise-előfizetések (előzetes verzió) létrehozása

Az Azure ügyfeleként [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), engedélyt adhat egy másik felhasználó vagy szolgáltatás egyszerű hozhatnak létre előfizetéseket a fiók számítunk fel. Ebben a cikkben megismerheti, hogyan használható [szerepköralapú hozzáférés-vezérlés (RBAC)](../active-directory/role-based-access-control-configure.md) képes létrehozni az előfizetések és előfizetés-létrehozás naplózása megosztásához. A tulajdonosi szerepkörrel kell rendelkeznie a megosztani kívánt fiókhoz.

Előfizetés létrehozásával kapcsolatban lásd: [(előzetes verzió) az Azure Enterprise-előfizetések létrehozása programozott módon](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>A delegált hozzáférés RBAC használatával regisztrációs fiókhoz

Biztosíthat egy másik felhasználó vagy szolgáltatásnév egy adott fiókon-előfizetéseket hozhasson létre [tegye lehetővé számukra egy RBAC tulajdonosi szerepkör a hatókörben, a regisztrációs fiók](../active-directory/role-based-access-control-manage-access-rest.md). Az alábbi példában a felhasználó adja meg a bérlő `principalId` , `<userObjectId>` (a SignUpEngineering@contoso.com) egy tulajdonosi szerepkör a regisztrációs fiókhoz. A regisztrációs fiók- és résztvevő-azonosító talál [(előzetes verzió) az Azure Enterprise-előfizetések létrehozása programozott módon](programmatically-create-subscription.md).

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Használja a [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) biztosíthat egy másik felhasználó tulajdonosi hozzáféréssel a regisztrációs fiókhoz.

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja a [az szerepkör-hozzárendelés létrehozása](../active-directory/role-based-access-control-manage-access-azure-cli.md) biztosíthat egy másik felhasználó tulajdonosi hozzáféréssel a regisztrációs fiókhoz.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Miután egy felhasználó a regisztrációs fiókhoz tartozó RBAC tulajdonosa válik, azok alatta előfizetések programozott módon hozhat létre. Delegált felhasználó által létrehozott előfizetés még az eredeti fiók tulajdonosa, szolgáltatás-rendszergazda, de azt is a delegált felhasználó tulajdonosként alapértelmezés szerint. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Naplózási, aki létrehozta az előfizetéseket Tevékenységnaplók segítségével

Az API-n keresztül létrehozott előfizetések nyomon követéséhez használhatja a [bérlői tevékenység Log API](/rest/api/monitor/tenantactivitylogs). Ez jelenleg nem lehetséges a PowerShell, CLI vagy az Azure portal segítségével nyomon követheti az előfizetés létrehozása.

1. Az Azure AD-bérlő bérlői rendszergazdájaként [emelheti a jogosultságszintet](../active-directory/role-based-access-control-tenant-admin-access.md), majd Olvasó szerepkört rendelhet a naplózást végző felhasználóhoz a `/providers/microsoft.insights/eventtypes/management` hatókörben.
1. A naplózási felhasználóként hívja a [bérlői tevékenység Log API](/rest/api/monitor/tenantactivitylogs) előfizetés létrehozása tevékenységek megtekintéséhez. Példa:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Az API-t kényelmesen meghívhatja a parancssorból az [ARMClient](https://github.com/projectkudu/ARMClient) segítségével.

## <a name="next-steps"></a>További lépések

* Most, hogy a felhasználó vagy szolgáltatás-előfizetés létrehozása engedéllyel rendelkezik, használhatja az identitás [Azure Enterprise-előfizetések létrehozása programozott módon](programmatically-create-subscription.md).
* A .NET használatával előfizetések létrehozása egy példa: [mintát a githubon](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Azure Resource Manager és az API-kkal kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](resource-group-overview.md).
* Nagyszámú felügyeleti csoportok használatával előfizetések kezelésével kapcsolatos további tudnivalókért lásd: [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](management-groups-overview.md)
* Egy átfogó ajánlott eljárásokkal kapcsolatos útmutatás a nagy szervezetek is használnak az előfizetés-irányítás, olvassa el [Azure enterprise scaffold - előíró előfizetés-irányítás](/azure/architecture/cloud-adoption-guide/subscription-governance)
