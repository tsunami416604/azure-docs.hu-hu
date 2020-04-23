---
title: Alkalmazás fejlesztése meglévő Helm-diagrammal a Kubernetes-en
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használhatja az Azure Dev Spaces-t és a parancssort egy meglévő Helm-diagramot tartalmazó alkalmazás fejlesztéséhez az Azure Kubernetes szolgáltatásban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: e767b1ade2a80882ee33ff1fdd718c691dcefcf3
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82033563"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Rövid útmutató: Alkalmazás fejlesztése egy meglévő Helm-diagrammal a Kubernetes-en – Azure Dev Spaces
Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Futtasson egy alkalmazást egy meglévő Helm-diagrammal az AKS-ben az Azure Dev Spaces használatával a parancssorban.

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

A `use-dev-spaces` paranccsal engedélyezheti a fejlesztői szóközöket az AKS-fürtön, és kövesse az utasításokat. Az alábbi parancs engedélyezi a Fejlesztői szóközöket a *MyResourceGroup* csoport *MyAKS-fürtén,* és létrehoz egy *fejlesztői területet.*

> [!NOTE]
> A `use-dev-spaces` parancs az Azure Dev Spaces CLI-t is telepíti, ha még nincs telepítve. Az Azure Dev Spaces CLI nem telepíthető az Azure Cloud Shellben.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Mintaalkalmazás kódjának beszereznie

Ebben a cikkben az [Azure Dev Spaces mintaalkalmazás](https://github.com/Azure/dev-spaces) használatával bemutatja az Azure Dev Spaces használatával.

Klónozza az alkalmazást a GitHubról, és navigáljon a *fejlesztői terek/minták/python/getting-started/webfrontend* könyvtárba:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>A kérelem előkészítése

Az alkalmazás Azure Dev Spaces futtatásához dockerfile és helm diagramra van szükség. Egyes nyelvek, például a [Java][java-quickstart], [.NET core][netcore-quickstart]és [node.js,][nodejs-quickstart]az Azure Dev Spaces ügyféleszköz-készítési hozhat létre az összes szükséges eszközöket. Sok más nyelvek, például a Go, php és Python, az ügyfél-eszközkészítés generálhatja a Helm diagram, amíg meg tudja adni egy érvényes Dockerfile. Ebben az esetben a mintaalkalmazás rendelkezik egy meglévő Dockerfile és Helm diagram

Hozza létre az alkalmazás futtatásához az Azure Dev Spaces a meglévő `azds prep` Helm chart és Dockerfile a parancs használatával:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

A parancsot `prep` a *fejlesztői terekből/mintákból/python/getting-started/webfrontend* könyvtárból kell futtatnia, és meg kell adnia a Helm-diagram helyét a használatával. `--chart`

> [!NOTE]
> Előfordulhat, hogy a figyelmeztetés: *FIGYELEM: Dockerfile nem lehet létrehozni, mert a nem támogatott nyelv.* futás `azds prep`közben . A `azds prep` parancs megkísérli [a Dockerfile és a Helm diagramot](how-dev-spaces-works-prep.md#prepare-your-code) a projekthez, de nem írja felül a meglévő Dockerfiles vagy Helm diagramokat.

## <a name="build-and-run-code-in-kubernetes"></a>Kód létrehozása és futtatása Kubernetesben

A kód létrehozása és futtatása `azds up` az AKS-ben a következő paranccsal:

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

A futó szolgáltatás a nyilvános URL megnyitásával érhető el, `azds up` amely a parancs kimenetében jelenik meg. Ebben a példában a *http://dev.service.1234567890abcdef1234.eus.azds.io/* nyilvános URL-cím .

> [!NOTE]
> Amikor futás `azds up`közben a szolgáltatáshoz navigál, a HTTP-kérelem nyomkövetései is megjelennek a `azds up` parancs kimenetén. Ezek a nyomok segíthetnek a szolgáltatás hibaelhárításában és hibakeresésében. Ezeket a nyomkövetéseket a futás során `--disable-http-traces` letilthatja. `azds up`

Ha a `azds up` *Ctrl+c billentyűkombinációval*leállítja a parancsot, a szolgáltatás továbbra is futni fog az AKS-ben, és a nyilvános URL továbbra is elérhető marad.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának telepítéséhez frissítheti a projekt bármely `azds up` fájlját, és újra futtathatja a parancsot. Például:

1. Ha `azds up` még fut, nyomja *le a Ctrl+c billentyűkombinációt.*
1. A [13-as `webfrontend.py` sor](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) frissítése a következőre:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Mentse a módosításokat.
1. Futtassa `azds up` újra a parancsot:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
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