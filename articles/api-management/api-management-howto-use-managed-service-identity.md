---
title: Felügyelt identitások használata az Azure API Managementban | Microsoft Docs
description: Ismerje meg, hogyan használhatók a felügyelt identitások a API Management
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249632"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Felügyelt identitások használata az Azure-ban API Management

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást egy API Management szolgáltatási példányhoz, és hogyan férhet hozzá más erőforrásokhoz. Azure Active Directory (Azure AD) által generált felügyelt identitás lehetővé teszi, hogy a API Management-példány könnyen és biztonságosan hozzáférhessen más Azure AD-védelemmel ellátott erőforrásokhoz, például a Azure Key Vaulthoz. Ezt az identitást az Azure felügyeli, és nem igényli semmilyen titok kiépítését és elforgatását. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Felügyelt identitás létrehozása API Management-példányhoz

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Ha felügyelt identitást szeretne beállítani a portálon, először létre kell hoznia egy API Management példányt a szokásos módon, majd engedélyeznie kell a szolgáltatást.

1. A szokásos módon hozzon létre egy API Management példányt a portálon. Navigáljon a portálon.
2. Válassza a **felügyelt szolgáltatás identitások**lehetőséget.
3. Váltson a Azure Active Directory-ra való regisztrációra. Kattintson a Mentés gombra.

![MSI engedélyezése](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>A Azure Resource Manager sablon használata

A következő tulajdonságot az erőforrás-definícióban is létrehozhatja az identitással rendelkező API Management-példányt:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Ez azt jelzi, hogy az Azure létrehozza és kezeli az API Management-példány identitását.

A teljes Azure Resource Manager sablon például a következőhöz hasonló lehet:

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
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>A felügyelt szolgáltatás identitásának használata más erőforrásokhoz való hozzáféréshez

> [!NOTE]
> A felügyelt identitások jelenleg API Management egyéni tartománynevek Azure Key Vault tanúsítványok beszerzésére használhatók. Hamarosan további forgatókönyvek is támogatottak lesznek.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Tanúsítvány beszerzése Azure Key Vault

#### <a name="prerequisites"></a>Előfeltételek
1. A pfx-tanúsítványt tartalmazó Key Vaultnak ugyanahhoz az Azure-előfizetéshez kell tartoznia, és ugyanazt az erőforráscsoportot kell megadnia, mint a API Management szolgáltatásnak. Ez a Azure Resource Manager sablon követelménye.
2. A titkos kód tartalomtípusának az *Application/x-PKCS12/pfx-profil*kell lennie. A tanúsítvány feltöltéséhez a következő szkriptet használhatja:

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
> Ha nincs megadva a tanúsítvány objektum-verziója, akkor a API Management automatikusan beolvassa a tanúsítvány újabb verzióját, miután feltöltötte Key Vault.

Az alábbi példa egy Azure Resource Manager sablont mutat be, amely a következő lépéseket tartalmazza:

1. Hozzon létre egy API Management példányt felügyelt identitással.
2. Frissítse egy Azure Key Vault példány hozzáférési szabályzatait, és engedélyezze, hogy a API Management-példány megszerezze a titkos kulcsokat.
3. Frissítse a API Management példányt úgy, hogy egyéni tartománynevet állít be a Key Vault-példány tanúsítványán keresztül.

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

További információ az Azure-erőforrások felügyelt identitásáról:

* [Az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-sablonok](https://github.com/Azure/azure-quickstart-templates)
* [Hitelesítés felügyelt identitással egy házirendben](./api-management-authentication-policies.md#ManagedIdentity)
