---
title: A GitHub-műveletek használata a Azure Functions a kód frissítéseinek elvégzéséhez
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe Azure Functions-projekteket a GitHubon a GitHub-műveletek használatával.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: cshoe
ms.custom: tracking-python
ms.openlocfilehash: 1a7cc37f297f902fb5de473303f1dc260cbea9ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84559094"
---
# <a name="continuous-delivery-by-using-github-action"></a>Folyamatos teljesítés a GitHub-művelet használatával

A [GitHub-műveletek](https://github.com/features/actions) lehetővé teszi, hogy Definiáljon egy munkafolyamatot a functions kód automatikus létrehozásához és üzembe helyezéséhez az Azure-ban. 

A GitHub-műveletekben a [munkafolyamat](https://help.github.com/articles/about-github-actions#workflow) egy automatizált folyamat, amelyet a GitHub-tárházban határozhat meg. Ezzel a folyamattal megtudhatja, hogyan hozhat létre és helyezhet üzembe functions-projekteket a GitHubon. 

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza. 

Azure Functions munkafolyamathoz a fájl három szakaszt tartalmaz: 

| Section | Feladatok |
| ------- | ----- |
| **Hitelesítés** | <ol><li>Adjon meg egy egyszerű szolgáltatásnevet.</li><li>Közzétételi profil letöltése.</li><li>Hozzon létre egy GitHub-titkot.</li></ol>|
| **Felépítés** | <ol><li>Állítsa be a környezetet.</li><li>Hozza létre a Function alkalmazást.</li></ol> |
| **Telepítés** | <ol><li>Telepítse a Function alkalmazást.</li></ol>|

> [!NOTE]
> Nem kell létrehoznia egy egyszerű szolgáltatásnevet, ha úgy dönt, hogy közzétételi profilt használ a hitelesítéshez.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

[Egyszerű szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) létrehozásához használja az az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot az [Azure CLI](/cli/azure/)-ben. Ezt a parancsot a Azure Portal [Azure Cloud Shell](https://shell.azure.com) használatával vagy a **kipróbálás** gombra kattintva futtathatja.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Ebben a példában az erőforrásban található helyőrzőket cserélje le az előfizetés-AZONOSÍTÓra, az erőforráscsoport és a Function alkalmazás nevére. A kimenet az a szerepkör-hozzárendelési hitelesítő adatok, amelyek hozzáférést biztosítanak a Function alkalmazáshoz. Másolja ezt a JSON-objektumot, amelyet a GitHubról történő hitelesítéshez használhat.

> [!IMPORTANT]
> Mindig jó gyakorlat a minimális hozzáférés megadására. Ezért az előző példában szereplő hatókör az adott Function alkalmazásra korlátozódik, nem a teljes erőforráscsoporthoz.

## <a name="download-the-publishing-profile"></a>A közzétételi profil letöltése

A Function alkalmazás közzétételi profiljának letöltése:

1. Válassza ki a Function alkalmazás **Áttekintés** lapját, majd válassza a **közzétételi profil beolvasása**elemet.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Közzétételi profil letöltése":::

1. Mentse és másolja a közzétételi beállítások fájljának tartalmát.

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

1. A [githubon](https://github.com)keresse meg a tárházat, és válassza a **Beállítások**  >  **titkok**  >  **új titok hozzáadása**elemet.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Titkos kód hozzáadása":::

1. Adjon hozzá egy új titkot.

   * Ha az Azure CLI használatával létrehozott egyszerű szolgáltatást használja, a `AZURE_CREDENTIALS` **nevet**adja meg. Ezután illessze be a vágólapra másolt JSON-objektum kimenetét az **érték**mezőbe, majd válassza a **titkos kód hozzáadása**elemet.
   * Ha közzétételi profilt használ, használja `SCM_CREDENTIALS` a **nevet**. Ezután használja a közzétételi profil fájljának tartalmát az **Érték mezőben**, majd válassza a **titkos kód hozzáadása**elemet.

A GitHub mostantól képes hitelesíteni az Azure-beli Function-alkalmazást.

## <a name="set-up-the-environment"></a>A környezet beállítása 

A környezet beállítása a nyelvspecifikus közzétételi beállítás használatával történik.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa bemutatja a munkafolyamat azon részét, amely a műveletet használja a `actions/setup-node` környezet beállításához:

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

Az alábbi példa bemutatja a munkafolyamat azon részét, amely a műveletet használja a `actions/setup-python` környezet beállításához:

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

Az alábbi példa bemutatja a munkafolyamat azon részét, amely a műveletet használja a `actions/setup-dotnet` környezet beállításához:

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

Az alábbi példa bemutatja a munkafolyamat azon részét, amely a műveletet használja a `actions/setup-java` környezet beállításához:

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

## <a name="build-the-function-app"></a>A Function alkalmazás összeállítása

Ez a Azure Functions által támogatott nyelvtől és nyelvtől függ. a szakasznak az egyes nyelvek standard Build lépéseinek kell lennie.

Az alábbi példa bemutatja a munkafolyamat azon részét, amely létrehozza a Function alkalmazást, amely a nyelvspecifikus:

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

A kód a Function alkalmazásban való üzembe helyezéséhez a műveletet kell használnia `Azure/functions-action` . Ennek a műveletnek két paramétere van:

|Paraméter |Magyarázat  |
|---------|---------|
|**_alkalmazás neve_** | Kötelező A függvény alkalmazásának neve. |
|_**tárolóhely neve**_ | Választható Annak a [telepítési tárolóhelynek](functions-deployment-slots.md) a neve, amelyre telepíteni kívánja a-t. A tárolóhelynek már definiálva kell lennie a Function alkalmazásban. |


Az alábbi példa a következők 1. verzióját használja `functions-action` :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>További lépések

A teljes munkafolyamat. YAML fájl megtekintéséhez tekintse meg az [Azure GitHub-műveletek munkafolyamat-minták](https://aka.ms/functions-actions-samples) tárházában található, a névvel rendelkező fájlok egyikét `functionapp` . Ezek a minták kiindulási pontként használhatók a munkafolyamathoz.

> [!div class="nextstepaction"]
> [További információ a GitHub-műveletekről](https://help.github.com/en/articles/about-github-actions)
