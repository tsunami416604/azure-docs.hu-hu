---
title: 'Rövid útmutató: Azure Kubernetes-szolgáltatásfürt üzembe helyezése'
description: Ismerje meg, hogyan hozhat létre gyorsan egy Kubernetes-fürtöt, helyezhet üzembe egy alkalmazást, és figyelheti a teljesítményt az Azure Kubernetes-szolgáltatásban (AKS) az Azure CLI használatával.
services: container-service
ms.topic: quickstart
ms.date: 09/13/2019
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
ms.openlocfilehash: 0317be6652ff5f03c4c095788ecdde7cba3d1d98
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240463"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Rövid útmutató: Azure Kubernetes-szolgáltatásfürt üzembe helyezése az Azure CLI használatával

Ebben a rövid útmutatóban üzembe helyez egy Azure Kubernetes-szolgáltatás (AKS) fürtaz Azure CLI használatával. Az AKS egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi a fürtök gyors üzembe helyezését és kezelését. Egy többtárolós alkalmazás, amely magában foglalja a webes előtér és a Redis-példány fut a fürtben. Ezután láthatja, hogyan figyelheti az alkalmazást futtató fürt és podok állapotát.

Ha Windows Server-tárolókat szeretne használni (jelenleg előzetes verzióban az AKS-ben), olvassa el a [Windows Server-tárolókat támogató AKS-fürt létrehozása című témakört.][windows-container-cli]

![Az Azure Kubernetes szolgáltatásban telepített szavazóalkalmazás](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Kubernetes alapfogalmak az Azure Kubernetes Service (AKS)][kubernetes-concepts].

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a rövid útmutató megköveteli, hogy az Azure CLI 2.0.64-es vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

> [!NOTE]
> Ha a parancsokfuttatását ebben a rövid útmutatóban helyileg (az Azure Cloud Shell helyett), győződjön meg arról, hogy a parancsokat rendszergazdaként futtatja.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely az erőforráscsoport metaadatainak tárolása, ahol az erőforrások az Azure-ban futnak, ha nem ad meg másik régiót az erőforrás-létrehozás során. Hozzon létre egy erőforráscsoportot az [az csoport létrehozása][az-group-create] paranccsal.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A következő példa kimenete a sikeresen létrehozott erőforráscsoportot mutatja:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS-fürt létrehozása

Használja az [az aks create][az-aks-create] parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. A Tárolókhoz készült Azure Monitor szintén engedélyezve van az *--enable-addons monitoring* paraméterrel.  Ez több percet vesz igénybe.

> [!NOTE]
> AKS-fürt létrehozásakor egy második erőforráscsoport automatikusan létrejön az AKS-erőforrások tárolására. További információ: [Miért jön létre két erőforráscsoport az AKS-sel?](https://docs.microsoft.com/azure/aks/faq#why-are-two-resource-groups-created-with-aks)

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Néhány perc múlva a parancs befejezi és visszaadja a FürtJSON-formátumú adatait.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürt kezeléséhez [kubectl][kubectl], a Kubernetes parancssori ügyfél használatával kell használnia. Ha az Azure Cloud `kubectl` Shell, már telepítve van. A `kubectl` helyi telepítéshez használja az [aks install-cli][az-aks-install-cli] parancsot:

```azurecli
az aks install-cli
```

Az [aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példakimenet az előző lépésekben létrehozott csomópontot mutatja be. Győződjön meg arról, hogy a csomópont állapota *készen áll:*

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes jegyzékfájl határozza meg a kívánt állapotot a fürt, például milyen tárolólemezképek futtatásához. Ebben a rövid útmutatóban egy jegyzékfájlt használunk az Azure Vote-alkalmazás futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl két [Kubernetes-telepítést][kubernetes-deployment] tartalmaz – az egyik az Azure Vote Python-alkalmazások minta, a másik pedig egy Redis-példány. Két [Kubernetes-szolgáltatás][kubernetes-service] is létrejön – egy belső szolgáltatás a Redis-példányhoz, és egy külső szolgáltatás az Azure Vote alkalmazás internetről való eléréséhez.

> [!TIP]
> A rövid útmutatóban manuálisan hozza létre és helyezi üzembe az alkalmazásjegyzék-fájlokat az AKS-fürtön. A valósághoz közelebbi felhasználási forgatókönyvekben az [Azure Dev Spaces][azure-dev-spaces] használatával közvetlenül az AKS-fürtön végezheti a kód gyors iterálását és hibaelhárítását. A Dev Spaces több operációsrendszer-platformon és fejlesztői környezetben használható, és támogatja a csapaton belüli együttműködést.

Hozzon létre `azure-vote.yaml` egy elnevezett fájlt, és másolja a következő YAML-definícióba. Ha az Azure Cloud Shellt használja, `vi` ez `nano` a fájl úgy hozható létre, mintha virtuális vagy fizikai rendszeren dolgozna:

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

```azurecli-interactive
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

![Az Azure Kubernetes szolgáltatásban telepített szavazóalkalmazás](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Az AKS-fürt létrehozásakor az [Azure Monitor tárolók](../azure-monitor/insights/container-insights-overview.md) számára engedélyezve volt a fürtcsomópontok és a podok állapotmetrikok rögzítésére. Ezek az állapotmetrikák elérhetők az Azure Portalon.

## <a name="delete-the-cluster"></a>A fürt törlése

Az Azure-díjak elkerülése érdekében meg kell tisztítania a szükségtelen erőforrásokat.  Ha a fürtre már nincs szükség, az [az group delete][az-group-delete] paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.

## <a name="get-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban előre létrehozott tárolórendszerképek et használtak a Kubernetes-telepítés létrehozásához. A kapcsolódó alkalmazáskód, Docker-fájl és Kubernetes-jegyzékfájl a GitHubon érhetőek el.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Kubernetes-fürtöt és azon egy többtárolós alkalmazást helyezett üzembe. Az [AKS-fürt Kubernetes webes irányítópultját][kubernetes-dashboard] is elérheti.

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AKS-oktatóanyag][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md
