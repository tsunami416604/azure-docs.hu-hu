---
title: A hálózati házirendek az Azure Kubernetes Service (AKS) használatával biztonságos podok
description: Ismerje meg, hogyan teheti biztonságossá a forgalom adataikkal podok Kubernetes hálózati házirendek segítségével az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: 81b45a25c8040916b835ab333c5ce80ab6c1a788
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772313"
---
# <a name="secure-traffic-between-pods-by-using-network-policies-in-azure-kubernetes-service"></a>Podok hálózati házirendek segítségével az Azure Kubernetes Service közötti adatforgalom védelme

A modern, mikroszolgáltatás-alapú alkalmazások a Kubernetesben való futtatásakor gyakran érdemes szabályozhatja, hogy mely összetevők kommunikálhatnak egymással. Hogyan forgalom is az Azure Kubernetes Service (AKS)-fürt podok áramlanak a legalacsonyabb jogosultsági szint elvének kell alkalmazni. Tegyük fel, valószínűleg szeretné tiltani a forgalom közvetlenül a háttér-alkalmazásokhoz. A *hálózati házirend* a Kubernetes szolgáltatás lehetővé teszi egy fürt podok közötti bejövő és kimenő forgalomra vonatkozó szabályok meghatározásához.

Calico, egy nyílt forráskódú hálózatkezeléssel és a hálózati biztonsági megoldás Tigera, által megalkotott kínál egy hálózati házirend motor, amely a Kubernetes hálózati házirend-szabályok valósíthat meg. Ez a cikk bemutatja, hogyan telepíti az Calico hálózati házirend-motort, és hozhat létre Kubernetes hálózati szabályzatokat, amelyekkel szabályozható a podok az aks-ben közötti forgalmat.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. Ezt a szolgáltatást bizonyos aspektusainak általánosan (elérhetővé tétel GA) előtt módosulhat.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.56 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

Hozzon létre egy AKS-fürtöt, amelyekkel a hálózati házirend, először engedélyeznie kell az előfizetés szolgáltatásjelzőre. Regisztrálja a *EnableNetworkPolicy* jelző funkciót, használja a [az a funkció regisztrálása] [ az-feature-register] parancsot az alábbi példában látható módon:

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Az állapot megjelenítése néhány percet vesz igénybe *regisztrált*. A regisztrációs állapot ellenőrzéséhez használatával a [az szolgáltatáslistát] [ az-feature-list] parancsot:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a regisztrációját a *Microsoft.ContainerService* erőforrás-szolgáltató használatával a [az provider register] [ az-provider-register] parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-network-policy"></a>A hálózati házirend áttekintése

AKS-fürt összes podok küldhet és fogadhat, korlátozás nélkül forgalom alapértelmezés szerint. A biztonság növelése érdekében megadhatja a szabályokat, amelyek a forgalom szabályozására. Háttér-alkalmazások gyakran csak jelennek meg a szükséges előtér-szolgáltatások, például. Vagy adatbázis-összetevői csak elérhetők az alkalmazásrétegek, amely csatlakozni hozzájuk.

Hálózati házirendek olyan Kubernetes-erőforrásokat, amelyekkel podok közötti adatforgalom szabályozásához. Kiválaszthatja, hogy engedélyezik vagy megtagadják a forgalmat a beállítások, például a hozzárendelt címkék, névtérre vagy forgalmat port alapján. Hálózati házirendek YAML jegyzékfájlok lettek definiálva. Ezek a szabályzatok a szélesebb körű jegyzékfájl, amely is létrehoz egy központi telepítés vagy a szolgáltatás része lehet.

Nézzük megtekintéséhez működés közben, a hálózati házirendeket hozzon létre, és bontsa ki a forgalom áramlását szabályzatban:

* A pod minden forgalom tiltása.
* Engedélyezi a forgalmat a pod címkék alapján.
* Engedélyezi a forgalmat a névtér alapján.

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS-fürt létrehozása és a hálózati házirend engedélyezése

A hálózati házirend csak a fürt létrehozásakor engedélyezhető. Nem engedélyezhető a hálózati házirend egy meglévő AKS-fürtre. 

A hálózati házirend-val való használatához egy AKS-fürtöt kell használnia a [beépülő modul Azure CNI] [ azure-cni] és a saját virtuális hálózat és alhálózatok megadása. Részletesebb információk tervezze meg a szükséges alhálózati tartományokat, lásd: [speciális hálózatkezelés konfigurálását][use-advanced-networking].

Az alábbi példa parancsfájl:

* Egy virtuális hálózatot és alhálózatot hoz létre.
* Létrehoz egy Azure Active Directory (Azure AD) használja az egyszerű szolgáltatást az AKS-fürtöt.
* Hozzárendeli *közreműködői* engedélyeit az AKS-fürt egyszerű szolgáltatás a virtuális hálózati.
* A megadott virtuális hálózat egy AKS-fürtöt hoz létre, és lehetővé teszi, hogy a hálózati házirend.

