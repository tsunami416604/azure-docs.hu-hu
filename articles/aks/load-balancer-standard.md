---
title: Standard SKU Load Balancer használata az Azure Kubernetes Service-ben (ak)
description: Megtudhatja, hogyan használhatja a Load balancert standard SKU-val, hogy szolgáltatásai elérhetők legyenek az Azure Kubernetes szolgáltatással (ak).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/05/2019
ms.author: zarhoads
ms.openlocfilehash: d2a0ff5db6707c4f765c71937a7d0f0749401959
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172208"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Standard SKU Load Balancer használata az Azure Kubernetes Service-ben (ak)

Ha az Azure Kubernetes szolgáltatásban (ak) kíván hozzáférést biztosítani az alkalmazásaihoz, létrehozhat és használhat egy Azure Load Balancer. Az AK-on futó Load Balancer belső vagy külső terheléselosztóként is használható. A belső terheléselosztó csak az AK-fürttel azonos virtuális hálózaton futó alkalmazások számára teszi elérhetővé a Kubernetes szolgáltatást. A külső terheléselosztó egy vagy több nyilvános IP-címet kap a bejövő forgalom számára, és a nyilvános IP-címek használatával a Kubernetes szolgáltatás kívülről is elérhetővé válik.

Azure Load Balancer két SKU-ban érhető el – alapszintű és *standard*. Alapértelmezés szerint a rendszer az *alapszintű* SKU-t használja, amikor egy szolgáltatási jegyzékfájlt használ a TERHELÉSELOSZTÓ az AK-ban való létrehozásához. A *standard* SKU Load Balancer használata további funkciókat és funkciókat kínál, például nagyobb méretű háttérrendszer-készletet és Availability Zones. Fontos, hogy megértse a *standard* és az *alapszintű* terheléselosztó közötti különbségeket, mielőtt kiválasztja, hogy melyiket kívánja használni. Ha egy AK-fürtöt hoz létre, az adott fürthöz tartozó terheléselosztó SKU nem módosítható. Az *alapszintű* és a *standard* SKU-ról további információt az [Azure Load Balancer SKU-összehasonlítását][azure-lb-comparison]ismertető témakörben talál.

Ebből a cikkből megtudhatja, hogyan hozhat létre és használhat egy *szabványos* SKU-val rendelkező Azure Load Balancert az Azure Kubernetes Service (ak) használatával.

Ez a cikk a Kubernetes és a Azure Load Balancer fogalmak alapszintű megismerését feltételezi. További információ: a [Kubernetes alapvető fogalmai az Azure Kubernetes Service-ben (ak)][kubernetes-concepts] és [Mi az Azure Load Balancer?][azure-lb]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.59 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="before-you-begin"></a>Előkészületek

Ha meglévő alhálózatot vagy erőforráscsoportot használ, a (z) alhálózati erőforrás-kezelőhöz a hálózati erőforrások kezeléséhez engedély szükséges. Általában rendelje hozzá a *hálózati közreműködő* szerepkört az egyszerű szolgáltatáshoz a delegált erőforrásokon. Az engedélyekkel kapcsolatos további információkért lásd: [AK-hozzáférés delegálása más Azure-erőforrásokhoz][aks-sp].

Létre kell hoznia egy AK-fürtöt, amely a terheléselosztó SKU-jának *standard* értékre állítja az alapértelmezett *alapszintű*helyett.

### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

Az Azure Load Balancer standard SKU használatához szüksége van az *AK-előnézeti CLI-* bővítmény 0.4.12 vagy újabb verziójára. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Korlátozások

A következő korlátozások érvényesek a terheléselosztó és a *szabványos* SKU-t támogató AK-fürtök létrehozásakor és kezelésekor:

