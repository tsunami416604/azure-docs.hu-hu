---
title: Key Vault titkos sablonnal
description: Bemutatja, hogyan adja át a titkos kulcsot a key vault paraméterként az üzembe helyezés során.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 08b4042c6bad83f13ebaea0f46046ea7707fd868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460194"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Biztonságos paraméterérték megadása az Üzembe helyezés során az Azure Key Vault használatával

Ahelyett, hogy egy biztonságos értéket (például egy jelszót) helyezne el közvetlenül a sablonban vagy a paraméterfájlban, lekérheti az értéket egy [Azure Key Vaultból](../../key-vault/key-vault-overview.md) a központi telepítés során. Az értéket a key vault és a paraméterfájl titkos kulcsa hivatkozva szerezheti be. Az érték soha nem érhető el, mert csak a key vault-azonosítóra hivatkozik. A key vault létezhet egy másik előfizetés, mint az erőforráscsoport, amelyüzembe helyezése.

Ez a cikk arra összpontosít, hogy a forgatókönyv egy bizalmas érték átadása sablonparaméterként. Nem terjed ki a forgatókönyv beállítása egy virtuális gép tulajdonság a tanúsítvány URL-címét a Key Vault. A forgatókönyv rövid útmutató sablonját az [Azure Key Vault tanúsítványának telepítése virtuális gépen című témakörben olvashat.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

## <a name="deploy-key-vaults-and-secrets"></a>Kulcstartók és titkos kulcsok üzembe helyezése

Ha a sablon üzembe helyezése `enabledForTemplateDeployment` során szeretne hozzáférni `true`egy kulcstartóhoz, állítsa be a key vaulton.

Ha már rendelkezik egy Key Vault, győződjön meg arról, hogy lehetővé teszi a sablon központi telepítések.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Új kulcstartó létrehozásához és titkos kulcs hozzáadásához használja a következőket:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

A kulcstartó tulajdonosaként automatikusan hozzáférhet a titkos kulcsok létrehozásához. Ha a titkos rendszerű felhasználó nem a kulcstartó tulajdonosa, adjon hozzáférést a következőkkel:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

A kulcstartók létrehozásáról és a titkos kulcsok hozzáadásáról a következő témakörökben talál további információt:

- [Titkos kulcs beállítása és beolvasása cli használatával](../../key-vault/quick-create-cli.md)
- [Titkos titok beállítása és beolvasása a Powershell használatával](../../key-vault/quick-create-powershell.md)
- [Titkos titok beállítása és beolvasása a portál használatával](../../key-vault/quick-create-portal.md)
- [Titkos titok beállítása és beolvasása a .NET használatával](../../key-vault/quick-create-net.md)
- [Titkos titok beállítása és beolvasása a Node.js használatával](../../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Hozzáférés a titkokhoz

A sablont üzembe helyező `Microsoft.KeyVault/vaults/deploy/action` felhasználónak engedéllyel kell rendelkeznie az erőforráscsoport és a kulcstartó hatóköréhez. A [tulajdonos és](../../role-based-access-control/built-in-roles.md#owner) a [közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szerepkörök egyaránt engedélyezik ezt a hozzáférést. Ha létrehozta a key vault, te vagy a tulajdonos, így rendelkezik az engedéllyel.

Az alábbi eljárás bemutatja, hogyan hozhat létre szerepkört a minimális engedéllyel, és hogyan rendelheti hozzá a felhasználót

1. Egyéni szerepkör-definíciós JSON-fájl létrehozása:

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
    Cserélje le a "00000000-0000-0000-0000-000000000000" helyett az előfizetésazonosítót.

2. Hozza létre az új szerepkört a JSON-fájl használatával:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    A minták hozzárendelik az egyéni szerepkört a felhasználóhoz az erőforráscsoport szintjén.

Ha egy Key Vault egy [felügyelt alkalmazás](../managed-applications/overview.md)sablonját használja, hozzáférést kell biztosítania a **készülékerőforrás-szolgáltató** egyszerű szolgáltatásához. További információ: [Access Key Vault secret az Azure felügyelt alkalmazások telepítésekor.](../managed-applications/key-vault-access.md)

## <a name="reference-secrets-with-static-id"></a>Hivatkozási titkok statikus azonosítóval

Ezzel a módszerrel a paraméterfájlban lévő key vaultra hivatkozik, nem a sablonra. Az alábbi képen látható, hogy a paraméterfájl hogyan hivatkozik a titkos titokra, és hogyan adja át ezt az értéket a sablonnak.

![Az Erőforrás-kezelő kulcstartójának integrációja Statikus azonosító diagram](./media/key-vault-parameter/statickeyvault.png)

[Oktatóanyag: Az Azure Key Vault integrálása a Resource Manager-sablon üzembe helyezésében](./template-tutorial-use-key-vault.md) ezt a módszert használja.

A következő sablon egy rendszergazdai jelszót tartalmazó SQL-kiszolgálót telepít. A jelszóparaméter biztonságos karakterláncra van beállítva. De a sablon nem határozza meg, hogy ez az érték honnan származik.

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
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
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

Most hozzon létre egy paraméterfájlt az előző sablonhoz. A paraméterfájlban adjon meg egy paramétert, amely megegyezik a sablonban lévő paraméter nevével. A paraméter érték, hivatkozzon a titkos kulcsot a key vault. A titkos kulcsra hivatkozva adja át a kulcstartó erőforrás-azonosítóját és a titkos kulcs nevét:

A következő paraméterfájlban a key vault titkos kulcsának már léteznie kell, és statikus értéket kell megadnia az erőforrás-azonosítóhoz.

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

Ha a titkos kulcsot az aktuális verziótól eltérő verziót kell használnia, használja a `secretVersion` tulajdonságot.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Telepítse a sablont, és adja át a paraméterfájlban:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Hivatkozási titkok dinamikus azonosítóval

Az előző szakasz ban bemutatták, hogyan adható át egy statikus erőforrás-azonosító a paraméter kulcstároló titkos kulcsa. Bizonyos esetekben azonban egy olyan key vault-titkos kulcsra kell hivatkoznia, amely az aktuális központi telepítéstől függően változik. Vagy előfordulhat, hogy paraméterértékeket szeretne átadni a sablonnak ahelyett, hogy hivatkozási paramétert hozna létre a paraméterfájlban. Mindkét esetben dinamikusan létrehozhatja az erőforrás-azonosítót egy kulcstartó titkos kulcsegy csatolt sablon használatával.

Az erőforrás-azonosító nem hozható létre dinamikusan a paraméterfájlban, mert a sablonkifejezések nem engedélyezettek a paraméterfájlban.

A szülősablonban hozzáadja a beágyazott sablont, és átadja a dinamikusan generált erőforrás-azonosítót tartalmazó paramétert. Az alábbi képen látható, hogy a csatolt sablonban lévő paraméter hogyan hivatkozik a titkos titokra.

![Dinamikus azonosító](./media/key-vault-parameter/dynamickeyvault.png)

A következő sablon dinamikusan létrehozza a key vault-azonosítót, és paraméterként adja át.

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
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
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
  }
}
```

## <a name="next-steps"></a>További lépések

- A kulcstartókról a [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)
- A kulcstitkokra való hivatkozás teljes példáit lásd: [Key Vault-példák.](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)