Adja meg a saját biztonságos *SP_PASSWORD*. Lecserélheti a *RESOURCE_GROUP_NAME* és *fürtnév* változókat:

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.6 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

A fürt létrehozása néhány percet vesz igénybe. Ha a fürt elkészült, konfigurálja `kubectl` használatával csatlakozni a Kubernetes-fürtöt a [az aks get-credentials] [ az-aks-get-credentials] parancsot. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori Felületét azok használatára:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Az összes bejövő forgalmat egy pod megtagadása

Meghatározhat szabályokat az adott hálózati forgalmat, ahhoz először hozzon létre egy hálózati házirend az összes forgalom. Ez a házirend, amelyeket engedélyezni kell a kezdéshez csak a kívánt forgalmat kiindulási pontot biztosít. Továbbá egyértelműen láthatja, hogy a rendszer elvetette a forgalmat a hálózati házirend alkalmazásakor.

A minta alkalmazás környezetben és a forgalomra vonatkozó szabályokat, először hozzon létre egy névteret nevű *fejlesztési* a példa podok futtatásához:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Hozzon létre egy nginx-et futtató példa háttér-pod. A háttér-pod szimulálása egy háttér-web-alapú mintaalkalmazás használható. Ez a pod létrehozása a *fejlesztési* névteret, és nyissa meg a portot *80-as* kiszolgálására a webes forgalom. Címkével rendelkező podot *app = webapp, szerepkör = háttérrendszer* úgy, hogy mi is ez a szakasz a hálózati házirend-cél:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Hozzon létre egy másik pod, és csatlakoztassa egy terminál-munkamenetben, hogy sikeresen érhető el az alapértelmezett NGINX-weblap teszteléséhez:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Használja a shell parancssornál `wget` , győződjön meg arról, hogy az alapértelmezett NGINX-weblap elérhető:

```console
wget -qO- http://backend
```

Az alábbi kimeneti példa azt mutatja, hogy az alapértelmezett NGINX-weblap vissza:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Lépjen ki a csatolt terminál-munkamenetben. A teszt pod automatikusan törlődik.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Hozzon létre, és a alkalmazni a hálózati házirend

Most, hogy az alapszintű NGINX-weblap használhatja a mintául szolgáló háttér-pod jóváhagyta, hozzon létre egy hálózati házirend az összes forgalom. Hozzon létre egy fájlt `backend-policy.yaml` , és illessze be a következő YAML-jegyzékfájlt. A jegyzékfájlt használ egy *podSelector* podok, amelyeken a házirend csatolni a *app:webapp, szerepkör: háttérrendszer* címke, például a minta az NGINX-pod. Nincsenek megadva szabályok alapján *bejövő*, így az összes bejövő forgalmat a pod megtagadva:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

A hálózati házirend alkalmazásához használja a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>A hálózati házirend tesztelése


Nézzük meg, ha az NGINX-weblapot a háttér-pod meg újra. Hozzon létre egy másik teszt pod, és csatlakoztassa egy terminál-munkamenetben:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Használja a shell parancssornál `wget` akkor is hozzáférhet, ha az alapértelmezett NGINX-weblap megtekintéséhez. Megadott idő beállítása egy időtúllépési értéket *2* másodperc. A hálózati házirend most már letiltja az összes bejövő forgalmat, így nem lehet betölteni a lapot, az alábbi példában látható módon:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Lépjen ki a csatolt terminál-munkamenetben. A teszt pod automatikusan törlődik.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>A pod címke alapján forgalom engedélyezéséhez.

Az előző szakaszban egy háttér-NGINX pod lett ütemezve, és a hálózati házirend az összes forgalom megtagadásához lett létrehozva. Most hozzon létre egy előtér-pod, és frissítse a hálózati házirend előtér-podok érkező adatforgalom engedélyezéséhez.

Frissítse a hálózati házirend, a feliratokkal podok érkező adatforgalom engedélyezéséhez *app:webapp, szerepkör: előtérbeli* és minden olyan névtér. Szerkessze az előző *háttér-policy.yaml* fájlt, és adja hozzá *matchLabels* bejövő szabályok, hogy a következő példához hasonlóan jelenik meg a jegyzékfájlban:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Használja a hálózati házirend- *namespaceSelector* és a egy *podSelector* elem a bejövő szabály. A YAML szintaxisa a következő fontos lehet a bejövő szabályok a additív. Mindkét elem ebben a példában meg kell egyeznie a alkalmazni bejövő szabály. Kubernetes terméknél korábbi verziókat *1.12* előfordulhat, hogy ezek az elemek értelmezi helyesen és nem a hálózati forgalom korlátozására a várt módon. Ezzel a viselkedéssel kapcsolatos további információkért lásd: [viselkedését, és a választók][policy-rules].

A frissített hálózati házirend alkalmazásához használja a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Egy pod van jelölve, hogy az ütemezés *app = webapp, szerepkör = előtérbeli* és a egy terminál-munkamenetben csatlakoztassa:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Használja a shell parancssornál `wget` akkor is hozzáférhet, ha az alapértelmezett NGINX-weblap megtekintéséhez:

