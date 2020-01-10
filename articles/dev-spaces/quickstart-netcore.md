---
title: 'Hibakeresés és iteráció a Kubernetes-on: Visual Studio Code & .NET Core'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure dev Spaces és a Visual Studio Code egy .NET Core-alkalmazás hibakereséséhez és gyors megismétléséhez az Azure Kubernetes Service-ben
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 29d601b9996b9cdf7825e367cae04521d4c4bc4a
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771087"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>Gyors útmutató: Hibakeresés és iteráció a Kubernetes-on: Visual Studio Code és .NET Core – Azure dev Spaces

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- A Visual Studio Code használatával iteratív fejleszthet a tárolókban.
- Hibakeresés a kód alapján a fejlesztői térben a Visual Studio Code-ból.

Az Azure dev Spaces Emellett lehetővé teszi a hibakeresést és a közelítést a használatával:
- [A Java és a Visual Studio Code](quickstart-java.md)
- [Node. js és Visual Studio Code](quickstart-nodejs.md)
- [A .NET Core és a Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).
- A [Visual Studio Code telepítése megtörtént](https://code.visualstudio.com/download).
- A Visual Studio Code- [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) hoz készült [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) és Extensions.
- [Telepített Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service-fürt létrehozása

Létre kell hoznia egy AK-fürtöt egy [támogatott régióban][supported-regions]. Az alábbi parancsok létrehoznak egy *MyResourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AK-fürtöt.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev-helyek engedélyezése az AK-fürtön

A `use-dev-spaces` parancs használatával engedélyezze a fejlesztői szóközöket az AK-fürtön, és kövesse az utasításokat. Az alábbi parancs lehetővé teszi a dev Spaces használatát a *MyAKS* -fürtön a *MyResourceGroup* csoportban, és létrehoz egy *alapértelmezett* fejlesztői helyet.

> [!NOTE]
> A `use-dev-spaces` parancs az Azure dev Spaces CLI-t is telepíti, ha még nincs telepítve. Az Azure dev Spaces CLI nem telepíthető a Azure Cloud Shell.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Minta alkalmazás kódjának beolvasása

Ebben a cikkben az [Azure dev Spaces minta alkalmazásával](https://github.com/Azure/dev-spaces) mutatjuk be az Azure dev Spaces használatát.

Az alkalmazás klónozása a GitHubról.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>A minta alkalmazás előkészítése a Visual Studio Code-ban

Nyissa meg a Visual Studio Code-ot, kattintson a *fájl* , majd a *Megnyitás...* lehetőségre, keresse meg a *dev-Spaces/Samples/dotnetcore/Getting-Started/webfrontend* könyvtárat, és kattintson a *Megnyitás*gombra.

Most már megnyitotta a *webfrontend* -projektet a Visual Studio Code-ban. Az alkalmazás fejlesztői tárhelyen való futtatásához a Docker és a Helm chart objektumokat a parancs paletta Azure dev Spaces bővítményének használatával hozhatja ki.

A parancs paletta a Visual Studio Code-ban való megnyitásához kattintson a *nézet* , majd a *parancs paletta*elemre. Kezdje el begépelni `Azure Dev Spaces` és kattintson a `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`elemre.

![Konfigurációs fájlok előkészítése az Azure dev Spaces szolgáltatáshoz](./media/common/command-palette.png)

Ha a Visual Studio Code azt is kéri, hogy konfigurálja a nyilvános végpontot, válassza a `Yes` lehetőséget egy nyilvános végpont engedélyezéséhez.

![Nyilvános végpont kiválasztása](media/common/select-public-endpoint.png)

Ez a parancs előkészíti a projektet az Azure dev Spaces-ben való futtatásra egy Docker és egy Helm diagram létrehozásával. Létrehoz egy *. vscode* könyvtárat is, amely hibakeresési konfigurációval rendelkezik a projekt gyökerében.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Kód létrehozása és futtatása a Kubernetes a Visual studióból

Kattintson a bal oldali *hibakeresés* ikonra, és kattintson a felül található *.net Core Launch (AZDS)* elemre.

![](media/get-started-netcore/debug-configuration.png)

Ez a parancs hibakeresési módban létrehozza és futtatja a szolgáltatást az Azure dev Spaces szolgáltatásban. Az alján található *Terminálablak* megjeleníti az Azure dev Spaces szolgáltatást futtató szolgáltatáshoz tartozó Build kimenetet és URL-címeket. A *hibakeresési konzol* megjeleníti a napló kimenetét.

> [!Note]
> Ha nem látja az Azure dev Spaces-parancsokat a *parancssorban*, győződjön meg arról, hogy telepítette a [Visual Studio Code bővítményt az Azure dev Spaces szolgáltatáshoz](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Győződjön meg arról is, hogy megnyitotta a *dev-Spaces/Samples/dotnetcore/Getting-Started/webfrontend* könyvtárat a Visual Studio Code-ban.

A szolgáltatás futását a nyilvános URL-cím megnyitásával tekintheti meg.

A hibakereső leállításához kattintson a *hibakeresés* gombra, majd *állítsa le a hibakeresést* .

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának üzembe helyezéséhez frissítheti a projektben lévő összes fájlt, és újra futtathatja a *.net Core launcht (AZDS)* . Példa:

1. Ha az alkalmazás még fut, kattintson a *hibakeresés* lehetőségre, majd állítsa le a *hibakeresést* .
1. [Az `Controllers/HomeController.cs`22. sorának frissítése a következőre](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22) :
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Mentse a módosításokat.
1. Futtassa újra a *.net Core launcht (AZDS)* .
1. Navigáljon a futó szolgáltatáshoz, és kattintson *a névjegy*elemre.
1. Figyelje meg a módosításokat.
1. Az alkalmazás leállításához kattintson a *hibakeresés* gombra, majd állítsa le a *hibakeresést* .

## <a name="setting-and-using-breakpoints-for-debugging"></a>Töréspontok beállítása és használata hibakereséshez

Indítsa el a szolgáltatást hibakeresési módban a *.net Core Launch (AZDS)* használatával.

A *nézet* , majd az *Intéző*elemre kattintva térjen vissza a *tallózó* nézethez. Nyissa meg `Controllers/HomeController.cs`, és kattintson a 22. sorban a kurzorra. Ha a töréspontot az *F9 billentyűre* szeretné beállítani, vagy kattintson a *hibakeresés* lehetőségre, majd a *töréspontot*.

Nyissa meg a szolgáltatást egy böngészőben, és figyelje meg, hogy nem jelenik meg üzenet. Térjen vissza a Visual Studio Code-ba, és figyelje meg, hogy a 20. sor ki van emelve. A beállított töréspont szüneteltette a szolgáltatást a 20. sorban. A szolgáltatás folytatásához nyomja meg az *F5 billentyűt* , vagy kattintson a *hibakeresés* gombra, és *folytassa a művelettel*. Térjen vissza a böngészőhöz, és figyelje meg, hogy az üzenet most megjelenik.

A szolgáltatás Kubernetes való futtatásakor a hibakeresőhöz csatolva teljes hozzáférése van a hibakeresési adatokhoz, például a hívási verem, a helyi változók és a kivételek adataihoz.

Távolítsa el a töréspontot úgy, hogy a kurzort a 22-es vonalra helyezi a `Controllers/HomeController.cs` és az *F9 billentyűt*.

## <a name="update-code-from-visual-studio-code"></a>Kód frissítése a Visual Studio Code-ból

Amíg a szolgáltatás hibakeresési módban fut, frissítse `Controllers/HomeController.cs`a 22-es sort. Példa:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Mentse a fájlt. Kattintson a *hibakeresés* elemre, majd *indítsa újra a hibakeresést* , vagy a hibakeresés *eszköztáron*kattintson a *hibakeresés újraindítása* gombra.

![](media/common/debug-action-refresh.png)

Nyissa meg a szolgáltatást egy böngészőben, és figyelje meg, hogy a frissített üzenet jelenik meg.

Az új Container-lemezképek újraépítése és újratelepítése helyett az Azure dev Spaces fokozatosan újrafordítja a kódot a meglévő tárolóban, így gyorsabb szerkesztési/hibakeresési hurkot biztosít.

## <a name="clean-up-your-azure-resources"></a>Azure-erőforrások karbantartása

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogy az Azure dev Spaces hogyan segíti az összetettebb alkalmazások fejlesztését több tárolóban, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést, ha a kód különböző verzióival vagy ágaival dolgozik a különböző helyeken. 

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-netcore.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
