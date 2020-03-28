---
title: Folyamatos integráció az Azure Pipelinesszal
description: Ismerje meg, hogyan hozhat létre, tesztelhet és helyezhet üzembe Azure Resource Manager-sablonokat.
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 303edac9221b2a58986129388e33c84c3817d96e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369374"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Oktatóanyag: Az Azure Resource Manager-sablonok folyamatos integrációja az Azure-folyamatokkal

Az [előző oktatóanyagban](./deployment-tutorial-linked-template.md)egy csatolt sablont telepít.  Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Pipelines segítségével az Azure Resource Manager-sablonprojektek folyamatos létrehozásához és üzembe helyezéséhez.

Az Azure DevOps fejlesztői szolgáltatásokat nyújt a csapatok számára a munka megtervezéséhez, a kódfejlesztésben való együttműködéshez, valamint az alkalmazások létrehozásához és telepítéséhez. A fejlesztők az Azure DevOps-szolgáltatások használatával dolgozhatnak a felhőben. Az Azure DevOps olyan integrált funkciókat biztosít, amelyeket a webböngészőn vagy az IDE-ügyfélen keresztül érhet el. Az Azure Pipeline az egyik ilyen funkció. Az Azure Pipelines egy teljes körűen kiemelt folyamatos integrációs (CI) és folyamatos kézbesítési (CD) szolgáltatás. Együttműködik a kívánt Git-szolgáltatóval, és a legtöbb főbb felhőszolgáltatásra telepíthető. Ezután automatizálhatja a kód összeállítását, tesztelését és telepítését a Microsoft Azure, a Google Cloud Platform vagy az Amazon Web Services szolgáltatásba.

> [!NOTE]
> Válasszon egy projektnevet. Amikor végigmegy az oktatóanyagon, cserélje le az **AzureRmPipeline-t** a projekt nevére.
> Ez a projektnév erőforrásnevek létrehozására szolgál.  Az egyik erőforrás egy tárfiók. A tárfióknevek nek 3 és 24 karakter közötti hosszúságúnak kell lenniük, és csak számokat és kisbetűket használhatnak. A névnek egyedinek kell lennie. A sablonban a tárfiók neve a projekt neve az "store" csatolt, és a projekt neve 3 és 11 karakter között kell lennie. Így a projekt nevének meg kell felelnie a tárfiók nevére vonatkozó követelményeknek, és kevesebb, mint 11 karakterből áll.

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
* (nem kötelező) **Visual Studio-kód a Resource Manager Tools bővítménylel.** Az [Azure Resource Manager-sablonok létrehozásához lásd: Visual Studio-kód használata.](use-vs-code-to-create-template.md)

## <a name="prepare-a-github-repository"></a>GitHub-adattár előkészítése

A GitHub a projekt forráskódjának tárolására szolgál, beleértve az Erőforrás-kezelő sablonokat is. Más támogatott adattárak, lásd: [az Azure DevOps által támogatott adattárak.](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types)

### <a name="create-a-github-repository"></a>GitHub-tárház létrehozása

