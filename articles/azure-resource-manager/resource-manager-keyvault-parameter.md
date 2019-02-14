---
title: Key Vault titkos kulcsából, Azure Resource Manager-sablonnal |} A Microsoft Docs
description: Bemutatja, hogyan adhatók át titkos key vault paraméterként üzembe helyezése során.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 93b92a8a3b8aacd1f665725643314858fe92ad3c
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233768"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Az Azure Key Vault segítségével biztonságos paraméter értéke továbbítása üzembe helyezés során

Helyett írja a biztonságos értéket (például a jelszó) közvetlenül az alkalmazásparaméter-fájlt, az értéket lekérheti egy [Azure Key Vault](../key-vault/key-vault-whatis.md) egy üzembe helyezés során. A key vaulttal és a titkos kulcsot az alkalmazásparaméter-fájlt a hivatkozó kérje le az értéket. Az érték sosem hagyja el, mert csak hivatkozhat a key vault azonosítója. A key vault egy másik előfizetésben, mint az erőforráscsoport telepíti, akkor is szerepel.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-key-vaults-and-secrets"></a>Kulcstartó és a titkos kódok üzembe helyezése

Key vault-Kulcstartók létrehozása, és adja hozzá a titkos kulcsokat, lásd:

- [Beállítása és lekérése egy titkos kulcsot a parancssori felület használatával](../key-vault/quick-create-cli.md)
- [Beállítása és lekérése egy titkos kulcsot a Powershell használatával](../key-vault/quick-create-powershell.md)
- [Beállítása és lekérése egy titkos kulcsot a portál használatával](../key-vault/quick-create-portal.md)
- [Beállítása és titkos kulcs lekérése a .NET-keretrendszerrel](../key-vault/quick-create-net.md)
- [Beállítása és lekérése egy titkos kulcsot a Node.js használatával](../key-vault/quick-create-node.md)

Vannak továbbá szempontok és követelmények integrációja a Key Vault Resource Manager-sablonalapú telepítéssel:

- `enabledForTemplateDeployment` a key vaulttal tulajdona. A titkos kulcsok a Key vaultban hozzá a Resource Manager-alapú `enabledForTemplateDeployment` kell `true`. 
- Ha nem a key vault tulajdonosának, a tulajdonos frissítenie kell a key vault biztonsági házirendjének beállításai is hozzáadhat a titkos kulcsok.

A következő Azure CLI-vel és az Azure PowerShell-példák bemutatják, hogyan történik:

```azurecli
# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list
```

```azurepowershell
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
```

## <a name="grant-access-to-the-secrets"></a>A titkos kódokhoz való hozzáférés engedélyezése

A felhasználó, aki helyezi üzembe a sablont kell rendelkeznie a `Microsoft.KeyVault/vaults/deploy/action` hatókörhöz, amely tartalmazza a Key Vault, beleértve az erőforráscsoportot és a Key Vault engedély. A [tulajdonosa](../role-based-access-control/built-in-roles.md#owner) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkör egyaránt a hozzáférés engedélyezéséhez. A Key Vault hoz létre, ha Ön a tulajdonosa, az engedéllyel rendelkezik. Ha a Key Vault egy másik előfizetésben, a Key Vault tulajdonosának kell grand a hozzáférést.

A következő eljárás azt mutatja be, a minimális hozzáférésű szerepkör létrehozása, és rendelje hozzá a felhasználót

1. Hozzon létre egy egyéni szerepkör definíció JSON-fájlt:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Cserélje le a "00000000-0000-0000-0000-000000000000" az előfizetés-azonosító, a felhasználó, aki a sablonok üzembe helyezése.

2. Az új szerepkör JSON-fájl létrehozása:

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    A `New-AzRoleAssignment` a példa hozzárendeli az egyéni szerepkör a felhasználóhoz, az az erőforráscsoport szintjén.  

A sablon egy Key Vault használata esetén egy [felügyelt alkalmazás](../managed-applications/overview.md), hozzáférést kell biztosítania a **készülék erőforrás-szolgáltató** egyszerű szolgáltatást. További információkért lásd: [Access Key Vault titkos kulcsából, Azure által felügyelt alkalmazások telepítésekor](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Titkos kódok hivatkozás statikus azonosítója

Ezzel a módszerrel a key vault a paraméterfájlban, nem a sablon hivatkozik. Az alábbi képen látható, hogyan alkalmazásparaméter-fájlt hivatkozik a titkos kulcsot, és ezt az értéket átadja a sablonhoz.

![Erőforrás-kezelő a key vault integration statikus azonosító diagramja](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Oktatóanyag: Integrálhatja az Azure Key Vault Resource Manager-sablon üzembe helyezési](./resource-manager-tutorial-use-key-vault.md) ezt a módszert használja. Az oktatóanyag üzembe helyezése egy virtuális gép rendszergazdai jelszavának tartalma. A password paramétert egy biztonságos karakterláncra van beállítva:

![Erőforrás-kezelő a key vault integration statikus azonosító sablonfájl](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-template-file.png)

Most hozzon létre egy paraméterfájlt az előző sablon. A paraméterfájlban adjon meg egy paramétert, amely megegyezik-e a paraméter a sablonban. A paraméter értéke tekintse a titkos kulcsot a kulcstartóban található. A titkos kulcsot a key vault erőforrás-azonosítója és a titkos kód nevét átadásával hivatkozik:

![Erőforrás-kezelő a key vault integration statikus azonosító alkalmazásparaméter-fájlt](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-parameter-file.png)

Ha szeretné a titkos kód verziója eltérő verzióját használja, használja a `secretVersion` tulajdonság.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Helyezze üzembe a sablont, és adja át az alkalmazásparaméter-fájlt:

Azure CLI esetén használja az alábbi parancsot:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Hivatkozás titkos kódok dinamikus azonosítója

Az előző szakaszban bemutatta, hogyan adhatók át a paraméter egy statikus erőforrás-azonosító számára a kulcstartó titkos kulcsot. Azonban bizonyos esetekben szüksége a key vault titkos kulcs, amely alapján a jelenlegi üzemelő példány hivatkozni. Vagy előfordulhat, hogy szeretne paraméterértékek át a sablon a paraméterfájlban hivatkozási paraméter létrehozása helyett. Mindkét esetben dinamikusan csatolt sablonok segítségével hozhatja létre a key vault titkos kulcs az erőforrás-azonosító.

A paraméterfájl dinamikusan, nem hozható létre az erőforrás-azonosító, mert a sablon kifejezésekben nem engedélyezett a paraméterfájlban. 

A szülő-sablonban adja hozzá a hivatkozott sablonnak, és adja át a paramétert, amely tartalmazza a dinamikusan létrehozott erőforrás-azonosítója. Az alábbi képen látható, hogyan a társított sablon egyik paraméterének hivatkozik-e a titkos kulcsot.

![A dinamikus azonosítója](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

A [sablon alábbi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) dinamikusan hoz létre a key vault-azonosítója és paraméterként átadja azokat.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Az előző sablon üzembe helyezése, és adja meg a paraméterek értékeit. A példa-sablont a Githubból is használhat, de a paraméter értékét meg kell adnia a környezetben.

Azure CLI esetén használja az alábbi parancsot:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>További lépések

- Kulcstartók kapcsolatos általános információkért lásd: [Mi az Azure Key Vault?](../key-vault/key-vault-overview.md).
- A hívóbetű titkos kulcsait hivatkozó teljes példákért lásd [Key Vault példák](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
