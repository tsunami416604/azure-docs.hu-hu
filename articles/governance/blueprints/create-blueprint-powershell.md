---
title: 'Rövid útmutató: Blueprint létrehozása a PowerShell használatával'
description: Ebben a rövid útmutatóban az Azure Blueprints használatával hozza létre, határozza meg és telepítse az összetevőket a PowerShell használatával.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: 53fcf53bdcc3b5fcd1c6ec07a7d8ab45b39955b6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676594"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Rövid útmutató: Azure Blueprint definiálása és hozzárendelése a PowerShell segítségével

A tervek létrehozásának és hozzárendelésének elsajátítása lehetővé teszi a közös minták definiálását, hogy újrahasználható és gyorsan üzembe helyezhető konfigurációkat dolgozhasson ki Resource Manager-sablonok, szabályzatok, biztonsági és egyéb szempontok alapján. Ez az oktatóanyag bemutatja, hogyan hajthatja végre az Azure Blueprints használatával a tervek a szervezeten belüli létrehozásával, közzétételével és hozzárendelésével kapcsolatos olyan általános feladatokat, mint az alábbiak:

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) mielőtt elkezdené.

Ha még nincs telepítve, kövesse [az Az.Blueprint modul hozzáadása az Az.Blueprint modul](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) telepítéséhez és érvényesítéséhez az **Az.Blueprint** modult a PowerShell-galériából.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Terv létrehozása

A megfelelőségi szabványminták definiálásának első lépése, hogy összeállítunk egy tervet az elérhető erőforrásokból. Létrehozzuk a „MyBlueprint” nevű tervet az előfizetés szerepkör- és szabályzat-hozzárendeléseinek konfigurálására. Ezután hozzáadunk egy erőforráscsoportot, egy Resource Manager-sablont és egy szerepkör-hozzárendelést az erőforráscsoporton.

> [!NOTE]
> PowerShell használatakor a _tervezet_ objektum jön létre először. Mindegyik hozzáadott, paraméterekkel rendelkező _összetevő_ esetében a paramétereket előre definiálni kell a kezdeti _terven_.

1. Hozza létre a kezdeti _terv_ objektumot. A **BlueprintFile** paraméter egy JSON-fájlt vesz fel, amely tartalmazza a tervezet, a létrehozandó erőforráscsoportok és az összes tervezetszint paraméter tulajdonságait. A paraméterek a hozzárendelés során vannak megadva, és a későbbi lépésekben hozzáadott összetevők használják azokat.

   - JSON fájl - blueprint.json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "defaultValue": "Standard_LRS",
                     "allowedValues": [
                         "Standard_LRS",
                         "Standard_GRS",
                         "Standard_ZRS",
                         "Premium_LRS"
                     ],
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Használja a _blueprint.json_ fájlnevet a tervezetdefiníciók programozott létrehozásakor.
     > Ez a fájlnév az [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact)hívásakor használatos.

     A tervezet objektum jön létre az alapértelmezett előfizetés alapértelmezés szerint. A felügyeleti csoport megadásához használja a **ManagementGroupId paramétert.** Az előfizetés megadásához használja a **SubscriptionId paramétert.**

1. Szerepkör-hozzárendelés hozzáadása az előfizetésben. A **ArtifactFile** határozza meg a műtermék _fajtáját,_ a tulajdonságok igazodnak a szerepkör-definícióazonosítóhoz, és a fő identitások értékek tömbjeként kerülnek átadásra. Az alábbi példában a megadott szerepkörrel felruházott egyszerű identitások egy olyan paraméterre vannak konfigurálva, amely a tervhozzárendelés során van megadva. Ez a példa a _közreműködő_ beépített szerepkörét használja a GUID azonosítójával. `b24988ac-6180-42a0-ab88-20f7382dd24c`

   - JSON-fájl – \artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Szabályzat-hozzárendelés hozzáadása az előfizetésben. Az **ArtifactFile** határozza meg a műtermék, a házirendvagy kezdeményezés definíciójához igazodó tulajdonságok _at,_ és úgy konfigurálja a házirend-hozzárendelést, hogy a meghatározott tervezetparamétereket használja a tervezet hozzárendelése során konfigurálható. Ez a példa az Apply címkét és annak alapértelmezett értékét használja `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`a beépített _házirendre,_ amelynek GUID azonosítója van.

   - JSON-fájl – \artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Egy másik szabályzat-hozzárendelés hozzáadása egy Storage-címke számára (a _storageAccountType_ paraméter ismételt felhasználásával) az előfizetésen. Ez az újabb szabályzat-hozzárendelési összetevő bemutatja, hogy a terveken definiált paramétereket több összetevő is használhatja. A példában a **storageAccountType** használatával beállítunk egy címkét az erőforráscsoporton. Ez az érték a következő lépésben létrehozott tárfiókkal kapcsolatos információkat szolgáltat. Ez a példa az Apply címkét és annak alapértelmezett értékét használja `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`a beépített _házirendre,_ amelynek GUID azonosítója van.

   - JSON-fájl - \artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Sablon hozzáadása az erőforráscsoport alatt. Az Erőforrás-kezelő sablon **sablonja** tartalmazza a sablon normál JSON-összetevőjét. A sablon újra felhasználja a **storageAccountType**, a **tagName** és a **tagValue** tervparamétert is, mivel továbbadja azokat a sablonnak. A tervezet paraméterei a **TemplateParameterFile** paraméter használatával érhetők el a sablon számára, és a JSON sablonon belül, amely kulcs-érték párt használja az érték befecskendezéséhez. A tervezet és a sablon paraméternevei megegyezhetnek.

   - JSON Azure Resource Manager sablonfájl – \artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - JSON Azure Resource Manager sablon paraméterfájlja – \artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Szerepkör-hozzárendelés hozzáadása az erőforráscsoport alatt. Az előző szerepkör-hozzárendelési bejegyzéshez hasonlóan az alábbi példa a **Tulajdonos** szerepkör definíciós azonosítóját használja, és egy másik paramétert ad neki a tervből. Ez a példa a _Tulajdonos_ beépített szerepkört `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`használja a GUID azonosítójával.

   - JSON-fájl - \artifacts\roleOwner.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Terv közzététele

