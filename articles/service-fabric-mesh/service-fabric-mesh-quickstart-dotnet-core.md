---
title: Rövid útmutató – Webalkalmazás létrehozása és üzembe helyezése az Azure Service Fabric Mesh-ben | Microsoft Docs
description: Ez a rövid útmutató bemutatja az ASP.NET Core-webhelyek létrehozását és közzétételét az Azure Service Fabric Mesh-ben.
services: service-fabric-mesh
documentationcenter: .net
author: tylermsft
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 7e324b80968017e0160f41b88fa1824669050ac9
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407399"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Rövid útmutató: Webalkalmazás létrehozása és üzembe helyezése az Azure Service Fabric Mesh-ben

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül.

Ebben a rövid útmutatóban egy ASP.NET Core-webalkalmazásból álló új Service Fabric Mesh-alkalmazást hozhat létre és futtathatja azt a helyi fejlesztési fürtön, majd közzéteheti azt az Azure-ban való futtatáshoz.

Szüksége lesz Azure-előfizetésre Ha még nem rendelkezik Azure-előfizetéssel, egyszerűen létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a kezdés előtt. Ezenfelül szüksége lesz [fejlesztői környezete beállítására](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Service Fabric mesh-projekt létrehozása

Nyissa meg a Visual Studio alkalmazást, majd válassza a **File** (Fájl)  > **New** (Új)  > **Project...** (Projekt) elemet

A **New Project** (Új projekt) párbeszédpanel fenti **Search** (Keresés) mezőjébe írja be a következőt: `mesh`. Válassza a **Service Fabric Mesh Application** (Service Fabric mesh-alkalmazás) sablont. Ha nem látja a sablont, győződjön meg róla, hogy telepítette a Mesh SDK-t és a VS-eszközök előzetes verzióját a [fejlesztési környezet beállításáról szóló szakaszban](service-fabric-mesh-howto-setup-developer-environment-sdk.md) ismertetett módon. 

A **Name** (Név) mezőbe írja be a(z) **ServiceFabricMesh1** kifejezést, a **Location** (Hely) mezőben pedig adja meg a mappa elérési útját a projekt tárolandó fájljaihoz.

Ügyeljen rá, hogy bejelölje a **Create directory for solution** (Címtár létrehozása a megoldáshoz) jelölőnégyzetet, majd kattintson az **OK** gombra a Service Fabric mesh-projekt létrehozásához.

![A Visual Studio új Service Fabric mesh-projektjének párbeszédpanele](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Szolgáltatás létrehozása

Az **OK** gombra kattintva megjelenik a **New Service Fabric Service** (Új Service Fabric-szolgáltatás) párbeszédpanel. Válassza ki az **ASP.NET Core** projekttípust, és ügyeljen rá, hogy a **Container OS** (Tároló operációs rendszere) beállítás **Windows** értékre van állítva, majd kattintson az **OK** gombra az ASP.NET Core-projekt létrehozásához. 

![A Visual Studio új Service Fabric mesh-projektjének párbeszédpanele](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

Ekkor megjelenik az **New ASP.NET Core Web Application** (Új ASP.NET Core-webalkalmazás) párbeszédpanel. Válassza a **Web Application** (Webalkalmazás) lehetőséget, majd kattintson az **OK** gombra.

![Visual Studio új ASP.NET Core-alkalmazás](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

A Visual Studio létrehozza a Service Fabric Mesh-alkalmazásprojektet és az ASP.NET Core-projektet.

## <a name="build-and-publish-to-your-local-cluster"></a>Fejlesztés és közzététel a helyi fürtön

Amint a projekt betölt, a rendszer automatikusan létrehozza és közzéteszi a Docker-rendszerképet a helyi fürtön. Ez a folyamat eltarthat egy ideig. Az **Output** (Kimenet) ablakban nyomon követheti a Service Fabric-eszközök folyamatállapotát a **Service Fabric Tools** (Service Fabric-eszközök) elemet választva az **Output** (Kimenet) ablak legördülő listájában. A Docker-rendszerkép üzembe helyezése közben folytathatja munkáját.

A projekt létrehozása után nyomja le az **F5** billentyűt a szolgáltatás helyi hibakereséséhez. Amikor a helyi üzembe helyezés befejeződött, és a Visual Studio elindította a projektet, egy böngészőablakban megnyílik a mintául szolgáló weboldal.

Ha befejezte a telepített szolgáltatás tallózását, állítsa le a projekt hibakeresését a **Shift + F5** a billentyűkombináció lenyomásával a Visual Studióban.

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

A Service Fabric Mesh-projekt Azure-on történő közzétételéhez a Visual Studióban kattintson a jobb gombbal a **Service Fabric Mesh project** elemre, és válassza a **Publish...** (Közzététel) lehetőséget.

![Visual Studio jobb kattintás Service Fabric Mesh project](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Ekkor megjelenik a **Publish Service Fabric Application** (Service Fabric-alkalmazás közzététele) párbeszédpanel.

![Service Fabric Mesh párbeszédpanel a Visual Studióban](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Válassza ki az Azure-fiókját és -előfizetését. Válasszon ki egy **helyet**. Ebben a cikkben az **USA keleti régiója** szerepel.

Az **Erőforráscsoport** területen válassza az **\<Új erőforráscsoport létrehozása...>** lehetőséget. Megjelenik az **Erőforráscsoport létrehozása** párbeszédpanel. Adja meg az **Erőforráscsoport neve** és a **Hely** értékét.  Ebben a rövid útmutatóban az **USA keleti régiója** és a **sfmeshTutorial1RG** nevű csoport szerepel (ha a cégnél többen használják ugyanazt az előfizetést, válasszon egyedi erőforráscsoport-nevet).  Kattintson a **Létrehozás** elemre az erőforrás létrehozásához, majd térjen vissza a párbeszédpanelhez.

![A Service Fabric Mesh új párbeszédpanelje a Visual Studióban](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Miután visszatért a **Service Mesh-alkalmazás közzététele** párbeszédpanelhez, az **Azure Container Registry** területen válassza a **\<Create New Container Registry...>** (Tárolóregisztrációs adatbázis létrehozása) lehetőséget. A **Tárolóregisztrációs adatbázis** párbeszédpanelen használjon egyedi nevet a **Tárolóregisztrációs adatbázis neve** elemhez. Adjon meg egy **helyet** (ebben a rövid útmutatóban az **USA keleti régiója** szerepel). A legördülő listában válassza ki az előző lépésben létrehozott **Erőforráscsoportot**, például a **sfmeshTutorial1RG** nevűt. Állítsa **Alapszintűre** a **termékváltozatot**, majd a **Létrehozás** elemre kattintva térjen vissza a közzétételi párbeszédpanelhez.

![A Service Fabric Mesh új párbeszédpanelje a Visual Studióban](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

A Service Fabric Mesh-alkalmazás Azure-ban történő üzembe helyezéséhez kattintson a **Közzététel** elemre a közzétételi párbeszédpanelen.

Az Azure-ban történő első közzététel alkalmával továbbítani kell a docker-rendszerképet az Azure Container Registrybe (ACR), és ez a kép méretétől függően időbe telhet. Ugyanebben a projektben a további közzétételek már gyorsabbak lesznek. Az üzembe helyezés állapotának figyeléséhez a Visual Studio **Output** (Kimenet) ablak legördülő listájában válassza a **Service Fabric-eszközök** ablakot. Az üzembe helyezés befejeztével a **Service Fabric-eszközök** kimenete egy URL-cím formájában jeleníti meg az alkalmazás IP-címét és portját.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Nyisson meg egy webböngészőt, és a megadott URL-címen tekintse meg az Azure-ban futó webhelyet:

![Service Fabric Mesh-webalkalmazás futtatása](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a jelen rövid útmutatóban létrehozott erőforrásokra, törölje az összeset. Mivel az ACR és a Service Fabric Mesh szolgáltatás erőforrásaihoz már létrehozott egy új erőforráscsoportot, ezt az erőforráscsoportot nyugodtan törölheti, és ezzel egyszerűen törli az összes vele társított erőforrást is.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Az erőforráscsoportot az [Azure Portalról](https://portal.azure.com) is törölheti.

## <a name="next-steps"></a>További lépések

A Service Fabric Mesh-alkalmazások létrehozásáról és üzembe helyezésével kapcsolatos további információért lépjen tovább az oktatóanyagra.
> [!div class="nextstepaction"]
> [Többszolgáltatású webalkalmazás létrehozása, hibaelhárítása és üzembe helyezése a Service Fabric Mesh-ben](service-fabric-mesh-tutorial-create-dotnetcore.md)