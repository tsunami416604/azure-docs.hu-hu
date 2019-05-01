---
title: Rövid útmutató – Azure Kubernetes Service (AKS)-fürt létrehozása
description: Megtudhatja, hogyan hozhat létre gyorsan egy Azure Resource Manager-sablon használatával a Kubernetes-fürt és az alkalmazás üzembe helyezése az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 04/19/2019
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 524eb97a2c865a14800cf503edd7f506151521bb
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920213"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-azure-resource-manager-template"></a>Gyors útmutató: Fürt üzembe helyezése az Azure Kubernetes Service (AKS) egy Azure Resource Manager-sablon használatával

Az Azure Kubernetes Service (AKS) egy felügyelt Kubernetes szolgáltatás, amellyel gyorsabban helyezheti üzembe és kezelheti a fürtöket. Ebben a rövid útmutatóban egy AKS-fürtöt egy Azure Resource Manager-sablonnal helyezi üzembe. Egy többtárolós alkalmazást helyezünk egy webes előtérrendszert és a egy Redis-példányt tartalmazó fut a fürtben.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-vote.png)

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információkért lásd: [Kubernetes alapvető fogalmait Azure Kubernetes Service (AKS)][kubernetes-concepts].

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület, ehhez a rövid útmutatóhoz az Azure CLI verziója 2.0.61 futtatnia vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy AKS-fürtöt egy Resource Manager-sablon használatával, adja meg egy nyilvános SSH-kulcs és az Azure Active Directory-szolgáltatásnévhez. Ha ezek az erőforrások közül választhat, tekintse meg a következő szakasz; Ellenkező esetben ugorjon a [AKS-fürt létrehozása](#create-an-aks-cluster) szakaszban.

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

AKS-csomópontok eléréséhez, akkor csatlakozzon ssh-kulcs használatával. Használja a `ssh-keygen` parancsot SSH nyilvános és titkos kulcs fájljai. Alapértelmezés szerint ezek a fájlok jönnek létre a *~/.ssh* könyvtár. Ha ssh-kulcs ezzel a névvel már létezik az adott helyen, ezeket a fájlokat írja felül.

A következő parancsot egy SSH-kulcspár, RSA-titkosítás és 2048 bit hosszúságú használatával hoz létre:

```azurecli-interactive
ssh-keygen -t rsa -b 2048
```

Az SSH-kulcsok létrehozásával kapcsolatos további információkért lásd: [létrehozása és kezelése az SSH-kulcsokat a hitelesítéshez az Azure-ban][ssh-keys].

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. Hozzon létre egy szolgáltatás egyszerű a [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] parancsot. A `--skip-assignment` paraméter korlátozza a további engedélyek hozzárendelését. Alapértelmezés szerint a szolgáltatásnévnek a érvényes egy év.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló:

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

Jegyezze fel az *appID* és a *password* értékét. A következő lépésekben szükség lesz ezekre az értékekre.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

A rövid útmutatóban használt sablon [Azure Kubernetes Service-fürt üzembe helyezése](https://azure.microsoft.com/resources/templates/101-aks/). További AKS minták, tekintse meg a [AKS gyorsindítási sablonok] [ aks-quickstart-templates] hely.

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](./media/kubernetes-walkthrough-rm-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.  

    Ez a rövid útmutatóhoz hagyja az alapértelmezett értékeit a *operációs rendszer lemezméret (GB)*, *ügynökök száma*, *ügynök VM-mérete*, *operációs rendszer típusa*, és *Kubernetes-verzió*. A következő sablon paraméterekkel adja meg a saját értékeit:

    * **Előfizetés**: Válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: Válassza az **Új létrehozása** lehetőséget. Adja meg egy egyedi nevet az erőforráscsoport, például *myResourceGroup*, majd válassza a **OK**.
    * **Hely**: Válasszon egy helyet, például **USA keleti Régiójában**.
    * **Fürt neve**: Adja meg egy egyedi nevet az AKS-fürtöt, például *myAKSCluster*.
    * **DNS-előtag**: Adjon meg egy egyedi DNS-előtagot a fürthöz, például *myakscluster*.
    * **Linux-rendszergazda felhasználóneve**: Adjon meg egy felhasználónevet, hogyan csatlakozhat ssh-t, például segítségével *azureuser*.
    * **SSH RSA nyilvános kulcs**: Másolja és illessze be a *nyilvános* az SSH-kulcspár része (alapértelmezésben tartalmát *~/.ssh/id_rsa.pub*).
    * **Egyszerű szolgáltatás ügyfél-azonosító**: Másolja és illessze be a *appId* a az egyszerű szolgáltatás a `az ad sp create-for-rbac` parancsot.
    * **Egyszerű szolgáltatás titkos Ügyfélkód**: Másolja és illessze be a *jelszó* a az egyszerű szolgáltatás a `az ad sp create-for-rbac` parancsot.
    * **Elfogadom a fenti feltételek és kikötések állapot**: A négyzet fogadjon el.

    ![Resource Manager-sablon Azure Kubernetes Service-fürt létrehozása a portálon](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Válassza a **Beszerzés** lehetőséget.

Az AKS-fürt létrehozása néhány percet vesz igénybe. Várjon, amíg a fürt sikeresen telepített, mielőtt továbblép a következő lépéssel.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Kubernetes-fürtök kezeléséhez használja [kubectl][kubectl], a Kubernetes parancssori ügyfelét. Ha az Azure Cloud Shellben használja `kubectl` már telepítve van. A telepítendő `kubectl` helyileg, használja a [az aks install-cli] [ az-aks-install-cli] parancsot:

```azurecli
az aks install-cli
```

Az [aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori Felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

Az alábbi példa kimenetében látható, a csomópontok az előző lépésekben létrehozott. Győződjön meg arról, hogy van-e az összes csomópont állapotát *készen*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Kubernetes-jegyzékfájl meghatározza, milyen tároló-lemezkép futtatásához például a fürt célállapotát. Ebben a rövid útmutatóban egy jegyzékfájlt használunk az Azure Vote-alkalmazás futtatásához szükséges összes objektum létrehozásához. A jegyzékfájlt tartalmaz két [Kubernetes-üzembehelyezés] [ kubernetes-deployment] – egy az Azure Vote Python mintaalkalmazásból, a másik pedig a Redis-példánynak. Két [Kubernetes-szolgáltatás] [ kubernetes-service] is létrejönnek - egy belső szolgáltatás a Redis-példányt, és a egy külső szolgáltatás az Azure Vote-alkalmazás elérésére az internetről.

> [!TIP]
> A rövid útmutatóban manuálisan hozza létre és helyezi üzembe az alkalmazásjegyzék-fájlokat az AKS-fürtön. A valósághoz közelebbi felhasználási forgatókönyvekben az [Azure Dev Spaces][azure-dev-spaces] használatával közvetlenül az AKS-fürtön végezheti a kód gyors iterálását és hibaelhárítását. A Dev Spaces több operációsrendszer-platformon és fejlesztői környezetben használható, és támogatja a csapaton belüli együttműködést.

Hozzon létre egy fájlt `azure-vote.yaml` , és másolja a következő YAML-definícióban. Ha az Azure Cloud Shellt használja, ez a fájl lehet létrehozni használatával `vi` vagy `nano` , mintha egy virtuális vagy fizikai rendszeren:

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

Üzembe helyezés az alkalmazás a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

Az alábbi példa kimenetében látható, a központi telepítések és a szolgáltatások létrehozása sikerült:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor egy Kubernetes-szolgáltatást mutatja meg az alkalmazás előtér az internethez. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Kezdetben a *EXTERNAL-IP* számára a *azure-vote-front* szolgáltatás jelenik meg, mint *függőben lévő*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a *EXTERNAL-IP* -cím módosul a *függőben lévő* egy tényleges nyilvános IP-címet használja `CTRL-C` leállítani a `kubectl` tekintse meg a folyamatot. Az alábbi példa kimenetében látható, érvényes nyilvános IP-címet rendel hozzá a szolgáltatáshoz:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

A művelet az Azure Vote alkalmazás megtekintéséhez nyissa meg a szolgáltatás külső IP-címét egy webböngészőben.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>A fürt törlése

Ha a fürtre már nincs szükség, az [az group delete][az-group-delete] paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.

## <a name="get-the-code"></a>A kód letöltése

Ez a rövid útmutatóban előre létrehozott tárolórendszerképek használták a Kubernetes üzembe helyezése. A kapcsolódó alkalmazáskód, Docker-fájl és Kubernetes-jegyzékfájl a GitHubon érhetőek el.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Kubernetes-fürtöt és azon egy többtárolós alkalmazást helyezett üzembe. [Hozzáférés a Kubernetes webes irányítópulthoz] [ kubernetes-dashboard] hozott létre a fürt számára.

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AKS-oktatóanyag][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

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
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
