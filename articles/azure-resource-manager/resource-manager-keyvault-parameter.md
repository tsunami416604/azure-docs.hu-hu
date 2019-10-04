---
title: Key Vault titkos kód Azure Resource Manager sablonnal | Microsoft Docs
description: Bemutatja, hogyan lehet átadni egy titkos kulcsot a Key vaultból paraméterként az üzembe helyezés során.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: 489b09d2523393ae67668ed13c651c9b7b0217b4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998891"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>A Azure Key Vault használata a biztonságos paraméterek értékének átadására az üzembe helyezés során

Ahelyett, hogy egy biztonságos értéket (például a jelszót) közvetlenül a sablonban vagy a paraméterben található fájlba helyezze, lekérheti az értéket egy [Azure Key Vault](../key-vault/key-vault-overview.md) egy központi telepítés során. Az értéket úgy kell lekérnie, hogy a kulcstárolóra hivatkozik, és a titkos kulcsot a paraméter fájljában. Az érték soha nem lesz kitéve, mert csak a Key Vault-AZONOSÍTÓra hivatkozik. A Key Vault egy másik előfizetésben is létezhet, mint az üzembe helyezett erőforráscsoport.

## <a name="deploy-key-vaults-and-secrets"></a>Kulcstartók és titkos kulcsok üzembe helyezése

Egy kulcstartó eléréséhez a sablon üzembe helyezése során `enabledForTemplateDeployment` állítsa be a Key `true`vaultot a következőre:.

A következő Azure CLI és Azure PowerShell minták bemutatják, hogyan hozhatja létre a kulcstartót, és hogyan adhat hozzá egy titkos kulcsot.

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

A Key Vault tulajdonosaként automatikusan hozzáférhet a titkok létrehozásához. Ha a titkos kulcsokkal dolgozó felhasználó nem tulajdonosa a kulcstartónak, a következőkhöz biztosít hozzáférést:

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

A kulcstartók létrehozásával és a titkok hozzáadásával kapcsolatos további információkért lásd:

- [Titkos kód beállítása és lekérése a parancssori felület használatával](../key-vault/quick-create-cli.md)
- [Titkos kód beállítása és beolvasása a PowerShell használatával](../key-vault/quick-create-powershell.md)
- [Titkos kód beállítása és lekérése a portál használatával](../key-vault/quick-create-portal.md)
- [Titkos kód beállítása és lekérése a .NET használatával](../key-vault/quick-create-net.md)
- [Titkos kód beállítása és lekérése a Node. js használatával](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Hozzáférés biztosítása a titkokhoz

A sablont telepítő felhasználónak `Microsoft.KeyVault/vaults/deploy/action` engedéllyel kell rendelkeznie az erőforráscsoport és a kulcstartó hatóköréhez. A [tulajdonos](../role-based-access-control/built-in-roles.md#owner) és a [közreműködő](../role-based-access-control/built-in-roles.md#contributor) szerepkör egyaránt megadja ezt a hozzáférést. Ha létrehozta a kulcstartót, akkor Ön a tulajdonosa, hogy Ön rendelkezik az engedélyekkel.

A következő eljárás azt mutatja be, hogyan hozható létre egy szerepkör a minimális engedélyekkel, és hogyan rendelhető hozzá a felhasználó

1. Hozzon létre egy egyéni szerepkör-definíciós JSON-fájlt:

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
    Cserélje le az "00000000-0000-0000-0000-000000000000" kifejezést az előfizetés-AZONOSÍTÓra.

2. Hozza létre az új szerepkört a JSON-fájl használatával:

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

    A minták hozzárendelik az egyéni szerepkört a felhasználóhoz az erőforráscsoport szintjén.  

Ha egy felügyelt alkalmazáshoz tartozó sablonnal Key Vault [](../managed-applications/overview.md)használ, hozzáférést kell biztosítania a **készülék erőforrás** -szolgáltatói egyszerű szolgáltatásához. További információ: [hozzáférés Key Vault secret Azure Managed Applications telepítésekor](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Hivatkozási titkok statikus AZONOSÍTÓval

Ezzel a módszerrel a Key vaultra hivatkozik a paraméter fájljában, nem pedig a sablonban. Az alábbi képen látható, hogy a paraméter fájlja hogyan hivatkozik a titkos kulcsra, és átadja ezt az értéket a sablonnak.

![Resource Manager Key Vault-integráció – statikus azonosító diagramja](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Oktatóanyag: A Azure Key Vault integrálása a Resource](./resource-manager-tutorial-use-key-vault.md) Manager template Deployment ezt a metódust használja.

A következő sablon olyan SQL Servert telepít, amely rendszergazdai jelszót tartalmaz. A Password paraméter egy biztonságos karakterláncra van beállítva. A sablon azonban nem határozza meg, hogy az adott érték honnan származik.

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

Most hozzon létre egy paramétert az előző sablonhoz. A paraméter fájljában adjon meg egy paramétert, amely megegyezik a sablonban található paraméter nevével. A paraméter értékeként hivatkozzon a Key Vault titkos kódjára. A titkos kulcsot a Key Vault erőforrás-azonosítójának és a titkos kulcs nevének a megadásával hivatkozhat:

A következő paraméter fájljában a Key Vault-titoknak már léteznie kell, és statikus értéket kell megadnia az erőforrás-AZONOSÍTÓhoz.

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

Ha az aktuális verziótól eltérő titkos verziót kell használnia, használja a `secretVersion` tulajdonságot.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Telepítse a sablont, és adja meg a paramétert a fájlban:

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

## <a name="reference-secrets-with-dynamic-id"></a>Hivatkozási titkok dinamikus AZONOSÍTÓval

Az előző szakasz azt mutatta be, hogyan lehet átadni a Key Vault titkos kulcsának statikus erőforrás-AZONOSÍTÓját a paraméterből. Bizonyos helyzetekben azonban egy Key Vault-titokra kell hivatkoznia, amely az aktuális telepítéstől függően változik. Vagy előfordulhat, hogy paramétereket kell átadnia a sablonnak, és nem kell hivatkozási paramétert létrehoznia a paraméter fájljában. Mindkét esetben a Key Vault-titok erőforrás-AZONOSÍTÓját egy csatolt sablon használatával lehet dinamikusan előállítani.

A Parameters fájlban nem lehet dinamikusan előállítani az erőforrás-azonosítót, mert a Parameters fájlban nem engedélyezettek a sablon kifejezései. 

A fölérendelt sablonban adja hozzá a csatolt sablont, és adjon meg egy olyan paramétert, amely a dinamikusan generált erőforrás-azonosítót tartalmazza. Az alábbi képen látható, hogy a csatolt sablon egyik paramétere hogyan hivatkozik a titkos kulcsra.

![Dinamikus azonosító](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

A [következő sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) dinamikusan létrehozza a Key Vault-azonosítót, és paraméterként továbbítja azt.

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

Telepítse az előző sablont, és adja meg a paraméterek értékeit. Használhatja a GitHubról a példa sablont, de meg kell adnia a környezet paramétereinek értékét.

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

- A Key vaultokkal kapcsolatos általános információkért lásd: [Mi az Azure Key Vault?](../key-vault/key-vault-overview.md)
- A legfontosabb titkokra hivatkozó példákat itt talál: [Key Vault példák](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
