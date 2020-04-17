---
title: Kulcstartó-hivatkozások használata
description: Megtudhatja, hogyan állíthatja be az Azure App Service-t és az Azure Functionst az Azure Key Vault-hivatkozások használatához. Tegye elérhetővé a Key Vault titkos kulcsait az alkalmazáskód számára.
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: dd0a03ea76d517486bb9bda6d9628fb529166dd8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453727"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Key Vault-hivatkozások használata az App Service és az Azure Functions szolgáltatáshoz

Ez a témakör bemutatja, hogyan dolgozhat az Azure Key Vault titkos kulcsaival az App Service-ben vagy az Azure Functions alkalmazásban anélkül, hogy kódmódosításokat kellene végrehajtania. [Az Azure Key Vault](../key-vault/general/overview.md) egy olyan szolgáltatás, amely központosított titkos kulcsok kezelését biztosítja, teljes hozzáférés-házirendek és naplózási előzmények teljes körű vezérlésével.

## <a name="granting-your-app-access-to-key-vault"></a>Az alkalmazás hozzáférése a Key Vaulthoz

Annak érdekében, hogy olvassa el a titkos kulcsokat a Key Vault, létre kell hoznia egy trezort, és adja meg az alkalmazás számára, hogy hozzáférjen.

1. Hozzon létre egy key vault ot a [Key Vault rövid útmutatójának követésével.](../key-vault/secrets/quick-create-cli.md)

1. Hozzon létre egy [rendszer által hozzárendelt felügyelt identitást](overview-managed-identity.md) az alkalmazáshoz.

   > [!NOTE] 
   > A Key Vault-hivatkozások jelenleg csak a rendszer által hozzárendelt felügyelt identitásokat támogatják. A felhasználó által hozzárendelt identitások nem használhatók.