* Legalább egy nyilvános IP-cím vagy IP-előtag szükséges ahhoz, hogy a kimenő forgalom az AK-fürtből legyen engedélyezve. A nyilvános IP-cím vagy IP-előtag szükséges a vezérlési sík és az ügynök csomópontjai közötti kapcsolat fenntartásához, valamint az AK korábbi verzióival való kompatibilitás fenntartásához is. A következő lehetőségek közül választhat a *szabványos* SKU Load balancerrel rendelkező nyilvános IP-címek vagy IP-előtagok megadásához:
    * Adja meg saját nyilvános IP-címeit.
    * Adja meg saját nyilvános IP-előtagjait.
    * 100-ig terjedő számot kell megadnia, amely lehetővé teszi, hogy az AK-fürt számos *szabványos* SKU-beli nyilvános IP-címet hozzon létre ugyanabban az erőforráscsoporthoz, amely az AK-fürthöz lett létrehozva, amely általában a *MC_* elején található. Az AK a nyilvános IP-címet a *standard* SKU Load Balancerhez rendeli. Alapértelmezés szerint a rendszer automatikusan létrehoz egy nyilvános IP-címet ugyanabban az erőforráscsoportban, mint az AK-fürtöt, ha nincs megadva nyilvános IP-cím, nyilvános IP-előtag vagy IP-címek száma. Emellett engedélyeznie kell a nyilvános címeket, és el kell kerülnie az IP-létrehozást megtiltó Azure Policy létrehozását.
* A terheléselosztó *szabványos* SKU-jának használatakor a 1,13-es vagy újabb Kubernetes-verziót kell használnia.
* A terheléselosztó SKU definiálása csak akkor hajtható végre, ha AK-fürtöt hoz létre. Egy AK-fürt létrehozása után nem módosíthatja a terheléselosztó SKU-t.
* Egyetlen fürtben csak egy terheléselosztó SKU-t használhat.

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
Ha olyan AK-fürtöt szeretne futtatni, amely támogatja a *standard* SKU-t a terheléselosztó számára, a fürtnek a *Load-Balancer-SKU* paramétert a *standard*értékre kell állítania. Ez a paraméter létrehoz egy Load balancert a *standard* SKU-val a fürt létrehozásakor. Amikor *terheléselosztó* szolgáltatást futtat a fürtön, a *standard* SKU Load Balancer konfigurációja frissül a szolgáltatás konfigurációjával. Az az [AK Create][az-aks-create] paranccsal hozzon létre egy *myAKSCluster*nevű AK-fürtöt.

