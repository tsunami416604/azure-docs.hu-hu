---
title: Oktatóanyag – Service Fabric Mesh-alkalmazás üzembe helyezése  | Microsoft Docs
description: Ismerje meg, hogyan tehet közzé egy háttér-webszolgáltatással kommunikáló ASP.NET Core webhelyet tartalmazó Azure Service Mesh-alkalmazást a Visual Studióval.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 467484824ec3a3ceffb6dfa692953406ed6acc1b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963321"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>Oktatóanyag: Service Fabric Mesh-alkalmazás üzembe helyezése

Ez az oktatóanyag egy sorozat harmadik része, és azt mutatja be, hogyan tehető közzé az Azure Service Fabric Mesh-alkalmazás közvetlenül a Visual Studióból.

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Alkalmazás közzététele az Azure-ban a Visual Studio használatával.
> * Az alkalmazás üzembe helyezésének ellenőrzése
> * Az előfizetésben jelenleg üzembe helyezett összes alkalmazás megtekintése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Service Fabric Mesh-alkalmazás létrehozása Visual Studióban](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Egy helyi fejlesztési fürtben futó Service Fabric Mesh-alkalmazás hibakeresése](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Service Fabric Mesh-alkalmazás üzembe helyezése
> * [Service Fabric Mesh-alkalmazás frissítése](service-fabric-mesh-tutorial-upgrade.md)
> * [A Service Fabric Mesh erőforrásainak eltávolítása](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Először [alakítsa ki a fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md) a Service Fabric futtatókörnyezet, az SDK, a Docker, és a Visual Studio 2017 telepítésével.

## <a name="download-the-to-do-sample-application"></a>A teendőlista-mintaalkalmazás letöltése

Ha nem hozta létre a teendőlista-mintaalkalmazást az [oktatóanyag második részében](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md), le is töltheti azt. Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Az alkalmazás az `src\todolistapp` könyvtárban található.

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

A Service Fabric Mesh-projekt Azure-on történő közzétételéhez a Visual Studióban kattintson a jobb gombbal a **todolistapp** elemre, és válassza a **Közzététel...** lehetőséget.

Ekkor megjelenik a **Service Fabric-alkalmazás közzététele** párbeszédpanel.

![Service Fabric Mesh párbeszédpanel a Visual Studióban](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Válassza ki az Azure-fiókját és -előfizetését. Válasszon ki egy **helyet**. Ebben a cikkben az **USA keleti régiója** szerepel.

Az **Erőforráscsoport** területen válassza az **\<Új erőforráscsoport létrehozása...>** lehetőséget. Ekkor egy párbeszédpanel jelenik meg, ahol új erőforráscsoportot hozhat létre. Ebben a cikkben az **USA keleti régiója** és a **sfmeshTutorial1RG** nevű csoport szerepel (ha a cégnél többen használják ugyanazt az előfizetést, válasszon egyedi csoportnevet).  Kattintson a **Létrehozás** elemre az erőforrás létrehozásához, majd térjen vissza a párbeszédpanelhez.

![A Service Fabric Mesh új párbeszédpanelje a Visual Studióban](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

Miután visszatért a **Service Mesh-alkalmazás közzététele** párbeszédpanelhez, az **Azure Container Registry** területen válassza a **\<Create New Container Registry...>** (Tárolóregisztrációs adatbázis létrehozása) lehetőséget. A **Tárolóregisztrációs adatbázis** párbeszédpanelen használjon egyedi nevet a **Tárolóregisztrációs adatbázis neve** elemhez. Adjon meg egy **helyet** (ebben az oktatóanyagban az **USA keleti régiója** szerepel). A legördülő listában válassza ki az előző lépésben létrehozott **Erőforráscsoportot**, például a **sfmeshTutorial1RG** nevűt. Állítsa **Alapszintűre** a **termékváltozatot**, majd a **Létrehozás** elemre kattintva hozzon létre a privát Azure Container Registryt, és térjen vissza a közzétételi párbeszédpanelhez.

![A Service Fabric Mesh új tárolóregisztrációs adatbázisról szóló párbeszédpanelje a Visual Studióban](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Ha olyan hibaüzenetet kap, hogy egy erőforrás-szolgáltató nincs regisztrálva az előfizetésében, regisztrálhatja azt. Először is nézze meg, hogy az erőforrás-szolgáltató elérhető-e az előfizetése számára:

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Ha a(z) (`Microsoft.ContainerRegistry`) tárolószolgáltató elérhető, regisztrálja azt a PowerShellből:

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

A Service Fabric-alkalmazás Azure-ban történő üzembe helyezéséhez kattintson a **Közzététel** elemre a közzétételi párbeszédpanelen.

Az Azure-ban történő első közzététel alkalmával továbbítani kell a docker-rendszerképet az Azure Container Registrybe (ACR), és ez a kép méretétől függően időbe telhet. Ugyanebben a projektben a további közzétételek már gyorsabbak lesznek. Az üzembe helyezés állapotának figyeléséhez a Visual Studio **Output** (Kimenet) ablakában válassza a **Service Fabric-eszközök** ablaktáblát. Az üzembe helyezés befejeztével a **Service Fabric-eszközök** kimenete egy URL-cím formájában jeleníti meg az alkalmazás IP-címét és portját.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Nyisson meg egy webböngészőt, és a megadott URL-címen tekintse meg az Azure-ban futó webhelyet.

## <a name="set-up-service-fabric-mesh-cli"></a>A Service Fabric Mesh parancssori felületének beállítása 
A további lépésekhez használhatja az Azure Cloud Shellt vagy az Azure CLI helyileg telepített példányát. Az Azure Service Fabric Mesh CLI-bővítmény moduljának telepítéséhez kövesse ezeket az [útmutatásokat](service-fabric-mesh-howto-setup-cli.md).

## <a name="check-application-deployment-status"></a>Az alkalmazás üzembe helyezésének ellenőrzése

Mostanra befejeződött az alkalmazás telepítése. A(z) `app show` paranccsal ellenőrizheti az állapotát. 

Az oktatóanyagban használt alkalmazás neve: `ServiceMeshApp`. A következő paranccsal összegyűjtheti az alkalmazás adatait:

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Az előfizetésben jelenleg üzembe helyezett összes alkalmazás megtekintése

Az „app list” paranccsal lekérheti az előfizetéséhez üzembe helyezett alkalmazások listáját.

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:
> [!div class="checklist"]
> * Az alkalmazás közzététele az Azure-ban.
> * Az alkalmazás üzembe helyezésének ellenőrzése
> * Az előfizetésben jelenleg üzembe helyezett összes alkalmazás megtekintése

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Service Fabric Mesh-alkalmazás frissítése](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
