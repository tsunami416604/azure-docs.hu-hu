---
title: A Key Vault hivatkozások – az Azure App Service |} A Microsoft Docs
description: Fogalmi referencia és beállítási útmutató az Azure Key Vault hivatkozik az Azure App Service és az Azure Functions
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 662260c3cf37f8f8a675c522f3d3dea41153e485
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663564"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>A Key Vault hivatkozások használata az App Service és az Azure Functions (előzetes verzió)

> [!NOTE] 
> A Key Vault hivatkozások jelenleg előzetes verzióban érhető el.

Ez a témakör bemutatja, hogyan használható a az App Service-ben vagy az Azure Functions-alkalmazás az Azure Key Vault titkos kulcsainak kódváltozások nélkül. [Az Azure Key Vault](../key-vault/key-vault-overview.md) egy szolgáltatás, amely titkok központosított kezelése, teljes körűen felügyelve hozzáférési házirendek és a naplózási előzmények.

## <a name="granting-your-app-access-to-key-vault"></a>Az alkalmazás-hozzáférés biztosítása a Key Vaulthoz

Olvassa el a titkos kulcsok a Key Vaultból, kell rendelkeznie egy létrehozott és az alkalmazás engedélye az eléréséhez.

1. Key vault létrehozása a következő a [Key Vault rövid](../key-vault/quick-create-cli.md).

1. Hozzon létre egy [alapértelmezett felügyelt identitás](overview-managed-identity.md) az alkalmazáshoz.

   > [!NOTE] 
   > A Key Vault jelenleg csak a támogatási rendszer hozzárendelt felügyelt identitások hivatkozik. Felhasználó által hozzárendelt identitások nem használható.

1. Hozzon létre egy [hozzáférési szabályzatot a Key Vaultban](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) a korábban létrehozott identita aplikace. Engedélyezze ezt a házirendet a "Get" titkos engedélyt. Ne konfigurálja a "engedélyezett alkalmazás" vagy `appliationId` beállításai, mivel ezt, nem kompatibilis egy felügyelt identitás.

## <a name="reference-syntax"></a>Hivatkozási szintaxisa

Egy Key Vault-hivatkozást a rendszer az űrlap `@Microsoft.KeyVault({referenceString})`, ahol `{referenceString}` helyébe a következő lehetőségek közül:

> [!div class="mx-tdBreakAll"]
> | Hivatkozási karakterlánc                                                            | Leírás                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | A **SecretUri** az adatsík URI-ját egy titkos kulcsot a Key Vaultban, többek között például egy verziót kell lennie. https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | A **VaultName** kell a Key Vault-erőforrás nevét. A **SecretName** a céloldali titkos kód nevét kell megadni. A **SecretVersion** a használandó titkoskulcs-verziót kell lennie. |

> [!NOTE] 
> Az aktuális előzetes verziója is szükséges. Elforgatás titkos kulcsokat, ha az alkalmazás konfigurációjában a verzióra frissíteni kell.

Például teljes lenne a következőhöz hasonló:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Azt is megteheti:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>A Key Vault alkalmazás forrásbeállítások

Key Vault hivatkozások használható értékeit [Alkalmazásbeállítások](web-sites-configure.md#app-settings), lehetővé téve, hogy a titkos kulcsok a Key Vaultban helykonfigurációjában helyett. Alkalmazásbeállítások biztonságosan titkosított inaktív, de a titkos kódok kezelése képességek van szüksége, ha azok kell lépjen be a Key Vault.

Alkalmazás-beállítás, a Key Vault hivatkozás használatához állítja be a hivatkozást a beállítás értékét. Az alkalmazás a szokásos módon keresztül is lehet hivatkozni a titkos kulcsot. Kódmódosítás nélkül szükség.

> [!TIP]
> A legtöbb alkalmazás beállításokat a Key Vault hivatkozásokat kell megjelölve lennie tárolóhely-beállítások, mint minden környezethez külön tárolóra van szükség.

### <a name="azure-resource-manager-deployment"></a>Az Azure Resource Manager üzembe helyezése

Amikor automatizálása az erőforrás üzembe helyezést az Azure Resource Manager-sablonok, szükség lehet előkészítéséhez a függőségek, hogy működik ez a funkció egy adott sorrendben. Vegye figyelembe, hogy a kell az alkalmazás-beállítások megadása a saját erőforrásként használata helyett egy `siteConfig` tulajdonságot a helydefiníció. Ennek az oka, hogy a rendszer által hozzárendelt identitás jön létre, és a hozzáférési házirendben használható először definiálni kell a hely.

Egy példa psuedo-sablon egy függvényalkalmazáshoz előfordulhat, hogy a következőhöz hasonló:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> Ebben a példában a forrás-vezérlő telepítése az alkalmazás beállításaitól függ. Ez a lehetőség általában nem biztonságos viselkedését, aszinkron módon viselkedik az alkalmazás-beállítás frissítése. Azonban mivel vannak megadva a `WEBSITE_ENABLE_SYNC_UPDATE_SITE` alkalmazás-beállításához, a frissítés tárreplikában. Ez azt jelenti, hogy a forrás-vezérlő telepítése csak után megkezdődik az alkalmazásbeállítások teljes körűen frissítve lett-e.
