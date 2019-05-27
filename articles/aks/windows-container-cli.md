---
title: Előzetes verzió – hozzon létre egy Windows Server-tárolót az Azure Kubernetes Service (AKS)-fürtön
description: Megtudhatja, hogyan hozzon létre egy Kubernetes-fürtöt, egy Windows Server-tárolót az Azure Kubernetes Service (AKS) az Azure CLI használatával az alkalmazások üzembe helyezése.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: zarhoads
ms.openlocfilehash: 328fb9707c7151b8696cfb55e13567db90e45b7f
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991139"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Előzetes verzió – hozzon létre egy Windows Server-tárolót az Azure Kubernetes Service (AKS)-fürtön az Azure CLI használatával

Az Azure Kubernetes Service (AKS) egy felügyelt Kubernetes szolgáltatás, amellyel gyorsabban helyezheti üzembe és kezelheti a fürtöket. Ebben a cikkben üzembe helyezi egy AKS-fürtöt az Azure CLI használatával. A fürtöt is telepíthet egy ASP.NET-mintaalkalmazást, a Windows Server-tárolóban.

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

![ASP.NET-mintaalkalmazás keresését ábrázoló kép](media/windows-container/asp-net-sample-app.png)

Ez a cikk feltételezi, hogy a Kubernetes fogalmainak alapszintű ismerete. További információkért lásd: [Kubernetes alapvető fogalmait Azure Kubernetes Service (AKS)][kubernetes-concepts].

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület, ez a cikk van szükség, hogy futnak-e az Azure CLI verziója 2.0.61 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="before-you-begin"></a>Előkészületek

A fürt, amely képes futtatni a Windows Server-tárolók létrehozása után hozzá kell adnia egy további fürtcsomópontra készletet. Egy további fürtcsomópontra készlet hozzáadása egy későbbi lépésben tárgyalunk, de először engedélyeznie kell néhány előzetes verziójú funkciók.

> [!IMPORTANT]
> Az AKS előzetes verziójú funkciók a következők: az önkiszolgáló és vehetnek részt. Visszajelzés és hibák gyűjtsön közösségünkhöz előzetes verziók vannak megadva. Azonban nem támogatja őket az Azure műszaki támogatást. Hozzon létre egy fürtöt, vagy adja hozzá ezeket a funkciókat a meglévő fürtökre, ha a fürt nem támogatott, mindaddig, amíg a funkció már nem előzetes verzióban érhető el és hallgatóknak az általánosan elérhető (GA).
>
> Ha az előzetes verziójú szolgáltatásaihoz is problémák merülnek fel [nyisson egy problémát a AKS GitHub-adattárat a] [ aks-github] az előzetes verziójú funkció a bejelentett hiba címét nevére.

### <a name="install-aks-preview-cli-extension"></a>Az aks előzetes CLI-bővítmény telepítése
    
A CLI-parancsok hozhat létre és kezelhet több csomópont készletek érhetők el a *aks előzetes* CLI-bővítményt. Telepítse a *aks előzetes* Azure CLI-bővítmény használata a [az bővítmény hozzáadása] [ az-extension-add] parancsot, az alábbi példában látható módon:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Ha korábban telepítette a *aks előzetes* bővítmény, az elérhető frissítések telepítése használatával a `az extension update --name aks-preview` parancsot.

### <a name="register-windows-preview-feature"></a>Regisztrálja a Windows előzetes verziójú funkció

Hozzon létre egy AKS-fürtöt, amely képes több csomópont készleteket használni, és a Windows Server-tárolókat futtathat, először engedélyezze a *WindowsPreview* jelzők az előfizetéshez, a szolgáltatás. A *WindowsPreview* szolgáltatás is használ a készlet több csomópontos fürtök és a virtuálisgép-méretezési telepítését és konfigurálását a Kubernetes-csomópontok kezelésére. Regisztrálja a *WindowsPreview* funkció jelző használatával a [az a funkció regisztrálása] [ az-feature-register] parancsot az alábbi példában látható módon:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Bármely AKS-fürtöt hoz létre, miután sikeresen regisztrálta a *WindowsPreview* funkció előzetes fürt felület használja. Továbbra is rendszeres, teljes mértékben támogatott fürtök létrehozását, ne engedélyezze az előzetes verziójú funkciók az éles üzemű előfizetéseket. Előzetes verziójú funkciók teszteléséhez használja egy külön teszt- vagy Azure-előfizetés.

