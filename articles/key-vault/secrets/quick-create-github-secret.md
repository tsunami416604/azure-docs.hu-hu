---
title: Rövid útmutató – Azure Key Vault titkok használata a GitHub-műveletek munkafolyamataiban
description: Key Vault Secrets használata a GitHub-műveletekben a szoftverfejlesztői munkafolyamatok automatizálásához
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 9509f84b14a42180189a529282b5db348deab279
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920283"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>Key Vault Secrets használata a GitHub-műveletek munkafolyamataiban

A [GitHub-műveletekben](https://help.github.com/en/articles/about-github-actions) Key Vault titkokat használhat, és biztonságosan tárolhat jelszavakat és egyéb titkokat egy Azure Key vaultban. További információ a [Key Vaultról](../general/overview.md). 

A Key Vault-és GitHub-műveletekkel a központosított titkokat kezelő eszköz és a GitHub-műveletek minden előnyét élvezheti. A GitHub-műveletek a GitHub szolgáltatásainak egyik csomagja, amely automatizálja a szoftverfejlesztői munkafolyamatokat. A munkafolyamatokat ugyanazon a helyen helyezheti üzembe, ahol a kódot tárolja, és együttműködik a lekéréses kérelmekkel és problémákkal. 


## <a name="prerequisites"></a>Előfeltételek 
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, regisztráljon [ingyenesen](https://github.com/join).  
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy GitHub-tárházhoz csatlakoztatott Azure-alkalmazás. Ez a példa a [tárolók üzembe helyezését használja a Azure app Service](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01). 
- Egy Azure Key Vault.  Azure Key Vault az Azure Portal, az Azure CLI vagy a Azure PowerShell használatával hozhat létre.

## <a name="workflow-file-overview"></a>A munkafolyamat-fájl áttekintése

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

A fájl a GitHub-műveletekkel való hitelesítéshez két szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Adjon meg egy szolgáltatásnevet. <br /> 2. hozzon létre egy GitHub-titkot. <br /> 3. szerepkör-hozzárendelés hozzáadása. |
|**Key Vault** | 1. adja hozzá a Key Vault-műveletet. <br /> 2. a Key Vault titkos kulcsa. |

## <a name="authentication"></a>Hitelesítés

Az [Azure CLI](/cli/azure/)-ben létrehozhat egy [egyszerű szolgáltatást](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) az az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) parancs használatával. Futtassa ezt a parancsot [Azure Cloud Shell](https://shell.azure.com/) a Azure Portalban, vagy kattintson a **TRY IT (kipróbálás** ) gombra.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

A fenti példában cserélje le a helyőrzőket az előfizetés-AZONOSÍTÓra és az erőforráscsoport nevére. Cserélje le a helyőrzőt az `myApp` alkalmazás nevére. A kimenet egy JSON-objektum, amely a szerepkör-hozzárendelés hitelesítő adatait tartalmazza, amelyek hozzáférést biztosítanak a App Service alkalmazáshoz az alábbihoz hasonló módon. A JSON-objektum másolása később. Csak a `clientId` , `clientSecret` , `subscriptionId` és `tenantId` értékekkel rendelkező szakaszt kell megadnia. 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

Hozzon létre titkos kódokat az Azure-beli hitelesítő adataihoz, erőforráscsoporthoz és előfizetéséhez. 

1. A [githubon](https://github.com/)tallózzon a tárházban.

1. Válassza a **beállítások > titkok > új titok** lehetőséget.

1. Illessze be a teljes JSON-kimenetet az Azure CLI-parancsból a titok érték mezőjébe. Adja meg a titkot a nevet `AZURE_CREDENTIALS` .

1. Másolja a értéket a `clientId` későbbi használatra. 

### <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása 
 
Hozzáférést kell biztosítania az Azure-szolgáltatáshoz, hogy el tudja érni a kulcstartót a `get` és a `list` műveletekhez. Ha ezt nem teszi meg, akkor nem fogja tudni használni az egyszerű szolgáltatásnevet. 

Cserélje le a értéket a Key Vault nevére, a helyére pedig a `keyVaultName` `clientIdGUID` értékét `clientId` . 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>Az Azure Key Vault művelet használata

Az [Azure Key Vault művelettel](https://github.com/marketplace/actions/azure-key-vault-get-secrets)egy vagy több titkot is beolvashat egy Azure Key Vault-példányból, és felhasználhatja azt a GitHub-művelet munkafolyamataiban.

A beolvasott titkos kódok kimenetként, valamint környezeti változókként vannak beállítva. A változók automatikusan maszkolásra kerülnek, amikor kinyomtatják őket a konzolra vagy a naplókba.

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

Ha kulcstartót szeretne használni a munkafolyamatban, akkor a Key Vault műveletre is szüksége lesz, és hivatkozni kell erre a műveletre. 

Ebben a példában a Key Vault neve `containervault` . A rendszer két Key Vault-titkot ad hozzá a környezethez a Key Vault művelettel – `containerPassword` és `containerUsername` . 

A Key Vault-értékeket a rendszer később a Docker bejelentkezési feladatában hivatkozik az előtaggal `steps.myGetSecretAction.outputs` . 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az Azure-alkalmazást, a GitHub-tárházat és a Key vaultot már nem igénylik, törölje az üzembe helyezett erőforrásokat az alkalmazás, a GitHub-adattár és a Key Vault erőforráscsoport törlésével.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Azure Key Vault](../general/overview.md)
