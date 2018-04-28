---
title: Használata Azure felügyelete az Azure API Management Szolgáltatásidentitás |} Microsoft Docs
description: Az Azure Managed Service Identity az API Management használata
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
ms.openlocfilehash: 98aa70935a3efbbe2edb07aade85fa3ea17ce786
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Azure felügyelt Szolgáltatásidentitás használata az Azure API Management

Ez a cikk bemutatja, hogyan az API Management szolgáltatáspéldány felügyelt szolgáltatásidentitás létrehozása és egyéb erőforrások elérését. Azure Active Directory (Azure AD) által létrehozott felügyelt szolgáltatásidentitás lehetővé teszi, hogy az API Management példány könnyen és biztonságosan férhetnek hozzá más Azure AD által védett erőforrások, például az Azure Key Vault. A felügyelt szolgáltatásidentitás Azure kezeli, és nem kell kiosztania vagy forgassa el a titkos kulcsok. Az Azure Managed Service Identity kapcsolatos további információkért lásd: [Szolgáltatásidentitás felügyelt Azure-erőforrások](../active-directory/msi-overview.md).

## <a name="create-a-managed-service-identity-for-an-api-management-instance"></a>Az API Management-példány felügyelt szolgáltatásidentitás létrehozása

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A portálon kezelt szolgáltatásidentitás beállításához először a szokásos módon API Management példánya létrehoz és majd engedélyezze a szolgáltatást.

1. Az API Management példányt létrehozni a portálon, a szokásos módon. Keresse meg azt a portálon.
2. Válassza ki **identitás**.
3. A regisztráció az Azure Active Directoryval vált. Kattintson a Mentés gombra.

![MSI engedélyezése](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Az Azure Resource Manager sablon használatával

Az API Management példány létrehozhatja identitással többek között a következő tulajdonság az erőforrás-definícióban: 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Ez alapján Azure létrehozásához és kezeléséhez az API Management-példány azonosítója. 

Például egy teljes Azure Resource Manager-sablon nézhet ki például a következőket:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0"
    },
    "resources": [
        {
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
        }
    ]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Egyéb erőforrásainak elérésére a felügyelt identitás használatára

> [!NOTE]
> Felügyelt szolgáltatásidentitás jelenleg az Azure Key Vault tanúsítványok beszerzése API Management egyéni tartománynevek használható. További forgatókönyvek is támogatottak lesznek hamarosan.
> 
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Az Azure Key Vault egy tanúsítvány beszerzése

#### <a name="prerequisites"></a>Előfeltételek
1. A Key Vault a pfx-tanúsítvány tartalmazó azonos Azure-előfizetést és az API Management szolgáltatás ugyanabban az erőforráscsoportban kell lennie. Ez feltétele az Azure Resource Manager sablon. 
2. A titkos kulcs tartalom típusúnak kell lennie *alkalmazás/x-pkcs12*. A következő parancsfájl segítségével töltse fel a tanúsítványt:

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
> Ha a tanúsítvány objektum verziója nem áll rendelkezésre, az API Management automatikusan beszerzi az újabb verzió, a tanúsítvány Key Vault való feltöltésük után. 

A következő példa bemutatja az Azure Resource Manager-sablon, amely a következő lépéseket tartalmazza:

1. Felügyelt szolgáltatásidentitás hozzon létre egy API Management-példányt.
2. A hozzáférési házirendek az Azure Key Vault-példány frissítéséhez, és lehetővé teszik a titkos kulcsok beszerzése az API Management-példányt.
3. A tanúsítvány egyéni tartománynév beállítása a Key Vault példányból frissíteni az API Management-példány.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "description": "Reference to the KeyVault certificate."
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

További tudnivalók az Azure Managed Service Identity:

* [Az Azure-erőforrások felügyelt identitás](../active-directory/msi-overview.md)
* [Az Azure Resource Manager-sablonok](https://github.com/Azure/azure-quickstart-templates)