Az állapot megjelenítése néhány percet vesz igénybe *regisztrált*. A regisztrációs állapot használatával ellenőrizheti a [az szolgáltatáslistát] [ az-feature-list] parancsot:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a regisztrációját a *Microsoft.ContainerService* erőforrás-szolgáltató használatával a [az provider register] [ az-provider-register] parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Korlátozások

Az alábbi korlátozások érvényesek a felhőszolgáltatásnak, amely támogatja több csomópontkészletek AKS-fürtök kezelése:

* Miután sikeresen regisztrálta létrehozott fürtök esetében érhetők el több csomópontkészleteit a *WindowsPreview*. Több csomópont készletek is akkor érhetők el, ha regisztrál a *MultiAgentpoolPreview* és *VMSSPreview* funkciók az előfizetéshez. Nem adhat hozzá, és csomópont-készletek kezelése a meglévő AKS-fürt létrehozása előtt ezeket a funkciókat sikeresen regisztrálva.
* Az első node-készlet nem törölhető.

Bár ez a funkció előzetes verzióban érhető el, a következő további korlátozások vonatkoznak:

* Az AKS-fürtöt egy legfeljebb nyolc csomópontkészletek rendelkezhet.
* Az AKS-fürt legfeljebb 400 csomópontok között nyolc csomópont készletekben.
* A Windows Server-csomópont készlet nevének maximális hossza 6 karakter rendelkezik.
* A Windows Server csomópontkészletek nem érhetők az Kanada régióban jelenleg.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely az erőforrás-csoport metaadatok tárolására, egyben ahol az erőforrások futtatása az Azure-ban, ha nem ad meg egy másik régióba erőforrás létrehozásakor. Hozzon létre egy erőforrás csoport a [az csoport létrehozása] [ az-group-create] parancsot.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Az alábbi példa kimenetében látható, az erőforráscsoport létrehozása sikerült:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>AKS-fürt létrehozása
A fürt egy AKS-fürtöt, amely támogatja a csomópontkészleteit a Windows Server-tárolók futtatásához kell használnia a által használt hálózati házirend [Azure CNI] [ azure-cni-about] (speciális) hálózati beépülő modult. További részletes információ segít előre tervezni a szükséges alhálózati címtartományok és hálózati szempontjai lásd: [Azure CNI a hálózatkezelés konfigurálását][use-advanced-networking]. Használja a [az aks létrehozása] [ az-aks-create] paranccsal hozzon létre egy AKS-fürt nevű *myAKSCluster*. Ez a parancs létrehozza a szükséges hálózati erőforrások, ha azok még nem léteznek.
  * A fürt van konfigurálva, egy csomóponttal
  * A *windows-rendszergazda-jelszó* és *windows-rendszergazda-username* paraméterei beállítva a rendszergazdai hitelesítő adatait bármilyen Windows Server-tárolók létrehozásáról a fürtön.

Adja meg a saját biztonságos *PASSWORD_WIN*.

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

Néhány perc múlva a parancs befejeződik, és visszaadja a fürttel kapcsolatos adatokat JSON formátumban.

## <a name="add-a-windows-server-node-pool"></a>A Windows Server csomópont készlet hozzáadása