> [!NOTE]
> A *Load-Balancer-SKU* tulajdonság csak akkor használható, ha a fürt létrejött. Egy AK-fürt létrehozása után nem módosíthatja a terheléselosztó SKU-t. Emellett egyetlen fürtben csak egyetlen típusú terheléselosztó SKU-t használhat.
> 
> Ha saját nyilvános IP-címeket szeretne használni, használja a *Load-Balancer-kimenő-* IP-címeket vagy a *Load-Balancer-kimenő-IP-előtag* paramétereket. Mindkét paraméter használható [a fürt frissítésekor](#optional---provide-your-own-public-ips-or-prefixes-for-egress)is.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
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
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
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

## <a name="optional---scale-the-number-of-managed-public-ips"></a>Nem kötelező – a felügyelt nyilvános IP-címek számának méretezése

Ha egy *szabványos* SKU-Load balancert használ a felügyelt kimenő nyilvános IP-címekkel, amelyek alapértelmezés szerint jönnek létre, akkor a felügyelt kimenő nyilvános IP-címeket a *terheléselosztó – felügyelt-IP-Count* paraméter használatával méretezheti.

Meglévő fürt frissítéséhez futtassa a következő parancsot. Ez a paraméter a fürt létrehozási ideje beállításnál is beállítható, hogy több felügyelt kimenő nyilvános IP-cím legyen.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

A fenti példa a felügyelt kimenő nyilvános IP-címek számát állítja be a *myResourceGroup*-ben található *myAKSCluster* - *fürt esetében.* 

A terheléselosztó által *felügyelt-IP-Count* paraméterrel is beállíthatja a felügyelt kimenő nyilvános IP-címek kezdeti számát a fürt létrehozásakor a `--load-balancer-managed-outbound-ip-count` paraméter hozzáfűzésével és a kívánt értékre való beállításával. A felügyelt kimenő nyilvános IP-címek alapértelmezett száma 1.

## <a name="optional---provide-your-own-public-ips-or-prefixes-for-egress"></a>Opcionális – saját nyilvános IP-címek vagy előtagok megadása a kimenő forgalomhoz

*Standard* SKU Load Balancer használatakor az AK-fürt automatikusan létrehoz egy nyilvános IP-címet ugyanabban az erőforráscsoporthoz, amely az AK-fürthöz lett létrehozva, és hozzárendeli a nyilvános IP-címet a *standard* SKU Load Balancerhez. Azt is megteheti, hogy a fürt létrehozási idején saját nyilvános IP-címet rendel hozzá, vagy frissítheti a meglévő fürt terheléselosztó-tulajdonságait.

Több IP-cím vagy előtag megadásával több háttér-szolgáltatást is meghatározhat, ha az IP-címet egyetlen terheléselosztó objektum mögött definiálja. Az adott csomópontok kimenő végpontja attól függ, hogy milyen szolgáltatáshoz vannak társítva.

> [!IMPORTANT]
> *Szabványos* SKU nyilvános IP-címeket kell használnia a kimenő forgalomhoz a Load Balancer *standard* SKU-jának megfelelően. A nyilvános IP-címek SKU-jának ellenőrzéséhez használja az az [Network Public-IP show][az-network-public-ip-show] parancsot:
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

A nyilvános IP-címek azonosítóinak listázásához használja az az [Network Public-IP show][az-network-public-ip-show] parancsot.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

A fenti parancs a *myPublicIP* nyilvános IP-CÍMÉnek azonosítóját jeleníti meg a *myResourceGroup* -erőforráscsoporthoz.

Használja az az *AK Update* parancsot a *Load-Balancer-kimenő-IPS* paraméterrel, és frissítse a fürtöt a nyilvános IP-címekkel.

A következő példa a *Load-Balancer-kimenő-IPS* paramétert használja az előző parancs azonosítói alapján.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Nyilvános IP-előtagokat is használhat a *standard* SKU Load balancerrel való kimenő forgalomhoz. Az alábbi példa az az [Network Public-IP előtag show][az-network-public-ip-prefix-show] parancsot használja a nyilvános IP-előtagok azonosítóinak listázásához:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

A fenti parancs a *myPublicIPPrefix* nyilvános IP-előtag azonosítóját jeleníti meg a *myResourceGroup* erőforráscsoporthoz.

Az alábbi példa a *Load-Balancer-kimenő-IP-előtag* paramétert használja az előző parancs azonosítói alapján.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> A nyilvános IP-címeknek és az IP-ELŐTAGOKNAK ugyanabban a régióban kell lenniük, és ugyanannak az előfizetésnek kell szerepelniük, mint az AK-fürt. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Saját nyilvános IP-cím vagy előtag definiálása a fürt létrehozási idején

Előfordulhat, hogy saját IP-címeket vagy IP-előtagokat kíván létrehozni a kimenő forgalomhoz a fürt létrehozási ideje alatt, hogy támogassa a kimenő végpontok engedélyezési forgatókönyveit. Fűzze hozzá ugyanezeket a paramétereket a fürt létrehozási lépéseként, hogy meghatározza a saját nyilvános IP-címeit és az IP-előtagokat a fürt életciklusának elején.

*A* *Load-Balancer-kimenő-IPS* paraméterrel hozzon létre egy új fürtöt a nyilvános IP-címekkel az elején.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Használja az az *AK Create* parancsot a *Load-Balancer-kimenő-IP-előtag* paraméterrel egy új fürt létrehozásához a nyilvános IP-előtagokkal az elején.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

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
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