```console
wget -qO- http://backend
```

Mivel a bejövő szabály lehetővé teszi, hogy a forgalom, amely rendelkezik a címkék podok *app: webapp, szerepkör: előtérbeli*, szeretné továbbítani a forgalmat az előtér-pod engedélyezett. Az alábbi példa kimenetében látható, a visszaadott alapértelmezett NGINX-weblap:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Lépjen ki a csatolt terminál-munkamenetben. A pod automatikusan törlődik.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>A pod megfelelő címke nélkül tesztelése

A hálózati házirend engedélyezi a forgalmat a címkével ellátott podok *app: webapp, szerepkör: előtérbeli*, azonban célszerű tiltani az összes többi forgalom. Most ellenőrizze, hogy egy másik pod visszaimportálhatja a címkéket nélkül hozzáférhessenek-e a háttér-NGINX pod. Hozzon létre egy másik teszt pod, és csatlakoztassa egy terminál-munkamenetben:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Használja a shell parancssornál `wget` akkor is hozzáférhet, ha az alapértelmezett NGINX-weblap megtekintéséhez. A hálózati házirend blokkolja a bejövő forgalmat, ezért a lap nem tölthető be, az alábbi példában látható módon:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Lépjen ki a csatolt terminál-munkamenetben. A teszt pod automatikusan törlődik.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Egy meghatározott névtéren belül csak érkező adatforgalom engedélyezéséhez

Az előző lépéseknél hozott létre egy hálózati házirend, amely megtagadja a minden forgalmat, és majd frissíteni a szabályzat adott címkével rendelkező podok érkező adatforgalom engedélyezéséhez. Egy másik gyakori kell, hogy csak egy adott névtéren belül a forgalmat. Ha az előző példák forgalmához is egy *fejlesztési* névtér, létrehozása, amely megakadályozza, hogy a forgalom egy másik névtér, például a hálózati házirend *éles*, elérése a podokat.

Először hozzon létre egy új névteret szimulálása a production névtérből:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

A teszt podot ütemezése a *éles* névtér van jelölve, hogy az *app = webapp, szerepkör = előtérbeli*. Csatolhat egy terminál-munkamenetben:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Használja a shell parancssornál `wget` , győződjön meg arról, hogy az alapértelmezett NGINX-weblap elérhető:

```console
wget -qO- http://backend.development
```

Mivel a címkéket a pod felel meg, mi jelenleg engedélyezett a hálózati házirend, engedélyezi a forgalmat. A hálózati házirend nem tekintse meg a névterek, csak a pod-címkéket. Az alábbi példa kimenetében látható, a visszaadott alapértelmezett NGINX-weblap:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Lépjen ki a csatolt terminál-munkamenetben. A teszt pod automatikusan törlődik.

```console
exit
```

### <a name="update-the-network-policy"></a>A hálózati házirend frissítése

Frissítsük a bejövő szabály *namespaceSelector* szakaszban csak a belül érkező adatforgalom engedélyezéséhez a *fejlesztési* névtér. Szerkessze a *háttér-policy.yaml* jegyzékfájl, az alábbi példában látható módon:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Az összetettebb példában, meghatározhat több bejövő szabályt, például egy *namespaceSelector* , majd egy *podSelector*.

A frissített hálózati házirend alkalmazásához használja a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>A frissített hálózati házirend tesztelése

A másik pod ütemezése a *éles* névteret és a egy terminál-munkamenetben csatlakoztassa:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Használja a shell parancssornál `wget` megtekintéséhez, hogy a hálózati házirend most megtagadja a forgalmat:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Lépjen ki a teszt pod:

```console
exit
```

A tiltott a forgalom a *éles* névteret, az ütemezés egy teszt pod újból a *fejlesztési* névtér és a egy terminál-munkamenetben csatlakoztassa:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Használja a shell parancssornál `wget` megtekintéséhez, hogy a hálózati házirend engedélyezi a forgalmat:

```console
wget -qO- http://backend
```

Forgalom használata engedélyezett, mert a pod van ütemezve a névtérben, hogy egyezések mi számára engedélyezett a hálózati házirendben. Az alábbi kimeneti példa azt mutatja, hogy a visszaadott alapértelmezett NGINX-weblap:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Lépjen ki a csatolt terminál-munkamenetben. A teszt pod automatikusan törlődik.

```console
exit
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben hozunk létre a két névtér és a alkalmazni a hálózati házirend. Ezek az erőforrások törléséhez használja a [kubectl törlése] [ kubectl-delete] parancsot, majd adja meg az erőforrások nevei:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>További lépések

Hálózati erőforrások kapcsolatos további információkért lásd: [hálózati alkalmazások az Azure Kubernetes Service (AKS) fogalmai][concepts-network].

Szabályzatokkal kapcsolatos további tudnivalókért lásd: [Kubernetes hálózati házirendek][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
