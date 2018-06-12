---
title: Az Azure Kubernetes szolgáltatáshoz (AKS) OpenFaaS használható
description: Üzembe helyezés és használat OpenFaaS Azure Kubernetes szolgáltatás (AKS)
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: b5484233c7d3d32e51098baad8c22ec51df8f0d8
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260617"
---
# <a name="using-openfaas-on-aks"></a>A AKS OpenFaaS használatával

[OpenFaaS] [ open-faas] keretrendszere, amely fölött tárolók kiszolgáló nélküli funkciók felépítése. Nyílt forráskódú projektként azt köszönhetően Közösségen belüli nagyméretű bevezetését. Ez a dokumentum részletesen telepítéséről és használatáról az Azure Kubernetes szolgáltatás (AKS) fürt OpenFaas.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk belül lépések végrehajtásához a következők szükségesek.

* Kubernetes kapcsolatos alapvető ismeretekkel.
* Az Azure Kubernetes szolgáltatás (AKS) fürt és a fejlesztői rendszeren konfigurált AKS hitelesítő adatokat.
* Az Azure CLI-t a fejlesztői rendszeren telepítve.
* Git parancssori eszközök van telepítve a rendszeren.

## <a name="get-openfaas"></a>OpenFaaS beolvasása

A fejlesztői rendszerhez a OpenFaaS projekt tárház klónozása.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Módosítsa a klónozott tárház a könyvtárba.

```azurecli-interactive
cd faas-netes
```

## <a name="deploy-openfaas"></a>OpenFaaS telepítése

Jó gyakorlat szerint OpenFaaS és OpenFaaS funkciók saját Kubernetes névtér kell tárolni.

A OpenFaaS rendszer névtér létrehozása.

```azurecli-interactive
kubectl create namespace openfaas
```

Hozzon létre egy második OpenFaaS funkciók névteret.

```azurecli-interactive
kubectl create namespace openfaas-fn
```

A klónozott tárház OpenFaaS Helm diagramot tartalmazza. Ez a diagram segítségével OpenFaaS üzembe helyezés a AKS fürt.

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

Nyilvános IP-címnek a OpenFaaS átjáró eléréséhez jön létre. Az IP-cím lekéréséhez használja a [kubectl beolvasása szolgáltatás] [ kubectl-get] parancsot. Az IP-cím hozzá kell rendelni a szolgáltatás percet is igénybe vehet.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Kimeneti.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

A OpenFaaS rendszer teszteléséhez keresse meg a külső IP-címnek, 8080-as porton `http://52.186.64.52:8080` ebben a példában.

![OpenFaaS felhasználói felület](media/container-service-serverless/openfaas.png)

Végezetül a OpenFaaS parancssori felület telepítése. Ebben a példában használt brew, tekintse meg a [OpenFaaS CLI dokumentáció] [ open-faas-cli] további lehetőségekért.

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Első függvény létrehozása

Most, hogy OpenFaaS működési, hozzon létre egy függvényt, OpenFaas portál használatával.

Kattintson a **új funkció telepítése** keresse meg a **Figlet**. Válassza ki a Figlet függvényt, és kattintson a **telepítés**.

![Figlet](media/container-service-serverless/figlet.png)

Curl használatával függvényt. Cserélje le a következő példa az IP-cím, amely a OpenFaas átjáró.

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

Most hozzon létre egy második függvényt. Ebben a példában a OpenFaaS parancssori felület használatával telepíti, és egy egyéni tároló lemezképet és az adatok lekérdezése egy Cosmos DB tartalmazza. Több elemet be kell állítani a függvény létrehozása előtt.

Először hozzon létre egy új erőforráscsoportot a Cosmos-adatbázis számára.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Központi telepítése egy CosmosDB példány típusú `MongoDB`. A példány kell egy egyedi nevet, a frissítéskezelés `openfaas-cosmos` valamely különleges a környezetben.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

A Cosmos adatbázis-kapcsolati karakterlánc beolvasása és tárolható egy változóban.

Frissítse az értéket a `--resource-group` argumentum az erőforráscsoport nevét és a `--name` argumentum a Cosmos-adatbázis nevét.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

A Cosmos DB teszt adatokkal most feltöltéséhez. Hozzon létre egy fájlt `plans.json` , és másolja a következő json-ban.

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

Használja a *mongoimport* eszköz adatokkal CosmosDB-példányok betöltéséhez.

Szükség esetén telepítse a MongoDB-eszközök. A következő példa telepíti ezeket az eszközöket brew, tekintse meg a [MongoDB dokumentációt] [ install-mongo] lehetőségek.

```azurecli-interactive
brew install mongodb
```

Az adatok betöltése az adatbázisba.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Kimenet:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

A következő parancsot a függvény létrehozásához. Frissítse az értéket a `-g` a OpenFaaS átjárócímet argumentumot.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Amennyiben a telepített, a függvény az újonnan létrehozott OpenFaaS végpont kell megjelennie.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

A függvény használata curl használatával tesztelése. Az IP-címének frissítése OpenFaaS átjáró címével.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Kimenet:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Azt is tesztelni, a függvény a OpenFaaS felhasználói felületen belül.

![helyettesítő szöveg](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>További lépések

OpenFaas alapértelmezett telepítését OpenFaaS átjáró és a funkciók zárolni kell. [Alex Ellis blogbejegyzés](https://blog.alexellis.io/lock-down-openfaas/) további részleteket tartalmaz a biztonságos konfigurációs beállításokat.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
