---
title: Rövid útmutató – Hozzon létre egy Azure Kubernetes-szolgáltatás (AKS) fürtöt a portálon
description: Megtudhatja, hogyan hozhat létre gyorsan egy Kubernetes-fürtöt, helyezhet üzembe egy alkalmazást, és figyelheti a teljesítményt az Azure Kubernetes-szolgáltatásban (AKS) az Azure Portalon.
services: container-service
ms.topic: quickstart
ms.date: 01/21/2020
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: f4885bea686267ce0397e9ca6f3e2c0ac8640971
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240617"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Rövid útmutató: Egy Azure Kubernetes-szolgáltatás (AKS) fürt üzembe helyezése az Azure Portalhasználatával

Az Azure Kubernetes-szolgáltatás (AKS) egy felügyelt Kubernetes-szolgáltatás, amely lehetővé teszi a fürtök gyors üzembe helyezését és kezelését. Ebben a rövid útmutatóban egy AKS-fürtöt helyezünk üzembe az Azure Portal használatával. Egy többtárolós alkalmazás, amely magában foglalja a webes előtér és a Redis-példány fut a fürtben. Ezután láthatja, hogyan figyelheti az alkalmazást futtató fürt és podok állapotát.

![Az Azure Vote mintaalkalmazás keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Kubernetes alapfogalmak az Azure Kubernetes Service (AKS)][kubernetes-concepts].

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

AKS-fürt létrehozásához hajtsa végre a következő lépéseket:

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

2. Válassza a **Tárolók** >  **Kubernetes szolgáltatás lehetőséget.**