Ha nem rendelkezik GitHub-fiókkal, olvassa el az Előfeltételek című [témakört.](#prerequisites)

1. Jelentkezzen be a [GitHubra.](https://github.com)
1. Válassza ki a fiókképét a jobb felső sarokban, majd válassza **a Tárolók lehetőséget**.

    ![Az Azure DevOps Azure-folyamatok az Azure Resource Manager ben github-tárházat hoznak létre](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Válassza **az Új**lehetőséget, egy zöld gombot.
1. A **tárház neve**mezőbe írja be a tárház nevét.  Például **az AzureRmPipeline-repo.** Ne felejtse el lecserélni az **AzureRmPipeline** bármelyikét a projekt nevére. Az oktatóanyag hoz hatja át a **nyilvános** vagy **a privát** lehetőséget. Ezután válassza **a Tárház létrehozása**lehetőséget.
1. Írja le az URL-t. A tárház URL-címe a következő formátum:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Ezt az adattárat *távoli tárháznak nevezzük.* Ugyanannak a projektnek a fejlesztői klónozhatják saját *helyi tárházát,* és egyesíthetik a módosításokat a távoli tárházzal.

### <a name="clone-the-remote-repository"></a>A távoli tárház klónozása

1. Nyissa meg a Git Shell vagy a Git Bash.  Lásd: [Előfeltételek](#prerequisites).
1. Ellenőrizze, hogy az aktuális mappa **a GitHub.**
1. Futtassa az alábbi parancsot:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Cserélje le **a [YourAccountName]** nevet a GitHub-fiók nevére, és cserélje le **a [YourGitHubRepositoryName]** nevet az előző eljárásban létrehozott tárháznevére.

A **CreateWebApp** mappa az a mappa, amelyben a sablon tárolják. A **pwd** parancs a mappa elérési útját mutatja. Az elérési út az alábbi eljárás ban a sablon mentésére szolgál.

### <a name="download-a-quickstart-template"></a>Rövid útmutató sablon letöltése

A sablonok létrehozása helyett letöltheti a sablonokat, és mentheti őket a **CreateWebApp** mappába.

* A fő sablon:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* A csatolt sablon:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

A rendszer a mappanevet és a fájlneveket is a folyamat folyamatában használja.  Ha módosítja ezeket a neveket, frissítenie kell a folyamatban használt neveket.

### <a name="push-the-template-to-the-remote-repository"></a>A sablon leküldése a távoli tárházba

Az azuredeploy.json hozzá lett adva a helyi tárházhoz. Ezután töltse fel a sablont a távoli tárházba.

1. Nyissa meg *a Git Shell* vagy a Git Bash ( *Git Bash*) megnyitását, ha nincs megnyitva.
1. Módosítsa a könyvtárat a CreateWebApp mappára a helyi tárházban.
1. Ellenőrizze, hogy az **azuredeploy.json** fájl a mappában van-e.
1. Futtassa az alábbi parancsot:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    Lehet, hogy kapsz egy figyelmeztetést lf-ről. Figyelmen kívül hagyhatja a figyelmeztetést. **mester** a fő ág.  Általában minden frissítéshez létrehoz egy ágat. Az oktatóanyag egyszerűsítése érdekében közvetlenül használja a főágat.
1. Tallózással keresse meg a GitHub-tárházat egy böngészőből.  Az URL-cím ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. Megjelenik a **CreateWebApp** mappa és a mappában lévő három fájl.
1. A sablon megnyitásához válassza a **linkedStorageAccount.json lehetőséget.**
1. Válassza a **Raw** gombot. Az URL-cím a **raw.githubusercontent.com**kezdődik.
1. Másolja az URL-címet.  Ezt az értéket meg kell adnia, amikor a folyamatot az oktatóanyag későbbi részében konfigurálja.

Eddig létrehozott egy GitHub-tárházat, és feltöltötte a sablonokat a tárházba.

## <a name="create-a-devops-project"></a>DevOps-projekt létrehozása

A DevOps-szervezet szükséges, mielőtt a következő eljárás.  Ha még nem rendelkezik ilyen, olvassa el az Előfeltételek című [témakört.](#prerequisites)

1. Jelentkezzen be az [Azure DevOps szolgáltatásba.](https://dev.azure.com)
1. Válasszon ki egy DevOps-szervezetet a bal oldalon.

    ![Az Azure DevOps Azure Pipelines azure-erőforrás-kezelő létrehozása Azure DevOps-projekt](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Válassza a **New project** (Új projekt) lehetőséget. Ha nincs enek projektje, a projekt létrehozása lap automatikusan megnyílik.
1. Írja be a következő értékeket:

    * **Projekt neve**: adjon meg egy projektnevet. Használhatja a projekt nevét választotta a legelején az oktatóanyag.
    * **Verziókövetés**: Válassza **a Git**lehetőséget. Előfordulhat, hogy a **Verziókövetés**megtekintéséhez ki kell **bontania** a Speciális elemet.

    Használja a többi tulajdonság alapértelmezett értékét.
1. Kattintson a **Létrehozás** gombra.

Hozzon létre egy szolgáltatáskapcsolatot, amely a projektek azure-beli üzembe helyezéséhez szolgál.

1. Válassza a Bal oldali menü Aljáról a **Projekt beállításai** lehetőséget.
1. Válassza a **Szolgáltatáskapcsolatok lehetőséget** **a Folyamatok csoportban.**
1. Válassza **az Új szolgáltatás kapcsolat**lehetőséget, válassza az Azure Resource **Manager**lehetőséget, majd a **Tovább**gombot.
1. Válassza **az Egyszerű szolgáltatás**lehetőséget, majd a **Tovább**gombot.
1. Írja be a következő értékeket:

    * **Hatókör szint**: válassza **az Előfizetés**lehetőséget.
    * **Előfizetés**: válassza ki az előfizetést.
    * **Erőforráscsoport**: Hagyja üresen.
    * **Kapcsolat neve**: adja meg a kapcsolat nevét. Például **Az AzureRmPipeline-conn**. Írja le ezt a nevet, szüksége van a névre a folyamat létrehozásakor.
    * **Hozzáférési engedély megadása az összes folyamathoz.** (kiválasztva)
1. Kattintson a **Mentés** gombra.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Eddig a következő feladatokat végezte el.  Ha kihagyja az előző szakaszokat, mert ismeri a GitHubot és a DevOps-t, a folytatás előtt el kell végeznie a feladatokat.

* Hozzon létre egy GitHub-tárházat, és mentse a sablonokat a **CreateWebApp** mappába a tárházban.
* Hozzon létre egy DevOps-projektet, és hozzon létre egy Azure Resource Manager-szolgáltatáskapcsolatot.

Sablon üzembe helyezésének lépésével rendelkező folyamat létrehozása:

1. Válassza a bal oldali menü **Folyamatok parancsát.**
1. Válassza az **Új folyamat lehetőséget.**
1. Válassza a **GitHub** lehetőséget a **Connect** (Csatlakozás) lapon. Ha a rendszer kéri, adja meg a GitHub hitelesítő adatait, majd kövesse az utasításokat. Ha a következő képernyő látható, válassza a **Csak a tárat választó**lehetőséget, és ellenőrizze, hogy a tárház szerepel-e a listában, mielőtt & a Telepítés **jóváhagyása**lehetőséget választaná.

    ![Az Azure Resource Manager Azure DevOps Azure-folyamatok csak az adattárakat választják ki](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. A **Kijelölés** lapon válassza ki a tárházat.  Az alapértelmezett név **a[YourAccountName]/[YourGitHubRepositoryName]** név.
1. A **Konfigurálás** lapon válassza a **Kezdő folyamat lehetőséget.** Az **azure-pipelines.yml** folyamatfájlt két parancsfájllépéssel jeleníti meg.
1. Törölje a két parancsfájllépést az yml fájlból.
1. A kurzor mozgatása a sorra a **lépések után:**.
1. Válassza a Képernyő jobb oldalán található **Segéd megjelenítése** lehetőséget a **Feladatok** ablaktábla megnyitásához.
1. Válassza **az ARM sablon telepítését**.
1. Írja be a következő értékeket:

    * **deploymentScope**: Válassza az **Erőforráscsoport**lehetőséget .. A hatókörökről a [Telepítési hatókörök című témakörben olvashat bővebben.](deploy-rest.md#deployment-scope)
    * **Azure Resource Manager-kapcsolat**: Válassza ki a korábban létrehozott szolgáltatáskapcsolat nevét.
    * **Előfizetés**: Adja meg a cél-előfizetés azonosítóját.
    * **Művelet**: Válassza ki az **Erőforráscsoport létrehozása vagy frissítése** művelet et 2 művelet - 1. erőforráscsoport létrehozása, ha új erőforráscsoport név van megadva; 2. a megadott sablon telepítése.
    * **Erőforráscsoport**: Adjon meg egy új erőforráscsoport nevet. Például **Az AzureRmPipeline-rg**.
    * **Hely**: Válassza ki az erőforráscsoport helyét, például **USA középső részén.**
    * **Sablon helye**: Válassza a **Csatolt műtermék**lehetőséget, ami azt jelenti, hogy a feladat a sablonfájlt közvetlenül a csatlakoztatott tárházból keresi meg.
    * **Sablon**: Írja be **a CreateWebApp/azuredeploy.json parancsot.** Ha módosította a mappa nevét és a fájlnevet, módosítania kell ezt az értéket.
    * **Sablonparaméterek**: Hagyja üresen ezt a mezőt. A paraméterértékeket a **A sablon paramétereinek felülbírálása.
    * **overrideParameters**: Enter **-projectName [EnterAProjectName] -linkedTemplateUri [EnterTheLinkedTemplateURL]**. Cserélje le a projekt nevét és a csatolt sablon URL-címét. A csatolt sablon URL-címe az, amit a [GitHub-tárház létrehozása](#create-a-github-repository)végén írt le.
    * **Központi telepítési mód**: Válassza a **Növekményes**lehetőséget.
    * **A telepítés neve**: Írja be **a DeployPipelineTemplate értéket.** A **Telepítés neve**előtt válassza **a Speciális** lehetőséget.

    ![Az Azure DevOps Azure-folyamatok azure-kezelői erőforrás-kezelője](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Válassza a **Hozzáadás** lehetőséget.

    A feladatról további információt az [Azure Resource Group Deployment task](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)és az Azure Resource Manager sablontelepítési feladat című témakörben [talál.](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

    Az yml fájlnak hasonlónak kell lennie a következőkhöz:

    ![Az Azure Resource Manager Azure DevOps Azure-folyamatok](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Kattintson a **Mentés és futtatás** elemre.
1. A **Mentés és futtatás** ablaktáblán válassza a **Mentés és újrafuttatás** lehetőséget. A YAML-fájl egy példánya a csatlakoztatott tárházba kerül. A YAML-fájlt a tárház tallózásával láthatja.
1. Ellenőrizze, hogy a folyamat végrehajtása sikeresen megtörtént-e.

    ![Az Azure Resource Manager Azure DevOps Azure-folyamatok](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Nyissa meg az erőforráscsoportot. A név az, amit a folyamat YAML-fájljában megadott.  Egy létrehozott tárfiókot fog látni.  A tárfiók neve **a tárolóval**kezdődik.
1. Válassza ki a tárfiók nevét a megnyitásához.
1. Válassza **a Tulajdonságok lehetőséget.** Figyelje meg, hogy a **replikáció** **helyileg redundáns tárolás (LRS).**

## <a name="update-and-redeploy"></a>Frissítés és újratelepítés

Amikor frissíti a sablont, és leküldéses a módosításokat a távoli tárházba, a folyamat automatikusan frissíti az erőforrásokat, a tárfiókot ebben az esetben.

1. Nyissa meg **a linkedStorageAccount.json** fájlt a Visual Studio Code helyi tárházából vagy bármely szövegszerkesztőből.
1. Frissítse a **storageAccountType** **defaultValue értékét** **a Standard_GRS**értékre. Tekintse meg a következő képernyőképet:

    ![Az Azure Resource Manager Azure DevOps Azure-folyamatok frissítése yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Mentse a módosításokat.
1. A módosítások leküldése a távoli tárházba a Git Bash/Shell következő parancsainak futtatásával.

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    Az első parancs (lekérése) szinkronizálja a helyi tárházat a távoli tárházzal. A folyamat YAML-fájlja csak a távoli tárházba lett hozzáadva. A lekéréses parancs futtatása letölti a YAML-fájl egy példányát a helyi ágba.

    A negyedik parancs (leküldéses) feltölti a módosított linkedStorageAccount.json fájlt a távoli tárházba. A távoli tárház főága frissül, a folyamat újra aktiválódik.

A módosítások ellenőrzéséhez ellenőrizheti a tárfiók Replikáció tulajdonságát.  Lásd: [A telepítés ellenőrzése](#verify-the-deployment).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

Előfordulhat, hogy törölni szeretné a GitHub-tárházat és az Azure DevOps-projektet is.

## <a name="next-steps"></a>További lépések

Gratulálunk, befejezte ezt a Resource Manager-sablon telepítési oktatóanyagát. Tudassa velünk, ha bármilyen észrevétele és javaslata van a visszajelzés részben. Köszönjük!
Készen áll arra, hogy a sablonokra adott speciálisabb fogalmakba ugorjon. A következő oktatóanyag részletesebben ismerteti a sablonreferencia-dokumentáció használatát az üzembe helyezhető erőforrások meghatározásához.

> [!div class="nextstepaction"]
> [A sablonreferencia felhasználása](./template-tutorial-use-template-reference.md)
