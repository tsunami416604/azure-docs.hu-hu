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
ms.date: 06/12/2020
ms.author: apimpm
ms.openlocfilehash: 028b26537c9fe8a976dbc68a4776b2ea4101d811
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789409"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Felügyelt identitások használata az Azure-ban API Management

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást egy API Management-példányhoz, és hogyan férhet hozzá más erőforrásokhoz. Azure Active Directory (Azure AD) által generált felügyelt identitás lehetővé teszi, hogy a API Management-példány könnyen és biztonságosan hozzáférhessen más Azure AD-védelemmel ellátott erőforrásokhoz, például a Azure Key Vaulthoz. Ezt az identitást az Azure felügyeli, és nem igényli semmilyen titok kiépítését és elforgatását. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása](../active-directory/managed-identities-azure-resources/overview.md).

Egy API Management-példány két típusú identitást biztosíthat:

- A **rendszer által hozzárendelt identitás** a szolgáltatáshoz van kötve, és törlődik, ha a szolgáltatás törölve van. A szolgáltatásnak csak egy rendszerhez rendelt identitása lehet.
- A **felhasználó által hozzárendelt identitás** egy önálló Azure-erőforrás, amelyet hozzá lehet rendelni a szolgáltatáshoz. A szolgáltatás több felhasználó által hozzárendelt identitással (*) is rendelkezhet.

## <a name="create-a-system-assigned-managed-identity-for-an-api-management-instance"></a>Rendszerhez rendelt felügyelt identitás létrehozása API Management-példányhoz

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Ha felügyelt identitást szeretne beállítani a portálon, először létre kell hoznia egy API Management példányt a szokásos módon, majd engedélyeznie kell a szolgáltatást.

1. A szokásos módon hozzon létre egy API Management példányt a portálon. Navigáljon a portálon.
2. Válassza a **felügyelt identitások**lehetőséget.
3. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva **értékre**. Kattintson a **Save** (Mentés) gombra.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="A rendszer által hozzárendelt felügyelt identitás engedélyezése." border="true":::


### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő lépések végigvezetik egy API Management példány létrehozásán és az identitás Azure PowerShell használatával történő hozzárendelésének lépésein. 

1. Szükség esetén telepítse a Azure PowerShell a [Azure PowerShell útmutatóban](/powershell/azure/install-az-ps)található utasításokkal, majd futtassa a parancsot az `Connect-AzAccount` Azure-hoz való kapcsolódáshoz.

