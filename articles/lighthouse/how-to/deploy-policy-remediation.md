---
title: Javítható szabályzat telepítése
description: Az Azure Világítótoronyon keresztül szervizelési feladatot használó házirendek telepítéséhez létre kell hoznia egy felügyelt identitást az ügyfél-bérlőben.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: eb473fe2f589cf719e3944c887d46e75e9e7fdbf
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670491"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>A delegált előfizetésen belüli szervizelésre alkalmas szabályzat telepítése

Az [Azure Lighthouse](../overview.md) lehetővé teszi a szolgáltatók számára, hogy szabályzat-definíciókat hozzanak létre és szerkesszenek egy delegált előfizetésen belül. Ahhoz azonban, hogy olyan házirendeket helyezzen üzembe, amelyek [szervizelési feladatot](../../governance/policy/how-to/remediate-resources.md) használnak (azaz a [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) vagy a [módosítás](../../governance/policy/concepts/effects.md#modify) hatása), létre kell hoznia egy [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md) az ügyfél bérlője számára. Ezt a felügyelt identitást Azure Policy használhatja a sablon üzembe helyezéséhez a szabályzaton belül. Ennek a forgatókönyvnek az engedélyezéséhez szükség van, ha az ügyfelet az Azure-beli delegált erőforrás-kezeléshez használja, és a házirendet maga telepíti.

> [!TIP]
> Bár a jelen témakörben a szolgáltatók és az ügyfelekre is hivatkozunk, a [több bérlőt kezelő vállalatok](../concepts/enterprise.md) ugyanazt a folyamatot használhatják.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Hozzon létre egy felhasználót, aki szerepköröket rendelhet egy felügyelt identitáshoz az ügyfél bérlője számára

Amikor egy ügyfelet az Azure Lighthouse-be telepít, egy [Azure Resource Manager sablont](onboard-customer.md#create-an-azure-resource-manager-template) , valamint egy olyan paramétereket tartalmazó fájlt használ, amely meghatározza a felügyeleti bérlőben lévő felhasználókat, felhasználói csoportokat és egyszerű szolgáltatásokat, amelyek képesek lesznek hozzáférni a delegált erőforrásokhoz az ügyfél bérlője számára. A parameters (paraméterek) fájlban ezek a felhasználók (**principalId**-EK) egy [beépített szerepkört](../../role-based-access-control/built-in-roles.md) (**roleDefinitionId**) kapnak, amely meghatározza a hozzáférési szintet.

Ahhoz, hogy egy **principalId** felügyelt identitást hozzon létre az ügyfél bérlője számára, be kell állítania a **RoleDefinitionId** a **felhasználói hozzáférési rendszergazdának**. Habár ez a szerepkör nem támogatott általánosan, ez az adott forgatókönyvben használható, így a felhasználók ezzel az engedéllyel rendelkezhetnek egy vagy több speciális beépített szerepkör hozzárendeléséhez a felügyelt identitásokhoz. Ezeket a szerepköröket a **delegatedRoleDefinitionIds** tulajdonság határozza meg. Ide belefoglalhatja a beépített szerepkört, kivéve a felhasználói hozzáférés rendszergazdáját vagy a tulajdonost.

Az ügyfél bevezetését követően az ebben az engedélyezésben létrehozott **principalId** ezeket a beépített szerepköröket hozzárendelheti a felügyelt identitásokhoz az ügyfél bérlője számára. Azonban nem rendelkeznek a felhasználói hozzáférés rendszergazdai szerepkörhöz tartozó egyéb engedélyekkel.

Az alábbi példa egy olyan **principalId** mutat be, amely a felhasználói hozzáférés rendszergazdai szerepkört fogja tartalmazni. Ez a felhasználó két beépített szerepkört tud hozzárendelni a felügyelt identitásokhoz az ügyfél bérlője: közreműködő és Log Analytics közreműködő.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Szervizelhető házirendek üzembe helyezése

Miután létrehozta a felhasználót a fent ismertetett szükséges engedélyekkel, a felhasználó telepíthet olyan házirendeket, amelyek szervizelési feladatokat használnak a delegált ügyfél-előfizetéseken belül.

Tegyük fel például, hogy engedélyezni szeretné a diagnosztika engedélyezését az ügyfél bérlőn lévő Azure Key Vault erőforrásokon, ahogy azt ebben a [példában](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring)is látható. A bérlő a megfelelő engedélyekkel rendelkező felhasználója egy [Azure Resource Manager sablon](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) üzembe helyezésével engedélyezi ezt a forgatókönyvet.

Vegye figyelembe, hogy a delegált előfizetéssel használandó szabályzat-hozzárendelés létrehozása jelenleg API-kon keresztül történik, nem pedig a Azure Portal. Ha így tesz, a **apiVersion** a **2019-04-01-Preview** értékre kell állítani, amely tartalmazza az új **delegatedManagedIdentityResourceId** tulajdonságot. Ez a tulajdonság lehetővé teszi, hogy olyan felügyelt identitást tartalmazzon, amely az ügyfél bérlőben található (egy előfizetésben vagy erőforráscsoporthoz, amely az Azure Lighthouse-ban van bevezetve).

Az alábbi példa egy **delegatedManagedIdentityResourceId** rendelkező szerepkör-hozzárendelést mutat be.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> A rendszer egy [hasonló mintát](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) mutat be, amely bemutatja, hogyan helyezhet üzembe egy olyan szabályzatot, amely egy (a módosítás hatásával) címkét hozzáad vagy eltávolít egy delegált előfizetéshez.

## <a name="next-steps"></a>Következő lépések

- A [Azure Policy](../../governance/policy/index.yml)megismerése.
- Ismerje meg [Az Azure-erőforrások felügyelt identitásait](../../active-directory/managed-identities-azure-resources/overview.md).
