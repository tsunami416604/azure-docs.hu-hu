---
title: Azure-útmutató – Azure Key Vault és egy tároló hozzáférési szabályzatának létrehozása Azure Resource Manager sablon használatával | Microsoft Docs
description: Bemutatja, hogyan hozhatók létre Azure Key Vault-és tároló-hozzáférési házirendek Azure Resource Manager sablon használatával.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804339"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>Azure Key Vault-és tár-hozzáférési szabályzat létrehozása Resource Manager-sablonnal

A [Azure Key Vault](../general/overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a titkok számára, például kulcsokat, jelszavakat, tanúsítványokat és egyéb titkokat. Ez az útmutató egy Azure Resource Manager sablon (ARM-sablon) üzembe helyezésének folyamatát ismerteti a kulcstartó létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.


## <a name="create-key-vault-resource-manager-template"></a>Key Vault Resource Manager-sablon létrehozása

A következő sablon a Key Vault létrehozásának alapvető módját mutatja be. Néhány érték a sablonban van megadva.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

További információ az Key Vault sablon beállításairól: [Key Vault ARM-sablon referenciája](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Ha a sablon újratelepítése megtörténik, a rendszer felülbírálja a Key vaultban lévő összes meglévő hozzáférési szabályzatot. A `accessPolicies` Key vaulthoz való hozzáférés elvesztésének elkerülése érdekében ajánlott a tulajdonságot meglévő hozzáférési házirendekkel feltölteni. 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>Hozzáférési szabályzat hozzáadása Key Vault Resource Manager-sablonhoz

A meglévő Key vaultra vonatkozó hozzáférési szabályzatokat a teljes Key Vault-sablon újbóli üzembe helyezése nélkül is üzembe helyezheti. A következő sablon egy alapszintű módszert mutat be hozzáférési szabályzatok létrehozásához.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
További információ az Key Vault sablon beállításairól: [Key Vault ARM-sablon referenciája](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="other-available-key-vault-resource-manager-templates"></a>Egyéb elérhető Key Vault Resource Manager-sablonok

Más Resource Manager-sablonok is elérhetők Key Vault objektumokhoz:

| Titkos kulcsok | Kulcsok | Tanúsítványok |
|--|--|--|
|[Gyors útmutató](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[Referencia](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|N.A.|N.A.|

További Key Vault-sablonok itt találhatók: [Key Vault Resource Manager – dokumentáció](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>A sablonok üzembe helyezése

Az alábbi útmutatóban a "saját sablon létrehozása a szerkesztőben" lehetőséggel telepítheti a fenti sablonokat a Azure Portal használatával: [erőforrások üzembe helyezése egyéni sablonból](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

A fenti sablonokat fájlokat is mentheti, és a következő parancsokat használhatja:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és [az Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és az azt követő rövid útmutatók és oktatóanyagok is megtalálhatók, ezeket az erőforrásokat helyben hagyhatja. Ha az erőforrásokra már nincs szükség, törölje az erőforráscsoportot, amely törli a kulcstartót és a kapcsolódó erőforrásokat. Az erőforráscsoport Azure CLI vagy Azure PowerShell használatával történő törléséhez kövesse az alábbi lépéseket.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>További források

- [A Azure Key Vault áttekintése](../general/overview.md)
- További információ az [Azure Resource Managerről](../../azure-resource-manager/management/overview.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md) áttekintése

## <a name="next-steps"></a>További lépések

- [Biztonságos hozzáférés a kulcstartóhoz](secure-your-key-vault.md)
- [Hitelesítés a Key vaultban](authentication.md)
- [Azure Key Vault fejlesztői útmutató](developers-guide.md)
