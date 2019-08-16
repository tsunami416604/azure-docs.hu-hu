---
title: Előzetes verzió – szabványos SKU Load Balancer használata az Azure Kubernetes Service-ben (ak)
description: Megtudhatja, hogyan használhatja a Load balancert standard SKU-val, hogy szolgáltatásai elérhetők legyenek az Azure Kubernetes szolgáltatással (ak).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: 68968c1f846a1052600a8ed108f4d40fd9d8f2cb
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534728"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Előzetes verzió – szabványos SKU Load Balancer használata az Azure Kubernetes Service-ben (ak)

Ha az Azure Kubernetes szolgáltatásban (ak) kíván hozzáférést biztosítani az alkalmazásaihoz, létrehozhat és használhat egy Azure Load Balancer. Az AK-on futó Load Balancer belső vagy külső terheléselosztóként is használható. A belső terheléselosztó csak az AK-fürttel azonos virtuális hálózaton futó alkalmazások számára teszi elérhetővé a Kubernetes szolgáltatást. A külső terheléselosztó egy vagy több nyilvános IP-címet kap a bejövő forgalom számára, és a nyilvános IP-címek használatával a Kubernetes szolgáltatás kívülről is elérhetővé válik.

Azure Load Balancer két SKU-ban érhető el – alapszintű és *standard*. Alapértelmezés szerint a rendszer az alapszintű SKU-t használja, amikor egy szolgáltatási jegyzékfájlt használ a TERHELÉSELOSZTÓ az AK-ban való létrehozásához. A *standard* SKU Load Balancer használata további funkciókat és funkciókat kínál, például nagyobb méretű háttérrendszer-készletet és Availability Zones. Fontos, hogy megértse a *standard* és az alapszintű terheléselosztó közötti különbségeket, mielőtt kiválasztja, hogy melyiket kívánja használni. Ha egy AK-fürtöt hoz létre, az adott fürthöz tartozó terheléselosztó SKU nem módosítható. Az alapszintű és a *standard* SKU-ról további információt az [Azure Load Balancer SKU][azure-lb-comparison]-összehasonlítását ismertető témakörben talál.

Ebből a cikkből megtudhatja, hogyan hozhat létre és használhat egy *szabványos* SKU-val rendelkező Azure Load Balancert az Azure Kubernetes Service (ak) használatával.

Ez a cikk a Kubernetes és a Azure Load Balancer fogalmak alapszintű megismerését feltételezi. További információ: a [Kubernetes alapvető fogalmai az Azure Kubernetes Service-ben (ak)][kubernetes-concepts] és [Mi az Azure Load Balancer?][azure-lb]

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.59 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="before-you-begin"></a>Előkészületek

Ha meglévő alhálózatot vagy erőforráscsoportot használ, a (z) alhálózati erőforrás-kezelőhöz a hálózati erőforrások kezeléséhez engedély szükséges. Általában rendelje hozzá a *hálózati közreműködő* szerepkört az egyszerű szolgáltatáshoz a delegált erőforrásokon. Az engedélyekkel kapcsolatos további információkért lásd: [AK-hozzáférés delegálása más Azure][aks-sp]-erőforrásokhoz.

Létre kell hoznia egy AK-fürtöt, amely a terheléselosztó SKU-jának *standard* értékre állítjaaz alapértelmezett alapszintű helyett. Egy AK-fürt létrehozása egy későbbi lépésben történik, de először engedélyeznie kell néhány előzetes verziót.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

Az Azure Load Balancer standard SKU használatához szüksége van az *AK-* előnézeti CLI-bővítmény 0.4.1 vagy újabb verziójára. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg az elérhető frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>AKSAzureStandardLoadBalancer előzetes funkciójának regisztrálása

Ha olyan AK-fürtöt szeretne létrehozni, amely a *standard* SKU-val használhat terheléselosztó-t, engedélyeznie kell a *AKSAzureStandardLoadBalancer* funkció jelzőjét az előfizetésében. A *AKSAzureStandardLoadBalancer* szolgáltatás a *VMSSPreview* -t is használja a virtuális gépek méretezési csoportjaival történő létrehozásakor. Ez a szolgáltatás a fürt konfigurálásakor a legújabb szolgáltatás-fejlesztéseket biztosítja. Habár nem szükséges, javasoljuk, hogy engedélyezze a *VMSSPreview* funkció jelzőjét is.

