---
title: Key Vault titkos Azure Resource Manager-sablonnal |} Microsoft Docs
description: Bemutatja, hogyan adhatók át a titkos kulcs kulcstároló paraméterként üzembe helyezése során.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/11/2018
ms.author: tomfitz
ms.openlocfilehash: 6a6c1f10b5a46633785d9c26a766df9334fe1cb0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Az Azure Key Vault használatával továbbítsa biztonságos paraméter értékét a telepítés során

Adjon át egy biztonságos értéket (például jelszót) paraméterként központi telepítése során kell, ha az érték le egy [Azure Key Vault](../key-vault/key-vault-whatis.md). A kulcstartó és a paraméter-fájlban a titkos kulcs Vezérlőpultjának kikeresheti az értéket. Az érték sosem hagyja, mert csak hivatkoznak a kulcstartót azonosítóját. Nem kell manuálisan adja meg az értékét a titkos kulcsot minden alkalommal, amikor az erőforrások telepítése. A key vault létezhet, mint az erőforráscsoportot, hogy telepít egy másik előfizetésben. A key vault hivatkozik, akkor adja meg az előfizetés-azonosító.

A kulcstartó létrehozásakor állítsa be a *enabledForTemplateDeployment* tulajdonságot *igaz*. Ez az érték true értékre állításával üzembe helyezése során engedélyezi a hozzáférést a Resource Manager-sablonok.

## <a name="deploy-a-key-vault-and-secret"></a>Telepítsen egy kulcstartót és a titkos kulcs

A kulcstartó és a titkos kulcs létrehozásához használja az Azure parancssori felület vagy a PowerShell. Figyelje meg, hogy a key vault sablon-üzembehelyezés engedélyezve van. 

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

## <a name="enable-access-to-the-secret"></a>A titkos kulcs hozzáférésének engedélyezése

Függetlenül attól, hogy egy új kulcstartó vagy egy meglévő, győződjön meg arról, hogy a sablon telepítése a felhasználó hozzáférhet-e a titkos kulcsot. A felhasználónak, a titkos kulcs hivatkozó sablonok telepítésével kell rendelkeznie a `Microsoft.KeyVault/vaults/deploy/action` a key vault engedély. A [tulajdonos](../role-based-access-control/built-in-roles.md#owner) és [közreműködő](../role-based-access-control/built-in-roles.md#contributor) szerepkörök is engedélyezik a hozzáférést.

## <a name="reference-a-secret-with-static-id"></a>Hivatkozás statikus azonosítójú titkos kulcs

A sablont, amely megkapja a kulcstartót titkos kulcs olyan, mint a többi sablont. Mivel ez **a paraméter fájlban, nem a sablon a key vault hivatkozik.** A következő kép bemutatja, hogyan a paraméterfájl hivatkozik, a titkos kulcsot, és ezt az értéket átadja a sablonhoz.

![Statikus azonosítója](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Például a [sablon következő](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) telepíti az SQL-adatbázis, amely egy rendszergazdai jelszót tartalmaz. A password paraméter értéke egy biztonságos karakterláncot kell megadnia. De a sablon nem adja meg, ha ezt az értéket származik.

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

Most a fenti sablon paraméter fájl létrehozása. A paraméterfájl adja meg a sablon a paraméter neve megegyezik-e paraméter. A paraméterérték használatával hivatkozik a key vault a titkos kulcsot. A titkos kulcsot úgy, hogy a key vault erőforrás-azonosítója és a titkos kulcs neve hivatkozik. Az a [a következő paraméterfájl](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json), léteznie kell a kulcstároló titkos kulcsot, és egy állandó érték megadása az erőforrás-azonosító. Másolja át helyileg a fájlt, és állítsa be az előfizetés-azonosító, a tároló neve és az SQL-kiszolgáló neve.

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

Ha a titkos kulcsot, a jelenlegi verziónál korábbi használata van szüksége, használja a `secretVersion` tulajdonság.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Most a sablon telepítéséhez, és adja át a paraméter fájlban. A példa-sablont a Githubból is használhat, de egy helyi paraméter fájlt kell használnia, állítsa be a környezetbe értékekkel.

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

## <a name="reference-a-secret-with-dynamic-id"></a>Dinamikus azonosítójú titkos kulcs hivatkozik

Az előző szakaszban bemutatta, hogyan felelt meg a kulcstartót titkos kulcsot egy statikus erőforrás azonosítója. Azonban bizonyos esetekben kell kulcstároló titkos kulcs eltérő az aktuális telepítés alapján hivatkozik. Ebben az esetben nem lehet szigorú kódot az erőforrás-azonosítója a paraméterfájlban. Sajnos nem lehet dinamikusan létrehozhat az erőforrás-azonosítója a paraméterfájlban mert sablon kifejezésekben nem engedélyezett a paraméterfájlban.

Lehet dinamikusan létrehozni a kulcstartó titkos kulcs az erőforrás-azonosító, át kell helyezni az erőforrás, amelyet a titkos kulcsot a csatolt sablonba. A szülő sablonban vegyen fel a csatolt sablont, majd adjon át egy paraméter, amely tartalmazza a dinamikusan generált erőforrás-azonosító. A következő kép bemutatja, hogyan a csatolt sablon egyik paraméterének hivatkozik a titkos kulcsot.

![Dinamikus azonosítója](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

A csatolt sablon keresztül egy külső URI elérhetőnek kell lennie. Általában a sablon hozzáadása egy tárfiókot, például az URI keresztül érhető el `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

A [sablon következő](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json) dinamikusan a kulcstartót Azonosítót hoz létre, és átadja paraméterként. Hivatkozik egy [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) a Githubon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

A fenti sablon üzembe helyezése, és adjon meg értékeket a paraméterek. A példa-sablont a Githubból is használhat, de a környezetnek meg kell adnia a paraméterértékek.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json \
    --parameters vaultName=<your-vault> vaultResourceGroup=examplegroup secretName=examplesecret adminLogin=exampleadmin sqlServerName=<server-name>
```

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json `
  -vaultName <your-vault> -vaultResourceGroup examplegroup -secretName examplesecret -adminLogin exampleadmin -sqlServerName <server-name>
```

## <a name="next-steps"></a>További lépések
* Kulcstároló kapcsolatos általános információkért lásd: [Ismerkedés az Azure Key Vault](../key-vault/key-vault-get-started.md).
* A titkos kulcs hivatkozó teljes példákért lásd [Key Vault példák](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