Most, hogy az összetevők hozzá lettek adva a tervhez, itt az idő közzétenni azt. A közzététellel a terv szabadon hozzárendelhető lesz az előfizetésekhez.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

A `{BlueprintVersion}` értéke egy betűket, számokat és kötőjeleket (szóközöket vagy speciális karaktereket nem) tartalmazó, legfeljebb 20 karakter hosszúságú sztring. Használjon egyedi, és jelentéssel bíró értékeket, például: **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Terv hozzárendelése

Miután egy tervrajz ot közzétett a PowerShell használatával, egy előfizetéshez rendelhető. A létrehozott tervet a felügyeleti csoport hierarchiájában rendelheti hozzá az egyik előfizetéshez. Ha a tervezet egy előfizetésbe van mentve, csak az adott előfizetéshez rendelhető hozzá. A **Blueprint** paraméter határozza meg a hozzárendelni hezrendelt tervrajzot. Név, hely, identitás, zárolás és tervezet paraméterek megadásához használja a parancsmagban a `New-AzBlueprintAssignment` megfelelő PowerShell-paramétereket, vagy adja meg azokat az **AssignmentFile** paraméter JSON-fájlban.

1. A tervpéldányt a futtatásához rendelje hozzá egy előfizetéshez. Mivel a **közreműködők** és a **tulajdonosok** paraméterei megkövetelik a rendszertagok objektumazonosítóinak tömbjét a szerepkör-hozzárendeléshez, használja az [Azure Active Directory Graph API-t](../../active-directory/develop/active-directory-graph-api.md) az objectIds összegyűjtéséhez a saját felhasználók, csoportok vagy egyszerű szolgáltatástagok számára az **AssignmentFile-ban** való használatra.

   - JSON-fájl - blueprintAssignment.json

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Felhasználó által hozzárendelt felügyelt identitás

     A tervezethozzárendelés ekis használhatják [a felhasználó által hozzárendelt felügyelt identitást.](../../active-directory/managed-identities-azure-resources/overview.md)
     Ebben az esetben a JSON-hozzárendelési fájl **identitásrésze** a következőképpen változik. Cserélje `{tenantId}` `{subscriptionId}`le `{yourRG}`a `{userIdentity}` , , és a tenantId, subscriptionId, erőforráscsoport nevét, illetve a felhasználó által hozzárendelt felügyelt identitás nevét.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     A **felhasználó által hozzárendelt felügyelt identitás** lehet bármely előfizetés és erőforráscsoport a felhasználó hozzárendelte a tervezet engedélyekkel rendelkezik.

     > [!IMPORTANT]
     > Az Azure Blueprints nem kezeli a felhasználó által hozzárendelt felügyelt identitást. A felhasználók felelősek a megfelelő szerepkörök és engedélyek hozzárendelése, vagy a tervezet hozzárendelés sikertelen lesz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="unassign-a-blueprint"></a>Terv hozzárendelésének megszüntetése

Eltávolíthatja a terveket az előfizetésekből. Az eltávolítás gyakori művelet az összetevők már szükségtelen erőforrásai esetén. Az egyes tervek eltávolításakor az adott tervek keretében hozzárendelt összetevők megmaradnak. Tervrajz-hozzárendelés eltávolításához `Remove-AzBlueprintAssignment` használja a parancsmabot:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott, hozzárendelt és eltávolított egy tervrajzot a PowerShell-el. Ha többet szeretne megtudni az Azure Blueprints, folytassa a blueprint életciklus-cikk.

> [!div class="nextstepaction"]
> [További információ a tervterv életciklusáról](./concepts/lifecycle.md)