1. Hozzon létre egy hozzáférési szabályzatot a [Key Vaultban](../key-vault/general/secure-your-key-vault.md#key-vault-access-policies) a korábban létrehozott alkalmazásidentitáshoz. Engedélyezze a "Get" titkos engedélyt a házirendhez. Ne konfigurálja az "engedélyezett `applicationId` alkalmazást" vagy beállításokat, mivel ez nem kompatibilis a felügyelt identitással.

    > [!NOTE]
    > A Key Vault-hivatkozások jelenleg nem képesek feloldani a hálózati korlátozásokkal rendelkező kulcstartóban tárolt titkos [kulcsokat.](../key-vault/general/overview-vnet-service-endpoints.md)

## <a name="reference-syntax"></a>Hivatkozási szintaxis

A Key Vault-hivatkozás `@Microsoft.KeyVault({referenceString})`az `{referenceString}` űrlap, ahol a következő lehetőségek egyike lép:

> [!div class="mx-tdBreakAll"]
> | Hivatkozási karakterlánc                                                            | Leírás                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_secretUri_                                                       | A **SecretUri** a Key Vault egy titkos kulcsának teljes adatsík-URI-jának kell lennie, beleértve egy verziót is, pl.https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_; SecretName=_secretName_; SecretVersion=_secretVersion_ | A **VaultName** kell a key vault-erőforrás nevét. A **titkos névnek** a céltitok nevének kell lennie. A **SecretVersion** kell a titkos kulcsot használni. |

A verzióval való teljes hivatkozás például a következőkre mutat:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
Alternatív megoldás:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>A forrásalkalmazás beállításai a Key Vaultból

Key Vault-hivatkozások az [alkalmazásbeállítások](configure-common.md#configure-app-settings)értékeként használhatók, így a webhely konfigurációja helyett a Key Vaultban is tarthatja a titkos kulcsokat. Az alkalmazásbeállítások biztonságosan titkosítva vannak intve, de ha titkos felügyeleti képességekre van szüksége, akkor a Key Vaultba kell menniük.

Ha egy alkalmazásbeállításhoz key vault-hivatkozást szeretne használni, állítsa be a hivatkozást a beállítás értékeként. Az alkalmazás a szokásos módon hivatkozhat a titkos kulcsra. Nincs szükség kódmódosításra.

> [!TIP]
> A legtöbb key vault-hivatkozást használó alkalmazásbeállításokat helyfoglalási beállításokként kell megjelölni, mivel minden környezethez külön tárolókkal kell rendelkeznie.

### <a name="azure-resource-manager-deployment"></a>Az Azure Resource Manager üzembe helyezése

Erőforrás-központi telepítések automatizálása az Azure Resource Manager-sablonok, előfordulhat, hogy a függőségek sorrendbe egy adott sorrendben, hogy ez a funkció működik. Fontos megjegyezni, hogy az alkalmazás beállításait saját erőforrásként kell `siteConfig` megadnia, nem pedig egy tulajdonságot kell használnia a webhelydefinícióban. Ennek az az oka, hogy a helyet először meg kell határozni, hogy a rendszeráltal hozzárendelt identitás vele jön létre, és használható legyen a hozzáférési házirendben.

Egy függvényalkalmazás psuedo-sablonja például a következőkre mutathatja ki:

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
> Ebben a példában a forrásvezérlő központi telepítése az alkalmazás beállításaitól függ. Ez általában nem biztonságos viselkedés, mivel az alkalmazásbeállítás frissítése aszinkron módon viselkedik. Mivel azonban az `WEBSITE_ENABLE_SYNC_UPDATE_SITE` alkalmazás beállítást is belefoglaltuk, a frissítés szinkron. Ez azt jelenti, hogy a forrásvezérlő központi telepítése csak akkor kezdődik meg, ha az alkalmazás beállításait teljes mértékben frissítették.

## <a name="troubleshooting-key-vault-references"></a>A kulcstartóhivatkozásokkal kapcsolatos hibák elhárítása

Ha a hivatkozás feloldása nem megfelelő, a függvény a referenciaértéket használja. Ez azt jelenti, hogy az alkalmazásbeállításokhoz létrejön `@Microsoft.KeyVault(...)` egy olyan környezeti változó, amelynek értéke a szintaxis. Ez azt eredményezheti, hogy az alkalmazás hibákat dob, mivel egy bizonyos struktúra titkára számított.

Ez leggyakrabban a [Key Vault hozzáférési házirend](#granting-your-app-access-to-key-vault)helytelen konfigurációjának köszönhető. Ennek oka azonban az is lehet, hogy egy titkos titok már nem létezik, vagy egy szintaktikai hiba a hivatkozás ban is.

Ha a szintaxis helyes, a portálaktuális felbontási állapotának ellenőrzésével megtekintheti a hiba egyéb okait. Nyissa meg az Alkalmazás beállításait, és válassza a "Szerkesztés" lehetőséget a kérdéses hivatkozáshoz. A beállítási konfiguráció alatt meg kell jelennie az állapotinformációknak, beleértve a hibákat is. Ezek hiánya azt jelenti, hogy a hivatkozási szintaxis érvénytelen.

A beépített érzékelők egyikével további információkat is kaphat.

### <a name="using-the-detector-for-app-service"></a>Az alkalmazásszolgáltatás érzékelőjének használata

1. A portálon keresse meg az alkalmazást.
2. Válassza a **Problémák diagnosztizálása és megoldása** lehetőséget.
3. Válassza **az Elérhetőség és teljesítmény lehetőséget,** majd a **Webalkalmazás lefelé lehetőséget.**
4. Keresse meg **a Key Vault alkalmazásbeállítások diagnosztikát,** és kattintson a **További információ gombra.**


### <a name="using-the-detector-for-azure-functions"></a>Az Azure Functions érzékelőjének használata

1. A portálon keresse meg az alkalmazást.
2. Keresse meg a **Platform funkcióit.**
3. Válassza a **Problémák diagnosztizálása és megoldása** lehetőséget.
4. Válassza **az Elérhetőség és teljesítmény lehetőséget,** majd válassza a **Függvényalkalmazás le vagy jelentési hibák lehetőséget.**
5. Kattintson a **Key Vault alkalmazásbeállítások diagnosztikájára.**
