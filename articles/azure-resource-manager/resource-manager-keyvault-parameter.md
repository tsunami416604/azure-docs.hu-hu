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
ms.date: 07/09/2018
ms.author: tomfitz
ms.openlocfilehash: 3a29319a0d478537dfc4905ee77865b8fea64587
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38598407"
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

Az előző szakaszban bemutatta, hogyan adhatók át a key vault titkos kulcsából egy statikus erőforrás azonosítója. Azonban bizonyos esetekben szüksége a key vault titkos kulcs, amely alapján a jelenlegi üzemelő példány hivatkozni. Ebben az esetben az erőforrás-azonosítója a paraméterfájlban rögzítse szoftveresen, nem. Sajnos nem lehet dinamikusan hozza létre az erőforrás-azonosítója a paraméterfájlban, mert a sablon kifejezésekben nem engedélyezett a paraméterfájlban.

Dinamikusan generálható a key vault titkos kulcs az erőforrás-azonosító, át kell helyezni az erőforrást, amelyet a titkos kulcsot egy csatolt sablonba be. A szülő-sablonban adja hozzá a hivatkozott sablonnak, és adja át a paramétert, amely tartalmazza a dinamikusan létrehozott erőforrás-azonosítója. Az alábbi képen látható, hogyan a társított sablon egyik paraméterének hivatkozik-e a titkos kulcsot.

![A dinamikus azonosítója](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

A hivatkozott sablonnak keresztül egy külső URI elérhetőnek kell lennie. Általában a sablon hozzáadása egy tárfiókot, és az URI-t, például keresztül érhető el `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

A [sablon alábbi](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json) dinamikusan hoz létre a key vault-azonosítója és paraméterként átadja azokat. Hivatkozik egy [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) a Githubon.

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

Az előző sablon üzembe helyezése, és adja meg a paraméterek értékeit. A példa-sablont a Githubból is használhat, de a paraméter értékét meg kell adnia a környezetben.

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
* Kulcstartók kapcsolatos általános információkért lásd: [első lépései az Azure Key Vault](../key-vault/key-vault-get-started.md).
* A hívóbetű titkos kulcsait hivatkozó teljes példákért lásd [Key Vault példák](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
