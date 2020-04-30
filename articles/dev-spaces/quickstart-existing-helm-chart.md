---
title: Alkalmazás fejlesztése meglévő Helm-diagrammal a Kubernetes-on
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure dev Spaces és a parancssor egy olyan alkalmazás fejlesztéséhez, amely egy meglévő Helm-diagrammal rendelkezik az Azure Kubernetes Service-ben
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: e767b1ade2a80882ee33ff1fdd718c691dcefcf3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82033563"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Gyors útmutató: alkalmazás fejlesztése meglévő Helm-diagrammal a Kubernetes-ben – Azure dev Spaces
Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Futtasson egy alkalmazást egy meglévő Helm-diagrammal az AK-ban az Azure dev Spaces használatával a parancssorban.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
- Az [Azure CLI telepítve van](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service-fürt létrehozása

Létre kell hoznia egy AK-fürtöt egy [támogatott régióban][supported-regions]. Az alábbi parancsok létrehoznak egy *MyResourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AK-fürtöt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev-helyek engedélyezése az AK-fürtön

A `use-dev-spaces` parancs használatával engedélyezze a fejlesztői szóközöket az AK-fürtön, és kövesse az utasításokat. Az alábbi parancs lehetővé teszi a dev Spaces használatát a *MyAKS* -fürtön a *MyResourceGroup* csoportban, és létrehoz egy *dev*nevű fejlesztői helyet.

> [!NOTE]
> A `use-dev-spaces` parancs az Azure dev SPACEs CLI-t is telepíti, ha még nincs telepítve. Az Azure dev Spaces CLI nem telepíthető a Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Minta alkalmazás kódjának beolvasása

Ebben a cikkben az [Azure dev Spaces minta alkalmazásával](https://github.com/Azure/dev-spaces) mutatjuk be az Azure dev Spaces használatát.

Az alkalmazás klónozása a GitHubról, majd a *dev-Spaces/Samples/Python/első lépések/webfrontend* könyvtárba való belépés:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Az alkalmazás előkészítése

Ahhoz, hogy alkalmazást futtasson az Azure dev Spaces szolgáltatásban, szüksége lesz egy Docker és egy Helm-diagramra. Bizonyos nyelveken (például a [Java][java-quickstart], a [.net Core][netcore-quickstart]és a [Node. js][nodejs-quickstart]esetében) az Azure dev Spaces Client Tooling az összes szükséges eszközt képes létrehozni. Számos más nyelv, például a go, a PHP és a Python esetében az ügyfél-eszközkészlet létrehozhatja a Helm diagramot, feltéve, hogy érvényes Docker ad meg. Ebben az esetben a minta alkalmazásnak van egy meglévő Docker és Helm diagramja

Az alkalmazás Azure dev Spaces-sel való futtatásához szükséges konfiguráció előállítása a meglévő Helm diagrammal és Docker a `azds prep` paranccsal:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

A `prep` parancsot a *dev-Spaces/Samples/Python/első lépések/webfrontend* könyvtárból kell futtatnia, és meg kell adnia a Helm- `--chart`diagram helyét a használatával.

> [!NOTE]
> Előfordulhat, hogy a figyelmeztetés: *Figyelmeztetés: a Docker nem hozható létre a nem támogatott nyelv miatt.* futtatásakor `azds prep`. A `azds prep` parancs megkísérli [egy Docker és egy Helm-diagram](how-dev-spaces-works-prep.md#prepare-your-code) létrehozását a projekthez, de nem írja felül a meglévő Dockerfiles vagy Helm-diagramokat.

## <a name="build-and-run-code-in-kubernetes"></a>Kód létrehozása és futtatása Kubernetesben

Hozza létre és futtassa a kódot az AK- `azds up` ban a parancs használatával:

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

A szolgáltatás futtatásához nyissa meg a `azds up` parancs kimenetében megjelenő nyilvános URL-címet. Ebben a példában a nyilvános URL-cím *http://dev.service.1234567890abcdef1234.eus.azds.io/* a következő:.

> [!NOTE]
> Ha a futás közben navigál a szolgáltatáshoz `azds up`, a rendszer a HTTP-kérelmek nyomkövetését is megjeleníti a `azds up` parancs kimenetében. Ezek a Nyomkövetések segítenek a szolgáltatás hibaelhárításában és hibakeresésében. Ezeket a nyomkövetéseket letilthatja `--disable-http-traces` a futtatásakor `azds up`.

Ha a `azds up` *CTRL + c billentyűkombinációval*állítja le a parancsot, a szolgáltatás továbbra is az AK-ban fog futni, és a nyilvános URL-cím továbbra is elérhető marad.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának üzembe helyezéséhez frissítheti a projektben lévő összes fájlt, majd újra futtathatja a `azds up` parancsot. Például:

1. Ha `azds up` még fut, nyomja le a *CTRL + c*billentyűkombinációt.
1. [A 13. sor `webfrontend.py` frissítése a következőre](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) :
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Mentse a módosításokat.
1. Futtassa újra `azds up` a parancsot:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navigáljon a futó szolgáltatáshoz, és figyelje meg a módosításokat.
1. Nyomja le a *CTRL + c* billentyűkombinációt a `azds up` parancs leállításához.

## <a name="clean-up-your-azure-resources"></a>Azure-erőforrások karbantartása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure dev Spaces hogyan segíti az összetettebb alkalmazások fejlesztését több tárolóban, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést, ha a kód különböző verzióival vagy ágaival dolgozik a különböző helyeken.

> [!div class="nextstepaction"]
> [Csoportmunka az Azure fejlesztői Spaces szolgáltatásban][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service