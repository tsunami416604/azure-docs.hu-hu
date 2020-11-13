---
title: Sérült Azure-beli automanage-fiók javítása
description: Megtudhatja, hogyan javíthat egy hibás automatikus felügyeletű fiókot
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: ad54b37da8a4945162b507232f33083890ec1fff
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557672"
---
# <a name="repair-a-broken-automanage-account"></a>Sérült automanage-fiók javítása
Az automatikus [felügyelet fiók](./automanage-virtual-machines.md#automanage-account) a biztonsági környezet vagy az az identitás, amely alatt az automatizált műveletek történnek. Ha nemrégiben áthelyezett egy automatikusan felügyelt fiókot tartalmazó előfizetést egy új bérlőre, újra kell konfigurálnia az automatikusan felügyelt fiókot. Az automatikusan felügyelt fiók újrakonfigurálásához alaphelyzetbe kell állítania az identitás típusát, és hozzá kell rendelnie a fiókhoz a megfelelő szerepköröket.

## <a name="step-1-reset-automanage-account-identity-type"></a>1. lépés: az automanage Account Identity típusának alaphelyzetbe állítása
Alaphelyzetbe állíthatja a fiók identitásának az alábbi Azure Resource Manager (ARM) sablonját. Mentse a fájlt helyileg `armdeploy.json` vagy hasonló módon. Jegyezze fel az automanage-fiók nevét és helyét, mivel ezek a szükséges paraméterek az ARM-sablonban.

1. Hozzon létre egy új ARM-telepítést az alábbi sablonnal, és használja a `identityType = None`
    * Ezt az Azure CLI használatával teheti meg `az deployment sub create` . További információ a `az deployment sub` parancsról [here](https://docs.microsoft.com/cli/azure/deployment/sub).
    * Ezt a PowerShell használatával is elvégezheti a `New-AzDeployment` modullal. További információ a `New AzDeployment` modulról [here](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment).

1. Futtassa újra ugyanazt az ARM-sablont a `identityType = SystemAssigned`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>2. lépés: a megfelelő szerepkörök kiosztása az automanage-fiókhoz
Az automanage fiókhoz a közreműködői és az erőforrás-házirend közreműködő szerepköre szükséges az olyan virtuális gépeket tartalmazó előfizetésben, amelyek a felügyelet alatt állnak. Ezeket a szerepköröket a Azure Portal, ARM-sablonok vagy az Azure CLI használatával rendelheti hozzá.

Ha ARM-sablont vagy Azure CLI-t használ, szüksége lesz az automatikusan felügyelt fiókjának résztvevő-AZONOSÍTÓra (más néven objektumazonosító) is (ez nem szükséges, ha a Azure Portalt használja). Az alábbi módszerekkel találhatja meg az automanage-fiók résztvevő-AZONOSÍTÓját (objektumazonosító):

- [Azure CLI](https://docs.microsoft.com/cli/azure/ad/sp): használja az parancsot `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: keresse meg a **Azure Active Directory** , és keresse meg a fiókját név szerint. A **vállalati alkalmazások** területen válassza ki a fiók autokezelése nevet a megjelenítéskor.

### <a name="azure-portal"></a>Azure Portal
1. Az **előfizetések** területen navigáljon az Ön által felügyelt virtuális gépeket tartalmazó előfizetéshez.
1. Navigáljon a **hozzáférés-vezérlés (iam)** elemre.
1. Kattintson a **szerepkör-hozzárendelések hozzáadása** lehetőségre.
1. Válassza ki a **közreműködő** szerepkört, és írja be az automanage-fiók nevét.
1. Kattintson a **Mentés** gombra.
1. Ismételje meg a 3-5 lépést, ezúttal az **erőforrás-házirend közreműködői** szerepkörével.

### <a name="arm-template"></a>ARM-sablon
Futtassa a következő ARM-sablont. Szüksége lesz az automatikusan kezelt fiók elsődleges AZONOSÍTÓJÁRA – a résztvevő AZONOSÍTÓjának lekéréséhez szükséges lépések. Ha a rendszer kéri, adja meg.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
Futtassa az alábbi parancsot:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Contributor" --scope /subscriptions/<your subscription id>

az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription id>
```

## <a name="next-steps"></a>Következő lépések
További információ az Azure [automanage](./automanage-virtual-machines.md)szolgáltatásról.
