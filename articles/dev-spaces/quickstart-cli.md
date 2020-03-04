---
title: Alkalmazás fejlesztése a Kubernetes-on
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure dev Spaces és a parancssor az alkalmazások fejlesztéséhez az Azure Kubernetes Service-ben
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 974f0039bb16dc685bb056e279df63933e358edd
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78245252"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>Gyors útmutató: alkalmazás fejlesztése a Kubernetes-ben – Azure dev Spaces
Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- A parancssor használatával fejlesszen és futtasson kódot a tárolókban.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [Telepített Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service-fürt létrehozása

Létre kell hoznia egy AK-fürtöt egy [támogatott régióban][supported-regions]. Az alábbi parancsok létrehoznak egy *MyResourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AK-fürtöt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev-helyek engedélyezése az AK-fürtön

A `use-dev-spaces` parancs használatával engedélyezze a fejlesztői szóközöket az AK-fürtön, és kövesse az utasításokat. Az alábbi parancs lehetővé teszi a dev Spaces használatát a *MyAKS* -fürtön a *MyResourceGroup* csoportban, és létrehoz egy *alapértelmezett* fejlesztői helyet.

> [!NOTE]
> A `use-dev-spaces` parancs az Azure dev Spaces CLI-t is telepíti, ha még nincs telepítve. Az Azure dev Spaces CLI nem telepíthető a Azure Cloud Shell.

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

## <a name="get-sample-application-code"></a>Minta alkalmazás kódjának beolvasása

Ebben a cikkben az [Azure dev Spaces minta alkalmazásával](https://github.com/Azure/dev-spaces) mutatjuk be az Azure dev Spaces használatát.

Az alkalmazás klónozása a GitHubról, és a *dev-Spaces/Samples/NodeJS/Getting-Started/webfrontend* könyvtárba való belépés:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Az alkalmazás előkészítése

Ahhoz, hogy alkalmazást futtasson az Azure dev Spaces szolgáltatásban, szüksége lesz egy Docker és egy Helm-diagramra. Bizonyos nyelveken (például a [Java][java-quickstart], a [.net Core][netcore-quickstart]és a [Node. js][nodejs-quickstart]esetében) az Azure dev Spaces Client Tooling az összes szükséges eszközt képes létrehozni. Számos más nyelv, például a go, a PHP és a Python esetében az ügyfél-eszközkészlet létrehozhatja a Helm diagramot, feltéve, hogy érvényes Docker ad meg.

A Docker és a Helm diagram eszközeinek előállítása az alkalmazás Kubernetes való futtatásához a `azds prep` parancs használatával:

```cmd
azds prep --enable-ingress
```

A Docker és a Helm diagram eszközeinek megfelelő létrehozásához futtatnia kell a `prep` parancsot a *dev-Spaces/Samples/NodeJS/Getting-Started/webfrontend* könyvtárból.

> [!TIP]
> A `prep` parancs megkísérli [egy Docker és egy Helm-diagram](how-dev-spaces-works.md#prepare-your-code) létrehozását a projekthez. Az Azure dev Spaces ezeket a fájlokat használja a kód összeállításához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné a projekt felépítésének és futtatásának módját.

## <a name="build-and-run-code-in-kubernetes"></a>Kód létrehozása és futtatása Kubernetesben

Hozza létre és futtassa a kódot az AK-ban a `azds up` parancs használatával:

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

A szolgáltatás futását a `azds up` parancs kimenetében megjelenő nyilvános URL-cím megnyitásával tekintheti meg. Ebben a példában a nyilvános URL-cím *http://webfrontend.1234567890abcdef1234.eus.azds.io/* .

> [!NOTE]
> Amikor `azds up`futtatása közben navigál a szolgáltatáshoz, a rendszer a HTTP-kérelmek nyomkövetését is megjeleníti a `azds up` parancs kimenetében. Ezek a Nyomkövetések segítenek a szolgáltatás hibaelhárításában és hibakeresésében. A nyomkövetéseket letilthatja a `--disable-http-traces` használatával `azds up`futtatásakor.

Ha leállítja a `azds up` parancsot a *CTRL + c billentyűkombinációval*, a szolgáltatás továbbra is az AK-ban fut, és a nyilvános URL-cím továbbra is elérhető marad.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának üzembe helyezéséhez frissítheti a projektben lévő összes fájlt, és újra futtathatja a `azds up` parancsot. Például:

1. Ha `azds up` még fut, nyomja le a *CTRL + c*billentyűkombinációt.
1. [A `server.js`13. sorának frissítése a következőre](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) :
    
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

1. Navigáljon a futó szolgáltatáshoz, és figyelje meg a módosításokat.
1. Nyomja le a *CTRL + c* billentyűkombinációt az `azds up` parancs leállításához.

## <a name="clean-up-your-azure-resources"></a>Azure-erőforrások karbantartása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogy az Azure dev Spaces hogyan segíti az összetettebb alkalmazások fejlesztését több tárolóban, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést, ha a kód különböző verzióival vagy ágaival dolgozik a különböző helyeken.

> [!div class="nextstepaction"]
> [Csoportmunka az Azure fejlesztői Spaces szolgáltatásban][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service