---
title: Resource Manager-sablonok üzembe helyezése GitHub-műveletek használatával
description: Leírja, hogyan lehet Azure Resource Manager sablonokat a GitHub-műveletek használatával telepíteni.
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 5fe147a9c42e83d5e644b0c08dfa67de88ec05c0
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82875193"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Azure Resource Manager sablonok üzembe helyezése GitHub-műveletek használatával

A [GitHub-műveletek](https://help.github.com/en/actions) lehetővé teszik, hogy egyéni szoftverfejlesztési életciklus-munkafolyamatokat hozzon létre közvetlenül a GitHub-tárházban, ahol a Azure Resource Manager-(ARM-) sablonokat tárolja. A [munkafolyamatot](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) egy YAML-fájl határozza meg. A munkafolyamatok egy vagy több olyan feladattal rendelkeznek, amely az egyes feladatok végrehajtásához szükséges lépéseket tartalmazza. A lépések parancsokat futtathatnak vagy műveleteket is használhatnak. Létrehozhat saját műveleteket, vagy használhatja a [GitHub-Közösség](https://github.com/marketplace?type=actions) által megosztott műveleteket, és igény szerint testre szabhatja azokat. Ez a cikk bemutatja, hogyan használható az [Azure CLI művelet](https://github.com/marketplace/actions/azure-cli-action) a Resource Manager-sablonok üzembe helyezéséhez.

Az Azure CLI-művelet két függő művelettel rendelkezik:

- **[Pénztár](https://github.com/marketplace/actions/checkout)**: Tekintse meg a tárházat, hogy a munkafolyamat hozzáférhessen a megadott Resource Manager-sablonokhoz.
- **[Azure-bejelentkezés](https://github.com/marketplace/actions/azure-login)**: Jelentkezzen be az Azure-beli hitelesítő adataival

A Resource Manager-sablonok üzembe helyezésének alapszintű munkafolyamata három lépésből állhat:

1. Tekintse meg a sablon fájlját.
2. Jelentkezzen be az Azure-ba.
3. Resource Manager-sablon üzembe helyezése

## <a name="prerequisites"></a>Előfeltételek

A Resource Manager-sablonok és a munkafolyamat-fájlok tárolásához GitHub-tárházra van szükség. A létrehozáshoz tekintse meg az [új Tárház létrehozása](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository)című témakört.

## <a name="configure-deployment-credentials"></a>Üzembe helyezési hitelesítő adatok konfigurálása

Az Azure login művelet egy egyszerű szolgáltatást használ az Azure-ban való hitelesítéshez. A CI/CD-munkafolyamatok rendszerbiztonsági tagjának általában a beépített közreműködő jogosultságra van szüksége az Azure-erőforrások üzembe helyezése érdekében.

A következő Azure CLI-szkript bemutatja, hogyan lehet egy Azure-erőforráscsoport közreműködői engedélyekkel rendelkező Azure-beli szolgáltatását előállítani. Ez az erőforráscsoport, ahol a munkafolyamat telepíti a Resource Manager-sablonban definiált erőforrásokat.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

**$ProjectName** és **$Location** értékének testreszabása a parancsfájlban. Az erőforráscsoport neve a projekt neve **RG** hozzáfűzéssel. Meg kell adnia az erőforráscsoport nevét a munkafolyamatban.

A szkript a következőhöz hasonló JSON-objektumot ad eredményül:

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

Másolja a JSON-kimenetet, és tárolja GitHub-titokként a GitHub-tárházban. Ha még nem rendelkezik adattárral, tekintse meg az [előfeltételt](#prerequisites) .

1. A GitHub-adattárból válassza a **Beállítások** lapot.
1. A bal oldali menüben válassza a **titok** lehetőséget.
1. Írja be a következő értékeket:

    - **Név**: AZURE_CREDENTIALS
    - **Érték**: (a JSON kimenetének beillesztése)
1. Válassza a **titkos kód hozzáadása**elemet.

Meg kell adnia a titok nevét a munkafolyamatban.

## <a name="add-resource-manager-template"></a>Resource Manager-sablon hozzáadása

Vegyen fel egy Resource Manager-sablont a GitHub-tárházba. Ha még nem rendelkezik ilyennel, használhatja a következő sablont. A sablon létrehoz egy Storage-fiókot.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

A fájlt bárhová is elhelyezheti a tárházban. A következő szakaszban szereplő munkafolyamat-minta azt feltételezi, hogy a sablonfájl neve **azuredeploy. JSON**, és a rendszer a tárház gyökerében található **sablonok** nevű mappában tárolja.

## <a name="create-workflow"></a>Munkafolyamat létrehozása

A munkafolyamat-fájlt a tárház gyökerében található **. GitHub/munkafolyamat** mappában kell tárolni. A munkafolyamat-fájlkiterjesztés lehet **. YML** vagy **. YAML**.

Létrehozhat egy munkafolyamat-fájlt, majd leküldheti vagy feltöltheti a fájlt a tárházba, vagy használhatja a következő eljárást:

1. A GitHub-adattárból válassza a felső menü **műveletek** elemét.
1. Válassza az **Új munkafolyamat**lehetőséget.
1. Válassza **a munkafolyamat beállítása saját maga**lehetőséget.
1. Nevezze át a munkafolyamat-fájlt, ha a **Main. YML**helyett más nevet szeretne adni. Például: **deployStorageAccount. YML**.
1. Cserélje le a YML fájl tartalmát a következőre:

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    A munkafolyamat-fájl három szakaszt tartalmaz:

    - **Name (név**): a munkafolyamat neve.
    - **bekapcsolva**: a munkafolyamatot kiváltó GitHub-események neve. A munkafolyamat akkor aktiválódik, ha van egy leküldéses esemény a főágra, amely módosítja a két megadott fájl legalább egyikét. A két fájl a munkafolyamat-fájl és a sablonfájl.

        > [!IMPORTANT]
        > Ellenőrizze, hogy a két fájl és az elérési útjai megfelelnek-e az Ön számára.
    - **feladatok**: egy munkafolyamat-Futtatás egy vagy több feladatból áll. Csak egy **üzembe helyezés – Storage-Account-template**nevű feladattípus található.  A feladatokhoz három lépés szükséges:

        - A **pénztár forráskódja**.
        - **Jelentkezzen be az Azure-** ba.

            > [!IMPORTANT]
            > Ellenőrizze, hogy a titkos név megegyezik-e a tárházba mentett névvel. Lásd: [telepítési hitelesítő adatok konfigurálása](#configure-deployment-credentials).
        - **ARM-sablon üzembe helyezése**. Cserélje le a **resourceGroupName**értékét.  Ha az Azure CLI parancsfájlt használta az [üzembe helyezési hitelesítő adatok konfigurálása](#configure-deployment-credentials)során, a generált erőforráscsoport neve a projekt neve, **RG** hozzáfűzéssel. Ellenőrizze a **templateLocation**értékét.

1. Válassza a **véglegesítés indítása**lehetőséget.
1. Válassza **a végrehajtás közvetlenül a Master ágat**lehetőséget.
1. Válassza az **új fájl véglegesítés** (vagy a **módosítások elvégzése**) lehetőséget.

Mivel a munkafolyamatot úgy konfigurálták, hogy a munkafolyamat-fájl vagy a frissítendő sablonfájl aktiválja, a munkafolyamat a módosítások véglegesítése után azonnal elindul.

## <a name="check-workflow-status"></a>Munkafolyamat állapotának megtekintése

1. Válassza a **műveletek** fület. Ekkor megjelenik egy **create deployStorageAccount. YML** munkafolyamat. A munkafolyamat végrehajtása 1-2 percet vesz igénybe.
1. Válassza ki a munkafolyamatot a megnyitásához.
1. Válassza a bal oldali menüben a **telepítés – Storage – fiók – sablon** (feladatnév) elemet. A feladatok neve a munkafolyamatban van definiálva.
1. Válassza ki az **ARM-sablon üzembe helyezése** (lépés neve) elemet a kibontásához. Az REST API választ láthatja.

## <a name="next-steps"></a>További lépések

A sablonok létrehozásának folyamatát ismertető lépésenkénti oktatóanyagért lásd [: oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](template-tutorial-create-first-template.md).
