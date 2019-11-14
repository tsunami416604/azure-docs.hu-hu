---
title: A GitHub-műveletek használata a Azure Functions a kód frissítéseinek elvégzéséhez
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe Azure Functions-projekteket a GitHubon a GitHub-műveletek használatával.
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: 8decf4622c4043cbac0f2e2f6e2ad59f1b5baaae
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074015"
---
# <a name="continuous-delivery-by-using-github-action"></a>Folyamatos teljesítés a GitHub-művelet használatával

A [GitHub-műveletek](https://github.com/features/actions) lehetővé teszi, hogy Definiáljon egy munkafolyamatot a functions kód automatikus létrehozásához és üzembe helyezéséhez az Azure-ban. 

A GitHub-műveletekben a [munkafolyamat](https://help.github.com/articles/about-github-actions#workflow) egy automatizált folyamat, amelyet a GitHub-tárházban határozhat meg. Ezzel a folyamattal megtudhatja, hogyan hozhat létre és helyezhet üzembe functions-projekteket a GitHubon. 

A munkafolyamatot egy YAML-fájl (. YML) határozza meg a tárház `/.github/workflows/` útvonalán. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza. 

Azure Functions munkafolyamathoz a fájl három szakaszt tartalmaz: 

| Section | Feladatok |
| ------- | ----- |
| **Hitelesítés** | <ol><li>Adjon meg egy egyszerű szolgáltatásnevet.</li><li>Közzétételi profil letöltése.</li><li>Hozzon létre egy GitHub-titkot.</li></ol>|
| **Build** | <ol><li>Állítsa be a környezetet.</li><li>Hozza létre a Function alkalmazást.</li></ol> |
| **Üzembe helyezés** | <ol><li>Telepítse a Function alkalmazást.</li></ol>|

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

A functionapp közzétételi profiljának letöltéséhez nyissa meg az alkalmazás **Áttekintés** lapját, és kattintson a **közzétételi profil beolvasása**lehetőségre.

   ![Közzétételi profil letöltése](media/functions-how-to-github-actions/get-publish-profile.png)

Másolja a fájl tartalmát.

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

1. A [githubon](https://github.com)tallózzon a tárházban, válassza a **beállítások** > **titkok** > **új titok hozzáadása**lehetőséget.

   ![Titkos kód hozzáadása](media/functions-how-to-github-actions/add-secret.png)

1. Ha ezt követően a **titok hozzáadása**lehetőséget **választja, használja**a `AZURE_CREDENTIALS` **nevet** és a másolt parancs kimenetét. Ha közzétételi profilt használ, használja a `SCM_CREDENTIALS` **nevet** és a fájl tartalmát az **értékhez**.

A GitHub mostantól képes hitelesíteni az Azure-beli Function-alkalmazást.

## <a name="set-up-the-environment"></a>A környezet beállítása 

A környezet beállítása a közzétételi telepítési műveletek egyikével végezhető el.

|Nyelv | Telepítési művelet |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**    | `actions/setup-java` |
|**JavaScript**     | `actions/setup-node` |
|**Python**   | `actions/setup-python` |

Az alábbi példák a munkafolyamatnak azt a részét mutatják be, amely a környezetet a különböző támogatott nyelvekhez állítja be:

**JavaScript**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

**Python**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-function-app"></a>A Function alkalmazás összeállítása

Ez a Azure Functions által támogatott nyelvtől és nyelvtől függ. a szakasznak az egyes nyelvek standard Build lépéseinek kell lennie.

Az alábbi példák a Function alkalmazást a különböző támogatott nyelveken felépítő munkafolyamat részét mutatják be:

**JavaScript**

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

**Python**

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

**.NET**

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

**Java**

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

## <a name="deploy-the-function-app"></a>A függvényalkalmazás üzembe helyezése

A kód egy Function alkalmazásban való üzembe helyezéséhez a `Azure/functions-action` műveletet kell használnia. Ennek a műveletnek két paramétere van:

|Paraméter |Magyarázat  |
|---------|---------|
|**_alkalmazás neve_** | Kötelező A függvény alkalmazásának neve. |
|_**tárolóhely neve**_ | Választható Annak a [telepítési tárolóhelynek](functions-deployment-slots.md) a neve, amelyre telepíteni kívánja a-t. A tárolóhelynek már definiálva kell lennie a Function alkalmazásban. |


Az alábbi példa a `functions-action`1. verzióját használja:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>További lépések

Egy teljes munkafolyamat megtekintéséhez tekintse meg az [Azure GitHub-műveletek munkafolyamat-minták](https://github.com/Azure/actions-workflow-samples) tárházában található egyik fájlt, amely `functionapp` szerepel a névben. Ezek a minták kiindulási pontként használhatók a munkafolyamathoz.

> [!div class="nextstepaction"]
> [További információ a GitHub-műveletekről](https://help.github.com/en/articles/about-github-actions)