3. Az **Alapok** lapon adja meg a következő beállításokat:
    - **A projekt részletei**: Válasszon ki egy Azure-előfizetést , majd válasszon vagy hozzon létre egy Azure Resource **csoportot**, például *a myResourceGroup*csoportot. **Subscription**
    - **Fürt adatai**: Adja meg a **Kubernetes-fürt nevét**, például *a myAKSCluster.* Válasszon egy **régiót**, **a Kubernetes-verziót**és az AKS-fürt **DNS-névelőtagot.**
    - **Elsődleges csomópontkészlet:** Válassza ki a **virtuálisgép-csomópont méretét** az AKS-csomópontokhoz. A virtuális gép mérete *nem* módosítható, ha egy AKS-fürt telepítve van. 
            - Válassza ki a fürtbe telepítendő csomópontok számát. Ehhez a rövid útmutatóhoz a **Csomópontok száma** beállítás értékeként adjon meg *1*-et. A csomópontok száma a fürt telepítése után is *módosítható*.
    
    ![AKS-fürt létrehozása – alapvető adatok megadása](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

    Válassza a **Tovább: Méretezés,** ha befejeződött.

4. A **Méretezés** lapon tartsa meg az alapértelmezett beállításokat. A képernyő alján kattintson a **Tovább: Hitelesítés**gombra.
    > [!CAUTION]
    > Új AAD szolgáltatásnévi tagok létrehozása több percet is igénybe vehet a propagálása, és elérhetővé válhat, ami az Egyszerű szolgáltatás nem talált hibákat és érvényesítési hibákat az Azure Portalon. Ha ön megüt ez legyen szíves látogat [itt](troubleshooting.md#im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one) részére mérséklés.

5. A **Hitelesítés** lapon adja meg a következő beállításokat:
    - Hozzon létre egy új egyszerű szolgáltatást úgy, hogy az Egyszerű **szolgáltatás** mezőt **(új) alapértelmezett szolgáltatásnévvel**hagyja el. Vagy *választhatja az Egyszerű szolgáltatás konfigurálása* meglévő használata. Ha egy meglévőt használ, meg kell adnia az SPN-ügyfélazonosítót és a titkos kulcsot.
    - Engedélyezze a Kubernetes szerepköralapú hozzáférés-vezérlők (RBAC) használatát. Ez részletesebb hozzáférést biztosít az AKS-fürtben üzembe helyezett Kubernetes-erőforrásokhoz való hozzáféréshez.

Alapértelmezés szerint *az alapszintű* hálózatkezelés használatos, és az Azure Monitor tárolók engedélyezve van. Kattintson **a Véleményezés + létrehozás,** majd a **Létrehozás gombra,** amikor az ellenőrzés befejeződik.

Az AKS-fürt létrehozása néhány percet vesz igénybe. Ha a központi telepítés befejeződött, kattintson az Ugrás az **erőforrásra**gombra, vagy keresse meg az AKS-fürt erőforráscsoportját( például *a myResourceGroup*), és válassza ki az AKS-erőforrást, például *a myAKSCluster elemet.* Az AKS-fürt irányítópultja látható, mint ebben a példában:

![Példa AKS-irányítópult az Azure Portalon](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürt kezeléséhez [kubectl][kubectl], a Kubernetes parancssori ügyfél használatával kell használnia. A `kubectl` ügyfél előzetesen már telepítve van az Azure Cloud Shellben.

Nyissa meg a `>_` Cloud Shellt az Azure Portal tetején található gombbal.

![Az Azure Cloud Shell megnyitása a portálon](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

Az [aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t azok használatára. A következő példa lekéri a *myResourceGroup* erőforrásban lévő *myAKSCluster* fürtnév hitelesítő adatait:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

A következő példakimenet az előző lépésekben létrehozott csomópontot mutatja be. Győződjön meg arról, hogy a csomópont állapota *készen áll:*

```output
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes jegyzékfájl határozza meg a kívánt állapotot a fürt, például milyen tárolólemezképek futtatásához. Ebben a rövid útmutatóban egy jegyzékfájlt használunk az Azure Vote-alkalmazás futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl két [Kubernetes-telepítést][kubernetes-deployment] tartalmaz – az egyik az Azure Vote Python-alkalmazások minta, a másik pedig egy Redis-példány. Két [Kubernetes-szolgáltatás][kubernetes-service] is létrejön – egy belső szolgáltatás a Redis-példányhoz, és egy külső szolgáltatás az Azure Vote alkalmazás internetről való eléréséhez.

> [!TIP]
> A rövid útmutatóban manuálisan hozza létre és helyezi üzembe az alkalmazásjegyzék-fájlokat az AKS-fürtön. A valósághoz közelebbi felhasználási forgatókönyvekben az [Azure Dev Spaces][azure-dev-spaces] használatával közvetlenül az AKS-fürtön végezheti a kód gyors iterálását és hibaelhárítását. A Dev Spaces több operációsrendszer-platformon és fejlesztői környezetben használható, és támogatja a csapaton belüli együttműködést.

A Cloud Shellben a `nano azure-vote.yaml` `vi azure-vote.yaml` vagy a paranccsal hozzon létre egy . `azure-vote.yaml` Ezután másolja a következő YAML-definíciót:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Telepítse az alkalmazást a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f azure-vote.yaml
```

A következő példa kimeneti mutatja a központi telepítések és szolgáltatások sikeresen létrehozott:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Amikor az alkalmazás fut, a Kubernetes szolgáltatás elérhetővé teszi az alkalmazás előtér-az interneten. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```console
kubectl get service azure-vote-front --watch
```

Kezdetben az *ex-IP* az *azure-vote-front* szolgáltatás jelenik *meg függőben lévő*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Amikor az *EXTERNAL-IP-cím* *függőben lévőről* tényleges `CTRL-C` nyilvános `kubectl` IP-címre változik, az órafolyamat leállításához használja. A következő példa kimenete a szolgáltatáshoz rendelt érvényes nyilvános IP-címet mutatja:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Az Azure Vote alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![Az Azure Vote mintaalkalmazás keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Állapot és naplók monitorozása

A fürt létrehozásakor az Azure Monitor tárolók engedélyezve lett. Ez a monitorozási funkció az AKS-fürt és a fürtön futó podok állapotával kapcsolatos mérőszámokat biztosít.

Az adatok Azure Portalra történő feltöltése eltarthat néhány percig. Az Azure Vote-podok jelenlegi állapotának, üzemidejének és erőforrás-felhasználásának megjelenítéséhez lépjen vissza az AKS-erőforráshoz az Azure Portalon (például *myAKSCluster*). Az állapotot ezután a következő módon érheti el:

1. A bal oldali **Figyelés** csoportban válassza az **Insights (Elemzési adatok) lehetőséget.**
1. Válassza a **+ Szűrő hozzáadása** elemet a képernyő felső részén
1. Válassza ki a *Névtér* tulajdonságot, majd az *\<All but kube-system\>* (Összes, kivéve a kube rendszer) lehetőséget
1. Válassza a **Tárolók** nézetet.

Megjelenik az *azure-vote-back* és az *azure-vote-front* tároló, az alábbi példában látható módon:

![Futó tárolók állapotának megtekintése az AKS-ben](media/kubernetes-walkthrough-portal/monitor-containers.png)

A `azure-vote-front` pod naplóinak megtekintéséhez válassza ki a **Tárolónaplók megtekintése** a tárolók listájának legördülő listájából. Ezek a naplók tartalmazzák a *stdout* és *stderr* streamek a tárolóból.

![Tárolók naplóinak megtekintése az AKS-ben](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Fürt törlése

Ha a fürtre már nincs szükség, törölje az erőforráscsoportot. Ezzel törli az összes társított erőforrást is. Ez a művelet az Azure Portalon az AKS-fürt **irányítópultján** a Törlés gombra kattintva hajtható végre. Másik lehetőségként az [az aks delete][az-aks-delete] parancs használható a Cloud Shellben:

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.

## <a name="get-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban előre létrehozott tárolórendszerképek et használtak a Kubernetes-telepítés létrehozásához. A kapcsolódó alkalmazáskód, Docker-fájl és Kubernetes-jegyzékfájl a GitHubon érhetőek el.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Kubernetes-fürtöt és azon egy többtárolós alkalmazást helyezett üzembe.

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AKS-oktatóanyag][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../monitoring/monitoring-container-health.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
