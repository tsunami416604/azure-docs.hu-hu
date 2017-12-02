---
title: "Használata Azure felügyelete az Azure API Management Szolgáltatásidentitás |} Microsoft Docs"
description: "Az Azure Managed Service Identity az API Management használata"
services: api-management
documentationcenter: 
author: miaojiang
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: cf27e4d9997a796fa61af6e6f0af3c0c5a0c296f
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Azure felügyelt Szolgáltatásidentitás használata az Azure API Management

> [!Note]
> Az Azure API Management felügyelt Szolgáltatásidentitás jelenleg előzetes verzió.

Ez a cikk bemutatja, hogyan az API Management szolgáltatáspéldány felügyelt szolgáltatásidentitás létrehozása és egyéb erőforrások elérését. Azure Active Directory (Azure AD) által létrehozott felügyelt szolgáltatásidentitás lehetővé teszi, hogy az API Management példány könnyen és biztonságosan férhetnek hozzá más Azure AD által védett erőforrások, például az Azure Key Vault. A felügyelt szolgáltatásidentitás Azure kezeli, és nem kell kiosztania vagy forgassa el a titkos kulcsok. Az Azure Managed Service Identity kapcsolatos további információkért lásd: [Szolgáltatásidentitás felügyelt Azure-erőforrások](../active-directory/msi-overview.md).

## <a name="create-an-api-management-instance-with-an-identity-by-using-a-resource-manager-template"></a>Az API Management példányt létrehozni identitással a Resource Manager-sablon használatával

Az API Management példány létrehozhatja identitással többek között a következő tulajdonság az erőforrás-definícióban: 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Ez a tulajdonság tájékoztatja az Azure létrehozásához és kezeléséhez az API Management-példány azonosítója. 

Például egy teljes Azure Resource Manager-sablon nézhet ki például a következőket:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "parameters": {
        "serviceName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The name of the api management service"
            }
        },
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "admin@contoso.com",
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "Contoso",
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "Developer",
                "Standard",
                "Premium"
            ],
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
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "[parameters('serviceName')]",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
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
        }
    ]
}
```

## <a name="obtain-a-certificate-from-azure-key-vault"></a>Az Azure Key Vault egy tanúsítvány beszerzése

A következő példa bemutatja, hogyan lehet lekérni a tanúsítványt az Azure Key Vault. A következő lépéseket tartalmazza:

1. Az API Management példányt létrehozni identitással.
2. A hozzáférési házirendek az Azure Key Vault-példány frissítéséhez, és lehetővé teszik a titkos kulcsok beszerzése az API Management-példányt.
3. A tanúsítvány egyéni tartománynév beállítása a Key Vault példányból frissíteni az API Management-példány.

### <a name="prerequisites"></a>Előfeltételek
Végrehajtásához az alábbi arm-sablon igazolnia kell a következő 
1. A pfx-tanúsítvány a ugyanazt az előfizetést és az Api Management szolgáltatás ugyanazon resourcegroup tartalmazó kulcstároló. Ez a követelmény az arm-sablont. 
2. A titkos kulcs tartalomtípusa kell *alkalmazás/x-pkcs12*. A következő parancsfájlt használhatja a tanúsítvány feltöltése

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

## <a name="next-steps"></a>Következő lépések

További tudnivalók az Azure Managed Service Identity:

* [Az Azure-erőforrások felügyelt identitás](../active-directory/msi-overview.md)
* [Az Azure Resource Manager-sablonok](https://github.com/Azure/azure-quickstart-templates)

