---
title: Folyamatos integráció Azure folyamatok |} A Microsoft Docs
description: Ismerje meg, hogyan folyamatosan összeállítását, tesztelését és üzembe helyezése Azure Resource Manager-sablonok.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/12/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 85dc0476da12bea64610b6910b0682fef00f4b5a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057791"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Oktatóanyag: Folyamatos integráció az Azure Resource Manager-sablonok az Azure-folyamatok

Ismerje meg, hogyan használható az Azure folyamatok folyamatos fejlesztését és üzembe helyezése Azure Resource Manager-sablon projektek.

Az Azure fejlesztési és üzemeltetési csapatok számára a munkahelyi másokkal közös használatához kód fejlesztési, és fejlesztésére és üzembe helyezhet alkalmazásokat támogató fejlesztői szolgáltatások biztosít. A fejlesztők dolgozhat a felhőben az Azure DevOps-szolgáltatások használatával. Az Azure DevOps biztosít egy integrált szolgáltatások készlete, amelyek a webböngésző, vagy IDE ügyfél keresztül érheti el. Az Azure-folyamat az egyik ezeket a funkciókat. Az Azure folyamatok egy teljes körű folyamatos integrációs (CI) és a folyamatos továbbítás (CD) szolgáltatás. Ez a együtt tudjon működni az előnyben részesített Git-szolgáltatóval, és telepítheti a legtöbb főbb felhőszolgáltatás. Ezután a buildelési, tesztelési és üzembe a kódot a Microsoft Azure, a Google Cloud Platform vagy Amazon Web Services segítségével automatizálhatja.

