---
title: Folyamatos integráció az Azure Pipelinesszal
description: Megtudhatja, hogyan hozhat létre, tesztelheti és telepítheti Azure Resource Manager-sablonokat.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 457d2a8868283eed2e211ff26ce77156eee68e01
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326411"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Oktatóanyag: Azure Resource Manager-sablonok folyamatos integrálása az Azure-folyamatokkal

Ismerje meg, hogyan hozhat létre és helyezhet üzembe Azure Resource Manager sablon-projekteket az Azure-folyamatok használatával.

Az Azure DevOps fejlesztői szolgáltatásokat biztosít a csapatok számára a munka megtervezéséhez, a programkódok fejlesztéséhez való együttműködéshez, valamint alkalmazások létrehozásához és üzembe helyezéséhez. A fejlesztők az Azure DevOps Services használatával dolgozhatnak a felhőben. Az Azure DevOps a webböngészőn vagy az IDE-ügyfélen keresztül elérő szolgáltatások integrált készletét nyújtja. Az Azure-folyamat ezen funkciók egyike. Az Azure-folyamatok egy teljes funkcionalitású folyamatos integrációs (CI) és folyamatos kézbesítési (CD) szolgáltatás. Az előnyben részesített git-szolgáltatóval működik, és a legtöbb jelentős felhőalapú szolgáltatásban üzembe helyezhető. Ezután automatizálhatja a kód kiépítését, tesztelését és üzembe helyezését Microsoft Azure, Google Cloud Platform vagy Amazon Web Services.

