---
title: Folyamatos integráció az Azure Pipelinesszal
description: Ismerje meg, hogyan hozhat létre, tesztelhet és helyezhet üzembe Azure Resource Manager-sablonokat.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b117861a528b6983876d28d5b343ea88c2bcadc0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260683"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Oktatóanyag: Az Azure Resource Manager-sablonok folyamatos integrációja az Azure-folyamatokkal

Ismerje meg, hogyan használhatja az Azure Pipelines segítségével az Azure Resource Manager-sablonprojektek folyamatos létrehozásához és üzembe helyezéséhez.

Az Azure DevOps fejlesztői szolgáltatásokat nyújt a csapatok számára a munka megtervezéséhez, a kódfejlesztésben való együttműködéshez, valamint az alkalmazások létrehozásához és telepítéséhez. A fejlesztők az Azure DevOps-szolgáltatások használatával dolgozhatnak a felhőben. Az Azure DevOps olyan integrált funkciókat biztosít, amelyeket a webböngészőn vagy az IDE-ügyfélen keresztül érhet el. Az Azure Pipeline az egyik ilyen funkció. Az Azure Pipelines egy teljes körűen kiemelt folyamatos integrációs (CI) és folyamatos kézbesítési (CD) szolgáltatás. Együttműködik a kívánt Git-szolgáltatóval, és a legtöbb főbb felhőszolgáltatásra telepíthető. Ezután automatizálhatja a kód összeállítását, tesztelését és telepítését a Microsoft Azure, a Google Cloud Platform vagy az Amazon Web Services szolgáltatásba.