Alapértelmezés szerint a Linux-tárolókat futtathat csomópontkészletek AKS-fürt jön létre. Használat `az aks nodepool add` parancs futtatható a Windows Server-tárolók egy további fürtcsomópontra készlet hozzáadása.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0
```

A fenti parancs létrehoz egy új csomópont-készletet, nevű *npwin* , és hozzáadja azt, hogy a *myAKSCluster*. A Windows Server-tárolók futtatásához csomópontkészletek létrehozásakor az alapértelmezett érték *vm-csomópontméret* van *Standard_D2s_v3*. Ha úgy dönt, hogy állítsa be a *vm-csomópontméret* paramétert, ellenőrizze a listája [Virtuálisgép-méretek korlátozott][restricted-vm-sizes]. A minimális méretnél van *Standard_D2s_v3*. A fenti parancsban is használja az alapértelmezett alhálózati futtatásakor létrehozott alapértelmezett virtuális hálózatban `az aks create`.

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

A következő példakimenet az előző lépésekben létrehozott csomópontot mutatja be. Győződjön meg arról, hogy a csomópont állapota *készen*:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.0
aksnpwin987654                      Ready    agent   108s   v1.14.0
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Kubernetes-jegyzékfájl meghatározza, milyen tároló-lemezkép futtatásához például a fürt célállapotát. Ebben a cikkben egy jegyzékfájlt használunk az ASP.NET-mintaalkalmazás egy Windows Server tárolóban való futtatásához szükséges összes objektum létrehozásához. A jegyzékfájlt tartalmaz egy [üzemelő Kubernetes-példányt] [ kubernetes-deployment] az ASP.NET-mintaalkalmazás és a egy külső [Kubernetes-szolgáltatást] [ kubernetes-service] , az alkalmazás eléréséhez az internetről.

A minta ASP.NET-alkalmazás részeként van megadva a [.NET-keretrendszer minták] [ dotnet-samples] és a egy Windows Server-tárolót futtat. Az AKS igényel alapja a lemezképek a Windows Server-tárolók *Windows Server 2019* vagy nagyobb. A Kubernetes-jegyzékfájl is meg kell határoznia egy [csomópont választó] [ node-selector] állapítható meg, hogy az AKS-fürt az ASP.NET mintaalkalmazás pod egy csomóponton, amelyek futtathatók a Windows Server-tárolók futtatásához.

Hozzon létre egy fájlt `sample.yaml` , és másolja a következő YAML-definícióban. Ha az Azure Cloud Shellt használja, ez a fájl lehet létrehozni használatával `vi` vagy `nano` , mintha egy virtuális vagy fizikai rendszeren:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800m
          requests:
            cpu: .1
            memory: 300m
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Üzembe helyezés az alkalmazás a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```azurecli-interactive
kubectl apply -f sample.yaml
```

Az alábbi példa kimenetében látható, az üzembe helyezés és a szolgáltatás sikeresen létrehozva:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor egy Kubernetes-szolgáltatást mutatja meg az alkalmazás előtér az internethez. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```azurecli-interactive
kubectl get service sample --watch
```

Kezdetben a *EXTERNAL-IP* számára a *minta* szolgáltatás jelenik meg, mint *függőben lévő*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a *EXTERNAL-IP* -cím módosul a *függőben lévő* egy tényleges nyilvános IP-címet használja `CTRL-C` leállítani a `kubectl` tekintse meg a folyamatot. Az alábbi példa kimenetében látható, érvényes nyilvános IP-címet rendel hozzá a szolgáltatáshoz:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

A mintaalkalmazás a működés megtekintéséhez nyissa meg a szolgáltatás külső IP-címét egy webböngészőben.

![ASP.NET-mintaalkalmazás keresését ábrázoló kép](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Fürt törlése

Ha a fürtre már nincs szükség, az [az group delete][az-group-delete] paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.

## <a name="next-steps"></a>További lépések

Ebben a cikkben üzembe helyezett egy Kubernetes-fürt és a központi telepítésű egy ASP.NET-mintaalkalmazást, a Windows Server-tárolóban. [Hozzáférés a Kubernetes webes irányítópulthoz] [ kubernetes-dashboard] az újonnan létrehozott fürt számára.

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AKS-oktatóanyag][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[aks-github]: https://github.com/azure/aks/issues
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
