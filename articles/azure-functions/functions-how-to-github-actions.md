---
title: Kódfrissítések et az Azure Functionsben a GitHub-műveletek használatával
description: Megtudhatja, hogyan használhatja a GitHub-műveleteket egy munkafolyamat definiálásához az Azure Functions-projektek létrehozásához és üzembe helyezéséhez a GitHubon.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: 54010269e5b61ebf28a29dd3165c4310f3472817
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878204"
---
# <a name="continuous-delivery-by-using-github-action"></a>Folyamatos kézbesítés a GitHub-művelet használatával

[A GitHub-műveletek](https://github.com/features/actions) segítségével definiálhatja a munkafolyamatot, amely automatikusan létrehozza és telepíti a függvénykódot az Azure-beli függvényalkalmazáshoz. 

A GitHub-műveletekben a [munkafolyamat](https://help.github.com/articles/about-github-actions#workflow) egy automatizált folyamat, amelyet a GitHub-tárházban határoz meg. Ez a folyamat megmondja a GitHubnak, hogyan hozhat létre és helyezhet üzembe a függvényalkalmazás-projektet a GitHubon. 

A munkafolyamatot egy YAML (.yml) fájl `/.github/workflows/` határozza meg a tárház elérési útján. Ez a definíció tartalmazza a munkafolyamatot kifutó különböző lépéseket és paramétereket. 

Az Azure Functions munkafolyamatok esetében a fájl három szakaszból ad választ: 

| Section | Feladatok |
| ------- | ----- |
| **Hitelesítés** | <ol><li>Egyszerű szolgáltatás definiálása.</li><li>Közzétételi profil letöltése.</li><li>Hozzon létre egy GitHub-titkot.</li></ol>|
| **Felépítés** | <ol><li>Állítsa be a környezetet.</li><li>A függvényalkalmazás létrehozása.</li></ol> |
| **Telepítés** | <ol><li>Telepítse a függvényalkalmazást.</li></ol>|

> [!NOTE]
> Nem kell egyszerű szolgáltatást létrehoznia, ha úgy dönt, hogy közzétételi profilt használ a hitelesítéshez.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az [Azure CLI-ben](/cli/azure/)található [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) paranccsal hozhat létre [egyszerű szolgáltatást.](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) Ezt a parancsot az [Azure Cloud Shell](https://shell.azure.com) használatával futtathatja az Azure Portalon, vagy a Try **it** gombra kattintva.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Ebben a példában cserélje le az erőforrás helyőrzőit az előfizetés-azonosítóra, az erőforráscsoportra és a függvényalkalmazás nevére. A kimenet a szerepkör-hozzárendelési hitelesítő adatok, amely hozzáférést biztosít a függvényalkalmazáshoz. Másolja ezt a JSON-objektumot, amellyel hitelesíthető a GitHubról.

> [!IMPORTANT]
> Mindig jó gyakorlat a minimális hozzáférés megadása. Ez az oka annak, hogy az előző példában szereplő hatókör az adott függvényalkalmazásra korlátozódik, és nem a teljes erőforráscsoportra.

## <a name="download-the-publishing-profile"></a>A közzétételi profil letöltése

A függvényalkalmazás közzétételi profilját az alkalmazás **Áttekintés lapján** a **Közzétételi profil letöltése**gombra kattintva töltheti le.

   ![Közzétételi profil letöltése](media/functions-how-to-github-actions/get-publish-profile.png)

Másolja a fájl tartalmát.

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

1. A [GitHubon](https://github.com)keresse meg a tárházat, válassza a **Beállítások** > **titkost ad** > hozzá egy új**titkot.**

   ![Titkos titok hozzáadása](media/functions-how-to-github-actions/add-secret.png)

1. Új titok hozzáadása.

   * Ha az Azure CLI használatával létrehozott egyszerű szolgáltatásnevet használja, használja `AZURE_CREDENTIALS` a **Nevet.** Ezután illessze be a másolt JSON-objektum kimenetét az **Érték beillesztéséhez,** és válassza **a Titkos fájl hozzáadása lehetőséget.**
   * Ha közzétételi profilt használ, `SCM_CREDENTIALS` használja a **Nevet.** Ezután használja a közzétételi profil fájltartalmát az **Érték**hez, és válassza **a Titkos hozzáadás lehetőséget.**

A GitHub most már hitelesíthető a függvényalkalmazásban az Azure-ban.

## <a name="set-up-the-environment"></a>A környezet beállítása 

A környezet beállítása egy nyelvspecifikus közzétételi beállítási művelettel történik.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A következő példa a munkafolyamat nak `actions/setup-node` azt a részét mutatja be, amely a műveletet használja a környezet beállításához:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

A következő példa a munkafolyamat nak `actions/setup-python` azt a részét mutatja be, amely a műveletet használja a környezet beállításához:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

A következő példa a munkafolyamat nak `actions/setup-dotnet` azt a részét mutatja be, amely a műveletet használja a környezet beállításához:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

A következő példa a munkafolyamat nak `actions/setup-java` azt a részét mutatja be, amely a műveletet használja a környezet beállításához:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>A függvényalkalmazás létrehozása

Ez az Azure Functions által támogatott nyelvtől és nyelvektől függ, ez a szakasz az egyes nyelvek szabványos buildlépései.

A következő példa a munkafolyamat nak azt a részét mutatja be, amely a függvényalkalmazást építi, amely nyelvspecifikus:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[C#](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>A függvényalkalmazás üzembe helyezése

A kód telepítése egy függvényalkalmazásba, a `Azure/functions-action` műveletet kell használnia. Ennek a műveletnek két paramétere van:

|Paraméter |Magyarázat  |
|---------|---------|
|**_alkalmazásneve_** | (Kötelező) A függvényalkalmazás neve. |
|_**bővítőhely neve**_ | (Nem kötelező) Annak a [központi telepítési helynek](functions-deployment-slots.md) a neve, amelybe telepíteni szeretné a központi telepítést. A tárolóhelyet már definiálni kell a függvényalkalmazásban. |


A következő példa a `functions-action`következő 1-es verziót használja:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>További lépések

A teljes munkafolyamat .yaml megtekintéséhez tekintse meg az egyik fájlt az Azure `functionapp` [GitHub-műveletek munkafolyamat-minták tárházban,](https://aka.ms/functions-actions-samples) amelyek a névben. Ezek a minták a munkafolyamat kiindulópontja.

> [!div class="nextstepaction"]
> [További információ a GitHub-műveletekről](https://help.github.com/en/articles/about-github-actions)
