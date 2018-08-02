---
title: Rövid útmutató – létrehozása és a egy webalkalmazás üzembe helyezése az Azure Service Fabric-háló |} A Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy ASP.NET Core-webhelyen, és közzé kell tennie az Azure Service Fabric-háló.
services: service-fabric-mesh
documentationcenter: .net
author: tylermsft
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: d14420a363cfea23c86f63533a4ea89c5f2fd06f
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412916"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Gyors útmutató: Hozzon létre, és a egy webalkalmazás üzembe helyezése az Azure Service Fabric-háló

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül.

Ebben a rövid útmutatóban egy új Service Fabric-háló magában foglaló alkalmazást egy ASP.NET Core-webalkalmazást fog létrehozni futtathatja a helyi fejlesztési fürtöt, és tegye közzé, hogy az Azure-ban.

Szüksége lesz Azure-előfizetésre Ha még nincs fiókja, létrehozhat egy ingyenes Azure-előfizetéssel, egyszerűen [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt. Is kell [a fejlesztői környezet beállítása](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Service Fabric mesh-projekt létrehozása

Nyissa meg a Visual Studiót, és válassza ki **fájl** > **új** > **projekt...**

A **New Project** (Új projekt) párbeszédpanel fenti **Search** (Keresés) mezőjébe írja be a következőt: `mesh`. Válassza a **Service Fabric Mesh Application** (Service Fabric mesh-alkalmazás) sablont. Ha nem látja a sablont, győződjön meg róla, hogy telepítette a Mesh SDK-t és a VS-eszközök előzetes verzióját a [fejlesztési környezet beállításáról szóló szakaszban](service-fabric-mesh-howto-setup-developer-environment-sdk.md) ismertetett módon. 

Az a **neve** mezőbe írja be **ServiceFabricMesh1** és a a **hely** állítsa be a mappa elérési útját a fájlokat a projekt helyét.

Ügyeljen rá, hogy bejelölje a **Create directory for solution** (Címtár létrehozása a megoldáshoz) jelölőnégyzetet, majd kattintson az **OK** gombra a Service Fabric mesh-projekt létrehozásához.

![A Visual Studio új Service Fabric mesh-projektjének párbeszédpanele](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Szolgáltatás létrehozása

Miután rákattintott **OK**, a **új Service Fabric-szolgáltatás** párbeszédpanel jelenik meg. Válassza ki a **ASP.NET Core** typ projektu, ellenőrizze, hogy **Container OS** értékre van állítva **Windows** kattintson **OK** az ASP.NET Core-projekt létrehozása . 

![A Visual Studio új Service Fabric mesh-projektjének párbeszédpanele](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

A **új ASP.NET Core-webalkalmazás** párbeszédpanel jelenik meg. Válassza ki **webalkalmazás** majd **OK**.

![Visual Studio új ASP.NET Core-alkalmazás](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio létrehozza a Service Fabric-háló-projektre, és az ASP.NET Core-projektet.

## <a name="build-and-publish-to-your-local-cluster"></a>Hozhat létre, és tegye közzé a helyi fürtön

Docker-rendszerkép összeállítása és, amint a projekt betölti a helyi fürtön közzétett automatikusan. Ez a folyamat eltarthat egy ideig. A Service Fabric-eszközök az előrehaladását nyomon követheti a **kimeneti** ablakot, ha azt szeretné, válassza a **Service Fabric-eszközök** elemnek, a **kimeneti** ablak legördülő listából. Továbbra is működik a docker-rendszerkép üzembe helyezéséhez.

A projekt létrehozása után kattintson **F5** a szolgáltatás helyi hibakereséséhez. Amikor a helyi üzembe helyezés befejeződött, és a Visual Studio fut-e a projekthez, lesz egy minta weblap nyisson meg egy böngészőablakban.

Ha befejezte a telepített szolgáltatáson Tallózás, állítsa le a hibakeresés a projekt a billentyű lenyomásával **Shift + F5** a Visual Studióban.

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

A Service Fabric-háló projekt közzététele az Azure-ba, kattintson a jobb gombbal a **project Service Fabric-háló** a Visual studióban, és válassza **Publish...**

![A Visual studióban kattintson a jobb gombbal a projekt Service Fabric-háló](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Látni fogja a **Service Fabric-alkalmazás közzététele** párbeszédpanel.

![Service Fabric Mesh párbeszédpanel a Visual Studióban](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Válassza ki az Azure-fiókját és -előfizetését. Válasszon ki egy **helyet**. Ebben a cikkben az **USA keleti régiója** szerepel.

Az **Erőforráscsoport** területen válassza az **\<Új erőforráscsoport létrehozása...>** lehetőséget. A **erőforráscsoport létrehozása** párbeszédpanel jelenik meg. Állítsa be a **erőforráscsoport-név** és **hely**.  Ebben a rövid útmutatóban használja a **USA keleti Régiójában** helyét és a csoport-neveket **sfmeshTutorial1RG** (Ha a szervezete több felhasználó használ ugyanabban az előfizetésben, válasszon egy egyedi erőforráscsoport-nevet).  Kattintson a **létrehozás** hozza létre az erőforráscsoportot, és a közzétételi párbeszédpanelre való visszatéréshez.

![A Service Fabric Mesh új párbeszédpanelje a Visual Studióban](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Miután visszatért a **Service Mesh-alkalmazás közzététele** párbeszédpanelhez, az **Azure Container Registry** területen válassza a **\<Create New Container Registry...>** (Tárolóregisztrációs adatbázis létrehozása) lehetőséget. A **Tárolóregisztrációs adatbázis** párbeszédpanelen használjon egyedi nevet a **Tárolóregisztrációs adatbázis neve** elemhez. Adjon meg egy **hely** (ebben a rövid útmutatóban használt **USA keleti Régiójában**). A legördülő listában válassza ki az előző lépésben létrehozott **Erőforráscsoportot**, például a **sfmeshTutorial1RG** nevűt. Állítsa be a **Termékváltozat** való **alapszintű** majd **létrehozása** a közzétételi párbeszédpanelre való visszatéréshez.

![A Service Fabric Mesh új párbeszédpanelje a Visual Studióban](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

A közzététel párbeszédpanelen kattintson a **közzététel** gombra kattintva helyezze üzembe a Service Fabric-háló alkalmazást az Azure-bA.

Az Azure-ban történő első közzététel alkalmával továbbítani kell a docker-rendszerképet az Azure Container Registrybe (ACR), és ez a kép méretétől függően időbe telhet. Ugyanebben a projektben a további közzétételek már gyorsabbak lesznek. Az üzembe helyezés állapotát figyelemmel kiválasztása **Service Fabric-eszközök** a Visual Studio **kimeneti** ablak legördülő listából. Az üzembe helyezés befejeztével a **Service Fabric-eszközök** kimenete egy URL-cím formájában jeleníti meg az alkalmazás IP-címét és portját.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Nyisson meg egy webböngészőt, és keresse meg az URL-cím megjelenítéséhez a webhely Azure-ban futó:

![Service Fabric-háló webes alkalmazás futtatása](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Létrehozott egy új erőforráscsoportot a ACR és a Service Fabric-háló service-erőforrások üzemeltetéséhez, mivel ez az erőforráscsoport, amely egy egyszerű módja annak, hogy törli az összes hozzá társított erőforrások nyugodtan törölheti.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Másik megoldásként törölje az erőforráscsoportot [az Azure Portalról](https://portal.azure.com).

## <a name="next-steps"></a>További lépések

A Service Fabric Mesh-alkalmazások létrehozásáról és üzembe helyezésével kapcsolatos további információért lépjen tovább az oktatóanyagra.
> [!div class="nextstepaction"]
> [Többszolgáltatású webalkalmazás létrehozása, hibaelhárítása és üzembe helyezése a Service Fabric Mesh-ben](service-fabric-mesh-tutorial-create-dotnetcore.md)