2. Hozzon létre egy API Management példányt a Azure PowerShell használatával. További példák a API Management-példánnyal való Azure PowerShell használatáról: [API Management PowerShell-minták](powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Frissítés és meglévő példány létrehozása az identitás létrehozásához:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

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
        "apiVersion": "2019-01-01",
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

A példány létrehozásakor a következő tulajdonságokkal rendelkezik:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A tenantId tulajdonság azt határozza meg, hogy az identitás melyik Azure AD-bérlőhöz tartozik. A principalId a példány új identitásának egyedi azonosítója. Az Azure AD-n belül az egyszerű szolgáltatásnév neve megegyezik a API Management példányával.


> [!NOTE]
> Egy API Management-példányhoz a rendszerhez hozzárendelt és felhasználó által hozzárendelt identitások is tartozhatnak egyszerre. Ebben az esetben a tulajdonság a következő: `type``SystemAssigned,UserAssigned`

### <a name="scenarios-supported"></a>Támogatott forgatókönyvek

#### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Egyéni TLS/SSL-tanúsítvány beszerzése a API Management-példányhoz Azure Key Vault
Egy API Management szolgáltatás rendszer által hozzárendelt identitása használható a Azure Key Vault tárolt egyéni TLS/SSL-tanúsítványok lekéréséhez. Ezeket a tanúsítványokat API Management-példányban lévő egyéni tartományokhoz lehet hozzárendelni.

1. A titkos kód tartalomtípusának az *Application/x-PKCS12/pfx-profil*kell lennie.
2. A Key Vault tanúsítvány titkos végpontját kell használni, amely a tényleges titkot tartalmazza.

> [!Important]
> Ha nincs megadva a tanúsítvány objektum-verziója, API Management automatikusan beolvassa a tanúsítvány újabb verzióját, miután Key Vault 4 órán belül feltöltötte azt.

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
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
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
        "apiVersion": "2019-01-01",
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

#### <a name="authenticate-using-api-management-identity-to-the-backend"></a>Hitelesítés API Management identitás használatával a háttérbe

A rendszer által hozzárendelt identitás használatával hitelesíthető a háttérrendszer a [hitelesítéssel felügyelt identitás](api-management-authentication-policies.md#ManagedIdentity) -házirenddel.


## <a name="create-a-user-assigned-managed-identity-for-an-api-management-instance"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása API Management-példányhoz

> [!NOTE]
> Egy API Management példány legfeljebb 10 felhasználó által hozzárendelt felügyelt identitással társítható.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Ha felügyelt identitást szeretne beállítani a portálon, először létre kell hoznia egy API Management példányt a szokásos módon, majd engedélyeznie kell a szolgáltatást.

1. A szokásos módon hozzon létre egy API Management példányt a portálon. Navigáljon a portálon.
2. Válassza a **felügyelt identitások**lehetőséget.
3. A **felhasználó által hozzárendelt** lapon kattintson a **Hozzáadás**gombra.
4. Keresse meg a korábban létrehozott identitást, és válassza ki. Kattintson a **Hozzáadás** parancsra.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="A felhasználóhoz rendelt felügyelt identitás engedélyezése." border="true":::

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő lépések végigvezetik egy API Management példány létrehozásán és az identitás Azure PowerShell használatával történő hozzárendelésének lépésein. 

1. Szükség esetén telepítse a Azure PowerShell a [Azure PowerShell útmutatóban](/powershell/azure/install-az-ps)található utasításokkal, majd futtassa a parancsot az `Connect-AzAccount` Azure-hoz való kapcsolódáshoz.

2. Hozzon létre egy API Management példányt a Azure PowerShell használatával. További példák a API Management-példánnyal való Azure PowerShell használatáról: [API Management PowerShell-minták](powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Frissítse és meglévő szolgáltatást, hogy identitást rendeljen a szolgáltatáshoz.

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="using-the-azure-resource-manager-template"></a>A Azure Resource Manager sablon használata

A következő tulajdonságot az erőforrás-definícióban is létrehozhatja az identitással rendelkező API Management-példányt:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

A felhasználó által hozzárendelt típus hozzáadásával közli az Azure-t, hogy a példányhoz megadott felhasználó által hozzárendelt identitást használja.

A teljes Azure Resource Manager sablon például a következőhöz hasonló lehet:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
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
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
        "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

A szolgáltatás létrehozása után a következő tulajdonságokkal rendelkezik:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

A principalId az Azure AD-felügyelethez használt identitás egyedi azonosítója. A clientId az alkalmazás új identitásának egyedi azonosítója, amely a futásidejű hívásokban használandó identitás megadására szolgál.

> [!NOTE]
> Egy API Management-példányhoz a rendszerhez hozzárendelt és felhasználó által hozzárendelt identitások is tartozhatnak egyszerre. Ebben az esetben a tulajdonság a következő: `type``SystemAssigned,UserAssigned`

### <a name="scenarios-supported"></a>Támogatott forgatókönyvek

#### <a name="authenticate-using-user-assigned-identity-to-the-backend"></a>Hitelesítés felhasználó által hozzárendelt identitással a háttérrendszer használatával

A felhasználó által hozzárendelt identitás használatával a [hitelesítéssel felügyelt identitás](api-management-authentication-policies.md#ManagedIdentity) -házirenddel végezheti el a hitelesítést a háttérbe.


## <a name="remove-an-identity"></a><a name="remove"></a>Identitás eltávolítása

A rendszer által hozzárendelt identitás eltávolításához tiltsa le a szolgáltatást a portálon vagy a Azure Resource Manager sablonnal ugyanúgy, ahogyan azt létrehozták. A felhasználó által hozzárendelt identitások egyenként eltávolíthatók. Az összes identitás eltávolításához állítsa az identitás típusát "None" értékre.

A rendszer által hozzárendelt identitások eltávolítása az Azure AD-ből is törölve lesz. A rendszer által hozzárendelt identitások automatikusan törlődnek az Azure AD-ből, ha a API Management példányt törlik.

Ha Azure Resource Manager sablon használatával szeretné eltávolítani az összes identitást, frissítse ezt a szakaszt:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Ha egy API Management-példány egyéni SSL-tanúsítvánnyal van konfigurálva a kulcstartóból, és kísérlet történt a felügyelt identitás letiltására, a kérelem sikertelen.
> Az ügyfél letilthatja önmagát, ha átvált Azure Key Vault tanúsítványról a beágyazott kódolt tanúsítvány biztosításához, majd letiltja a felügyelt identitást. Lásd az [egyéni tartomány konfigurálása](configure-custom-domain.md) című témakört.

## <a name="next-steps"></a>További lépések

További információ az Azure-erőforrások felügyelt identitásáról:

* [Az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-sablonok](https://github.com/Azure/azure-quickstart-templates)
* [Hitelesítés felügyelt identitással egy házirendben](./api-management-authentication-policies.md#ManagedIdentity)
