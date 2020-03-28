---
title: Az Azure Kubernetes telepítési központja
description: Az Azure DevOps üzembe helyezési központja leegyszerűsíti a robusztus Azure DevOps-folyamat beállítását az alkalmazáshoz
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: b0a9597e370648faab3787218c7d038798dbd455
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048108"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Az Azure Kubernetes telepítési központja

Az Azure DevOps üzembe helyezési központja leegyszerűsíti egy robusztus Azure DevOps-folyamat beállítását az alkalmazáshoz. Alapértelmezés szerint a Deployment Center konfigurálja az Azure DevOps-folyamatot az alkalmazásfrissítések kubernetes-fürtre való központi telepítéséhez. Kiterjesztheti az alapértelmezett konfigurált Azure DevOps-folyamatot, és gazdagabb képességeket is hozzáadhat: a jóváhagyás megszerzésének lehetősége üzembe helyezés előtt, további Azure-erőforrások kiépítése, parancsfájlok futtatása, az alkalmazás frissítése, sőt további érvényesítési tesztek futtatása.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Konfiguráljon egy Azure DevOps-folyamatot az alkalmazásfrissítések Kubernetes-fürtre való üzembe helyezéséhez.
> * Vizsgálja meg a folyamatos integrációs (CI) folyamatot.
> * Vizsgálja meg a folyamatos szállítási (CD) csővezetéket.
> * Takarítsd fel az erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

* Egy Azure Kubernetes-szolgáltatás (AKS) fürt.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Válassza ki a [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) opciót az Azure Portal menüsorának jobb oldalán.

1. Az AKS-fürt létrehozásához futtassa a következő parancsokat:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Alkalmazásfrissítések központi telepítése Kubernetes-fürtre

1. Nyissa meg az előző szakaszban létrehozott erőforráscsoportot.

1. Jelölje ki az AKS-fürtöt, majd válassza a **Telepítési központ (előzetes verzió)** lehetőséget a bal oldali panelen. Válassza **az Első lépések lehetőséget.**

   ![beállítások](media/deployment-center-launcher/settings.png)

