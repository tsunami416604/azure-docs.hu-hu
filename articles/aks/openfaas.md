---
title: OpenFaaS használata az Azure Kubernetes szolgáltatással (AKS)
description: Megtudhatja, hogyan telepítheti és használhatja az OpenFaaS-t egy Azure Kubernetes-fürt (AKS) fürtön kiszolgáló nélküli függvények tárolókkal történő létrehozásához.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 95039573c607f516755f08f1ebad8b968416ec8b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631467"
---
# <a name="using-openfaas-on-aks"></a>OpenFaaS használata AKS-en

[Az OpenFaaS][open-faas] egy keretrendszer a kiszolgáló nélküli függvények létrehozásához tárolók használatával. Mint egy nyílt forráskódú projekt, hogy szerzett nagyszabású elfogadása a közösségen belül. Ez a dokumentum részletezi az OpenFaas telepítését és használatát egy Azure Kubernetes-fürt (AKS) fürtön.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához a következőkre van szükség.

* A Kubernetes alapvető megértése.
* Egy Azure Kubernetes-szolgáltatás (AKS) fürt és AKS-hitelesítő adatok konfigurálva a fejlesztési rendszeren.
* Az Azure CLI telepítve van a fejlesztői rendszeren.
* Git parancssori eszközök telepítve a rendszerre.

## <a name="add-the-openfaas-helm-chart-repo"></a>Az OpenFaaS helmdiagram tártárjának hozzáadása

Nyissa [https://shell.azure.com](https://shell.azure.com) meg az Azure Cloud Shellt a böngészőjében.

Az OpenFaaS saját kormánydiagramokat tart fenn, hogy naprakész legyen a legújabb változásokkal.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>OpenFaaS telepítése

Ajánlott eljárásként az OpenFaaS és az OpenFaaS függvényeket saját Kubernetes névterükben kell tárolni.

Névtér létrehozása az OpenFaaS rendszerhez és függvényekhez:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Hozzon létre egy jelszót az OpenFaaS felhasználói felületi portálhoz és a REST API-hoz:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

A titk értékét a `echo $PASSWORD`segítségével kaphatja meg.

Az itt létrehozott jelszót a kormánydiagram fogja használni az egyszerű hitelesítés engedélyezéséhez az OpenFaaS átjárón, amely egy felhőloadbalancer-en keresztül érhető el az interneten.

Az OpenFaaS helmdiagramja megtalálható a klónozott tárházban. Ezzel a diagramkal telepítheti az OpenFaaS-t az AKS-fürtbe.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Kimenet:

```output
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

Nyilvános IP-cím jön létre az OpenFaaS átjáró eléréséhez. Az IP-cím beolvasásához használja a [kubectl get service parancsot.][kubectl-get] Eltarthat egy percig, amíg az IP-cím hozzá van rendelve a szolgáltatáshoz.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Kimeneti.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Az OpenFaaS rendszer teszteléséhez keresse meg a 8080-as port külső IP-címét ebben `http://52.186.64.52:8080` a példában. A rendszer kéri a bejelentkezést. A jelszó beolvasásához írja be a be írását. `echo $PASSWORD`

![OpenFaaS felhasználói felület](media/container-service-serverless/openfaas.png)

Végül telepítse az OpenFaaS CLI-t. Ez a példa használt sört, lásd az [OpenFaaS CLI dokumentáció][open-faas-cli] további lehetőségeket.

```console
brew install faas-cli
```

Állítsa `$OPENFAAS_URL` be a nyilvános IP fent található.

Jelentkezzen be az Azure CLI-vel:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Első függvény létrehozása

Most, hogy az OpenFaaS működőképes, hozzon létre egy függvényt az OpenFaas portálhasználatával.

Kattintson **az Új funkció telepítése gombra,** és keresse meg a **Figlet**kifejezést. Jelölje ki a Figlet függvényt, majd kattintson **a Telepítés gombra.**

![Figlet](media/container-service-serverless/figlet.png)

Curl használatával hívja meg a függvényt. Cserélje le az IP-címet a következő példában az OpenFaas átjárócímére.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Kimenet:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Második függvény létrehozása

Most hozzon létre egy második függvényt. Ez a példa az OpenFaaS CLI használatával lesz telepítve, és egy egyéni tárolórendszerképet tartalmaz, és adatokat a Cosmos DB-ből. A függvény létrehozása előtt több elemet kell konfigurálni.

Először hozzon létre egy új erőforráscsoportot a Cosmos DB számára.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

CosmosDB-példány `MongoDB`telepítése. A példánynak szüksége van `openfaas-cosmos` egy egyedi névre, és frissítenie kell a környezetére való frissítést.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

A Cosmos adatbázis-kapcsolati karakterláncbe, és tárolja azt egy változóban.

Frissítse az `--resource-group` argumentum értékét az erőforráscsoport nevére, és az `--name` argumentumot a Cosmos DB nevére.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Most feltölti a Cosmos DB-t tesztadatokkal. Hozzon létre `plans.json` egy elnevezett fájlt, és másolja a következő json.

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

A *mongoimport* eszközzel töltse be a CosmosDB-példányadatokkal.

Szükség esetén telepítse a MongoDB eszközöket. A következő példa ezeket az eszközöket a brew használatával telepíti, további lehetőségeket a [MongoDB dokumentációjában][install-mongo] talál.

```console
brew install mongodb
```

Töltse be az adatokat az adatbázisba.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Kimenet:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

A függvény létrehozásához futtassa a következő parancsot. Frissítse az argumentum értékét az `-g` OpenFaaS átjárócímmel.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Üzembe helyezés után látnia kell az újonnan létrehozott OpenFaaS-végpontot a függvényhez.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Tesztelje a funkciót göndör. Frissítse az IP-címet az OpenFaaS átjárócímmel.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Kimenet:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

A funkciót az OpenFaaS felhasználói felületén is tesztelheti.

![helyettesítő szöveg](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Következő lépések

Az OpenFaaS-műhelyrel továbbra is tanulhat olyan gyakorlati laborokon keresztül, amelyek olyan témaköröket fednek le, mint például a saját GitHub-robot létrehozása, a titkok fogyasztása, a metrikák megtekintése és az automatikus méretezés.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
