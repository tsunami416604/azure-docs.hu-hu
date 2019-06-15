---
title: Key Vault titkos kulcsából, Azure Resource Manager-sablonnal |} A Microsoft Docs
description: Bemutatja, hogyan adhatók át titkos key vault paraméterként üzembe helyezése során.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: e47a087e27b6a8ade947e36ded762ce2e518ca25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507994"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Az Azure Key Vault segítségével biztonságos paraméter értéke továbbítása üzembe helyezés során

Helyett üzembe egy biztonságos értékre (például a jelszó) közvetlenül a sablon vagy paraméter fájlban, az értéket lekérheti egy [Azure Key Vault](../key-vault/key-vault-whatis.md) egy üzembe helyezés során. A key vaulttal és a titkos kulcsot az alkalmazásparaméter-fájlt a hivatkozó kérje le az értéket. Az érték sosem hagyja el, mert csak hivatkozhat a key vault azonosítója. A key vault létezhet az erőforráscsoport, helyezi üzembe, mint egy másik előfizetésben található.

## <a name="deploy-key-vaults-and-secrets"></a>Kulcstartó és a titkos kódok üzembe helyezése

Key vault eléréséhez a sablon üzembe helyezése során, állítsa `enabledForTemplateDeployment` , a kulcstartóra vonatkozó `true`.

A következő Azure CLI-vel és az Azure PowerShell-példák bemutatják, hogyan hozhat létre a key vaultban, és egy titkos kulcsot.

```azurecli
az group create --name $resourceGroupName --location $location
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $keyVaultName --name "ExamplePassword" --value "hVFkk965BuUv"
```

```azurepowershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName $keyVaultName -Name 'ExamplePassword' -SecretValue $secretvalue
```

A key vault tulajdonosának akkor automatikusan hozzáférést kapnak a titkos kulcsok létrehozása. Ha a felhasználó titkos kulcsok használata a key vault tulajdonosának nem, a hozzáférést:

```azurecli
az keyvault set-policy \
  --upn $userPrincipalName \
  --name $keyVaultName \
  --secret-permissions set delete get list
```

```azurepowershell
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName $keyVaultName `
  -UserPrincipalName $userPrincipalName `
  -PermissionsToSecrets set,delete,get,list
```

Kulcstartó létrehozása és a titkos kulcsok hozzáadása kapcsolatos további információkért lásd:

- [Beállítása és lekérése egy titkos kulcsot a parancssori felület használatával](../key-vault/quick-create-cli.md)
- [Beállítása és lekérése egy titkos kulcsot a Powershell használatával](../key-vault/quick-create-powershell.md)
- [Beállítása és lekérése egy titkos kulcsot a portál használatával](../key-vault/quick-create-portal.md)
- [Beállítása és titkos kulcs lekérése a .NET-keretrendszerrel](../key-vault/quick-create-net.md)
- [Beállítása és lekérése egy titkos kulcsot a Node.js használatával](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>A titkos kódokhoz való hozzáférés engedélyezése

A felhasználó, aki helyezi üzembe a sablont kell rendelkeznie a `Microsoft.KeyVault/vaults/deploy/action` engedéllyel a hatókör az erőforráscsoportot és a key vaultban. A [tulajdonosa](../role-based-access-control/built-in-roles.md#owner) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkör egyaránt a hozzáférés engedélyezéséhez. Létrehozta a kulcstartót, Ön a tulajdonosa, így Ön jogosult.

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

    ```azurecli
    az role definition create --role-definition "<PathToRoleFile>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee $userPrincipalName \
      --resource-group $resourceGroupName
    ```

    ```azurepowershell
    New-AzRoleDefinition -InputFile "<PathToRoleFile>" 
    New-AzRoleAssignment `
      -ResourceGroupName $resourceGroupName `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName $userPrincipalName
    ```

    A minták az egyéni szerepkör hozzárendelése a felhasználói az erőforráscsoport szintjén.  

A sablon egy Key Vault használata esetén egy [felügyelt alkalmazás](../managed-applications/overview.md), hozzáférést kell biztosítania a **készülék erőforrás-szolgáltató** egyszerű szolgáltatást. További információkért lásd: [Access Key Vault titkos kulcsából, Azure által felügyelt alkalmazások telepítésekor](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Titkos kódok hivatkozás statikus azonosítója

Ezzel a módszerrel a key vault a paraméterfájlban, nem a sablon hivatkozik. Az alábbi képen látható, hogyan alkalmazásparaméter-fájlt hivatkozik a titkos kulcsot, és ezt az értéket átadja a sablonhoz.

![Erőforrás-kezelő a key vault integration statikus azonosító diagramja](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Oktatóanyag: Integrálhatja az Azure Key Vault Resource Manager-sablon üzembe helyezési](./resource-manager-tutorial-use-key-vault.md) ezt a módszert használja.

Az alábbi sablont helyez üzembe egy SQL server rendszergazdai jelszót tartalmaz. A password paramétert egy biztonságos karakterláncra van beállítva. De a sablon nem adja meg, ha az érték származik.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Most hozzon létre egy paraméterfájlt az előző sablon. A paraméterfájlban adjon meg egy paramétert, amely megegyezik-e a paraméter a sablonban. A paraméter értéke tekintse a titkos kulcsot a kulcstartóban található. A titkos kulcsot a key vault erőforrás-azonosítója és a titkos kód nevét átadásával hivatkozik:

A következő paraméterfájl már léteznie kell, a key vault titkos kulcsából, és statikus érték megadása az erőforrás-azonosítója.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "ExamplePassword"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Ha szeretné a titkos kód verziója eltérő verzióját használja, használja a `secretVersion` tulajdonság.

```json
"secretName": "ExamplePassword",
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
