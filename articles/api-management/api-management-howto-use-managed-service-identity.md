---
title: Felügyelt identitások használata az Azure API Management ben | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja a felügyelt identitásokat az API Management ben
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 49576b805e6c6d01340e663bfb5d8e9013917625
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249632"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Felügyelt identitások használata az Azure API Managementben

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást egy API Management szolgáltatáspéldányhoz, és hogyan érhet el más erőforrásokat. Az Azure Active Directory (Azure AD) által létrehozott felügyelt identitás lehetővé teszi, hogy az API Management-példány könnyen és biztonságosan hozzáférjen más Azure AD-védett erőforrásokhoz, például az Azure Key Vaulthoz. Ezt az identitást az Azure kezeli, és nem követeli meg, hogy kiépítse vagy elforgassa a titkos kulcsokat. A felügyelt identitásokról a [Mi az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md)című témakörben talál további információt.

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Felügyelt identitás létrehozása API Management-példányhoz

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Felügyelt identitás beállítása a portálon, először hozzon létre egy API Management-példányt a szokásos módon, majd engedélyezze a funkciót.

1. Hozzon létre egy API Management-példányt a portálon, ahogy általában tenné. Keresse meg a portálon.
2. Válassza **a Felügyelt szolgáltatás identitásait**.
3. Váltás a Regisztráció az Azure Active Directoryval be Kattintson a Mentés gombra.

![Az MSI engedélyezése](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Az Azure Resource Manager sablon használata

Létrehozhat egy API Management-példányt identitással, ha a következő tulajdonságot tartalmazza az erőforrás-definícióban:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Ez arra utasítja az Azure-t, hogy hozza létre és kezelje az API Management-példány identitását.

Egy teljes Azure Resource Manager-sablon például a következőkre néz ki:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>A felügyelt szolgáltatásidentitás használata más erőforrások eléréséhez

> [!NOTE]
> Jelenleg felügyelt identitások segítségével szerezhet tanúsítványokat az Azure Key Vault api management egyéni tartománynevek. További forgatókönyvek hamarosan támogatni fogják.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Tanúsítvány beszerzése az Azure Key Vaultból

#### <a name="prerequisites"></a>Előfeltételek
1. A pfx-tanúsítványt tartalmazó Key Vaultnak ugyanabban az Azure-előfizetésben és az API-felügyeleti szolgáltatással megegyező erőforráscsoportban kell lennie. Ez az Azure Resource Manager sablon követelménye.
2. A titkos titok tartalomtípusának *alkalmazásnak/x-pkcs12-nek*kell lennie. A tanúsítvány feltöltéséhez a következő parancsfájl használható:

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Ha a tanúsítvány objektumverziója nincs megadva, az API Management automatikusan beszerzi a tanúsítvány újabb verzióját a Key Vaultba való feltöltés után.

A következő példa egy Azure Resource Manager-sablont mutat be, amely a következő lépéseket tartalmazza:

1. Hozzon létre egy API Management-példányt felügyelt identitással.
2. Frissítse az Azure Key Vault-példány hozzáférési szabályzatait, és engedélyezze, hogy az API Management-példány titkokat szerezzen be belőle.
3. Frissítse az API Management-példányt egyéni tartománynév beállításával a Key Vault-példány tanúsítványán keresztül.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

## <a name="next-steps"></a>További lépések

További információ az Azure-erőforrások felügyelt identitásairól:

* [Mi az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-sablonok](https://github.com/Azure/azure-quickstart-templates)
* [Hitelesítés felügyelt identitással egy házirendben](./api-management-authentication-policies.md#ManagedIdentity)
