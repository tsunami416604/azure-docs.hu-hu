---
title: Key Vault referenciák – Azure App Service | Microsoft Docs
description: A Azure App Service és a Azure Functions Azure Key Vault referenciáinak fogalmi referenciája és beállítási útmutatója
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 311a9fc887db399cb16d6cbb2bcec665a7ddfce7
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240122"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Key Vault referenciák használata App Service és Azure Functionshoz (előzetes verzió)

> [!NOTE] 
> Key Vault referenciák jelenleg előzetes verzióban érhetők el, és a Linux-fogyasztási csomagok jelenleg nem támogatják őket.

Ebből a témakörből megtudhatja, hogyan dolgozhat fel a titkokat a App Service vagy Azure Functions alkalmazásban Azure Key Vault a kód módosítása nélkül. A [Azure Key Vault](../key-vault/key-vault-overview.md) egy olyan szolgáltatás, amely központosított titkok felügyeletét teszi lehetővé a hozzáférési házirendek és a naplózási előzmények teljes körű szabályozásával.

## <a name="granting-your-app-access-to-key-vault"></a>Az alkalmazás hozzáférésének biztosítása Key Vault

A Key Vault titkainak beolvasásához létre kell hoznia egy tárolót, és engedélyt kell adni az alkalmazásnak az eléréséhez.

1. Hozzon létre egy kulcstartót a [Key Vault](../key-vault/quick-create-cli.md)rövid útmutató alapján.

1. Hozzon létre egy [rendszerhez rendelt felügyelt identitást](overview-managed-identity.md) az alkalmazáshoz.

   > [!NOTE] 
   > Key Vault referenciák jelenleg csak a rendszer által hozzárendelt felügyelt identitásokat támogatják. Felhasználó által hozzárendelt identitások nem használhatók.

1. Hozzon létre egy [hozzáférési szabályzatot a Key Vaultban](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) a korábban létrehozott alkalmazás-identitáshoz. A "Get" Secret engedély engedélyezése a szabályzathoz. Ne konfigurálja a "meghatalmazott alkalmazás" vagy a `applicationId` beállításokat, mivel ez nem kompatibilis a felügyelt identitással.

    > [!NOTE]
    > Key Vault hivatkozások nem képesek a Key vaultban tárolt titkos kódok feloldására [hálózati korlátozásokkal](../key-vault/key-vault-overview-vnet-service-endpoints.md).

## <a name="reference-syntax"></a>Hivatkozás szintaxisa

A Key Vault hivatkozás `@Microsoft.KeyVault({referenceString})` formátumú, ahol a `{referenceString}` a következő lehetőségek egyikével lett lecserélve:

> [!div class="mx-tdBreakAll"]
> | Hivatkozási sztring                                                            | Leírás                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_SecretUri_                                                       | A **SecretUri** az Key Vault titkos kulcsa teljes adatsík URI-ja, beleértve a verziószámot is, például https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName =_VaultName_; SecretName =_SecretName_; Titkoskulcsverziója =_titkoskulcsverziója_ | A **VaultName** meg kell egyeznie a Key Vault erőforrás nevével. A **SecretName** a célként megadott titkos kód nevének kell lennie. A **titkoskulcsverziója** a használni kívánt titkos kulcs verziószámának kell lennie. |

> [!NOTE] 
> Az aktuális előzetes verzióban a verziók megadása kötelező. A titkok elforgatásakor frissítenie kell a verziót az alkalmazás konfigurációjában.

A teljes hivatkozás például a következőhöz hasonló lesz:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Vagylagosan

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>A forrásoldali Alkalmazásbeállítások Key Vault