1. Válassza ki a kód helyét, és válassza a **Tovább gombot.** Ezután válassza ki a jelenleg támogatott adattárak egyikét: **[az Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** vagy a **GitHub.**

    Az Azure Repos verziókezelő eszközök készlete, amelyek segítenek a kód kezelésében. Az, hogy a szoftverprojekt nagy vagy kicsi, a verziókövetés a lehető leghamarabb történő használata jó ötlet.

    - **Azure repos:** Válasszon egy tárházat a meglévő projekt és szervezet.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub:** Engedélyezze és válassza ki a GitHub-fiók tárházát.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. A Deployment Center elemzi a tárházat, és észleli a Docker-fájlt. Ha frissíteni szeretné a Docker-fájlt, szerkesztheti az azonosított portszámot.

    ![Alkalmazásbeállítások](media/deployment-center-launcher/application-settings.png)

    Ha a tárház nem tartalmazza a Docker-fájlt, a rendszer megjelenít egy üzenetet, hogy véglegesítse az egyik.

    ![Docker-fájl](media/deployment-center-launcher/dockerfile.png)

1. Jelöljön ki egy meglévő tárolójegyzéket, vagy hozzon létre egyet, majd válassza a **Befejezés gombot.** A folyamat automatikusan létrejön, és várólistára kerül az Azure Pipelines.The pipeline is created automatically and queues a build in [Azure Pipelines.](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops)

    Az Azure Pipelines egy felhőalapú szolgáltatás, amely segítségével automatikusan létre, és tesztelje a kód projekt, és elérhetővé teszi a többi felhasználó számára. Az Azure Pipelines egyesíti a folyamatos integrációt és a folyamatos kézbesítést, hogy folyamatosan és következetesen tesztelje és építse össze a kódot, és szállítsa azt bármely célra.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Válassza ki a kapcsolatot a folyamatban lévő folyamat megtekintéséhez.

1. A telepítés befejezése után megjelennek a sikeres naplók.

    ![Naplók](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

A Deployment Center automatikusan konfigurálja az Azure DevOps-szervezet CI/CD-folyamatát. A folyamat feltárható és testreszabható.

1. Nyissa meg a Központi központ irányítópultját.  

1. Válassza ki a build számát a sikeres naplók listájából a projekt buildelési folyamatának megtekintéséhez.

1. Jelölje ki a három pontot (...) a jobb felső sarokban. A menüben számos lehetőség látható, például egy új build sorba állása, a build megtartása és a buildfolyamat szerkesztése. Válassza **a Folyamat szerkesztése lehetőséget.** 

1. Ebben az ablaktáblában megvizsgálhatja a buildfolyamat különböző feladatait. A build különböző feladatokat hajt végre, például a Git-tárházból származó források gyűjtése, lemezkép létrehozása, lemezkép lenyomása a tároló beállításjegyzékébe, és a központi telepítésekhez használt kimenetek közzététele.

1. Válassza ki a buildfolyamat nevét a folyamat tetején.

1. Módosítsa a buildfolyamat nevét valami leíróbbra, válassza **& várólista mentése**lehetőséget, majd kattintson a **Mentés gombra.**

1. A buildfolyamat alatt válassza az **Előzmények**lehetőséget. Ez az ablaktábla a legutóbbi build-módosítások naplózási nyomvonalát jeleníti meg. Az Azure DevOps figyeli a buildfolyamat on végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza **az Eseményindítók lehetőséget.** Az ágakat felveheti vagy kizárhatja a CI folyamatból.

1. Válassza a **Megtartást**. A forgatókönyvtől függően megadhatja a több build megtartásához vagy eltávolításához kívánt házirendeket.

## <a name="examine-the-cd-pipeline"></a>A CD-folyamat vizsgálata

A Deployment Center automatikusan létrehozza és konfigurálja az Azure DevOps-szervezet és az Azure-előfizetés közötti kapcsolatot. A leírt lépések közé tartozik egy Azure-szolgáltatás-kapcsolat beállítása az Azure-előfizetés Azure DevOps-szal való hitelesítéséhez. Az automatizált folyamat egy kiadási folyamatot is létrehoz, amely folyamatos kézbesítést biztosít az Azure-ba.

1. Válassza **a Folyamatok**lehetőséget, majd a **Felszabaduláslehetőséget.**

1. A kiadási folyamat szerkesztéséhez válassza a **Szerkesztés lehetőséget.**

1. Válassza a **Legördülő** lista **Legördülő listáját.** Az előző lépésekben a megvizsgált építési folyamat a műtermékhez használt kimenetet hozza létre. 

1. Válassza ki a **Folyamatos üzembe helyezés** eseményindítóa **Drop** beállítás jobb oldalán. Ez a kiadási folyamat rendelkezik egy engedélyezett CD-eseményindítóval, amely központi telepítést futtat, amikor új buildösszetevő érhető el. Az eseményindítót is letilthatja, hogy manuális végrehajtást igényeljen a központi telepítésekhez.

1. A folyamat összes feladatának vizsgálatához válassza a **Feladatok lehetőséget.** A kiadás beállítja a talajművelési környezetet, konfigurálja a paramétert, telepíti a `imagePullSecrets` Helm eszközöket, és telepíti a Helm-diagramokat a Kubernetes-fürtre.

1. A kiadási előzmények megtekintéséhez válassza a **Kiadások megtekintése**lehetőséget.

1. Az összegzés megtekintéséhez válassza a **Kiadás**lehetőséget. Jelölje ki bármelyik szakaszt több menü feltárásához, például a kiadások összegzését, a kapcsolódó munkaelemeket és a teszteket. 

1. Válassza a **Véglegesítéseket**. Ebben a nézetben a központi telepítéshez kapcsolódó kódvéglegesítések láthatók. A kiadások összehasonlítása a központi telepítések közötti véglegesítési különbségek megtekintéséhez.

1. Válassza **a Naplók lehetőséget.** A naplók hasznos központi telepítési információkat tartalmaznak, amelyeket a központi telepítések alatt és után tekinthet meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti a létrehozott kapcsolódó erőforrásokat, ha már nincs rájuk szüksége. Használja a törlési funkciót a DevOps-projektek irányítópulton.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Vagy használhatja ezt a CI/CD modellt sablonként a többi folyamathoz.