Ez az oktatóanyag az Azure Resource Manager-sablonfejlesztők számára készült, akik új Azure DevOps-szolgáltatások és Azure-folyamatok. Ha már ismeri a GitHubot és a DevOps-ot, ugorjon [a Folyamat létrehozása elemre.](#create-a-pipeline)

> [!NOTE]
> Válasszon egy projektnevet. Amikor végigmegy az oktatóanyagon, cserélje le az **AzureRmPipeline-t** a projekt nevére.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * GitHub-adattár előkészítése
> * Azure DevOps-projekt létrehozása
> * Azure-folyamat létrehozása
> * A folyamat telepítésének ellenőrzése
> * A sablon frissítése és újratelepítés
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* **Egy GitHub-fiók,** ahol a sablonok tárházának létrehozásához használhatja. Ha még nem rendelkezik ilyen, [létrehozhat egyet ingyen](https://github.com). A GitHub-adattárak használatáról további információt a [GitHub-adattárak létrehozása című témakörben talál.](/azure/devops/pipelines/repos/github)
* **Telepítse a Git**- alkalmazást. Ez az oktatóanyag *git bash* vagy *git shell*. További információt a [Git telepítése című]( https://www.atlassian.com/git/tutorials/install-git)témakörben talál.
* **Egy Azure DevOps-szervezet.** Ha még nem rendelkezik ilyen, létrehozhat egyet ingyen. Lásd: [Szervezet vagy projektgyűjtemény létrehozása]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Visual Studio CodeResource Manager Tools bővítménnyel. Az [Azure Resource Manager-sablonok létrehozásához lásd: Visual Studio-kód használata.](use-vs-code-to-create-template.md)

## <a name="prepare-a-github-repository"></a>GitHub-adattár előkészítése

A GitHub a projekt forráskódjának tárolására szolgál, beleértve az Erőforrás-kezelő sablonokat is. Más támogatott adattárak, lásd: [az Azure DevOps által támogatott adattárak.](/azure/devops/pipelines/repos/?view=azure-devops)

### <a name="create-a-github-repository"></a>GitHub-tárház létrehozása

Ha nem rendelkezik GitHub-fiókkal, olvassa el az Előfeltételek című [témakört.](#prerequisites)

1. Jelentkezzen be a [GitHubra.](https://github.com)
2. Válassza ki a fiókképét a jobb felső sarokban, majd válassza **a Tárolók lehetőséget**.

    ![Az Azure DevOps Azure-folyamatok az Azure Resource Manager ben github-tárházat hoznak létre](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Válassza **az Új**lehetőséget, egy zöld gombot.
1. A **tárház neve**mezőbe írja be a tárház nevét.  Például **az AzureRmPipeline-repo.** Ne felejtse el lecserélni az **AzureRmPipeline** bármelyikét a projekt nevére. Az oktatóanyag hoz hatja át a **nyilvános** vagy **a privát** lehetőséget. Ezután válassza **a Tárház létrehozása**lehetőséget.
1. Írja le az URL-t. A tárház URL-címe a következő formátum:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Ezt az adattárat *távoli tárháznak nevezzük.* Ugyanannak a projektnek a fejlesztői klónozhatják saját *helyi tárházát,* és egyesíthetik a módosításokat a távoli tárházzal.

### <a name="clone-the-remote-repository"></a>A távoli tárház klónozása

1. Nyissa meg a Git Shell vagy a Git Bash.  Lásd: [Előfeltételek](#prerequisites).
1. Ellenőrizze, hogy az aktuális mappa **github.**
1. Futtassa az alábbi parancsot:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Cserélje le **a [YourAccountName]** nevet a GitHub-fiók nevére, és cserélje le **a [YourGitHubRepositoryName]** nevet az előző eljárásban létrehozott tárháznevére.

    A következő képernyőkép egy példát mutat be.

    ![Az Azure DevOps Azure-folyamatok az Azure Resource Manager-ben github-bash-t hoznak létre](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

A **CreateAzureStorage** mappa az a mappa, amelyben a sablon tárolják. A **pwd** parancs a mappa elérési útját mutatja. Az elérési út az alábbi eljárás ban a sablon mentésére szolgál.

### <a name="download-a-quickstart-template"></a>Rövid útmutató sablon letöltése

Sablon létrehozása helyett letöltheti a [rövid útmutató sablont.]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) Ez a sablon létrehoz egy Azure Storage-fiókot.

1. Nyissa meg a Visual Studio-kódot. Lásd: [Előfeltételek](#prerequisites).
2. Nyissa meg a sablont a következő URL-címmel:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Mentse a fájlt **azuredeploy.json** néven a **CreateAzureStorage** mappába. A rendszer a mappanevet és a fájlnevet is a folyamat folyamatában használja.  Ha módosítja ezeket a neveket, frissítenie kell a folyamatban használt neveket.

### <a name="push-the-template-to-the-remote-repository"></a>A sablon leküldése a távoli tárházba

Az azuredeploy.json hozzá lett adva a helyi tárházhoz. Ezután töltse fel a sablont a távoli tárházba.

1. Nyissa meg *a Git Shell* vagy a Git Bash ( *Git Bash*) megnyitását, ha nincs megnyitva.
1. Módosítsa a könyvtárat a CreateAzureStorage mappába a helyi tárházban.
1. Ellenőrizze, hogy az **azuredeploy.json** fájl a mappában van-e.
1. Futtassa az alábbi parancsot:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Lehet, hogy kapsz egy figyelmeztetést lf-ről. Figyelmen kívül hagyhatja a figyelmeztetést. **mester** a fő ág.  Általában minden frissítéshez létrehoz egy ágat. Az oktatóanyag egyszerűsítése érdekében közvetlenül használja a főágat.
1. Tallózással keresse meg a GitHub-tárházat egy böngészőből.  Az URL-cím ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. A **CreateAzureStorage** mappa és az **Azuredeploy.json** a mappában jelenik meg.

Eddig létrehozott egy GitHub-tárházat, és feltöltött egy sablont a tárházba.

## <a name="create-a-devops-project"></a>DevOps-projekt létrehozása

A DevOps-szervezet szükséges, mielőtt a következő eljárás.  Ha még nem rendelkezik ilyen, olvassa el az Előfeltételek című [témakört.](#prerequisites)

1. Jelentkezzen be az [Azure DevOps szolgáltatásba.](https://dev.azure.com)
1. Válasszon ki egy DevOps-szervezetet a bal oldalon.

    ![Az Azure DevOps Azure Pipelines azure-erőforrás-kezelő létrehozása Azure DevOps-projekt](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Válassza a **Create project** (Projekt létrehozása) lehetőséget. Ha nincs enek projektje, a projekt létrehozása lap automatikusan megnyílik.
1. Írja be a következő értékeket:

    * **Projekt neve**: adjon meg egy projektnevet. Használhatja a projekt nevét választotta a legelején az oktatóanyag.
    * **Verziókövetés**: Válassza **a Git**lehetőséget. Előfordulhat, hogy a **Verziókövetés**megtekintéséhez ki kell **bontania** a Speciális elemet.

    Használja a többi tulajdonság alapértelmezett értékét.
1. Válassza a **Create project** (Projekt létrehozása) lehetőséget.

Hozzon létre egy szolgáltatáskapcsolatot, amely a projektek azure-beli üzembe helyezéséhez szolgál.

1. Válassza a Bal oldali menü Aljáról a **Projekt beállításai** lehetőséget.
1. Válassza a **Szolgáltatáskapcsolatok lehetőséget** **a Folyamatok csoportban.**
1. Válassza **az Új szolgáltatás kapcsolat**lehetőséget, majd az **AzureResourceManager**lehetőséget.
1. Írja be a következő értékeket:

    * **Kapcsolat neve**: adja meg a kapcsolat nevét. Például **Az AzureRmPipeline-conn**. Írja le ezt a nevet, szüksége van a névre a folyamat létrehozásakor.
    * **Hatókör szint**: válassza **az Előfizetés**lehetőséget.
    * **Előfizetés**: válassza ki az előfizetést.
    * **Erőforráscsoport**: Hagyja üresen.
    * **A kapcsolat használatának engedélyezése az összes folyamat számára.** (kiválasztva)
1. Válassza **az OK gombot.**

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Eddig a következő feladatokat végezte el.  Ha kihagyja az előző szakaszokat, mert ismeri a GitHubot és a DevOps-t, a folytatás előtt el kell végeznie a feladatokat.

- Hozzon létre egy GitHub-tárházat, és mentse ezt a [sablont](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) a **CreateAzureStorage** mappába a tárházban.
- Hozzon létre egy DevOps-projektet, és hozzon létre egy Azure Resource Manager-szolgáltatáskapcsolatot.

Sablon üzembe helyezésének lépésével rendelkező folyamat létrehozása:

1. Válassza a bal oldali menü **Folyamatok parancsát.**
1. Válassza az **Új folyamat lehetőséget.**
1. Válassza a **GitHub** lehetőséget a **Connect** (Csatlakozás) lapon. Ha a rendszer kéri, adja meg a GitHub hitelesítő adatait, majd kövesse az utasításokat. Ha a következő képernyő látható, válassza a **Csak a tárat választó**lehetőséget, és ellenőrizze, hogy a tárház szerepel-e a listában, mielőtt & a Telepítés **jóváhagyása**lehetőséget választaná.

    ![Az Azure Resource Manager Azure DevOps Azure-folyamatok csak az adattárakat választják ki](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. A **Kijelölés** lapon válassza ki a tárházat.  Az alapértelmezett név **a[YourAccountName]/[YourGitHubRepositoryName]** név.
1. A **Konfigurálás** lapon válassza a **Kezdő folyamat lehetőséget.** Az **azure-pipelines.yml** folyamatfájlt két parancsfájllépéssel jeleníti meg.
1. Cserélje le a **lépések** szakaszt a következő YAML-re:

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

    Úgy kell kinéznie, mint:

    ![Az Azure Resource Manager Azure DevOps Azure-folyamatok](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Hajtsa végre a következő módosításokat:

    * **deploymentScope**: Válassza ki a központi `Management Group` `Subscription` telepítés `Resource Group`hatókörét a következő lehetőségek közül: , és . Használja **az erőforráscsoportot** ebben az oktatóanyagban. A hatókörökről a [Telepítési hatókörök című témakörben olvashat bővebben.](deploy-rest.md#deployment-scope)
    * **ConnectedServiceName**: Adja meg a korábban létrehozott szolgáltatáskapcsolat nevét.
    * **SubscriptionName**: Adja meg a cél-előfizetés azonosítóját.
    * **művelet**: Az **Erőforráscsoport létrehozása vagy frissítése** művelet 2 műveletet tesz le- 1. erőforráscsoport létrehozása, ha új erőforráscsoport név van megadva; 2. a megadott sablon telepítése.
    * **resourceGroupName**: adjon meg egy új erőforráscsoport nevet. Például **Az AzureRmPipeline-rg**.
    * **hely**: adja meg az erőforráscsoport helyét.
    * **templateLocation**: ha **csatolt műtermék** van megadva, a feladat a sablonfájlt közvetlenül a csatlakoztatott tárházból keresi.
    * **a csmFile** a sablonfájl elérési útja. Nem kell sablonparaméter-fájlt megadnia, mert a sablonban definiált összes paraméter alapértelmezett értékkel rendelkezik.

    A feladatról további információt az [Azure Resource Group Deployment task](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)és az Azure Resource Manager sablontelepítési feladat című témakörben [talál.](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. Kattintson a **Mentés és futtatás** elemre.
1. Válassza a **Mentés gombot, és futtassa** újra. A YAML-fájl egy példánya a csatlakoztatott tárházba kerül. A YAML-fájlt a tárház tallózásával láthatja.
1. Ellenőrizze, hogy a folyamat végrehajtása sikeresen megtörtént-e.

    ![Az Azure Resource Manager Azure DevOps Azure-folyamatok](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az erőforráscsoportot. A név az, amit a folyamat YAML-fájljában megadott.  Egy létrehozott tárfiókot fog látni.  A tárfiók neve **a tárolóval**kezdődik.
1. Válassza ki a tárfiók nevét a megnyitásához.
1. Válassza **a Tulajdonságok lehetőséget.** Figyelje meg, hogy a **replikáció** **helyileg redundáns tárolás (LRS).**

    ![Azure DevOps Azure-folyamatok portáljának ellenőrzése az Azure Resource Manager-ben](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Frissítés és újratelepítés

Amikor frissíti a sablont, és leküldéses a módosításokat a távoli tárházba, a folyamat automatikusan frissíti az erőforrásokat, a tárfiókot ebben az esetben.

1. Nyissa **meg az azuredeploy.json** webhelyet a Visual Studio-kód helyi tárházából.
1. Frissítse a **storageAccountType** **defaultValue értékét** **a Standard_GRS**értékre. Tekintse meg a következő képernyőképet:

    ![Az Azure Resource Manager Azure DevOps Azure-folyamatok frissítése yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Mentse a módosításokat.
1. A módosítások leküldése a távoli tárházba a Git Bash/Shell következő parancsainak futtatásával.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Az első parancs szinkronizálja a helyi tárházat a távoli tárházzal. Ne feledje, hogy a folyamat YAML-fájl hozzá lett adva a távoli tárházhoz.

    A távoli tárház főága frissül, a folyamat újra aktiválódik.

A módosítások ellenőrzéséhez ellenőrizheti a tárfiók Replikáció tulajdonságát.  Lásd: [A telepítés ellenőrzése](#verify-the-deployment).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

Előfordulhat, hogy törölni szeretné a GitHub-tárházat és az Azure DevOps-projektet is.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban hozzon létre egy Azure DevOps-folyamatot egy Azure Resource Manager-sablon üzembe helyezéséhez. Annak megismeréséhez, hogyan lehet Azure-erőforrásokat több régióban üzembe helyezni, és hogyan alkalmazhat biztonságos üzembehelyezési gyakorlatokat, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Biztonságos üzembehelyezési gyakorlatok](./deployment-manager-tutorial.md)
