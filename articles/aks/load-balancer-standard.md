---
title: Előzetes verzió – Standard Termékváltozatú terheléselosztó az Azure Kubernetes Service (AKS) használatához
description: Megtudhatja, hogyan tölthet fel szolgáltatásokat az Azure Kubernetes Service (AKS) egy Standard Termékváltozatú terheléselosztó használata.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476808"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Előzetes verzió – Standard Termékváltozatú terheléselosztó az Azure Kubernetes Service (AKS) használatához

A hozzáférés biztosításához az alkalmazások az Azure Kubernetes Service (AKS) létrehozása és a egy Azure Load Balancert használjon. Az aks-en futó load balancer belső vagy külső terheléselosztó használható. Belső terheléselosztó csak az AKS-fürt ugyanazon a virtuális hálózaton futó alkalmazások számára elérhető lesz az egy Kubernetes-szolgáltatást. Egy külső terheléselosztó egy vagy több nyilvános IP-címek a bejövő forgalom fogadja, és lehetővé teszi egy Kubernetes-szolgáltatás elérhető, külsőleg történik a nyilvános IP-címek.

Az Azure Load Balancer érhető el két termékváltozata - *alapszintű* és *Standard*. Alapértelmezés szerint a *alapszintű* Termékváltozatot használja a szolgáltatásjegyzék load balancer létrehozása AKS-en való használatakor. Használatával egy *Standard* Termékváltozatú terheléselosztó biztosít további szolgáltatásokat és funkciókat, például a nagyobb háttér-példánykészlet méretét és a rendelkezésre állási zónák. Fontos, hogy közötti különbségek megértéséhez *Standard* és *alapszintű* terheléselosztók használandó kiválasztása előtt. Ha egy AKS-fürtöt hoz létre, a terheléselosztó Termékváltozat, hogy a fürt nem módosítható. További információ a *alapszintű* és *Standard* SKU-k, lásd: [Azure load balancer Termékváltozat összehasonlító][azure-lb-comparison].

Ez a cikk bemutatja, hogyan hozhat létre és használhat egy Azure Load Balancer a *Standard* Azure Kubernetes Service (AKS) Termékváltozat.

Ez a cikk feltételezi, hogy ismeri a Kubernetes és az Azure Load Balancer fogalmak alapvető. További információkért lásd: [Kubernetes alapvető fogalmait Azure Kubernetes Service (AKS)][kubernetes-concepts] and [What is Azure Load Balancer?][azure-lb].

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület, ez a cikk van szükség, hogy futnak-e az Azure CLI verziója 2.0.59 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="before-you-begin"></a>Előkészületek

Egyszerű az AKS-fürt szolgáltatás hálózati erőforrások kezeléséhez, ha egy meglévő alhálózat vagy erőforráscsoport engedélyre van szüksége. Általánosságban elmondható, rendelje hozzá a *hálózati közreműködő* szerepkört a szolgáltatásnévhez a delegált erőforrásokon. Az engedélyekről további információkért lásd: [delegált AKS más Azure-erőforrásokhoz való hozzáférés][aks-sp].

Létre kell hoznia egy AKS-fürtöt, amely beállítja, hogy a terheléselosztó a Termékváltozat *Standard* helyett az alapértelmezett *alapszintű*. AKS-fürt létrehozása egy későbbi lépésben tárgyalunk, de először engedélyeznie kell néhány előzetes verziójú funkciók.

