---
title: Alkalmazás fejlesztése a Kubernetes-en
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használhatja az Azure Dev Spaces-t és a parancssort egy alkalmazás fejlesztéséhez az Azure Kubernetes szolgáltatáson
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8ee5cba06d9a526640d9057ee88a681d46392f4f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239699"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>Rövid útmutató: Alkalmazás fejlesztése a Kubernetes en – Azure Dev Spaces
Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Kód kifejlesztése és futtatása tárolókban a parancssor használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
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

Klónozza az alkalmazást a GitHubról, és navigáljon a *fejlesztői terekbe/mintákba/nodejs/getting-started/webfrontend* könyvtárba:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>A kérelem előkészítése

Az alkalmazás Azure Dev Spaces futtatásához dockerfile és helm diagramra van szükség. Egyes nyelvek, például a [Java][java-quickstart], [.NET core][netcore-quickstart]és [node.js,][nodejs-quickstart]az Azure Dev Spaces ügyféleszköz-készítési hozhat létre az összes szükséges eszközöket. Sok más nyelvek, például a Go, php és Python, az ügyfél-eszközkészítés generálhatja a Helm diagram, amíg meg tudja adni egy érvényes Dockerfile.

Hozza létre a Docker- és Helm-diagram eszközeit `azds prep` az alkalmazás Kubernetes-ben való futtatásához a parancs használatával:

```cmd
azds prep --enable-ingress
```

A Docker- és Helm-diagrameszközök megfelelő létrehozásához a parancsot a `prep` *fejlesztési-terek/samples/nodejs/getting-started/webfrontend* könyvtárból kell futtatnia.

> [!TIP]
> A `prep` parancs [docker-fájl- és helmdiagramot](how-dev-spaces-works-prep.md#prepare-your-code) próbál létrehozni a projekthez. Az Azure Dev Spaces ezeket a fájlokat használja a kód létrehozásához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné a projekt felépítését és futtatását.

## <a name="build-and-run-code-in-kubernetes"></a>Kód létrehozása és futtatása Kubernetesben

A kód létrehozása és futtatása `azds up` az AKS-ben a következő paranccsal:

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

A futó szolgáltatás a nyilvános URL megnyitásával érhető el, `azds up` amely a parancs kimenetében jelenik meg. Ebben a példában a *http://webfrontend.1234567890abcdef1234.eus.azds.io/* nyilvános URL-cím .

> [!NOTE]
> Amikor futás `azds up`közben a szolgáltatáshoz navigál, a HTTP-kérelem nyomkövetései is megjelennek a `azds up` parancs kimenetén. Ezek a nyomok segíthetnek a szolgáltatás hibaelhárításában és hibakeresésében. Ezeket a nyomkövetéseket a futás során `--disable-http-traces` letilthatja. `azds up`

Ha a `azds up` *Ctrl+c billentyűkombinációval*leállítja a parancsot, a szolgáltatás továbbra is futni fog az AKS-ben, és a nyilvános URL továbbra is elérhető marad.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának telepítéséhez frissítheti a projekt bármely `azds up` fájlját, és újra futtathatja a parancsot. Példa:

1. Ha `azds up` még fut, nyomja *le a Ctrl+c billentyűkombinációt.*
1. A [13-as `server.js` sor](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) frissítése a következőre:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Mentse a módosításokat.
1. Futtassa `azds up` újra a parancsot:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Nyissa meg a futó szolgáltatást, és figyelje meg a módosításokat.
1. A *ctrl+c billentyűkombinációval* állítsa le a `azds up` parancsot.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások karbantartása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure Dev Spaces hogyan segít összetettebb alkalmazások fejlesztésében több tárolóközött, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést a kód különböző verzióival vagy ágaival való együttműködéssel különböző helyeken.

> [!div class="nextstepaction"]
> [Csapatfejlesztés az Azure Dev Spaces-ben][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service