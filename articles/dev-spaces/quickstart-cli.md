---
title: A Kubernetes Azure-fejlesztési szóközzel alkalmazás üzembe helyezése
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Az aks-en az Azure fejlesztési tárolóhelyek mikroszolgáltatások üzembe helyezése
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s
manager: gwallace
ms.openlocfilehash: 39fb7658140a2eda948cd0dc0e58d71b0b9a053b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706270"
---
# <a name="quickstart-develop-an-application-on-kubernetes-using-azure-dev-spaces"></a>Gyors útmutató: Az Azure-fejlesztési szóközzel Kubernetes-alkalmazás fejlesztése
Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Fejlesztés és a tárolók a parancssorból futtassa a kódot.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [Telepített Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Az Azure Kubernetes Service-fürt létrehozása

Az AKS-fürt létrehozásához szükséges egy [támogatott régió][supported-regions]. Az alábbi parancsokkal nevű erőforráscsoport létrehozása *MyResourceGroup* és a egy AKS-fürt nevű *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
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

Futtassa az alkalmazást az Azure fejlesztési szóközt, egy docker-fájl és a Helm-diagram van szüksége. Néhány nyelv esetében például [Java][java-quickstart], [.NET core][netcore-quickstart], és [Node.js][nodejs-quickstart], az Azure fejlesztési tárolóhelyek ügyfél eszközöket kell az összes eszköz hozhat létre. Számos más nyelv, például a Go, a PHP és Python az ügyfél eszközöket hozhat létre a Helm-diagram, mindaddig, amíg adhat meg egy érvényes docker-fájlban.

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

Láthatja, hogy a szolgáltatás fut, nyissa meg a nyilvános URL-CÍMÉT, amely kimenetében megjelennek a `azds up` parancsot. Ebben a példában a nyilvános URL-je *http://webfrontend.1234567890abcdef1234.eus.azds.io/* .

Ha leállítja a `azds up` parancsának használatával *Ctrl + c*, a szolgáltatás továbbra is futtassa az aks-ben és a nyilvános URL-cím használható marad.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának üzembe helyezéséhez bármilyen fájl frissítése a projekt és futtassa újra a `azds up` parancsot. Példa:

1. Ha `azds up` továbbra is fut, nyomja le az *Ctrl + c*.
1. Frissítés [a 13. sor `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) való:
    
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

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások törlése

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, Azure fejlesztési tárolóhelyek segítségével miként több tárolóra kiterjedő összetettebb alkalmazásokat fejleszthet, és egyszerűsítését által biztosított együttműködési környezettel fejlesztési különböző verziói vagy az ágak a kódot a különböző tárolóhelyek segítségével.

> [!div class="nextstepaction"]
> [Az Azure fejlesztési tárolóhelyek csoportos fejlesztése][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: about.md#supported-regions-and-configurations