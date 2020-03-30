---
title: Rövid útmutató – Hozzon létre egy Azure Kubernetes-szolgáltatás (AKS) fürtöt
description: Megtudhatja, hogyan hozhat létre gyorsan egy Kubernetes-fürtöt egy Azure Resource Manager-sablon használatával, és hogyan helyezhet üzembe egy alkalmazást az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: quickstart
ms.date: 04/19/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: e8117eb1b521dc2e3fa9eaca1316e0b9c14f0e98
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80129459"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-azure-resource-manager-template"></a>Rövid útmutató: Azure Kubernetes-szolgáltatás (AKS) fürt telepítése Azure Resource Manager-sablon használatával

Az Azure Kubernetes-szolgáltatás (AKS) egy felügyelt Kubernetes-szolgáltatás, amely lehetővé teszi a fürtök gyors üzembe helyezését és kezelését. Ebben a rövid útmutatóban egy AKS-fürtet telepít egy Azure Resource Manager-sablon használatával. Egy többtárolós alkalmazás, amely magában foglalja a webes előtér és a Redis-példány fut a fürtben.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Kubernetes alapfogalmak az Azure Kubernetes Service (AKS)][kubernetes-concepts].

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a rövid útmutató megköveteli, hogy az Azure CLI 2.0.61-es vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="prerequisites"></a>Előfeltételek

Ha egy Erőforrás-kezelő sablon használatával aKS-fürt, meg kell adnia egy SSH nyilvános kulcs és az Azure Active Directory egyszerű szolgáltatás. Ha ezekre az erőforrásokra szüksége van, olvassa el a következő szakaszt; ellenkező esetben ugorjon az [AKS-fürt létrehozása](#create-an-aks-cluster) szakaszra.

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Az AKS-csomópontok eléréséhez SSH kulcspárhasználatával kell csatlakoznia. A `ssh-keygen` paranccsal SSH nyilvános és személyes kulcsfájlokat hozhat létre. Alapértelmezés szerint ezek a fájlok a *~/.ssh* könyvtárban jönnek létre. Ha az adott helyen létezik egy azonos nevű SSH-kulcspár, a rendszer felülírja ezeket a fájlokat.

Nyissa [https://shell.azure.com](https://shell.azure.com) meg a Cloud Shell t a böngészőjében.

A következő parancs létrehoz egy SSH kulcspárt RSA titkosítással és 2048-as bithosszal:

```console
ssh-keygen -t rsa -b 2048
```

Az SSH-kulcsok létrehozásáról az [SSH-kulcsok létrehozása és kezelése az Azure-ban című témakörben talál][ssh-keys]további információt.

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. Hozzon létre egy szolgáltatásnevet az [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] paranccsal. A `--skip-assignment` paraméter korlátozza a további engedélyek hozzárendelését. Alapértelmezés szerint ez a szolgáltatásnév egy évig érvényes.

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

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://azure.microsoft.com/resources/templates/101-aks/)

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json" range="1-126" highlight="86-118":::

További AKS-minták, tekintse meg az [AKS rövid útmutató sablonok][aks-quickstart-templates] webhely.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](./media/kubernetes-walkthrough-rm-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    Ehhez a rövid útmutatóhoz hagyja meg az *operációs rendszer lemezméretének GB*, *ügynökszám*, *ügynöki virtuálisgép-mérete*, *operációsrendszer-típusa*és *Kubernetes-verzió alapértelmezett értékeit.* Adja meg saját értékeit a következő sablonparaméterekhez:

    * **Előfizetés**: Válasszon egy Azure-előfizetést.
    * **Erőforráscsoport**: Válassza **az Új létrehozása**lehetőséget. Adjon meg egy egyedi nevet az erőforráscsoportnak, például *a myResourceGroup*lehetőséget, majd válassza az **OK gombot.**
    * **Hely**: Válasszon ki egy helyet, például **az USA keleti részét.**
    * **Fürtneve**: Adja meg az AKS-fürt egyedi nevét, például *a myAKSCluster nevet.*
    * **DNS-előtag**: Adjon meg egy egyedi DNS-előtagot a fürthöz, például *a myakscluster.*
    * **Linux felügyeleti felhasználónév**: Adjon meg egy felhasználónevet, amelyet az SSH használatával szeretne csatlakozni, például *az azureuser*.
    * **SSH RSA nyilvános kulcs**: Másolja és illessze be az SSH kulcspár *nyilvános* részét (alapértelmezés szerint a *~/.ssh/id_rsa.pub*tartalmát).
    * **Egyszerű szolgáltatásügyfél-azonosító:** Másolja és illessze be a `az ad sp create-for-rbac` szolgáltatásnév *alkalmazásazonosítóját* a parancsból.
    * **Szolgáltatás egyszerű ügyféltitkos:** Másolja *password* és illessze be `az ad sp create-for-rbac` a jelszót a szolgáltatásnév a parancsból.
    * **Elfogadom a fenti feltételeket:** Jelölje be ezt a négyzetet, hogy egyetértsen.

    ![Erőforrás-kezelő sablon az Azure Kubernetes-szolgáltatásfürt létrehozásához a portálon](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Válassza a **Beszerzés** lehetőséget.

Az AKS-fürt létrehozása néhány percet vesz igénybe. Várja meg, amíg a fürt sikeresen üzembe helyezése, mielőtt továbblépne a következő lépésre.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

### <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürt kezeléséhez [kubectl][kubectl], a Kubernetes parancssori ügyfél használatával kell használnia. Ha az Azure Cloud `kubectl` Shell, már telepítve van. A `kubectl` helyi telepítéshez használja az [aks install-cli][az-aks-install-cli] parancsot:

```azurecli
az aks install-cli
```

Az [aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

A következő példa kimeneti az előző lépésekben létrehozott csomópontokat mutatja be. Győződjön meg arról, hogy az összes csomópont állapota *készen áll:*

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

### <a name="run-the-application"></a>Az alkalmazás futtatása

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

### <a name="test-the-application"></a>Az alkalmazás tesztelése

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

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a fürtre már nincs szükség, az [az group delete][az-group-delete] paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.

## <a name="get-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban előre létrehozott tárolórendszerképek et használtak a Kubernetes-telepítés létrehozásához. A kapcsolódó alkalmazáskód, Docker-fájl és Kubernetes-jegyzékfájl a GitHubon érhetőek el.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Kubernetes-fürtöt és azon egy többtárolós alkalmazást helyezett üzembe. A létrehozott fürt [Kubernetes webes irányítópultjának elérése.][kubernetes-dashboard]

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
