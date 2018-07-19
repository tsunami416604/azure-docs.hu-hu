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
ms.openlocfilehash: ad8920ac01ce62eb676b495dcde2aae6b076cbe2
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125503"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Gyors útmutató: Hozzon létre, és a egy webalkalmazás üzembe helyezése az Azure Service Fabric-háló

Az Azure Service Fabric háló egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a mikroszolgáltatás-alapú alkalmazások üzembe helyezése nélkül kezelése virtuális gépek, tároló, vagy a hálózat.

Ebben a rövid útmutatóban egy új Service Fabric-háló magában foglaló alkalmazást egy ASP.NET Core-webalkalmazást fog létrehozni futtathatja a helyi fejlesztési fürtöt, és tegye közzé, hogy az Azure-ban.

Szüksége lesz Azure-előfizetésre Ha még nincs fiókja, létrehozhat egy ingyenes Azure-előfizetéssel, egyszerűen [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt. Is kell [a fejlesztői környezet beállítása](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>A Service Fabric-háló-projekt létrehozása

Nyissa meg a Visual Studiót, és válassza ki **fájl** > **új** > **projekt...**

Az a **új projekt** párbeszédpanel **keresési** tetején típus `mesh`. Válassza ki a **Service Fabric-háló alkalmazás** sablont. (Ha nem látja a sablont, győződjön meg arról, hogy a háló SDK-t telepítette, és a VS-eszközeinek előzetes verziója a leírtak szerint [a fejlesztési környezet beállítása](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

Az a **neve** mezőbe írja be **ServiceFabricMesh1** és a a **hely** állítsa be a mappa elérési útját a fájlokat a projekt helyét.

Győződjön meg arról, hogy **megoldás könyvtár létrehozása** be van jelölve, és kattintson a **OK** a Service Fabric-háló projekt létrehozásához.

![A Visual studio új Service Fabric-háló projekt párbeszédpanel](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Szolgáltatás létrehozása

Miután rákattintott **OK**, a **új Service Fabric-szolgáltatás** párbeszédpanel jelenik meg. Válassza ki a **ASP.NET Core** typ projektu, ellenőrizze, hogy **Container OS** értékre van állítva **Windows** kattintson **OK** az ASP.NET Core-projekt létrehozása . 

![A Visual studio új Service Fabric-háló projekt párbeszédpanel](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

A **új ASP.NET Core-webalkalmazás** párbeszédpanel jelenik meg. Válassza ki **webalkalmazás** majd **OK**.

![A Visual studio új ASP.NET core-alkalmazás](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio létrehozza a Service Fabric-háló-projektre, és az ASP.NET Core-projektet.

## <a name="build-and-publish-to-your-local-cluster"></a>Hozhat létre, és tegye közzé a helyi fürtön

Docker-rendszerkép összeállítása és, amint a projekt betölti a helyi fürtön közzétett automatikusan. Ez a folyamat eltarthat egy ideig. A Service Fabric-eszközök az előrehaladását nyomon követheti a **kimeneti** ablakot, ha azt szeretné, válassza a **Service Fabric-eszközök** elemnek, a **kimeneti** ablak legördülő listából. Továbbra is működik a docker-rendszerkép üzembe helyezéséhez.

A projekt létrehozása után kattintson **F5** a szolgáltatás helyi hibakereséséhez. Amikor a helyi üzembe helyezés befejeződött, és a Visual Studio fut-e a projekthez, lesz egy minta weblap nyisson meg egy böngészőablakban.

Ha befejezte a telepített szolgáltatáson Tallózás, állítsa le a hibakeresés a projekt a billentyű lenyomásával **Shift + F5** a Visual Studióban.

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

A Service Fabric-háló projekt közzététele az Azure-ba, kattintson a jobb gombbal a **project Service Fabric-háló** a Visual studióban, és válassza **Publish...**

![A Visual studióban kattintson a jobb gombbal a projekt Service Fabric-háló](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Látni fogja a **Service Fabric-alkalmazás közzététele** párbeszédpanel.

![Service Fabric-háló a Visual studio közzététel párbeszédpanel](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Válassza ki az Azure-fiók és -előfizetést. Válasszon egy **hely**. Ez a cikk **USA keleti Régiójában**.

A **erőforráscsoport**válassza  **\<új erőforráscsoport létrehozása... >**. A **erőforráscsoport létrehozása** párbeszédpanel jelenik meg. Állítsa be a **erőforráscsoport-név** és **hely**.  Ebben a rövid útmutatóban használja a **USA keleti Régiójában** helyét és a csoport-neveket **sfmeshTutorial1RG** (Ha a szervezete több felhasználó használ ugyanabban az előfizetésben, válasszon egy egyedi erőforráscsoport-nevet).  Kattintson a **létrehozás** hozza létre az erőforráscsoportot, és a közzétételi párbeszédpanelre való visszatéréshez.

![A Visual studio Service Fabric-háló új erőforrás csoport párbeszédpanel](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Térjen vissza a **Service Fabric-alkalmazás közzététele** párbeszédpanelen, a **Azure Container Registry**válassza  **\<hozzon létre új Container Registry... >**. Az a **tároló-beállításjegyzék létrehozása** párbeszédpanelen egy egyedi nevet a **Tárolóregisztrációs adatbázis nevét**. Adjon meg egy **hely** (ebben a rövid útmutatóban használt **USA keleti Régiójában**). Válassza ki a **erőforráscsoport** , amelyet az előző lépést a listából, például **sfmeshTutorial1RG**. Állítsa be a **Termékváltozat** való **alapszintű** majd **létrehozása** a közzétételi párbeszédpanelre való visszatéréshez.

Ha hibaüzenetet kap, amely egy erőforrás-szolgáltató nincs regisztrálva az előfizetéshez tartozó regisztrálhatja. Először tekintse meg, ha az előfizetés rendelkezésre áll-e az erőforrás-szolgáltató:

```Powershell
Connect-AzureRmAccount
Get-AzureRmResourceProvider -ListAvailable
```

Ha a tároló beállításjegyzék-szolgáltatójának (`Microsoft.ContainerRegistry`) érhető el, regisztrálja a PowerShellben:

```Powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

![A Visual studio Service Fabric-háló új erőforrás csoport párbeszédpanel](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

A közzététel párbeszédpanelen kattintson a **közzététel** gombra kattintva helyezze üzembe a Service Fabric-háló alkalmazást az Azure-bA.

Ha első alkalommal közzéteszi a az Azure-ba, a docker-rendszerkép, az Azure Container Registry (ACR), amely a rendszerkép méretétől függően némi időre leküldésekor. Ezt követő közzéteszi az projekt gyorsabbak lesznek. Az üzembe helyezés állapotát figyelemmel kiválasztása **Service Fabric-eszközök** a Visual Studio **kimeneti** ablak legördülő listából. Az üzembe helyezés befejeztével a **Service Fabric-eszközök** kimenet megjeleníti az IP-cím és port, az alkalmazás URL-cím formájában.

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

Létrehozása és alkalmazások Service Fabric-háló üzembe helyezése kapcsolatos további információkért folytassa az oktatóanyagot.
> [!div class="nextstepaction"]
> [Létrehozásához, hibakereséséhez és egy Service Fabric-háló több szolgáltatásos webalkalmazás üzembe helyezése](service-fabric-mesh-tutorial-create-dotnetcore.md)