> [!IMPORTANT]
> Az AKS előzetes verziójú funkciók önkiszolgáló, a rendszer. A biztosított gyűjthet visszajelzéseket és a hibák kapcsolódóan a Közösség részéről. Előzetes verzióban elérhető ezeket a funkciókat nem üzemi használat céljára. Nyilvános előzetes verzióban érhető el "ajánlott beavatkozást" támogatás keretében tartoznak. Az AKS technikai támogatási csapat segítségét munkaidőben csendes-óceáni időzóna (PST) csak alatt érhető el. További információkért tekintse meg a következő cikkek támogatja:
>
> * [Az AKS támogatási házirendek][aks-support-policies]
> * [Az Azure-támogatás – gyakori kérdések][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Az aks előzetes CLI-bővítmény telepítése

Használata az Azure load balancer standard Termékváltozat szükséges a *aks előzetes* CLI bővítmény verziója 0.4.1 vagy újabb verziója. Telepítse a *aks előzetes* Azure CLI-bővítmény használata a [az bővítmény hozzáadása][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] parancs::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>AKSAzureStandardLoadBalancer előzetes verziójú funkció regisztrálása

Az AKS-fürt által használható egy terheléselosztó létrehozása a *Standard* SKU, engedélyeznie kell a *AKSAzureStandardLoadBalancer* funkció az előfizetésében jelző. A *AKSAzureStandardLoadBalancer* szolgáltatás is használ *VMSSPreview* virtuálisgép-méretezési csoportok használata a fürt létrehozásakor. Ez a funkció teszi lehetővé a legújabb szolgáltatásfejlesztések fürt konfigurálása során. Bár ez nem szükséges, ajánlott engedélyezi a *VMSSPreview* funkció jelzőt is.

> [!CAUTION]
> A funkció egy adott előfizetés regisztrálásakor nem jelenleg regisztrációjának ezt a funkciót. Miután engedélyezte az egyes előzetes verziójú funkciók, alapértelmezett érték az összes AKS-fürt, majd az előfizetésben létrehozott használható. Nem engedélyezi az előzetes verziójú funkciók az éles üzemű előfizetéseket. Használjon különálló előfizetést előzetes verziójú funkciók teszteléséhez, és visszajelzést.

Regisztrálja a *VMSSPreview* és *AKSAzureStandardLoadBalancer* funkció jelzők segítségével a [az a funkció regisztrálása][az-feature-register] parancsot az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Bármely AKS-fürtöt hoz létre, miután sikeresen regisztrálta a *VMSSPreview* vagy *AKSAzureStandardLoadBalancer* funkció jelzők előzetes fürt felület használja. Továbbra is rendszeres, teljes mértékben támogatott fürtök létrehozását, ne engedélyezze az előzetes verziójú funkciók az éles üzemű előfizetéseket. Előzetes verziójú funkciók teszteléséhez használja egy külön teszt- vagy Azure-előfizetés.

Az állapot megjelenítése néhány percet vesz igénybe *regisztrált*. A regisztrációs állapot használatával ellenőrizheti a [az szolgáltatáslistát][az-feature-list] parancsot:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a regisztrációját a *Microsoft.ContainerService* erőforrás-szolgáltató használatával a [az provider register][az-provider-register] parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Korlátozások

Az alábbi korlátozások érvényesek, ha hoz létre, és a load balancer támogatja az AKS-fürtök kezelése a *Standard* Termékváltozat:

* Használatakor a *Standard* SKU egy terheléselosztóhoz, kell lehetővé teszik nyilvános cím, és ne hozzon létre bármelyik Azure-szabályzatot, amely a forgalomból IP létrehozása. Az AKS-fürt automatikusan létrehoz egy *Standard* Termékváltozat nyilvános IP-Címmel ugyanabban az erőforráscsoportban létrehozott az AKS-fürtöt, amely általában nevű *MC_* elején. Az AKS rendeli hozzá a nyilvános IP-cím a *Standard* Termékváltozat terheléselosztó. A nyilvános IP-cím megadása kötelező az AKS-fürtöt a kimenő forgalom engedélyezéséhez. A nyilvános IP-cím is szükséges a vezérlési sík és az ügynök csomópontok közötti, valamint az AKS korábbi verzióival való kompatibilitás megőrzése érdekében, hogy a kapcsolat fenntartása érdekében.
* Használatakor a *Standard* Termékváltozat egy terheléselosztó Kubernetes 1.13.5 verziót kell használnia, vagy nagyobb.

Bár ez a funkció előzetes verzióban érhető el, a következő további korlátozások vonatkoznak:

* Használatakor a *Standard* Termékváltozat a terheléselosztó az aks-ben, nem állíthat kimenő forgalom számára a saját nyilvános IP-címet a terheléselosztóhoz. Az AKS hozzárendeli a terheléselosztó IP-címet kell használnia.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely az erőforrás-csoport metaadatok tárolására, egyben ahol az erőforrások futtatása az Azure-ban, ha nem ad meg egy másik régióba erőforrás létrehozásakor. Hozzon létre egy erőforrás csoport a [az csoport létrehozása][az-group-create] parancsot.

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
Támogatja egy terheléselosztót az AKS-fürt futtatásához a *Standard* Termékváltozat, a fürt be kell állítania a *load-balancer-termékváltozat* paramétert *standard*. Ez a paraméter egy terheléselosztót hoz létre a *Standard* Termékváltozat, a fürt létrehozásakor. Ha futtatja egy *terheléselosztó* szolgáltatást a fürt konfigurációját a a *Standard* SK terheléselosztó frissül a szolgáltatás konfigurációját. Használja a [az aks létrehozása][az-aks-create] paranccsal hozzon létre egy AKS-fürt nevű *myAKSCluster*.

> [!NOTE]
> A *load-balancer-termékváltozat* tulajdonság csak akkor használható, amikor létrehozza a fürtöt. A load balancer Termékváltozat AKS-fürt létrehozása után nem módosítható. Emellett csak használhatja egy adott típusú terheléselosztóhoz Termékváltozat egy adott fürtben.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Néhány perc múlva a parancs befejeződik, és visszaadja a fürttel kapcsolatos adatokat JSON formátumban.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Kubernetes-fürtök kezeléséhez használja [kubectl][kubectl], a Kubernetes parancssori ügyfelét. Ha az Azure Cloud Shellben használja `kubectl` már telepítve van. A telepítendő `kubectl` helyileg, használja a [az aks install-cli][az-aks-install-cli] parancsot:

```azurecli
az aks install-cli
```

Konfigurálása `kubectl` a Kubernetes-fürt csatlakozni, használja a [az aks get-credentials][az-aks-get-credentials] parancsot. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori Felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példakimenet az előző lépésekben létrehozott csomópontot mutatja be. Győződjön meg arról, hogy a csomópont állapota *készen*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Ellenőrizze, hogy a fürt használja a *Standard* Termékváltozat

Használja a [az aks show][az-aks-show] , jelenítse meg a fürt konfigurációját.

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

Ellenőrizze a *loadBalancerSku* tulajdonság állapota *standard*.

## <a name="use-the-load-balancer"></a>A load balancer használata

A terheléselosztó a fürtön használatához hozzon létre egy Szolgáltatásjegyzék szolgáltatás típusú *terheléselosztó*. A terheléselosztó működik megjelenítéséhez, hozzon létre egy másik jegyzékfájl futtatunk a fürtön egy mintaalkalmazást. Ez a mintaalkalmazás a terheléselosztón keresztül van közzétéve, és egy böngészőablakban tekinthet meg.

Hozzon létre egy jegyzéket nevű `sample.yaml` az alábbi példában látható módon:

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

A fenti jegyzékfájl konfigurálja a két üzembe helyezés: *azure-vote-front* és *azure-vote-back*. Konfigurálása *azure-vote-front* ki vannak téve a load balancer használatával üzembe helyezési nevű jegyzék létrehozásához `standard-lb.yaml` az alábbi példában látható módon:

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

A szolgáltatás *azure-vote-front* használja a *terheléselosztó* írja be a terheléselosztó konfigurálásához szeretne csatlakozni az AKS-fürt a *azure-vote-front* központi telepítés.

A mintaalkalmazás üzembe helyezése és a load balancer használatával a [a kubectl a alkalmazni][kubectl-apply] adja meg a YAML jegyzékek nevét:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

A *Standard* Termékváltozatú terheléselosztó van konfigurálva a mintaalkalmazás elérhetővé. Szolgáltatás részleteinek megtekintéséhez *azure-vote-front* használatával [kubectl get][kubectl-get] a terheléselosztó nyilvános IP-cím megtekintéséhez. A terheléselosztó nyilvános IP-cím látható a *EXTERNAL-IP* oszlop. Eltarthat néhány percig is a módosítani kívánt IP-cím *\<függőben lévő\>* tényleges külső IP-címhez, az alábbi példában látható módon:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Keresse meg a nyilvános IP-cím böngészőben, és ellenőrizze, hogy látja a mintaalkalmazás. A fenti példában a nyilvános IP-cím `52.179.23.131`.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Belső lehet, hogy a terheléselosztó is konfigurálhatja, és nem teszi közzé a nyilvános IP-cím. A load balancer konfigurálása a belső, adjon hozzá `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` jegyzet, mint a *terheléselosztó* szolgáltatás. Láthatja, hogy egy belső terheléselosztót, valamint további részleteit manifest példa yaml [Itt][internal-lb-yaml].

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>A Standard Termékváltozatú terheléselosztó-konfiguráció törlése

Távolítsa el a minta alkalmazás és a load balancer konfigurációt, használja [kubectl törlése][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>További lépések

További tudnivalók a Kubernetes-szolgáltatás, a [Kubernetes services dokumentációja][kubernetes-services].

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