Ez az oktatóanyag az Azure Resource Manager sablon fejlesztőknek szól, akik az új Azure DevOps-szolgáltatásokkal és az Azure-folyamatok lett tervezve. Ha már ismeri a GitHub és DevOps, továbbléphet a [hozzon létre egy folyamatot](#create-a-pipeline).

> [!NOTE]
> Válassza ki a projekt nevét. Amikor az oktatóanyagot, cserélje le valamelyik a **AzureRmPipeline** a projekt nevére.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * GitHub-adattár előkészítése
> * Azure DevOps-projekt létrehozása
> * Hozzon létre egy Azure-folyamat
> * A folyamat telepítés ellenőrzése
> * Frissítse a sablon és ismételt üzembe helyezése
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* **GitHub-fiók**, ahol a segítségével hozzon létre egy adattár a sablonokat. Ha még nincs fiókja, akkor az [ingyenesen](https://github.com). GitHub-adattárak használatával kapcsolatos további információkért lásd: [összeállítása GitHub-adattárak](/azure/devops/pipelines/repos/github).
* **Telepítse a Git szoftvert**. Ez az oktatóanyag utasításait követve használja *a Git Bash* vagy *Git Shell*. Útmutatásért lásd: [telepítése Git]( https://www.atlassian.com/git/tutorials/install-git).
* **Az Azure DevOps-szervezet**. Ha még nincs fiókja, létrehozhat egy ingyenes. Lásd: [hozzon létre egy szervezet vagy projekt gyűjteményt]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* **[A Visual Studio Code](https://code.visualstudio.com/) a Resource Manager Tools bővítmény a**. Lásd [a bővítmény telepítését](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) ismertető részt.

## <a name="prepare-a-github-repository"></a>GitHub-adattár előkészítése

GitHub a projekt forráskódja, beleértve a Resource Manager-sablonok tárolására szolgál. Egyéb támogatott adattárak, lásd: [Azure DevOps által támogatott adattárak](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Hozzon létre egy GitHub-adattár

Ha nem rendelkezik GitHub-fiókkal, tekintse meg [Előfeltételek](#prerequisites).

1. Jelentkezzen be a [GitHub](https://github.com).
2. A fiók rendszerképet, a jobb felső sarokban, majd válassza ki és **az tárházak**.

    ![Az Azure Resource Manager Azure DevOps Azure folyamatok létrehozása a GitHub-adattár](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Válassza ki **új**, egy zöld gombot.
1. A **adattár neve**, adja meg a tárház nevét.  Ha például **AzureRmPipeline-tárház**. Ne felejtse el kicserélni bármelyik **AzureRmPipeline** a projekt nevére. Megadhatja **nyilvános** vagy **privát** az oktatóanyag a. Majd **tárház létrehozása**.
1. Jegyezze fel az URL-címet. Az adattár URL-címe a következő formátumban:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Ez a tárház néven egy *távoli tárházba*. A fejlesztőket az ugyanabban a projektben egyes klónozhat saját maga *helyi tárház*, és a távoli tárházba a változtatásokat.

### <a name="clone-the-remote-repository"></a>A távoli tárház klónozása

1. Nyissa meg a Git Shell vagy a Git Basht.  Lásd: [Előfeltételek](#prerequisites).
1. Ellenőrizze, hogy az aktuális mappa **github**.
1. Futtassa a következő parancsot:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Cserélje le **[YourAccountName]** a GitHub-fiók nevét, és cserélje le **[YourGitHubRepositoryName]** az adattár nevét, az előző eljárásban létrehozott együtt.

    Az alábbi képernyőfelvételnek megfelelően egy példa látható.

    ![Az Azure Resource Manager Azure DevOps Azure folyamatok létrehozása a bash a GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

A **CreateAzureStorage** a mappa a sablon tárolására. A **pwd** parancs megjeleníti a mappa elérési útját. Az elérési út, ahol az alábbi eljárás a sablon mentéséhez.

### <a name="download-a-quickstart-template"></a>Töltse le a gyorsindítási sablon

A sablon létrehozása helyett letöltheti egy [gyorsindítási sablon]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Ez a sablon létrehoz egy Azure Storage-fiókot.

1. Nyissa meg a Visual Studio Code-ot. Lásd: [Előfeltételek](#prerequisites).
2. Nyissa meg a sablon a következő URL-címet:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Mentse a fájlt az **azuredeploy.json** , a **CreateAzureStorage** mappát. A mappa nevét és a fájl nevét is használhatók, ha azok a folyamat.  Ha megváltoztatja ezeket a neveket, frissítenie kell a folyamatban használt nevekkel.

### <a name="push-the-template-to-the-remote-repository"></a>A sablon leküldéses a távoli tárházhoz

Az azuredeploy.JSON elemet a helyi tárházban bővült. Ezután feltöltheti a sablon a távoli tárházba.

1. Nyissa meg *Git Shell* vagy *a Git Bash*, ha nincs megnyitva.
1. Módosítsa a könyvtárat a CreateAzureStorage mappát a helyi tárházban.
1. Ellenőrizze a **azuredeploy.json** fájl a mappában van.
1. Futtassa a következő parancsot:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    LF kapcsolatos figyelmeztetést kaphat. A figyelmeztetés figyelmen kívül hagyhatja. **fő** a főágban van.  Általában hozzon létre egy ágat frissítése. Az oktatóanyagban egyszerűbb, használhatja a főágban közvetlenül.
1. Keresse meg a GitHub-adattár közvetlenül a böngészőből.  Az URL-cím  **https://github.com/ [YourAccountName] / [YourGitHubRepository]** . Meg kell jelennie a **CreateAzureStorage** mappát és **Azuredeploy.json** megadott mappába.

Eddig létrehozta egy GitHub-adattárból, és egy sablon feltölteni a tárházba.

## <a name="create-a-devops-project"></a>A DevOps-projekt létrehozása

A fejlesztési és üzemeltetési szervezet van szükség, a folytatás előtt a következő eljárással.  Ha még nincs fiókja, [Előfeltételek](#prerequisites).

1. Jelentkezzen be a [az Azure DevOps](https://dev.azure.com).
1. Jelöljön ki egy fejlesztési és üzemeltetési szervezet a bal oldalon.

    ![Az Azure Resource Manager Azure DevOps Azure folyamatokat az Azure DevOps-projekt létrehozása](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Válassza a **Create project** (Projekt létrehozása) lehetőséget. Ha nem rendelkezik projektekkel, automatikusan megnyílik a projekt létrehozása lap.
1. Írja be a következő értékeket:

    * **Projektnév**: Adja meg a projekt nevét. A projekt nevére, az oktatóanyag legelején kiválasztott is használhatja.
    * **Verziókövetés**: Válassza ki **Git**. Előfordulhat, hogy bővítenie kell a **speciális** megtekintéséhez **verziókövetés**.

    A többi tulajdonság esetén használja az alapértelmezett értéket.
1. Válassza a **Create project** (Projekt létrehozása) lehetőséget.

Hozzon létre egy kapcsolat a szolgáltatással, amely az Azure-projektek telepítésére szolgál.

1. Válassza ki **beállításai a** a bal oldali menü alján.
1. Válassza ki **kapcsolatok szolgáltatás** alatt **folyamatok**.
1. Válassza ki **új szolgáltatáskapcsolati**, majd válassza ki **AzureResourceManager**.
1. Írja be a következő értékeket:

    * **Kapcsolat neve**: Adja meg a kapcsolat nevét. Ha például **AzureRmPipeline-kap**. Jegyezze fel ezt a nevet, szüksége lesz a neve, amikor a folyamatot hoz létre.
    * **Hatókör szintű**: válasszon **előfizetés**.
    * **Előfizetés**: válassza ki az előfizetését.
    * **Erőforráscsoport**: Hagyja üresen.
    * **Használja ezt a kapcsolatot minden folyamatok engedélyezése**. (kiválasztva)
1. Kattintson az **OK** gombra.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Mostanáig befejeződött a következő feladatokat.  Ha kihagyja a korábbi szakaszokban, mert ismeri a GitHub és DevOps, a folytatás előtt a feladatokat kell végrehajtani.

- Hozzon létre egy GitHub-adattárból, és mentse [ezzel a sablonnal](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) , a **CreateAzureStorage** mappát a tárházban.
- Hozzon létre egy DevOps-projektet, és hozzon létre egy Azure Resource Manager szolgáltatáskapcsolódási.

Létrehoz egy folyamatot egy lépéssel a sablon üzembe helyezéséhez:

1. Válassza ki **folyamatok** a bal oldali menüből.
1. Válassza ki **új adatcsatorna**.
1. Az a **Connect** lapon jelölje be **GitHub**. Ha a rendszer rákérdez, adja meg a GitHub hitelesítő adataival, és kövesse az utasításokat. Ha a következő képernyőt látja, válassza ki a **csak válassza ki a tárházak**, és ellenőrizze, hogy az adattár a lista kiválasztása előtt **jóváhagyása és telepítése**.

    ![Az Azure Resource Manager Azure DevOps Azure folyamatok csak adattárak kiválasztása](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Az a **kiválasztása** lapra, válassza ki a tárházban.  Alapértelmezés szerint ez **[YourAccountName] / [YourGitHubRepositoryName]** .
1. Az a **konfigurálása** lapon jelölje be **alapszintű folyamat**. Megjeleníti a **azure-pipelines.yml** folyamat fájlt szkript két lépést.
1. Cserélje le a **lépéseket** szakaszt a következő yaml-kódot:

    ```yaml
    steps:
    - task: AzureResourceGroupDeployment@2
      inputs:
        azureSubscription: '[YourServiceConnectionName]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Ez hasonlóan kell kinéznie:

    ![Az Azure Resource Manager Azure DevOps Azure folyamatok yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Hajtsa végre a következő módosításokat:

    * **azureSubscription**: frissítse az értéket az előző eljárásban létrehozott szolgáltatáskapcsolódási.
    * **a művelet**: a **létrehozása vagy frissítése erőforráscsoport** művelet does 2 műveletek – 1. Hozzon létre egy erőforráscsoportot, ha egy új erőforráscsoport-nevet áll rendelkezésre; 2. a megadott sablon üzembe helyezéséhez.
    * **resourceGroupName**: Adjon meg egy új erőforráscsoport-nevet. Ha például **AzureRmPipeline-rg**.
    * **hely**: Adja meg az erőforráscsoport helyét.
    * **templateLocation**: amikor **társított összetevő** van megadva, a feladat megkeresi a sablonfájlt közvetlenül a csatlakoztatott tárházból.
    * **csmFile** a sablonfájl elérési útját. Nem kell adja meg a sablon paraméterfájljának, mivel minden, a sablonban definiált paraméterek alapértelmezett értékeit.

    A feladat kapcsolatos további információkért lásd: [Azure erőforráscsoport-telepítés a feladat](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)
1. Kattintson a **Mentés és futtatás** elemre.
1. Válassza ki **mentés és Futtatás** újra. Egy YAML-fájlt a rendszer menti a csatlakoztatott tárházba. Tallózással keresse meg a YAML-fájlt a tárház tekintheti.
1. Győződjön meg arról, hogy a folyamat sikeres végrehajtását.

    ![Az Azure Resource Manager Azure DevOps Azure folyamatok yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az erőforráscsoportot. A név a folyamat YAML fájlban megadott.  Létrehozott egy tárfiókot gondoskodnak.  A tárfiók nevével kezdődik **tárolására**.
1. Válassza ki a tárfiók nevét a megnyitásához.
1. Válassza ki **tulajdonságok**. Figyelje meg a **Termékváltozat** van **Standard_LRS**.

    ![Az Azure Resource Manager Azure DevOps Azure folyamatok portal-ellenőrzés](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Frissítés és ismételt üzembe helyezése

Ha a sablont frissítését, és küldje le a módosításokat a távoli tárházba, a folyamat automatikusan frissíti az erőforrásokat, a storage-fiók ebben az esetben.

1. Nyissa meg **azuredeploy.json** Visual Studio Code-ban a helyi adattárból.
1. Frissítés a **defaultValue** , **Tárfióktípus** való **Standard_GRS**. Tekintse meg a következő képernyőképet:

    ![Az Azure Resource Manager Azure DevOps Azure folyamatok yaml frissítése](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Mentse a módosításokat.
1. A módosítások leküldése a távoli tárház Git-Bash/felületet a következő parancsok futtatásával.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Az első parancs szinkronizálja a helyi tárházban, a távoli tárházzal. Ne feledje, hogy a folyamat YAML-fájlt a távoli tárházba hozzá lett adva.

    A frissített távoli tárház master ággal a folyamat újra aktiválódik.

A módosítások ellenőrzéséhez ellenőrizheti a tárfiók a Termékváltozat.  Lásd: [a telepítés ellenőrzése](#verify-the-deployment).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

Érdemes azt is, a GitHub-tárházat és az Azure DevOps-projekt törlése.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Resource Manager-sablon üzembe helyezéséhez Azure DevOps-folyamatot hoz létre. Annak megismeréséhez, hogyan lehet Azure-erőforrásokat több régióban üzembe helyezni, és hogyan alkalmazhat biztonságos üzembehelyezési gyakorlatokat, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Az Azure Deployment Manager használata](./resource-manager-tutorial-deploy-vm-extensions.md)
