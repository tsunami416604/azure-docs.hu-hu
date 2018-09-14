---
title: OpenFaaS használata az Azure Kubernetes Service (AKS)
description: Üzembe helyezés és használat OpenFaaS Azure Kubernetes Service (AKS)
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: dc0f4bd1e5b07e30f3c89807fbbbc908b3149810
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542531"
---
# <a name="using-openfaas-on-aks"></a>OpenFaaS használata AKS-en

[OpenFaaS] [ open-faas] egy keretrendszer, amellyel a kiszolgáló nélküli függvények tárolók felett. Egy nyílt forráskódú projektként, szerzett a Közösségben nagyméretű bevezetését. Ez a dokumentum részletesen, telepítése és OpenFaas használata az Azure Kubernetes Service (AKS)-fürtön.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben található lépések végrehajtásához a következők szükségesek.

* Kubernetes alapvető ismeretekkel.
* Az Azure Kubernetes Service (AKS)-fürt és a fejlesztői rendszeren konfigurálva az AKS hitelesítő adatok.
* Az Azure CLI telepítve van a fejlesztői rendszeren.
* A Git parancssori eszközök van telepítve a rendszeren.

## <a name="get-openfaas"></a>OpenFaaS beolvasása

A fejlesztői rendszerhez a OpenFaaS projekt tárház klónozása.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Módosítsa a könyvtárat a klónozott adattár.

```azurecli-interactive
cd faas-netes
```

## <a name="deploy-openfaas"></a>OpenFaaS üzembe helyezése

Jó gyakorlat szerint OpenFaaS és OpenFaaS funkciók kell tárolni a saját Kubernetes-névtér.

Hozzon létre egy névteret a OpenFaaS rendszer.

```azurecli-interactive
kubectl create namespace openfaas
```

Hozzon létre egy második névteret OpenFaaS funkciók.

```azurecli-interactive
kubectl create namespace openfaas-fn
```

A klónozott adattár egy Helm-diagramot a OpenFaaS tartalmazza. A diagram az AKS-fürt üzembe helyezése a OpenFaaS használata.

```azurecli-interactive
helm install --namespace openfaas -n openfaas \
  --set functionNamespace=openfaas-fn, \
  --set serviceType=LoadBalancer, \
  --set rbac=false chart/openfaas/
```

Kimenet:

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Nyilvános IP-cím a OpenFaaS átjárón keresztül jön létre. Az IP-cím lekéréséhez használja a [kubectl get service] [ kubectl-get] parancsot. Az IP-cím hozzá kell rendelni a szolgáltatás egy percig is eltarthat.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Kimenet.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

A OpenFaaS rendszer teszteléséhez, keresse meg a külső IP-címe, 8080-as porton `http://52.186.64.52:8080` ebben a példában.

![OpenFaaS felhasználói felület](media/container-service-serverless/openfaas.png)

Végül telepítse a OpenFaaS CLI-t. Ebben a példában használt brew, tekintse meg a [OpenFaaS CLI dokumentációját] [ open-faas-cli] a további lehetőségeket.

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Első függvény létrehozása

Most, hogy OpenFaaS működik, a OpenFaas portállal függvény létrehozása.

Kattintson a **új függvény üzembe helyezése** és keressen rá a **Figlet**. Válassza ki a Figlet függvényt, és kattintson a **telepítés**.

![Figlet](media/container-service-serverless/figlet.png)

A függvény hívása a curl használatával. Cserélje le a következő példában az IP-címet, amely a OpenFaas átjáró.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Kimenet:

```console
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Második függvény létrehozása

Most hozzon létre egy másik függvény. Ebben a példában a OpenFaaS parancssori felülettel szeretné telepíteni, és a egy egyéni tárolórendszerképbe és adatok lekérdezése a Cosmos DB-ből. Több elemet a függvény létrehozása előtt konfigurálni kell.

Először hozzon létre egy új erőforráscsoportot a Cosmos DB-hez készült.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

A cosmos DB-példány típusú üzembe helyezése `MongoDB`. A példány kell egy egyedi nevet, a frissítés `openfaas-cosmos` valami a környezet egyedi.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Cosmos-adatbázis kapcsolati karakterláncának lekérése, és a egy változóban tárolja.

Frissítse az értéket a a `--resource-group` argumentum az erőforráscsoport nevére és a `--name` argumentum a Cosmos DB nevére.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Most töltse fel a Cosmos DB-teszt adatai. Hozzon létre egy fájlt `plans.json` , és másolja a következő json-fájlban.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Használja a *mongoimport* eszköz betölteni a cosmos DB-példány adatokkal.

Ha szükséges, telepítse a MongoDB-eszközök. A következő példa telepíti a brew használatával ezek az eszközök, tekintse meg a [MongoDB dokumentációt] [ install-mongo] további lehetőségekért.

```azurecli-interactive
brew install mongodb
```

Az adatok betöltéséhez az adatbázisba.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Kimenet:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Futtassa a következő parancsot a függvény létrehozásához. Frissítse az értéket, a `-g` argumentum a OpenFaaS átjáró címével.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Miután üzembe helyezte az újonnan létrehozott OpenFaaS végpont a függvény kell megjelennie.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Tesztelheti a függvényt a curl használatával. Frissítse az IP-címet a OpenFaaS átjárócímet.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Kimenet:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Tesztelheti a függvényt a OpenFaaS felhasználói felületén belül is.

![helyettesítő szöveg](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>További lépések

OpenFaas alapértelmezett telepítését OpenFaaS átjáró és a Functions zárolni kell. [Alex Ellis blogbejegyzés](https://blog.alexellis.io/lock-down-openfaas/) további részleteket tartalmaz a biztonságos konfigurációs beállításokat.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