Key Vault referenciák az [Alkalmazásbeállítások](configure-common.md#configure-app-settings)értékeiként használhatók, ami lehetővé teszi, hogy a titkokat a hely konfigurációja helyett Key Vault tartsa. Az Alkalmazásbeállítások biztonságosan titkosítva maradnak, de ha titkos felügyeleti képességekre van szüksége, a Key Vaultba kell lépniük.

Ha Key Vault hivatkozást szeretne használni egy alkalmazás-beállításhoz, állítsa a hivatkozást a beállítás értékeként. Az alkalmazás a szokásos módon hivatkozhat a titkos kulcsra. Nincs szükség kód módosítására.

> [!TIP]
> Key Vault hivatkozásokat használó legtöbb Alkalmazásbeállítások tárolóhely-beállításokként vannak megjelölve, mivel minden környezethez külön tárolókat kell megadni.

### <a name="azure-resource-manager-deployment"></a>Az Azure Resource Manager üzembe helyezése

Az erőforrás-telepítések Azure Resource Manager sablonokon keresztüli automatizálásakor előfordulhat, hogy a funkció működéséhez egy adott sorrendben kell sorba rendeznie a függőségeket. Fontos megjegyezni, hogy az alkalmazás beállításait saját erőforrásként kell meghatároznia ahelyett, hogy `siteConfig` tulajdonságot kellene használnia a hely definíciójában. Ennek az az oka, hogy a helyet először meg kell határozni, hogy a rendszer által hozzárendelt identitás létre legyen hozva, és a hozzáférési házirendben is használható legyen.

A psuedo-sablon például a következőhöz hasonló lehet:

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
> Ebben a példában a verziókövetés központi telepítése az alkalmazásbeállításoktől függ. Ez általában nem biztonságos viselkedés, mivel az Alkalmazásbeállítások frissítése aszinkron módon történik. Mivel azonban a `WEBSITE_ENABLE_SYNC_UPDATE_SITE` alkalmazás beállítása is megtörtént, a frissítés szinkronban van. Ez azt jelenti, hogy a verziókövetés központi telepítése csak akkor kezdődik el, ha az Alkalmazásbeállítások teljes mértékben frissültek.

## <a name="troubleshooting-key-vault-references"></a>Hibaelhárítási Key Vault referenciái

Ha egy hivatkozás nem oldható fel megfelelően, a rendszer a hivatkozási értéket fogja használni. Ez azt jelenti, hogy az Alkalmazásbeállítások esetében létrejön egy környezeti változó, amelynek értéke `@Microsoft.KeyVault(...)` szintaxisú. Ez azt eredményezheti, hogy az alkalmazás hibát jelez, mivel egy adott struktúra titkát várta.

Ez általában a [Key Vault hozzáférési házirend](#granting-your-app-access-to-key-vault)helytelen konfigurációja miatt fordul elő. Az is előfordulhat azonban, hogy egy titkos kulcs már nem létezik, vagy szintaktikai hiba történt a hivatkozásban.

Ha a szintaxis helyes, a hiba egyéb okait úgy tekintheti meg, ha ellenőrzi a jelenlegi feloldási állapotot egy beépített detektor használatával.

### <a name="using-the-detector-for-app-service"></a>A App Service detektorának használata

1. A portálon navigáljon az alkalmazáshoz.
2. Válassza **a diagnosztizálás és problémák megoldása**lehetőséget.
3. Válassza a **rendelkezésre állás és teljesítmény** lehetőséget, majd válassza a **webalkalmazás lehetőséget.**
4. Keresse meg **Key Vault az Alkalmazásbeállítások diagnosztikát** , és kattintson a **További információ**elemre.


### <a name="using-the-detector-for-azure-functions"></a>A Azure Functions detektorának használata

1. A portálon navigáljon az alkalmazáshoz.
2. Navigáljon a **platform szolgáltatásaihoz.**
3. Válassza **a diagnosztizálás és problémák megoldása**lehetőséget.
4. Válassza a **rendelkezésre állás és teljesítmény** lehetőséget, majd válassza a **Function app Down vagy a jelentéskészítési hibák lehetőséget.**
5. Kattintson **Key Vault alkalmazás-beállítások diagnosztika** elemre.
