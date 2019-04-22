---
title: Fejlesztés node.js nyelven, a Kubernetes Azure fejlesztési tárolóhelyek használatával
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Gyors fejlesztés a tárolók, mikroszolgáltatások és Node.js az Azure-ban
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s
manager: jeconnoc
ms.openlocfilehash: bc18a06405c0fe620136642a409df576c8e8d8b3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361711"
---
# <a name="quickstart-develop-with-nodejs-on-kubernetes-using-azure-dev-spaces"></a>Gyors útmutató: Fejlesztés node.js nyelven, a Kubernetes Azure fejlesztési tárolóhelyek használatával

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Iteratív fejlesztés kód a Visual Studio Code és a parancssor használatával.
- A Visual Studio Code-ból a fejlesztési tárhely kódok hibakeresése.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [A Visual Studio Code telepítése](https://code.visualstudio.com/download).
- A [Azure fejlesztési tárolóhelyek](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) telepítve a Visual Studio Code-bővítmény.
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

Klónozza az alkalmazást a Githubról, és keresse meg a *fejlesztési-tárolóhelyek/samples/nodejs/első-lépések/webfrontend* könyvtár:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Az alkalmazás előkészítése

Az alkalmazás futtatása a Kubernetes használatával hozza létre a Docker és a Helm-diagram eszközök a `azds prep` parancsot:

```cmd
azds prep --public
```

Futtatnia kell a `prep` parancsot a *fejlesztési-tárolóhelyek/samples/nodejs/első-lépések/webfrontend* directory átjáróadatok megfelelő létrehozása a Docker és a Helm-diagram eszközök.

## <a name="build-and-run-code-in-kubernetes"></a>Kód létrehozása és futtatása Kubernetesben

Hozhat létre, és futtathatja a kódot az AKS-a `azds up` parancsot:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
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
1. Frissítés [a 10. sor `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L10) való:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
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

1. Keresse meg a futó szolgáltatás, és vizsgálja meg a módosításokat.
1. Nyomja meg *Ctrl + c* leállítani a `azds up` parancsot.

## <a name="initialize-code-for-debugging-in-kubernetes-with-visual-studio-code"></a>Hibakeresés a Visual Studio Code-dal Kubernetes kódját inicializálása

Nyissa meg a Visual Studio Code, kattintson a *fájl* majd *megnyitása...* , keresse meg a *fejlesztési-tárolóhelyek/samples/nodejs/első-lépések/webfrontend* könyvtárra, és kattintson *nyílt*.

Most már a *webfrontend* projektben nyissa meg a Visual Studio Code, amely ugyanazt a szolgáltatást használó futtatta a `azds up` parancsot. Az aks-ben a Visual Studio Code, használatával ellentétben ez a szolgáltatás hibakeresése `azds up` közvetlenül, elő kell készíteni a projektet a Visual Studio Code használatával kommunikálnak a fejlesztési tárhely.

A Visual Studio Code-ban a Parancskatalógus megnyitásához kattintson a *nézet* majd *Parancskatalógus*. Kezdje el beírni `Azure Dev Spaces` , majd kattintson a `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![](./media/common/command-palette.png)

Ez a parancs előkészíti a projekthez, hogy közvetlenül a Visual Studio Code az Azure fejlesztési tárolóhelyek futtatni. Is előállít egy *.vscode* könyvtárat, amely a projekt gyökerében konfigurációs hibakeresés.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Létrehozása és kód futtatása a Kubernetesben Visual Studio Code-ból

Kattintson a *Debug* ikonra a bal oldalon, majd a *indítsa el a kiszolgáló (AZDS)* tetején.

![](media/get-started-node/debug-configuration-nodejs.png)

A parancs épít, és a szolgáltatás fut az Azure fejlesztési tárolóhelyek hibakeresési módban. A *terminálon* ablak alján látható a felépítési művelet kimenetében és URL-címek az Azure fejlesztési tárolóhelyek futó szolgáltatás. A *hibakeresési konzolt* napló kimenetét mutatja be.

> [!Note]
> Ha nem látja az Azure fejlesztési tárolóhelyek parancsok a *Parancskatalógus*, győződjön meg arról, hogy telepítette a [Visual Studio Code-bővítmény az Azure fejlesztési tárolóhelyek](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Ellenőrizze azt is nyitotta meg a *fejlesztési-tárolóhelyek/samples/nodejs/első-lépések/webfrontend* könyvtárat a Visual Studio Code-ot.

Kattintson a *Debug* majd *hibakeresés leállításához* a hibakereső leállítása.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Beállítás és hibakeresési töréspontok használatával

A szolgáltatás használatának megkezdéséhez *indítsa el a kiszolgáló (AZDS)*.

Lépjen vissza a *Explorer* nézet kattintva *nézet* majd *Explorer*. Nyissa meg `server.js` valahol kattintson a sor létezik helyezze a kurzort a 10. Állítson be egy töréspontot, nyomja le az *F9* , vagy kattintson *Debug* majd *töréspont*.

Egy böngészőben nyissa meg a szolgáltatást, és figyelje meg, hogy nem jelenik meg. Térjen vissza a Visual Studio Code-ot, és vizsgálja meg, a 10. sornak ki van jelölve. A töréspont be 10-es sor a szolgáltatás szünetel. A szolgáltatás folytatásához nyomja le az *F5* , vagy kattintson *Debug* majd *Folytatás*. Térjen vissza a böngészőben, és figyelje meg, hogy az üzenet jelenik meg.

Miközben fut a szolgáltatás a Kubernetes egy hibakeresővel, akkor a hibakeresési információkat, például a hívási verem, a helyi változókat és a kivétel adatai teljes hozzáféréssel rendelkezik.

A töréspont eltávolítása ehhez vigye a kurzort 10. sorban a `server.js` és lenyomásával *F9*.

Kattintson a *Debug* majd *hibakeresés leállításához* a hibakereső leállítása.

## <a name="update-code-from-visual-studio-code"></a>A Visual Studio Code-ból kódjának frissítése

Módosítsa a hibakeresési módot *csatlakoztatása a kiszolgálóhoz (AZDS)* indítsa el a szolgáltatást:

![](media/get-started-node/attach-nodejs.png)

A parancs épít, és a szolgáltatás fut az Azure fejlesztési szóközöket. Is elindítja egy [nodemon](https://nodemon.io) feldolgozása a szolgáltatásban tároló és a VS Code rendeli hozzá. A *nodemon* folyamat az automatikus újraindításokat lehetővé teszi a forrás kód végrehajtott módosítások, gyorsabb belső ciklus fejlesztési hasonló fejlesztéséhez a helyi gépen engedélyezése.

A szolgáltatás elindulása után a böngésző használatával keresse meg és kezelheti azt.

A szolgáltatás futása térjen vissza a VS Code és a frissítés sor 10 `server.js`. Példa:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Mentse a fájlt, és térjen vissza a szolgáltatás egy böngészőben. A szolgáltatás használata, és figyelje meg, hogy a frissített üzenet jelenik meg.

Futtatás közben *nodemon*, a csomópont folyamat automatikusan újraindul, amint kódváltozások észlel. Az automatikus újraindítás folyamatban hasonló a szerkesztési és újraindítani a szolgáltatást a helyi gépén, egy belső ciklus fejlesztési biztosítása érdekében.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások törlése

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, Azure fejlesztési tárolóhelyek segítségével miként több tárolóra kiterjedő összetettebb alkalmazásokat fejleszthet, és egyszerűsítését által biztosított együttműködési környezettel fejlesztési különböző verziói vagy az ágak a kódot a különböző tárolóhelyek segítségével.

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-nodejs.md)
