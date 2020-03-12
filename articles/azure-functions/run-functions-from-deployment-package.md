---
title: Azure Functions futtatása csomagból
description: A Azure Functions futtatókörnyezettel futtassa a függvényeket a Function app-projektfájlok fájljait tartalmazó központi telepítési csomagfájl csatlakoztatásával.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: 3ae287939f22469b03f0e10f184f067274464905
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087025"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Azure Functions futtatása csomagfájl

Az Azure-ban a függvényeket közvetlenül egy központi telepítési csomagból is futtathatja a Function alkalmazásban. A másik lehetőség, hogy telepíti a fájlokat a Function alkalmazás `d:\home\site\wwwroot` könyvtárába.

Ez a cikk a függvények csomagból való futtatásának előnyeit ismerteti. Azt is bemutatja, hogyan engedélyezheti ezt a funkciót a Function alkalmazásban.

> [!IMPORTANT]
> Ha egy [prémium](functions-scale.md#premium-plan)szintű csomagban telepíti a függvényeket egy Linux-függvény alkalmazásba, mindig a csomagfájl alapján kell futtatnia, és [az alkalmazást a Azure functions Core Tools használatával kell közzétennie](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>A csomagfájl futtatásának előnyei
  
A csomagfájl számos előnnyel jár:

+ Csökkenti a fájlmásolás-zárolási problémák kockázatát.
+ Üzembe helyezhető egy éles alkalmazásban (újraindítással).
+ Biztos lehet benne, hogy az alkalmazásban futó fájlok vannak.
+ Javítja Azure Resource Manager üzemelő [példányok](functions-infrastructure-as-code.md)teljesítményét.
+ Csökkentheti a hideg kezdési időpontokat, különösen a JavaScript-függvények esetében nagyméretű NPM-csomagokkal.

További információkért tekintse meg [ezt a bejelentést](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>A függvények csomagból való futtatásának engedélyezése

Ha engedélyezni szeretné, hogy a Function app egy csomagból fusson, egyszerűen vegyen fel egy `WEBSITE_RUN_FROM_PACKAGE` beállítást a Function app-beállításokhoz. A `WEBSITE_RUN_FROM_PACKAGE` beállítás értéke a következő értékek egyike lehet:

| Érték  | Leírás  |
|---------|---------|
| **`1`**  | A Windows rendszeren futó Function apps esetében ajánlott. A Function alkalmazás `d:\home\data\SitePackages` mappájából futtassa a csomagot. Ha nem [telepíti a zip-telepítést](#integration-with-zip-deployment), ez a beállítás azt igényli, hogy a mappának rendelkeznie kell egy `packagename.txt`nevű fájllal is. Ez a fájl csak a mappában lévő csomagfájl nevét tartalmazza szóköz nélkül. |
|**`<URL>`**  | A futtatni kívánt csomag adott fájljának helye. A blob Storage használatakor egy [megosztott hozzáférési aláírással (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) rendelkező privát tárolót kell használnia, hogy a functions futtatókörnyezet hozzáférhessen a csomaghoz. A [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) használatával tölthet fel csomagokat a blob Storage-fiókjába. URL-cím megadásakor az [eseményindítókat is szinkronizálnia](functions-deployment-technologies.md#trigger-syncing) kell, miután közzétett egy frissített csomagot. |

> [!CAUTION]
> Ha Windows rendszeren futtat egy Function alkalmazást, a külső URL-cím beállításával rosszabb az indítási teljesítmény. Ha a Function alkalmazást a Windows rendszerre telepíti, akkor `WEBSITE_RUN_FROM_PACKAGE`t kell beállítania, hogy `1` és a közzététel a zip-telepítéssel történjen.

Az alábbi példa egy, az Azure Blob Storage-ban üzemeltetett. zip-fájlból való futtatásra konfigurált Function-alkalmazást mutat be:

![Alkalmazás-beállítás WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Jelenleg csak a. zip csomagfájl támogatottak.

## <a name="integration-with-zip-deployment"></a>Integráció a zip-telepítéssel

A [zip-telepítés][Zip deployment for Azure Functions] a Azure app Service egyik funkciója, amely lehetővé teszi a Function app-projekt üzembe helyezését a `wwwroot` könyvtárba. A projekt. zip telepítési fájlként van csomagolva. Ugyanazokat az API-kat használhatja a csomag `d:\home\data\SitePackages` mappába való telepítéséhez. A `1``WEBSITE_RUN_FROM_PACKAGE` Alkalmazásbeállítások értékével a zip telepítési API-k a `d:\home\data\SitePackages` mappába másolják a csomagot ahelyett, hogy a fájlokat a `d:\home\site\wwwroot`ba kicsomagoljuk. Emellett létrehozza a `packagename.txt` fájlt is. Újraindítás után a csomag írásvédett fájlrendszerként van csatlakoztatva a `wwwroot`hoz. További információ a zip-telepítésről: [a Azure functions zip központi telepítése](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Az WEBSITE_RUN_FROM_PACKAGE-beállítás hozzáadása

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="use-key-vault-references"></a>Key Vault referenciák használata

A további biztonság érdekében Key Vault hivatkozásokat is használhat a külső URL-címmel együtt. Ez megtartja az URL-címek titkosítását, és lehetővé teszi, hogy kihasználja Key Vault a titkos felügyelet és a forgás számára. Az Azure Blob Storage használata ajánlott, így egyszerűen elforgathatja a társított SAS-kulcsot. Az Azure Blob Storage inaktív állapotban van, így az alkalmazás adatai biztonságban maradnak, amikor nincs telepítve App Service.

1. Hozzon létre egy Azure Key Vault.

    ```azurecli
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus
    ```

1. Adja hozzá a külső URL-címet Key Vault-titokként.

    ```azurecli
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<insert-your-URL>"
    ```

1. Hozza létre a `WEBSITE_RUN_FROM_PACKAGE` alkalmazás beállítását, és állítsa be az értéket Key Vault hivatkozásként a külső URL-címre.

    ```azurecli
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"
    ```

További információt a következő cikkekben talál.

- [App Service Key Vault referenciái](../app-service/app-service-key-vault-references.md)
- [Azure Storage-titkosítás a REST-adatokhoz](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Hibakeresés

- A csomagból való futtatás `wwwroot` írásvédett, így hibaüzenetet kap, amikor fájlokat ír a könyvtárba.
- A tar és a gzip formátum nem támogatott.
- Ez a funkció nem a helyi gyorsítótárral együtt működik.
- A jobb hidegindító teljesítmény érdekében használja a helyi zip-beállítást (`WEBSITE_RUN_FROM_PACKAGE`= 1).
- A Futtatás a csomagból nem kompatibilis a telepítés testreszabási beállításával (`SCM_DO_BUILD_DURING_DEPLOYMENT=true`), a rendszer figyelmen kívül hagyja a Build lépést az üzembe helyezés során.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
