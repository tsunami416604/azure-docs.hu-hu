---
title: Fejlesztés a Kubernetes Azure fejlesztési szóközöket (Visual Studio Code) használatával a .NET Core használatával
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s
manager: jeconnoc
ms.openlocfilehash: 1c9c62dc4d674b8ce6f7c07e7426dbb0e1872685
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417989"
---
# <a name="quickstart-develop-with-net-core-on-kubernetes-using-azure-dev-spaces-visual-studio-code"></a>Gyors útmutató: Fejlesztés a Kubernetes Azure fejlesztési szóközöket (Visual Studio Code) használatával a .NET Core használatával

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Iteratív fejlesztés kód a Visual Studio Code és a parancssor használatával.
- A Visual Studio Code-ból a fejlesztési tárhely kódok hibakeresése.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [A Visual Studio Code telepítése](https://code.visualstudio.com/download).
- A [Azure fejlesztési tárolóhelyek](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) és [ C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) telepítve a Visual Studio Code-bővítmények.
- [Telepített Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Az Azure Kubernetes Service-fürt létrehozása

Az AKS-fürt létrehozásához szükséges egy [támogatott régió](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams). Az alábbi parancsokkal nevű erőforráscsoport létrehozása *MyResourceGroup* és a egy AKS-fürt nevű *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-count 1 --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az AKS-fürt az Azure fejlesztési tárolóhelyek engedélyezése

Használja a `use-dev-spaces` parancs fejlesztési szóközt engedélyezni az AKS-fürt, és kövesse az utasításokat. Az alábbi parancs segítségével fejlesztési tárolóhelyek a *MyAKS* a fürt a *MyResourceGroup* csoportból, és létrehoz egy *alapértelmezett* fejlesztési terület.

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

## <a name="get-sample-application-code"></a>Minta az alkalmazáskód letöltése

Ebben a cikkben fogja használni a [Azure fejlesztési tárolóhelyek mintaalkalmazás](https://github.com/Azure/dev-spaces) bemutatása, Azure-fejlesztési szóközzel.

Klónozza az alkalmazást a Githubról, és keresse meg a *fejlesztési-tárolóhelyek/samples/dotnetcore/első-lépések/webfrontend* könyvtár:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/dotnetcore/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Az alkalmazás előkészítése

Az alkalmazás futtatása a Kubernetes használatával hozza létre a Docker és a Helm-diagram eszközök a `azds prep` parancsot:

```cmd
azds prep --public
```

Futtatnia kell a `prep` parancsot a *fejlesztési-tárolóhelyek/samples/dotnetcore/első-lépések/webfrontend* directory átjáróadatok megfelelő létrehozása a Docker és a Helm-diagram eszközök.

## <a name="build-and-run-code-in-kubernetes"></a>Kód létrehozása és futtatása Kubernetesben

Hozhat létre, és futtathatja a kódot az AKS-a `azds up` parancsot:

```cmd
$ azds up
Synchronizing files...4s
Using dev space 'default' with target 'MyAKS'
Installing Helm chart...2s
Waiting for container image build...1m 43s
Building container image...
Step 1/12 : FROM microsoft/dotnet:2.2-sdk
Step 2/12 : ARG BUILD_CONFIGURATION=Debug
Step 3/12 : ENV ASPNETCORE_ENVIRONMENT=Development
Step 4/12 : ENV DOTNET_USE_POLLING_FILE_WATCHER=true
Step 5/12 : EXPOSE 80
Step 6/12 : WORKDIR /src
Step 7/12 : COPY ["webfrontend.csproj", "./"]
Step 8/12 : RUN dotnet restore "webfrontend.csproj"
Step 9/12 : COPY . .
Step 10/12 : RUN dotnet build --no-restore -c $BUILD_CONFIGURATION
Step 11/12 : RUN echo "exec dotnet run --no-build --no-launch-profile -c $BUILD_CONFIGURATION -- \"\$@\"" > /entrypoint.sh
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 3m 44s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Láthatja, hogy a szolgáltatás fut, nyissa meg a nyilvános URL-CÍMÉT, amely kimenetében megjelennek a `azds up` parancsot. Ebben a példában a nyilvános URL-je *http://webfrontend.1234567890abcdef1234.eus.azds.io/*.

Ha leállítja a `azds up` parancsának használatával *Ctrl + c*, a szolgáltatás továbbra is futtassa az aks-ben és a nyilvános URL-cím használható marad.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának üzembe helyezéséhez bármilyen fájl frissítése a projekt és futtassa újra a `azds up` parancsot. Példa:

1. Ha `azds up` továbbra is fut, nyomja le az *Ctrl + c*.
1. Frissítés [a 20. sor `Controllers/HomeController.cs` ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L20) való:
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Mentse a módosításokat.
1. Futtassa újra a `azds up` parancsot:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Lépjen a futó szolgáltatás, és kattintson *kapcsolatos*.
1. Figyelje meg a módosításokat.
1. Nyomja meg *Ctrl + c* leállítani a `azds up` parancsot.

## <a name="enable-visual-studio-code-to-debug-in-kubernetes"></a>A Kubernetes hibakeresése a Visual Studio Code engedélyezése

Nyissa meg a Visual Studio Code, kattintson a *fájl* majd *megnyitása...* , keresse meg a *fejlesztési-tárolóhelyek/samples/dotnetcore/első-lépések/webfrontend* könyvtárra, és kattintson *nyílt*.

Most már a *webfrontend* projektben nyissa meg a Visual Studio Code, amely ugyanazt a szolgáltatást használó futtatta a `azds up` parancsot. Az aks-ben a Visual Studio Code, használatával ellentétben ez a szolgáltatás hibakeresése `azds up` közvetlenül, elő kell készíteni a projektet a Visual Studio Code használatával kommunikálnak a fejlesztési tárhely.

A Visual Studio Code-ban a Parancskatalógus megnyitásához kattintson a *nézet* majd *Parancskatalógus*. Kezdje el beírni `Azure Dev Spaces` , majd kattintson a `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![](./media/common/command-palette.png)

Ez a parancs előkészíti a projekthez, hogy közvetlenül a Visual Studio Code az Azure fejlesztési tárolóhelyek futtatni. Is előállít egy *.vscode* könyvtárat, amely a projekt gyökerében konfigurációs hibakeresés.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Létrehozása és kód futtatása a Kubernetesben a Visual Studióból

Kattintson a *Debug* ikonra a bal oldalon, majd a *.NET Core indítása (AZDS)* tetején.

![](media/get-started-netcore/debug-configuration.png)

A parancs épít, és a szolgáltatás fut az Azure fejlesztési tárolóhelyek hibakeresési módban. A *terminálon* ablak alján látható a felépítési művelet kimenetében és URL-címek az Azure fejlesztési tárolóhelyek futó szolgáltatás. A *hibakeresési konzolt* napló kimenetét mutatja be.

> [!Note]
> Ha nem látja az Azure fejlesztési tárolóhelyek parancsok a *Parancskatalógus*, győződjön meg arról, hogy telepítette a [Visual Studio Code-bővítmény az Azure fejlesztési tárolóhelyek](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Ellenőrizze azt is nyitotta meg a *fejlesztési-tárolóhelyek/samples/dotnetcore/első-lépések/webfrontend* könyvtárat a Visual Studio Code-ot.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Beállítás és hibakeresési töréspontok használatával

Indítsa el a szolgáltatást a hibakeresés módban a *.NET Core indítása (AZDS)*.

Lépjen vissza a *Explorer* nézet kattintva *nézet* majd *Explorer*. Nyissa meg `Controllers/HomeController.cs` valahol kattintson a sor létezik helyezze a kurzort a 20. Állítson be egy töréspontot, nyomja le az *F9* , vagy kattintson *Debug* majd *töréspont*.

Egy böngészőben nyissa meg a szolgáltatást, és figyelje meg, hogy nem jelenik meg. Térjen vissza a Visual Studio Code-ot, és vizsgálja meg, 20 sor van kijelölve. A töréspont beállított sor 20 a szolgáltatás szünetel. A szolgáltatás folytatásához nyomja le az *F5* , vagy kattintson *Debug* majd *Folytatás*. Térjen vissza a böngészőben, és figyelje meg, hogy az üzenet jelenik meg.

Miközben fut a szolgáltatás a Kubernetes egy hibakeresővel, akkor a hibakeresési információkat, például a hívási verem, a helyi változókat és a kivétel adatai teljes hozzáféréssel rendelkezik.

A töréspont eltávolítása ehhez vigye a kurzort a 20 sor `Controllers/HomeController.cs` és lenyomásával *F9*.

## <a name="update-code-from-visual-studio-code"></a>A Visual Studio Code-ból kódjának frissítése

A szolgáltatás futása hibakeresési módban, frissítse a 20 sort a `Controllers/HomeController.cs`. Példa:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Mentse a fájlt. Kattintson a *Debug* majd *indítsa újra a hibakeresés* vagy a a *hibakeresési eszköztár*, kattintson a *indítsa újra a hibakeresés* gombra.

![](media/get-started-netcore/debug-action-refresh.png)

Egy böngészőben nyissa meg a szolgáltatást, és figyelje meg, hogy a frissített üzenet jelenik meg.

Újraépítése és a egy új tárolórendszerképet minden alkalommal, amikor a kód módosításokat végzett, az Azure fejlesztési tárolóhelyek növekményes újbóli üzembe helyezés helyett újrafordítja a kódot a Szerkesztés/debug gyorsabb hurkot biztosít a meglévő tárolón belül.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások törlése

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, Azure fejlesztési tárolóhelyek segítségével miként több tárolóra kiterjedő összetettebb alkalmazásokat fejleszthet, és egyszerűsítését által biztosított együttműködési környezettel fejlesztési különböző verziói vagy az ágak a kódot a különböző tárolóhelyek segítségével. 

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-netcore.md)
