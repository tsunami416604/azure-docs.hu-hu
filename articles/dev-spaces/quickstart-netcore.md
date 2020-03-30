---
title: 'Hibakeresés és iterálni a Kubernetes: Visual Studio Code & .NET Core'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használhatja az Azure Dev Spaces és a Visual Studio Code segítségével a .NET Core alkalmazások hibakeresését és gyors iterálni az Azure Kubernetes-szolgáltatásban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: b38562879fa67d7ee82e3251ea2fcaa57a2075d6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240209"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>Rövid útmutató: Hibakeresés és műveletjel a Kubernetesen: Visual Studio-kód és .NET Core – Azure dev spaces

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Iteratív módon kód kifejlesztése tárolókban a Visual Studio-kód használatával.
- Hibakeresés a fejlesztői térben a Visual Studio-kódból.

Az Azure Dev Spaces lehetővé teszi a hibakeresést és az ite):
- [Java és Visual Studio kód](quickstart-java.md)
- [Node.js és Visual Studio-kód](quickstart-nodejs.md)
- [.NET Core és Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [Visual Studio-kód telepítve](https://code.visualstudio.com/download).
- Az [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) és a [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) bővítmények Visual Studio-kód telepítve.
- [Az Azure CLI telepítve van.](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes-szolgáltatásfürt létrehozása

AKS-fürtöt támogatott régióban kell [létrehoznia.][supported-regions] Az alábbi parancsok létrehoznak egy *SamiresourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AKS-fürtöt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev spaces engedélyezése az AKS-fürtön

A `use-dev-spaces` paranccsal engedélyezheti a fejlesztői szóközöket az AKS-fürtön, és kövesse az utasításokat. Az alábbi parancs engedélyezi a Fejlesztői szóközöket a *MyResourceGroup* csoport *MyAKS* fürtjében, és létrehoz egy *alapértelmezett* fejlesztői területet.

> [!NOTE]
> A `use-dev-spaces` parancs az Azure Dev Spaces CLI-t is telepíti, ha még nincs telepítve. Az Azure Dev Spaces CLI nem telepíthető az Azure Cloud Shellben.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Mintaalkalmazás kódjának beszereznie

Ebben a cikkben az [Azure Dev Spaces mintaalkalmazás](https://github.com/Azure/dev-spaces) használatával bemutatja az Azure Dev Spaces használatával.

Klónozza az alkalmazást a GitHubról.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>A mintaalkalmazás előkészítése a Visual Studio-kódban

Nyissa meg a Visual Studio-kódot, kattintson a *Fájl,* majd *a Megnyitás...* parancsra, keresse meg a *fejlesztői tereket/mintákat/dotnetcore/getting-started/webfrontend* könyvtárat, és kattintson a *Megnyitás gombra.*

Most már meg van nyitva a *webfrontend* projekt a Visual Studio-kódban. Az alkalmazás futtatásához a fejlesztői térben, hozza létre a Docker és a Helm diagram eszközök az Azure Dev Spaces bővítmény a parancspaletta használatával.

A Parancspaletta Visual Studio-kódban való megnyitásához kattintson a *Nézet,* majd *a Parancspaletta parancsra.* Kezdjen `Azure Dev Spaces` el gépelni, és kattintson a gombra. `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`

![Konfigurációs fájlok előkészítése az Azure dev spaces-hez](./media/common/command-palette.png)

Ha a Visual Studio-kód a nyilvános végpont `Yes` konfigurálását is kéri, engedélyezze a nyilvános végpontot.

![Nyilvános végpont kiválasztása](media/common/select-public-endpoint.png)

Ez a parancs előkészíti a projekt futtatását az Azure Dev Spaces egy Dockerfile és Helm diagram létrehozásával. Ez is létrehoz egy *.vscode* könyvtár hibakeresési konfiguráció a gyökere a projekt.

> [!TIP]
> A [Dockerfile és helm diagram](how-dev-spaces-works-prep.md#prepare-your-code) a projekt azure dev spaces a kód létrehozásához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné, hogyan a projekt épül, és fut.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Kód létrehozása és futtatása a Kubernetes alkalmazásban a Visual Studio-kódból

Kattintson a bal oldali *Debug* ikonra, majd a tetején kattintson a *.NET Core Launch (AZDS)* elemre.

![](media/get-started-netcore/debug-configuration.png)

Ez a parancs létrehozza és futtatja a szolgáltatást az Azure Dev Spaces hibakeresési módban. A *terminál* ablak alján mutatja a build kimeneti és URL-címeket a szolgáltatás fut az Azure Dev Spaces. A *Hibakeresési konzol* megjeleníti a napló kimenetét.

> [!Note]
> Ha nem látja az Azure Dev Spaces parancsokat a *parancspalettán,* győződjön meg arról, hogy telepítette a [Visual Studio kódbővítményét](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)az Azure Dev Spaces számára. Ellenőrizze azt is, hogy megnyitotta-e a *dev-spaces/samples/dotnetcore/getting-started/webfrontend* könyvtárat a Visual Studio Code-ban.

A szolgáltatás a nyilvános URL megnyitásával láthatja.

> [!Note]
> Kezdetben a nyilvános URL-cím *hibás átjáróhibát* jelezhet. Várjon néhány másodpercet, mielőtt frissíti a weblapot, és látnia kell, hogy a szolgáltatás fut.

Kattintson a *Hibakeresés,* majd a *Hibakeresés leállítása gombra* a hibakereső leállításához.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának telepítéséhez frissítheti a projekt bármely fájlját, és újrafuttathatja a *.NET Core Launch (AZDS) programot.* Példa:

1. Ha az alkalmazás még fut, kattintson a *Hibakeresés,* majd *a Hibakeresés leállítása gombra* a leállításához.
1. A [22-es `Controllers/HomeController.cs` sor](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22) frissítése a következőhöz:
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Mentse a módosításokat.
1. Futtassa újra *a .NET Core Launch (AZDS) című sorozatot.*
1. Nyissa meg a futó szolgáltatást, és kattintson *a Be – gombra.*
1. Figyelje meg a változásokat.
1. Kattintson *a Debug,* majd *a Hibakeresés leállítása gombra* az alkalmazás leállításához.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Töréspontok beállítása és használata hibakereséshez

Indítsa el a szolgáltatást hibakeresési módban a *.NET Core Launch (AZDS)* segítségével.

Navigáljon vissza az *Intéző* nézetbe a *Nézet,* majd *az Intéző*elemre kattintva. Nyissa `Controllers/HomeController.cs` meg és kattintson valahol a 22-es vonalon, hogy a kurzort oda tegye. Ha az *F9* leadott töréspontot szeretné beállítani, vagy kattintson *a Hibakeresés gombra,* majd *a Töréspont váltása gombra*.

Nyissa meg a szolgáltatást a böngészőben, és észreveszi, hogy nem jelenik meg üzenet. Visszatérés a Visual Studio-kódhoz, és a 20-as sor figyelése ki van emelve. A beállított töréspont szüneteltette a szolgáltatást a 20-as sorban. A szolgáltatás folytatásához nyomja le az *F5* gombot, vagy kattintson *a Hibakeresés gombra,* majd *a Folytatás gombra.* Térjen vissza a böngészőbe, és figyelje meg, hogy az üzenet megjelenik.

A szolgáltatás Kubernetes-ben való futtatása közben egy hibakeresőcsatlakoztatva teljes hozzáféréssel rendelkezik a hibakeresési információkhoz, például a hívásveremhez, a helyi változókhoz és a kivételadatokhoz.

Távolítsa el a töréspontot azáltal, hogy `Controllers/HomeController.cs` a kurzort a 22-es vonalon helyezi el, és az *F9-et*üti.

## <a name="update-code-from-visual-studio-code"></a>Kód frissítése a Visual Studio kódjából

Amíg a szolgáltatás hibakeresési módban fut, frissítse `Controllers/HomeController.cs`a 22-es sort a alkalmazásban. Példa:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Mentse a fájlt. Kattintson a *Hibakeresés,* majd a *Hibakeresés újraindítása gombra,* vagy a *Hibakeresés eszköztáron*kattintson a *Hibakeresés újraindítása gombra.*

![](media/common/debug-action-refresh.png)

Nyissa meg a szolgáltatást egy böngészőben, és figyelje meg, hogy megjelenik a frissített üzenet.

Ahelyett, hogy újralétre, és újraüzembe egy új tárolórendszerkép minden egyes alkalommal kód szerkesztések készülnek, az Azure Dev Spaces növekményesen újrafordítja a kódot a meglévő tárolón belül, hogy egy gyorsabb szerkesztési/hibakeresési hurok.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások karbantartása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure Dev Spaces hogyan segít összetettebb alkalmazások fejlesztésében több tárolóközött, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést a kód különböző verzióival vagy ágaival való együttműködéssel különböző helyeken. 

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-netcore.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