> [!CAUTION]
> Ha regisztrál egy szolgáltatást egy előfizetéshez, jelenleg nem tudja regisztrálni a szolgáltatást. Az előzetes verziójú funkciók engedélyezése után az alapértelmezett beállítások az előfizetésben létrehozott összes AK-fürthöz használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

Regisztrálja a *VMSSPreview* és a *AKSAzureStandardLoadBalancer* funkció jelzőit az az [Feature Register][az-feature-register] paranccsal az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> A *VMSSPreview* vagy a *AKSAzureStandardLoadBalancer* funkciójának sikeres regisztrálását követően létrehozott AK-fürtök ezt az előzetes verziójú fürtöt használják. A rendszeres, teljes mértékben támogatott fürtök létrehozásához ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Az előzetes verzió funkcióinak teszteléséhez használjon külön tesztelési vagy fejlesztési Azure-előfizetést.

Néhány percet vesz igénybe, amíg az állapot *regisztrálva*jelenik meg. A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Korlátozások

A következő korlátozások érvényesek a terheléselosztó és a *szabványos* SKU-t támogató AK-fürtök létrehozásakor és kezelésekor:

* A terheléselosztó *szabványos* SKU-jának használatakor engedélyeznie kell a nyilvános címeket, és el kell kerülnie az IP-létrehozást megtiltó Azure Policy létrehozását. Az AK-fürt automatikusan létrehoz egy *szabványos* SKU nyilvános IP-címet ugyanabban az erőforráscsoporthoz, amely az AK-fürthöz lett létrehozva, amely általában a *MC_* elején szerepel. Az AK a nyilvános IP-címet a *standard* SKU Load Balancerhez rendeli. A nyilvános IP-cím szükséges ahhoz, hogy engedélyezi a kimenő forgalmat az AK-fürtről. Ez a nyilvános IP-cím a vezérlési sík és az ügynök csomópontjai közötti kapcsolat fenntartásához is szükséges, valamint az AK korábbi verzióival való kompatibilitás fenntartásához.
* A terheléselosztó *szabványos* SKU-jának használatakor a Kubernetes 1.13.5 vagy újabb verzióját kell használnia.

Habár ez a funkció előzetes verzióban érhető el, a következő további korlátozások érvényesek:

* Ha a *standard* SKU-t az AK-ban lévő terheléselosztó esetében használja, nem állíthatja be a kimenő forgalomhoz tartozó saját nyilvános IP-címet a terheléselosztó számára. A terheléselosztó IP-címét kell használnia.
* Ez a [csomópont nyilvános IP-](use-multiple-node-pools.md#assign-a-public-ip-per-node-in-a-node-pool)címével nem használható.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely határozza meg, hogy az erőforráscsoport metaadatai hol vannak tárolva, és az erőforrások hol futnak az Azure-ban, ha nem ad meg másik régiót az erőforrások létrehozásakor. Hozzon létre egy erőforráscsoportot az az [Group Create][az-group-create] paranccsal.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A következő példa kimenete azt mutatja, hogy az erőforráscsoport sikeresen létrejött:

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
Ha olyan AK-fürtöt szeretne futtatni, amely támogatja a *standard* SKU-t a terheléselosztó számára, a fürtnek a *Load-Balancer-SKU* paramétert a *standard*értékre kell állítania. Ez a paraméter létrehoz egy Load balancert a *standard* SKU-val a fürt létrehozásakor. Amikor *terheléselosztó* szolgáltatást futtat a fürtön, a *standard* SK Load Balancer konfigurációja frissül a szolgáltatás konfigurációjával. Az az [AK Create][az-aks-create] paranccsal hozzon létre egy *myAKSCluster*nevű AK-fürtöt.

> [!NOTE]
> A *Load-Balancer-SKU* tulajdonság csak akkor használható, ha a fürt létrejött. Egy AK-fürt létrehozása után nem módosíthatja a terheléselosztó SKU-t. Emellett egyetlen fürtben csak egyetlen típusú terheléselosztó SKU-t használhat.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Néhány perc elteltével a parancs befejeződik, és a fürthöz tartozó JSON-formátumú adatokat adja vissza.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez a [kubectl][kubectl], a Kubernetes parancssori ügyfélprogramot kell használnia. Ha Azure Cloud shellt használ, `kubectl` már telepítve van. A helyi `kubectl` telepítéshez használja az az [AK install-CLI][az-aks-install-cli] parancsot:

```azurecli
az aks install-cli
```

A Kubernetes `kubectl` -fürthöz való kapcsolódás konfigurálásához használja az az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példakimenet az előző lépésekben létrehozott csomópontot mutatja be. Győződjön meg arról, hogy a csomópont állapota *kész*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.9
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Ellenőrizze, hogy a fürt a *szabványos* SKU-t használja-e

A fürt konfigurációjának megjelenítéséhez használja az az [AK show][az-aks-show] -t.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

Ellenőrizze, hogy a *loadBalancerSku* tulajdonság *szabványosként*jelenik-e meg.

## <a name="use-the-load-balancer"></a>A terheléselosztó használata

A terheléselosztó a fürtön való használatához hozzon létre egy szolgáltatási jegyzékfájlt a *terheléselosztó*szolgáltatás típusával. A terheléselosztó működésének megjelenítéséhez hozzon létre egy újabb jegyzékfájlt egy minta alkalmazással a fürtön való futtatáshoz. Ez a minta alkalmazás a terheléselosztó segítségével érhető el, és böngészőben megtekinthető.

Hozzon létre egy `sample.yaml` nevű jegyzékfájlt az alábbi példában látható módon:

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
```

A fenti jegyzékfájl két üzemelő példányt konfigurál: *Azure-vote-elsőfékes* és *Azure-vote-back*. Ha az *Azure-vote-elsőfékes* üzembe helyezést úgy szeretné beállítani, hogy a terheléselosztó használatával elérhető legyen `standard-lb.yaml` , hozzon létre egy nevű jegyzékfájlt az alábbi példában látható módon:

```yaml
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

Az *Azure-vote* szolgáltatás a *terheléselosztó* típus használatával konfigurálja a TERHELÉSELOSZTÓ-t az AK-fürtön az *Azure-vote-elsőfékes* üzembe helyezéshez való kapcsolódáshoz.

Telepítse a minta alkalmazást és a Load balancert a [kubectl alkalmazásával][kubectl-apply] , és adja meg a YAML-jegyzékfájlok nevét:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

A *standard* SKU Load Balancer már konfigurálva van a minta alkalmazás elérhetővé tételéhez. A Load Balancer nyilvános IP-címének megtekintéséhez tekintse meg az *Azure-vote-* [kubectl][kubectl-get] használatával kapcsolatos információkat. A terheléselosztó nyilvános IP-címe a *külső IP-* oszlopban látható. Előfordulhat, hogy az IP-cím egy percet vagy kettőt is igénybe vehet, és az az alábbi példában látható módon változhat *\<a függőben lévő\>* külső IP-címről.

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Nyissa meg a nyilvános IP-címet egy böngészőben, és ellenőrizze, hogy megjelenik-e a minta alkalmazás. A fenti példában a nyilvános IP-cím `52.179.23.131`:.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Azt is beállíthatja, hogy a terheléselosztó belső legyen, és ne tegye közzé a nyilvános IP-címet. A terheléselosztó belsőként való konfigurálásához vegyen `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` fel jegyzetként a *terheléselosztó* szolgáltatásba. [Itt][internal-lb-yaml]láthat egy példát a YAML-jegyzékre, valamint további részleteket is megtudhat a belső terheléselosztó használatával kapcsolatban.

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>A standard SKU Load Balancer konfigurációjának törlése

A minta alkalmazás és a terheléselosztó konfigurációjának eltávolításához használja a [kubectl delete][kubectl-delete]parancsot:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>További lépések

További információ a Kubernetes Services szolgáltatásról a [Kubernetes Services dokumentációjában][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
