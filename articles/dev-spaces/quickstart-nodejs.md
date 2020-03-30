---
title: 'Hibakeresés és iterálni a Kubernetes: Visual Studio kód & Node.js'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használhatja az Azure Dev Spaces és a Visual Studio Code segítségével a Node.js alkalmazások hibakeresését és gyors iterálni egy Node.js alkalmazást az Azure Kubernetes szolgáltatásban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 18171a2f8d13bfcf3df76b1453c39c59cab89d7c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240202"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-nodejs---azure-dev-spaces"></a>Rövid útmutató: Hibakeresés és műveletlebaj a Kubernetes en a Visual Studio-kóddal és a Node.js -Azure Dev Spaces-szel

Ebben a rövid útmutatóban az Azure Dev Spaces egy felügyelt Kubernetes-fürttel van beállítva, és a Visual Studio-kód Node.js alkalmazást használja a tárolókban lévő Node.js alkalmazás használatával iteratív módon fejlesztheti és hibakeresésre használhatja. Az Azure Dev Spaces segítségével hibakeresést és tesztelést az alkalmazás az Azure Kubernetes szolgáltatás (AKS) minimális fejlesztői gép beállítása. 

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [A Node.js legújabb verziója.](https://nodejs.org/download/)
- [Visual Studio kód](https://code.visualstudio.com/download).
- Az [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) bővítmény a Visual Studio-kódhoz.
- [Az Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes-szolgáltatásfürt létrehozása

AKS-fürtöt támogatott régióban kell [létrehoznia.][supported-regions] A következő parancsok létrehoznak egy *SamiresourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AKS-fürtöt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev spaces engedélyezése az AKS-fürtön

A `use-dev-spaces` paranccsal engedélyezheti a fejlesztői szóközöket az AKS-fürtön, és kövesse az utasításokat. A következő parancs engedélyezi a fejlesztői szóközöket a *MyResourceGroup* csoport *MyAKS* fürtén, és létrehoz egy *alapértelmezett* fejlesztői területet.

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

Nyissa meg a Visual Studio-kódot, válassza a **Fájl** lehetőséget, majd a **Megnyitás**lehetőséget, keresse meg a *fejlesztői tereket/mintákat/nodejs/getting-started/webfrontend* könyvtárat, és válassza a **Megnyitás**lehetőséget.

Most már meg van nyitva a *webfrontend* projekt a Visual Studio-kódban. Az alkalmazás futtatásához a fejlesztői térben, hozza létre a Docker és a Helm diagram eszközök az Azure Dev Spaces bővítmény a parancspaletta használatával.

A Parancspaletta Visual Studio-kódban való megnyitásához válassza **a Nézet,** majd **a Parancspaletta**lehetőséget. Kezdje el `Azure Dev Spaces` a gépelést, és válassza az **Azure Dev Spaces: Konfigurációs fájlok előkészítése az Azure Dev Spaces**számára lehetőséget.

![Konfigurációs fájlok előkészítése az Azure dev spaces-hez](./media/common/command-palette.png)

Ha a Visual Studio-kód a nyilvános végpont `Yes` konfigurálását is kéri, engedélyezze a nyilvános végpontot.

![Nyilvános végpont kiválasztása](media/common/select-public-endpoint.png)

Ez a parancs előkészíti a projekt futtatását az Azure Dev Spaces egy Dockerfile és Helm diagram létrehozásával. Ez is létrehoz egy *.vscode* könyvtár hibakeresési konfiguráció a gyökere a projekt.

> [!TIP]
> A [Dockerfile és helm diagram](how-dev-spaces-works-prep.md#prepare-your-code) a projekt az Azure Dev Spaces segítségével hozza létre és futtatja a kódot, de módosíthatja ezeket a fájlokat, ha módosítani szeretné, hogyan a projekt épül és fuss.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Kód létrehozása és futtatása a Kubernetes alkalmazásban a Visual Studio-kódból

Kattintson a bal oldalon a **Hibakeresés** ikonra, és felül válassza a **Launch Server (AZDS)** lehetőséget.

![Kiszolgáló indítása](media/get-started-node/debug-configuration-nodejs.png)

Ez a parancs létrehozza és futtatja a szolgáltatást az Azure Dev Spaces.This command builds and runs your service in Azure Dev Spaces. Az alsó **terminálablak** az Azure Dev Spaces szolgáltatást futtató szolgáltatás buildkimenetét és URL-címeit jeleníti meg. A **Hibakeresési konzol** megjeleníti a napló kimenetét.

> [!Note]
> Ha nem látja az Azure Dev Spaces parancsokat a **parancspalettán,** győződjön meg arról, hogy telepítette a [Visual Studio kódbővítményét](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)az Azure Dev Spaces számára. Ellenőrizze azt is, hogy megnyitotta-e a *dev-spaces/samples/nodejs/getting-started/webfrontend* könyvtárat a Visual Studio Code-ban.

A szolgáltatás a nyilvános URL megnyitásával láthatja.

Válassza **a Debug,** majd a **Debugging leállítása** lehetőséget a hibakereső leállításához.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának telepítéséhez frissítheti a projekt bármely fájlját, és újrafuttathatja az **Indítási kiszolgálót**. Példa:

1. Ha az alkalmazás továbbra is fut, válassza **a Hibakeresés** lehetőséget, majd a **Hibakeresés leállítása** lehetőséget a leállításához.
1. A [13-as `server.js` sor](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) frissítése a következőre:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Mentse a módosításokat.
1. Futtassa újra **az indítási kiszolgálót**.
1. Nyissa meg a futó szolgáltatást, és figyelje meg a módosításokat.
1. Válassza **a Debug,** majd **a Hibakeresés leállítása** lehetőséget az alkalmazás leállításához.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Töréspontok beállítása és használata hibakereséshez

Indítsa el a szolgáltatást a **Launch Server (AZDS)** segítségével.

Navigáljon vissza az Intéző nézetbe a **Nézet,** majd **az Intéző**lehetőség kiválasztásával. Nyissa meg *a server.js-t,* és kattintson valahol a 13-as vonalon, hogy a kurzort oda tegye. A töréspont beállításához nyomja le **az F9** billentyűt, vagy válassza **a Hibakeresés** lehetőséget, majd **kapcsolja be a Töréspontot**.

Nyissa meg a szolgáltatást a böngészőben, és észreveszi, hogy nem jelenik meg üzenet. Visszatérés a Visual Studio-kódhoz, és a 13-as sor figyelése ki van emelve. A beállított töréspont szüneteltette a szolgáltatást a 13-as sorban. A szolgáltatás folytatásához nyomja le az **F5 billentyűt,** vagy válassza **a Hibakeresés** lehetőséget, majd folytassa a **munkát.** Térjen vissza a böngészőbe, és figyelje meg, hogy az üzenet megjelenik.

A szolgáltatás Kubernetes-ben való futtatása közben egy hibakeresőcsatlakoztatva teljes hozzáféréssel rendelkezik a hibakeresési információkhoz, például a hívásveremhez, a helyi változókhoz és a kivételadatokhoz.

Távolítsa el a töréspontot úgy, hogy a kurzort a *server.js* 13-as sorára helyezi, és lenyomja az **F9 billentyűt**.

Válassza **a Debug,** majd a **Debugging leállítása** lehetőséget a hibakereső leállításához.

## <a name="update-code-from-visual-studio-code"></a>Kód frissítése a Visual Studio kódjából

Módosítsa a hibakeresési módot **a Kiszolgálóhoz (AZDS) való csatolásra,** és indítsa el a szolgáltatást:

![](media/get-started-node/attach-nodejs.png)

Ez a parancs létrehozza és futtatja a szolgáltatást az Azure Dev Spaces.This command builds and runs your service in Azure Dev Spaces. Emellett egy [nodemon](https://nodemon.io) folyamatot is elindít a szolgáltatás tárolójában, és hozzácsatolja a VS Code-ot. A *nodemon* folyamat lehetővé teszi az automatikus újraindítást a forráskód módosításakor, lehetővé téve a gyorsabb belső hurok fejlesztést a helyi gépen történő fejlesztéshez.

A szolgáltatás indítása után keresse meg azt a böngészővel, és használja azt.

Amíg a szolgáltatás fut, térjen vissza a VS-kódhoz, és frissítse a 13-as sort a *server.js*. Példa:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Mentse a fájlt, és térjen vissza a szolgáltatáshoz egy böngészőben. Lépjen kapcsolatba a szolgáltatással, és figyelje meg, hogy a frissített üzenet megjelenik.

Nodemon *nodemon*futtatása közben a csomópontfolyamat automatikusan újraindul, amint a rendszer észleli a kódmódosításokat. Ez az automatikus újraindítási folyamat hasonló a szolgáltatás helyi gépen történő szerkesztésének és újraindításának élményéhez, belső hurok fejlesztési élményt nyújtva.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások karbantartása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure Dev Spaces hogyan segít összetettebb alkalmazások fejlesztésében több tárolóközött, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést a kód különböző verzióival vagy ágaival való együttműködéssel különböző helyeken.

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-nodejs.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
