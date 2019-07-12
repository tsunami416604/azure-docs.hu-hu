---
title: Az Azure App Service-ben üzembe helyezési központ
description: Az Azure DevOps Telepítőközpontot egyszerűbbé teszi a beállítása egy robusztus, az alkalmazás az Azure DevOps-folyamattal
ms.author: puagarw
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 8d1e467906b74c97c8b4f4e5c14af0814dd098f7
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854637"
---
# <a name="deployment-center-launcher"></a>Üzembe helyezési központ indítója

Deployment center az Azure DevOps egyszerűbbé teszi a hatékony fejlesztési és üzemeltetési folyamatot az alkalmazás, beállítás mentése. Alapértelmezés szerint úgy konfigurálja a kubernetes-fürt központi telepítése az alkalmazásfrissítések fejlesztési és üzemeltetési folyamatot. Az alapértelmezett konfigurált DevOps folyamat, és adja hozzá a fejlesztési és üzemeltetési gazdagabb képességekkel - a előtt üzembe helyezése, további Azure-erőforrások kiépítését, parancsfájlok futtatásához, az alkalmazás frissítéséhez vagy további érvényesítési teszteket futó jóváhagyások bővítheti.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A DevOps-folyamattal való az alkalmazás frissítéseket telepítheti a k8s fürt konfigurálása
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Az erőforrások törlése

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Kap egy ingyenes keresztül [Visual Studio Dev Essentials programhoz](https://visualstudio.microsoft.com/dev-essentials/)

* Az Azure Kubernetes Service (AKS)-fürt

## <a name="create-aks-cluster"></a>AKS-fürt létrehozása

1. Jelentkezzen be a [Azure Portalon](https://portal.azure.com/)

1. Válassza ki a [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) gombra a az Azure portal jobb felső sarkában található menüben.

1. Az AKS-fürt létrehozásához futtassa a következő parancsokat.

    ```cmd
    # The below command creates Resource Group in the south india location

    az group create --name azooaks --location southindia

    # The below command creates a cluster named azookubectl with one node. 

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-k8s-cluster"></a>Alkalmazásfrissítések üzembe helyezése K8s fürtön

1. Keresse meg a fent létrehozott erőforráscsoportot.

1. Válassza ki az AKS-fürtöt, majd kattintson a bal oldali panelen beállítások a a **Deployment Center (előzetes verzió)** . Kattintson a **Ismerkedés**.

   ![beállítások](media/deployment-center-launcher/settings.png)

1. Válassza ki a kódot a helyét, és kattintson a **tovább**. A támogatott adattárak jelenleg meg, **[Azure Adattárakkal](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** és **GitHub**. Követheti az alábbi lépéseket a tárház kijelölés alapján.

    Az Azure-Adattárakkal verzió vezérlő eszközöket, amelyek segítségével kezelheti a kódot. E szoftver projektjéhez nagy és kis méretű, minél hamarabb használatával a verziókezeléshez, célszerű.

    - **Az Azure-Adattárakkal**: Tárház kiválasztása a meglévő projekt és a szervezet.

        ![Azure-beli adattárak](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Engedélyezze, és válassza ki a tárház GitHub-fiókja.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. A docker-fájlban észlelni és elemezni a tárház fogunk. Ha szeretné azt frissíteni, szerkesztheti az azonosított portszámot.

    ![Nastavení aplikace](media/deployment-center-launcher/application-settings.png)

    Ha az adattár nem tartalmazza a docker-fájlban, a rendszer véglegesíti egy üzenetet jelenít meg. 

    ![A docker-fájl](media/deployment-center-launcher/dockerfile.png)

1. Válassza ki, vagy egy meglévő tároló-beállításjegyzék létrehozása, és kattintson a **Befejezés**. A folyamat automatikusan létrejön, és a build várólistára [Azure folyamatok](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Az Azure folyamatok egy felhőszolgáltatás, amely segítségével automatikusan hozhat létre és tesztelje a kódot a projekthez, és más felhasználók számára elérhető legyen. Az Azure folyamatok folyamatos integrációs (CI) és a folyamatos továbbítás (CD) következetesen és folyamatosan teszteléséhez és a kód felépítéséhez és bármilyen célra elszállítja egyesíti.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Kattintson a hivatkozásra kattintva megtekintheti a folyamatban lévő folyamat.

1. A sikeres naplók látni fogja, ahogy azt az üzembe helyezés befejezése után.

    ![Logs](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

Üzembe helyezési központ konfigurálása az Azure DevOps szervezet CI / CD folyamat automatikusan. A folyamat fel és egyéni. 

1. Nyissa meg az irányítópultot az üzembe helyezési központ.  

1. Kattintson a buildszám sikeres naplók megtekintéséhez a buildelési folyamat a projekt a listából. 

1. Kattintson a jobb felső sarokban ellipsis(...). Egy menü jeleníti meg, például egy új létrehozást queuing, build megőrzése és szerkesztését a buildelési folyamat számos lehetőséget. Válassza ki a **szerkesztési folyamat**. 

1. A különböző feladatok a buildelési folyamat ezen a panelen ellenőrizheti. A build hajt végre különböző feladatokat, például forrásokból gyűjt a Git-tárház, lemezkép létrehozása, egy rendszerkép leküldése a tároló-beállításjegyzék és a telepítéshez használt kimenetek közzététele.

1. Válassza ki a buildelési folyamat elején a buildelési folyamat nevét.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. Válassza ki **előzmények** a buildelési folyamat alatt. Ez a panel megjeleníti az auditnaplót friss build módosítást. Az Azure DevOps végzett módosítások a buildelési folyamat figyeli, és lehetővé teszi, hogy verziójának összehasonlítása.

1. Válasszon **eseményindítók**. Szükség esetén ágak tartalmaz is, vagy a CI folyamat zárva.

1. Válasszon **megőrzési**. Megadhatja, hogy megtartja vagy eltávolítja a buildeket a forgatókönyvtől függően számos házirendeket.

## <a name="examine-the-cd-pipeline"></a>A CD-folyamat vizsgálata

Üzembe helyezési központ hoz létre, és automatikusan konfigurálja a szükséges lépéseket az Azure DevOps-szervezet az Azure-előfizetéshez. Ezeket a lépéseket többek között a egy Azure-szolgáltatás-kapcsolat hitelesítéséhez az Azure DevOps Azure-előfizetésbe. Az automation is létrehoz egy kiadási folyamatot, amely biztosítja a CD-ről az Azure-bA.

1. Válasszon **folyamatok**, és válassza a **kiadásokban**.

1. A kiadási folyamathoz szerkesztéséhez kattintson **szerkesztése** .

1. Válassza ki **Drop** a **összetevők**. Az előző lépésekben a konstrukció vizsgálni, hozza létre egyetlen folyamat a kimeneti összetevőre használt. 

1. Válassza ki **folyamatos üzembe helyezés** eseményindító jobb oldalán a **Drop** ikonra. A kibocsátási folyamat, amely a központi telepítés fut, amikor egy új buildösszetevő érhető el engedélyezett CD eseményindító tartozik. Igény szerint letilthatja az eseményindító, az üzemelő példányok manuális végrehajtás szükséges.

1. A folyamatok a tevékenységek vizsgálata, kattintson a **feladatok**. A kiadás a tiller valóban környezeti, konfigurálja a imagePullSecrets, Helm-eszközök telepítése és üzembe helyezi a Helm-diagramok a K8s fürthöz.

1. Kiadások előzményeinek megtekintése, kattintson a **verziók megtekintéséhez**. 

1. Az összegzés megtekintéséhez kattintson a a **kiadási**. Kattintson bármely, a fázis böngészhet több menük, például egy összesítő, kiadás társított munkaelemekhez és a tesztek. 

1. Válassza a **Véglegesítéseket**. Ez a nézet megjeleníti az üzemelő példányhoz kapcsolódó kód véglegesítéseket. Hasonlítsa össze a véglegesítés közötti eltérések számára.

1. Válassza a **Naplókat**. A naplók hasznos információkat tartalmaznak. Során, és a központi telepítések után meg tudja őket tekinteni.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kapcsolódó erőforrásokat, ha többé már nincs szükség rájuk létrehozott törölheti. A törlési funkció használata a DevOps Projects-irányítópulton.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A DevOps-folyamattal való az alkalmazás frissítéseket telepítheti a k8s fürt konfigurálása
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Az erőforrások törlése
