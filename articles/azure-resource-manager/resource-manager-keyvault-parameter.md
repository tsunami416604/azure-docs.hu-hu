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
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: a885fda23bb76091705ebe388f40a6eae7b56416
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351509"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Az Azure Key Vault segítségével biztonságos paraméter értéke továbbítása üzembe helyezés során

Továbbítása (például a jelszó) biztonságos értéket paraméterként, üzembe helyezés során szüksége, amikor az értéket lekérheti egy [Azure Key Vault](../key-vault/key-vault-whatis.md). A key vaulttal és a titkos kulcsot az alkalmazásparaméter-fájlt a hivatkozó kérje le az értéket. Az érték sosem hagyja el, mert csak hivatkozhat a key vault azonosítója. A key vault egy másik előfizetésben, mint az erőforráscsoport telepíti, akkor is szerepel.

## <a name="deploy-a-key-vault-and-secret"></a>Telepítsen egy kulcstartó és a titkos kulcs

A key vaulttal és a titkos kulcs létrehozásához használja az Azure CLI vagy a PowerShell. `enabledForTemplateDeployment` a key vaulttal tulajdona. A titkos kulcsok a Key vaultban hozzá a Resource Manager-alapú `enabledForTemplateDeployment` kell `true`. 

Az alábbi Azure PowerShell és az Azure CLI-vel parancsprogram bemutatja, hogyan hozhat létre egy Key Vaultot és a egy titkos kulcsot.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
keyVaultName='{your-unique-vault-name}'
resourceGroupName='{your-resource-group-name}'
location='centralus'
userPrincipalName='{your-email-address-associated-with-your-subscription}'

# Create a resource group
az group create --name $resourceGroupName --location $location

# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

# Create a secret with the name, vmAdminPassword
password=$(openssl rand -base64 32)
echo $password
az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
$keyVaultName = "{your-unique-vault-name}"
$resourceGroupName="{your-resource-group-name}"
$location='Central US'
$userPrincipalName='{your-email-address-associated-with-your-subscription}'

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

New-AzureRmKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list

$password = openssl rand -base64 32
echo $password
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretvalue
```

Ha a Cloud Shellben kívül a PowerShell-parancsfájlt futtat, a következő paranccsal helyette létrehozni a jelszót:

```powershell
Add-Type -AssemblyName System.Web
[System.Web.Security.Membership]::GeneratePassword(16,3)
```

A Resource Manager-sablon használatával: Lásd: [oktatóanyag: Integrálhatja az Azure Key Vault Resource Manager-sablon üzembe helyezési](./resource-manager-tutorial-use-key-vault.md#prepare-the-key-vault).

> [!NOTE]
> Az egyes Azure-szolgáltatások különböző jelszókövetelményeket szabnak meg. Például az Azure virtuális gépekre vonatkozó követelmények fürtpéldány [Mik a jelszót a virtuális gép létrehozásakor?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="enable-access-to-the-secret"></a>A titkos kulcs hozzáférésének engedélyezése

Más, a beállítás `enabledForTemplateDeployment` való `true`, helyezi üzembe a sablont a felhasználónak rendelkeznie kell a `Microsoft.KeyVault/vaults/deploy/action` hatókörhöz, amely tartalmazza a Key Vault, beleértve az erőforráscsoportot és a Key Vault engedély. A [tulajdonosa](../role-based-access-control/built-in-roles.md#owner) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkör egyaránt a hozzáférés engedélyezéséhez. A Key Vault hoz létre, ha Ön a tulajdonosa, az engedéllyel rendelkezik. Ha a Key Vault egy másik előfizetésben, a Key Vault tulajdonosának kell hozzáférést adni.

A következő eljárás azt mutatja be, hogyan hozhat létre egy szerepkört a minimális permssion, és rendelje hozzá a felhasználót annak
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
    New-AzureRmRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzureRmRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    A `New-AzureRmRoleAssignment` minta az egyéni szerepkör hozzárendelése a felhasználóhoz, az az erőforráscsoport szintjén.  

A sablon egy Key Vault használata esetén egy [felügyelt alkalmazás](../managed-applications/overview.md), hozzáférést kell biztosítania a **készülék erőforrás-szolgáltató** egyszerű szolgáltatást. További információkért lásd: [Access Key Vault titkos kulcsából, Azure által felügyelt alkalmazások telepítésekor](../managed-applications/key-vault-access.md).

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
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

PowerShell esetén használja az alábbi parancsot:

```powershell-interactive
New-AzureRmResourceGroup -Name datagroup -Location $location
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
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>További lépések
* Kulcstartók kapcsolatos általános információkért lásd: [első lépései az Azure Key Vault](../key-vault/key-vault-get-started.md).
* A hívóbetű titkos kulcsait hivatkozó teljes példákért lásd [Key Vault példák](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