Ez az oktatóanyag olyan Azure Resource Manager sablon-fejlesztőknek készült, akik új Azure DevOps-szolgáltatásokat és Azure-folyamatokat használnak. Ha már ismeri a GitHubot és a DevOps-t, ugorjon a [folyamat létrehozása](#create-a-pipeline)lehetőségre.

> [!NOTE]
> Válasszon egy projekt nevét. Ha átugorja az oktatóanyagot, cserélje le bármelyik **AzureRmPipeline** a projekt nevével.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * GitHub-adattár előkészítése
> * Azure DevOps-projekt létrehozása
> * Azure-folyamat létrehozása
> * A folyamat központi telepítésének ellenőrzése
> * A sablon frissítése és újbóli üzembe helyezése
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* **Egy GitHub-fiók**, amelyben felhasználhatja a sablonok tárházának létrehozását. Ha még nem rendelkezik ilyennel, [létrehozhat egyet ingyen](https://github.com). További információ a GitHub-adattárak használatáról: [GitHub-adattárak létrehozása](/azure/devops/pipelines/repos/github).
* **Telepítse a Git szoftvert**. Ez az oktatóanyag-utasítás a *git bash* vagy a *git Shell*használatát ismerteti. Útmutatásért lásd: a [git telepítése]( https://www.atlassian.com/git/tutorials/install-git).
* **Egy Azure DevOps-szervezet**. Ha még nem rendelkezik ilyennel, létrehozhat egyet ingyen. Lásd: [szervezet vagy projekt gyűjtemény létrehozása]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Visual Studio Code a Resource Manager-eszközök bővítménnyel. További információ: [Azure Resource Manager sablonok létrehozása a Visual Studio Code használatával](./resource-manager-tools-vs-code.md).

## <a name="prepare-a-github-repository"></a>GitHub-adattár előkészítése

A GitHub a projekt forráskódjának tárolására szolgál, beleértve a Resource Manager-sablonokat is. Más támogatott adattárak esetében lásd: az [Azure DevOps által támogatott adattárak](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>GitHub-adattár létrehozása

Ha nem rendelkezik GitHub-fiókkal, tekintse meg az [Előfeltételek](#prerequisites)című témakört.

1. Jelentkezzen be a [githubba](https://github.com).
2. Válassza ki a fiókját a jobb felső sarokban, majd válassza ki **a tárházat**.

    ![Azure-DevOps Azure-folyamatok Azure Resource Manager létrehozása GitHub-tárház létrehozásához](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Válassza az **új**, zöld gomb lehetőséget.
1. A **tárház neve**mezőbe írja be az adattár nevét.  Például: **AzureRmPipeline-repo**. Ne felejtse el lecserélni bármelyik **AzureRmPipeline** a projekt nevével. Az oktatóanyagban a **nyilvános** vagy a **magánjellegű** lehetőség közül választhat. Majd válassza a **Tárház létrehozása**lehetőséget.
1. Írja le az URL-címet. Az adattár URL-címe a következő:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Ezt a tárházat *távoli tárháznak*nevezzük. Ugyanannak a projektnek minden fejlesztője klónozott saját *helyi tárházát*, és egyesítheti a változtatásokat a távoli tárházban.

### <a name="clone-the-remote-repository"></a>A távoli tárház klónozása

1. Nyissa meg a git-rendszerhéj vagy a git bash felületet.  Lásd: [Előfeltételek](#prerequisites).
1. Ellenőrizze, hogy az aktuális mappa a **GitHub**-e.
1. Futtassa az alábbi parancsot:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Cserélje le a **[YourAccountName]** nevet a GitHub-fiókja nevére, és cserélje le a **[YourGitHubRepositoryName]** helyére az előző eljárásban létrehozott adattár-névvel.

    Az alábbi képernyőképen egy példa látható.

    ![Azure DevOps Azure-folyamatok Azure Resource Manager létrehozása GitHub bash](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

A **CreateAzureStorage** mappa a sablon tárolására szolgáló mappa. A **pwd** parancs a mappa elérési útját jeleníti meg. Az elérési út a sablon mentése a következő eljárásba.

### <a name="download-a-quickstart-template"></a>Gyorsindítás sablon letöltése

Sablon létrehozása helyett egy gyors üzembe helyezési [sablon]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)is letölthető. Ez a sablon létrehoz egy Azure Storage-fiókot.

1. Nyissa meg a Visual Studio Code-ot. Lásd: [Előfeltételek](#prerequisites).
2. Nyissa meg a sablont a következő URL-címmel:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Mentse a fájlt **azuredeploy. JSON** néven a **CreateAzureStorage** mappába. A rendszer a mappa nevét és nevét is használja, mivel azok a folyamatban vannak.  Ha megváltoztatja ezeket a neveket, frissítenie kell a folyamatban használt neveket.

### <a name="push-the-template-to-the-remote-repository"></a>A sablon leküldése a távoli tárházba

A azuredeploy. JSON hozzá lett adva a helyi tárházhoz. Ezután töltse fel a sablont a távoli tárházba.

1. Ha nincs megnyitva, nyissa meg a *git-rendszerhéj* vagy a *git bash*eszközt.
1. Módosítsa a könyvtárat a helyi tárház CreateAzureStorage mappájába.
1. Ellenőrizze, hogy a **azuredeploy. JSON** fájl a mappában található-e.
1. Futtassa az alábbi parancsot:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Előfordulhat, hogy figyelmeztetést kap a LF-ről. Figyelmen kívül hagyhatja a figyelmeztetést. a **Master** a Master ág.  Általában létre kell hoznia egy ágat az egyes frissítésekhez. Az oktatóanyag leegyszerűsítése érdekében közvetlenül a Master ágat használja.
1. Tallózással keresse meg a GitHub-tárházat egy böngészőben.  Az URL-cím **https://github.com/[YourAccountName]/[YourGitHubRepository]** . A mappában a **CreateAzureStorage** mappát és a **Azuredeploy. JSON** fájlt kell látnia.

Eddig létrehozott egy GitHub-tárházat, és feltöltött egy sablont az adattárba.

## <a name="create-a-devops-project"></a>DevOps-projekt létrehozása

A következő eljárás folytatásához szükség van egy DevOps-szervezetre.  Ha még nem rendelkezik ilyennel, tekintse meg az [Előfeltételek](#prerequisites)című témakört.

1. Jelentkezzen be az [Azure DevOps](https://dev.azure.com)-ba.
1. Válasszon ki egy DevOps-szervezetet a bal oldalon.

    ![Azure DevOps-projekt létrehozása Azure-DevOps Azure-folyamatokkal Azure Resource Manager](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Válassza a **Create project** (Projekt létrehozása) lehetőséget. Ha nem rendelkezik projektekkel, a projekt létrehozása lap automatikusan megnyílik.
1. Írja be a következő értékeket:

    * **Projekt neve**: adja meg a projekt nevét. Az oktatóanyag legelején a projekt nevét is használhatja.
    * **Verziókövetés**: válassza a **git**lehetőséget. Előfordulhat, hogy a **speciális** elemre kell bontania a **verziókövetés**megjelenítéséhez.

    Használja az alapértelmezett értéket a többi tulajdonsághoz.
1. Válassza a **Create project** (Projekt létrehozása) lehetőséget.

Hozzon létre egy olyan szolgáltatási kapcsolódást, amely a projektek Azure-ba való telepítésére szolgál.

1. A bal oldali menü alján válassza a **projekt beállításai** lehetőséget.
1. Válassza a **szolgáltatási kapcsolatok** lehetőséget a **folyamatok**alatt.
1. Válassza az **új szolgáltatás-kapcsolatok**lehetőséget, majd válassza a **AzureResourceManager**lehetőséget.
1. Írja be a következő értékeket:

    * **Kapcsolatok neve**: adjon meg egy nevet. Például: **AzureRmPipeline-Conn**. Jegyezze fel ezt a nevet, a folyamat létrehozásakor szüksége lesz a névre.
    * **Hatóköri szint**: válassza az **előfizetés**lehetőséget.
    * **Előfizetés**: válassza ki az előfizetését.
    * **Erőforráscsoport**: hagyja üresen.
    * A **Kapcsolódás használatának engedélyezése az összes folyamat számára**. kiválasztott
1. Kattintson az **OK** gombra.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Eddig a következő feladatokat végezte el.  Ha kihagyja az előző szakaszt, mert már ismeri a GitHubot és a DevOps-t, a folytatás előtt végre kell hajtania a feladatokat.

- Hozzon létre egy GitHub-tárházat, és mentse a [sablont](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) az adattár **CreateAzureStorage** mappájába.
- Hozzon létre egy DevOps-projektet, és hozzon létre egy Azure Resource Manager szolgáltatás-összekötőt.

Folyamat létrehozása lépéssel a sablon üzembe helyezéséhez:

1. Válassza ki a **folyamatokat** a bal oldali menüben.
1. Válassza az **új folyamat**lehetőséget.
1. A **kapcsolat** lapon válassza a **GitHub**lehetőséget. Ha a rendszer kéri, adja meg a GitHub hitelesítő adatait, majd kövesse az utasításokat. Ha a következő képernyő jelenik meg, válassza a **csak adattárak lehetőséget**, és ellenőrizze, hogy az adattár szerepel-e a listában, mielőtt kiválasztja a **jóváhagyás & a telepítés**gombra.

    ![Azure Resource Manager Azure DevOps Azure-folyamatok csak adattárakat választanak](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. A **Select (kiválasztás** ) lapon válassza ki a tárházat.  Az alapértelmezett név: **[YourAccountName]/[YourGitHubRepositoryName]** .
1. A **configure (Konfigurálás** ) lapon válassza a **kezdő folyamat**elemet. Megjeleníti a **Azure-pipelines. YML** folyamat fájlját két parancsfájl lépéssel.
1. Cserélje le a **Steps** szakaszt a következő YAML:

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    A következőhöz hasonlóan néz ki:

    ![Azure DevOps Azure-YAML Azure Resource Manager](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Hajtsa végre a következő módosításokat:

    * **deloymentScope**: válassza ki az üzembe helyezés hatókörét a következő lehetőségek közül: `Management Group`, `Subscription` és `Resource Group`. Ebben az oktatóanyagban használja az **erőforráscsoportot** . További információ a hatókörökről: [telepítési hatókörök](./resource-group-template-deploy-rest.md#deployment-scope).
    * **ConnectedServiceName**: adja meg a korábban létrehozott szolgáltatási kapcsolatok nevét.
    * **SubscriptionName**: a cél ELŐfizetési azonosítójának meghatározása.
    * **művelet**: az **erőforráscsoport létrehozása vagy frissítése** művelet 2 műveletet hajt végre – 1. hozzon létre egy erőforráscsoportot, ha új erőforráscsoport-nevet ad meg; 2. a megadott sablon üzembe helyezése.
    * **resourceGroupName**: adjon meg egy új erőforráscsoport-nevet. Például: **AzureRmPipeline-RG**.
    * **hely**: határozza meg az erőforráscsoport helyét.
    * **templateLocation**: Ha a **csatolt** összetevő meg van adva, a feladat közvetlenül a csatlakoztatott tárházból keresi a sablonfájlt.
    * a **csmFile** a sablonfájl elérési útja. Nem kell megadnia a sablon paramétereit tartalmazó fájlt, mert a sablonban definiált összes paraméter alapértelmezett értékkel rendelkezik.

    További információ a feladatról: az [Azure-erőforráscsoport üzembe helyezési feladata](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)és [Azure Resource Manager sablon központi telepítési feladata](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. Kattintson a **Mentés és futtatás** elemre.
1. Válassza **a Mentés és Futtatás** újra lehetőséget. A rendszer menti a YAML fájl egy példányát a csatlakoztatott adattárba. A YAML-fájlt a tárház tallózásával érheti el.
1. Ellenőrizze, hogy a folyamat végrehajtása sikeresen megtörtént-e.

    ![Azure DevOps Azure-YAML Azure Resource Manager](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Nyissa meg az erőforráscsoportot. A nevet a folyamat YAML fájlban megadott név határozza meg.  Ekkor létrejön egy Storage-fiók.  A Storage-fiók neve a **tárolóval**kezdődik.
1. Válassza ki a Storage-fiók nevét a megnyitásához.
1. Válassza ki a **Tulajdonságok** elemet. Figyelje meg, hogy a **replikáció** **helyileg redundáns tárolást (LRS)** .

    ![Azure Resource Manager Azure DevOps Azure-folyamatok portáljának ellenőrzése](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Frissítés és újbóli üzembe helyezés

Amikor frissíti a sablont, és leküldi a módosításokat a távoli tárházba, a folyamat automatikusan frissíti az erőforrásokat, a Storage-fiókot ebben az esetben.

1. Nyissa meg a **azuredeploy. JSON** fájlt a helyi adattárból a Visual Studio Code-ban.
1. Frissítse a **Tárfióktípus** **defaultValue** értékét **Standard_GRSra**. Tekintse meg a következő képernyőképet:

    ![Azure-DevOps Azure-YAML frissítésének Azure Resource Manager](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Mentse a módosításokat.
1. A következő parancsok a git bash/shellből való futtatásával küldje el a módosításokat a távoli adattárba.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Az első parancs szinkronizálja a helyi tárházat a távoli adattárral. Ne feledje, hogy a folyamat YAML-fájlja hozzá lett adva a távoli tárházhoz.

    A távoli tárház főágának frissítése után a folyamat újra bekerül.

A módosítások ellenőrzéséhez ellenőrizze a Storage-fiók replikáció tulajdonságát.  Lásd: [a központi telepítés ellenőrzése](#verify-the-deployment).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

Előfordulhat, hogy törölni szeretné a GitHub-tárházat és az Azure DevOps-projektet is.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehoz egy Azure DevOps-folyamatot egy Azure Resource Manager-sablon üzembe helyezéséhez. Annak megismeréséhez, hogyan lehet Azure-erőforrásokat több régióban üzembe helyezni, és hogyan alkalmazhat biztonságos üzembehelyezési gyakorlatokat, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Biztonságos üzembehelyezési gyakorlatok](./deployment-manager-tutorial.md)
