---
title: Javítható szabályzat telepítése
description: Ismerje meg, hogyan lehet egy ügyfél az Azure delegált erőforrás-kezelés, amely lehetővé teszi, hogy az erőforrások elérése és kezelése a saját bérlőn keresztül.
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: b625e9e3c96866cfbc655a55b770c9ac07a626bd
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985167"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Delegált előfizetésen belül kiújítható házirend üzembe helyezése

[Az Azure Lighthouse](../overview.md) lehetővé teszi a szolgáltatók számára, hogy szabályzatdefiníciókat hozzanak létre és szerkessenek egy delegált előfizetésen belül. Azonban a [szervizelési feladatot](../../governance/policy/how-to/remediate-resources.md) használó házirendek (azaz a [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) vagy [a módosító](../../governance/policy/concepts/effects.md#modify) hatással rendelkező házirendek) üzembe helyezéséhez [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md) kell létrehoznia az ügyfél-bérlőben. Ezt a felügyelt identitást az Azure Policy használhatja a sablon központi telepítéséhez a szabályzaton belül. A forgatókönyv engedélyezéséhez lépéseket kell tenni, mind az Azure delegált erőforrás-kezeléséhez az ügyfél, mind pedig a szabályzat üzembe helyezésekor.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Olyan felhasználó létrehozása, aki szerepköröket rendelhet egy felügyelt identitáshoz az ügyfél-bérlőben

Amikor egy ügyfél az Azure delegált erőforrás-kezelése, egy [Azure Resource Manager sablont,](onboard-customer.md#create-an-azure-resource-manager-template) valamint egy paraméterfájlt, amely meghatározza a felhasználók, felhasználói csoportok és a szolgáltatásnévak a felügyeleti bérlő, amely képes lesz elérni a delegált erőforrások az ügyfél-bérlő. A paraméterfájlban ezek a felhasználók **(rendszerazonosító)** egy [beépített szerepkörrel](../../role-based-access-control/built-in-roles.md) **(roleDefinitionId)** vannak rendelve, amely meghatározza a hozzáférés szintjét.

Ahhoz, hogy egy **rendszerazonosító** felügyelt identitást hozzon létre az ügyfél-bérlőben, a **szerepkördefinition-azonosítóját** **felhasználói hozzáférés-rendszergazdára kell állítania.** Bár ez a szerepkör általában nem támogatott, ebben a konkrét esetben használható, lehetővé téve az ezzel az engedéllyel rendelkező felhasználók számára, hogy egy vagy több speciális beépített szerepkört rendeljenek hozzá a felügyelt identitásokhoz. Ezek a szerepkörök a **delegáltRoleDefinitionIds** tulajdonságban vannak definiálva. A felhasználói hozzáférés rendszergazdája vagy a tulajdonos kivételével bármilyen beépített szerepkört felvehet ide.

Miután az ügyfél be van táblázva, az ebben az engedélyezésben létrehozott **rendszerbiztonsági azonosító** képes lesz hozzárendelni ezeket a beépített szerepköröket az ügyfél-bérlő felügyelt identitásaihoz. A Felhasználói hozzáférés rendszergazdája szerepkörhöz azonban nem lesz más engedélyük.

Az alábbi példa egy **olyan rendszerbiztonsági tagazonosítót** mutat be, aki a Felhasználói hozzáférés rendszergazdája szerepkörrel fog rendelkezni. Ez a felhasználó két beépített szerepkört rendelhet hozzá az ügyfél-bérlő felügyelt identitásaihoz: közreműködőt és Log Analytics-közreműködőt.

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

## <a name="deploy-policies-that-can-be-remediated"></a>Helyreállítható házirendek üzembe helyezése

Miután létrehozta a felhasználót a fent leírt szükséges engedélyekkel, a felhasználó üzembe helyezheti a szervizelési feladatokat használó házirendeket az ügyfél-bérlőben.

Tegyük fel például, hogy engedélyezni kívánta a diagnosztikát az Azure Key Vault-erőforrásokon az ügyfél-bérlőben, amint azt ez a [minta is szemlélteti.](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) A megfelelő engedélyekkel rendelkező(fent leírt) a felügyeleti bérlő egyik felhasználója üzembe helyezne egy [Azure Resource Manager-sablont](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) ennek a forgatókönyvnek a engedélyezéséhez.

Vegye figyelembe, hogy a házirend-hozzárendelés létrehozása egy delegált előfizetéssel való használatra jelenleg API-kon keresztül kell elvégezni, nem az Azure Portalon. Ha így tesz, az **apiVersion** kell állítani **2019-04-01-preview**, amely tartalmazza az új **delegáltManagedIdentityResourceId** tulajdonság. Ez a tulajdonság lehetővé teszi, hogy egy felügyelt identitást, amely az ügyfél-bérlő (egy előfizetés vagy erőforráscsoport, amely az Azure delegált erőforrás-kezelés.

A következő példa egy **delegáltManagedIdentityResourceId**szerepkör-hozzárendelést mutat be.

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
> [Hasonló minta](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) áll rendelkezésre annak bemutatására, hogyan telepíthet egy olyan házirendet, amely címkét ad hozzá vagy távolít el (a módosítási effektushasználatával) egy delegált előfizetéshez.

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szabályzatról.](../../governance/policy/index.yml)
- További információ [az Azure-erőforrások felügyelt identitásairól.](../../active-directory/managed-identities-azure-resources/overview.md)
