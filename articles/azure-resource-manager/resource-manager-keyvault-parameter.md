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
ms.date: 09/12/2018
ms.author: tomfitz
ms.openlocfilehash: 9cb9fcbb6750bf854cca74ed6bd08a91caed9e26
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717590"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Az Azure Key Vault segítségével biztonságos paraméter értéke továbbítása üzembe helyezés során

Továbbítása (például a jelszó) biztonságos értéket paraméterként, üzembe helyezés során szüksége, amikor az értéket lekérheti egy [Azure Key Vault](../key-vault/key-vault-whatis.md). A key vaulttal és a titkos kulcsot az alkalmazásparaméter-fájlt a hivatkozó kérje le az értéket. Az érték sosem hagyja el, mert csak hivatkozhat a key vault azonosítója. A key vault egy másik előfizetésben, mint az erőforráscsoport telepíti, akkor is szerepel.

## <a name="enable-access-to-the-secret"></a>A titkos kulcs hozzáférésének engedélyezése

Ebben az esetben két fontos feltételt, amely léteznie kell a key vault eléréséhez a sablon üzembe helyezése során:

1. A key vaulttal tulajdonság `enabledForTemplateDeployment` kell `true`.
2. A felhasználó helyezi üzembe a sablont a titkos kulcsot, hozzáféréssel kell rendelkeznie. A felhasználónak rendelkeznie kell a `Microsoft.KeyVault/vaults/deploy/action` engedély a key vaultban. A [tulajdonosa](../role-based-access-control/built-in-roles.md#owner) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkör egyaránt a hozzáférés engedélyezéséhez.

A sablon egy Key Vault használata esetén egy [felügyelt alkalmazás](../managed-applications/overview.md), hozzáférést kell biztosítania a **készülék erőforrás-szolgáltató** egyszerű szolgáltatást. További információkért lásd: [Access Key Vault titkos kulcsából, Azure által felügyelt alkalmazások telepítésekor](../managed-applications/key-vault-access.md).


## <a name="deploy-a-key-vault-and-secret"></a>Telepítsen egy kulcstartó és a titkos kulcs

A key vaulttal és a titkos kulcs létrehozásához használja az Azure CLI vagy a PowerShell. Figyelje meg, hogy a key vault engedélyezve van-e a sablon üzembe helyezéshez. 

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

PowerShell esetén használja az alábbi parancsot:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="reference-a-secret-with-static-id"></a>Hivatkozás statikus azonosítójú titkos kulcs

A sablon, amely megkapja a key vault titkos kulcs olyan, mint a többi sablont. Ez azért van, **a key vault a paraméterfájlban, nem a sablon hivatkozik.** Az alábbi képen látható, hogyan alkalmazásparaméter-fájlt hivatkozik a titkos kulcsot, és ezt az értéket átadja a sablonhoz.

![Statikus azonosítója](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Ha például a [sablon a következő](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) helyez üzembe egy SQL-adatbázis, amely tartalmaz egy rendszergazdai jelszót. A password paramétert egy biztonságos karakterláncra van beállítva. De a sablon nem határozza meg, ha az érték származik.

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

Most hozzon létre egy paraméterfájlt az előző sablon. A paraméterfájlban adjon meg egy paramétert, amely megegyezik-e a paraméter a sablonban. A paraméter értéke tekintse a titkos kulcsot a kulcstartóban található. A titkos kulcsot a key vault erőforrás-azonosítója és a titkos kód nevét átadásával hivatkozik. Az a [alkalmazásparaméter-fájlt a következő](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json), léteznie kell a key vault titkos kulcsából, és statikus érték megadása az erőforrás-azonosítója. Másolja ezt a fájlt helyileg, és állítsa be az előfizetés-azonosító, a tároló neve és az SQL server neve.

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
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
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
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Most helyezze üzembe a sablont, és adja át az alkalmazásparaméter-fájlt. A példa-sablont a Githubból is használhat, de egy helyi alkalmazásparaméter-fájlt kell használnia a környezetben beállított értékeket.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Hivatkozási dinamikus azonosítójú titkos kulcs

Az előző szakaszban bemutatta, hogyan adhatók át a paraméter egy statikus erőforrás-azonosító számára a kulcstartó titkos kulcsot. Azonban bizonyos esetekben szüksége a key vault titkos kulcs, amely alapján a jelenlegi üzemelő példány hivatkozni. Vagy egyszerűen csak adja át a paraméter értékét a sablon létrehozása helyett egy hivatkozási paraméter a paraméterfájlban érdemes. Mindkét esetben dinamikusan csatolt sablonok segítségével hozhatja létre a key vault titkos kulcs az erőforrás-azonosító.

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

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>További lépések
* Kulcstartók kapcsolatos általános információkért lásd: [első lépései az Azure Key Vault](../key-vault/key-vault-get-started.md).
* A hívóbetű titkos kulcsait hivatkozó teljes példákért lásd [Key Vault példák](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
