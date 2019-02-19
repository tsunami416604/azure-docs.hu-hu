---
title: A hálózati házirendek az Azure Kubernetes Service (AKS) használatával biztonságos podok
description: Haladó pods Kubernetes hálózati házirendek segítségével az Azure Kubernetes Service (AKS)-ból adatforgalom biztonságossá tételének ismertetése
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: 5e19f7cd2aa249e1c9587963e005e8114eacbdb0
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342049"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Podok hálózati házirendek segítségével az Azure Kubernetes Service (AKS) közötti adatforgalom védelme

A modern, mikroszolgáltatás-alapú alkalmazások a Kubernetesben való futtatásakor gyakran érdemes szabályozhatja, hogy mely összetevők kommunikálhatnak egymással. Hogyan lehet adatforgalom podok AKS-fürt között a legalacsonyabb jogosultsági szint elvének kell alkalmazni. Például valószínűleg már szeretné adatforgalmat közvetlenül a háttéralkalmazásokhoz. A Kubernetes a *hálózati házirend* szolgáltatás lehetővé teszi egy fürt podok közötti bejövő és kimenő forgalomra vonatkozó szabályok meghatározásához.

Ez a cikk bemutatja, hogyan hálózati házirendek használata az aks-ben podok közötti forgalom szabályozására.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.56 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="overview-of-network-policy"></a>A hálózati házirend áttekintése

Alapértelmezés szerint egy AKS-fürt összes podok küldhet és korlátozások nélkül forgalom fogadására. A biztonság növelése érdekében megadhatja a szabályokat, amelyek a forgalom szabályozására. Például a háttéralkalmazásokhoz gyakran csak szükséges előtér-szolgáltatások érhetők el, vagy adatbázis-összetevői csak elérhetők az alkalmazásrétegek, amely csatlakozni hozzájuk.

Hálózati házirendek olyan Kubernetes-erőforrásokat, amelyekkel podok közötti adatforgalom szabályozásához. Ha szeretné, beállítások, például a hozzárendelt címkék, névtérre vagy forgalmat port alapján adatforgalom engedélyezéséhez vagy letiltásához. Hálózati házirendeket, egy YAML alkalmazásjegyzékeket, és a egy szélesebb körű jegyzékfájl, amely is létrehoz egy központi telepítés vagy a szolgáltatás része lehet.

A művelet hálózati házirendek megtekintéséhez hozzunk létre, és bontsa ki a szabályzat, amely meghatározza a forgalmat a következő:

* A pod minden forgalom tiltása.
* Engedélyezi a forgalmat a pod címkék alapján.
* Engedélyezi a forgalmat a névtér alapján.

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS-fürt létrehozása és a hálózati házirend engedélyezése

A hálózati házirend csak a fürt létrehozásakor engedélyezhető. Nem engedélyezhető a hálózati házirend egy meglévő AKS-fürtre. Hozzon létre egy AKS a hálózati házirend, először engedélyeznie kell az előfizetés szolgáltatásjelzőre. Regisztrálja a *EnableNetworkPolicy* jelző funkciót, használja a [az a funkció regisztrálása] [ az-feature-register] parancsot az alábbi példában látható módon:

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Az állapot megjelenítése néhány percet vesz igénybe *regisztrált*. A regisztrációs állapot használatával ellenőrizheti a [az szolgáltatáslistát] [ az-feature-list] parancsot:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a regisztrációját a *Microsoft.ContainerService* erőforrás-szolgáltató használatával a [az provider register] [ az-provider-register] parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

A hálózati házirend-val való használatához egy AKS-fürtöt kell használnia a [Azure CNI beépülő modul] [ azure-cni] és a saját virtuális hálózat és alhálózatok megadása. Részletesebb információk tervezze meg a szükséges alhálózati tartományokat, lásd: [speciális hálózatkezelés konfigurálását][use-advanced-networking]. Az alábbi példa parancsfájl:

* Egy virtuális hálózatot és alhálózatot hoz létre.
* Létrehoz egy Azure Active Directory (AD) használja az egyszerű szolgáltatást az AKS-fürtöt.
* Hozzárendeli *közreműködői* engedélyeit az AKS-fürt egyszerű szolgáltatás a virtuális hálózati.
* A megadott virtuális hálózat egy AKS-fürtöt hoz létre, és lehetővé teszi, hogy a hálózati házirend.

Adja meg a saját biztonságos *SP_PASSWORD*. Igény szerint cserélje le a *RESOURCE_GROUP_NAME* és *fürtnév* változókat:

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
read SP_ID <<< $(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

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
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
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

A fürt létrehozása néhány percet vesz igénybe. Amikor elkészült, konfigurálja `kubectl` a Kubernetes fürt használatával csatlakozni a [az aks get-credentials] [ az-aks-get-credentials] parancsot. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori Felületét azok használatára:

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

Most hozzon létre egy nginx-et futtató példa háttérrendszer pod. A háttérrendszer pod szimulálása egy háttérbeli webes mintaalkalmazás használható. Ez a pod létrehozása a *fejlesztési* névteret, és nyissa meg a portot *80-as* kiszolgálására a webes forgalom. Címkével rendelkező podot *app = webapp, szerepkör = háttérrendszer* úgy, hogy mi is ez a szakasz a hálózati házirend-cél:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Ha tesztelni szeretné, hogy sikeresen érhető el az alapértelmezett NGINX-weblapot, hozzon létre egy másik pod, és csatlakoztassa egy terminál-munkamenetben:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Egyszer shell parancssornál használata `wget` hozzáférhet az alapértelmezett NGINX-weblap megerősítéséhez:

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

Lépjen ki a csatolt terminál-munkamenetben. A teszt pod automatikusan törölve:

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Hozzon létre, és a alkalmazni a hálózati házirend

Most, hogy jóváhagyta az alapszintű NGINX-weblap a minta háttérrendszer pod hozzáférhet, hozzon létre egy hálózati házirend az összes forgalom. Hozzon létre egy fájlt `backend-policy.yaml` , és illessze be a következő YAML-jegyzékfájlt. A jegyzékfájlt használ egy *podSelector* podok, amelyeken a házirend csatolni a *app:webapp, szerepkör: háttérrendszer* címke, például a minta az NGINX-pod. Nincsenek megadva szabályok alapján *bejövő*, így az összes bejövő forgalmat a pod megtagadva:

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

A hálózati házirend használatával alkalmazza a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzék nevét:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>A hálózati házirend tesztelése

Nézzük meg, hogy hozzáfér az NGINX weblapot a háttér-pod újra. Hozzon létre egy másik teszt pod, és csatlakoztassa egy terminál-munkamenetben:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Egyszer shell parancssornál használata `wget` hozzáférhet-e el az alapértelmezett NGINX-weblap megtekintéséhez. Megadott idő beállítása egy időtúllépési értéket *2* másodperc. A hálózati házirend most már letiltja az összes bejövő forgalmat, így nem lehet betölteni a lapot, az alábbi példában látható módon:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Lépjen ki a csatolt terminál-munkamenetben. A teszt pod automatikusan törölve:

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>A pod címke alapján forgalom engedélyezéséhez.

Az előző szakaszban háttérrendszer NGINX podot lett ütemezve, és a hálózati házirend az összes forgalom megtagadásához lett létrehozva. Most hozzunk hozzon létre egy előtérbeli pod, és frissítse a hálózati házirend előtérbeli podok érkező adatforgalom engedélyezéséhez.

Frissítse a hálózati házirend, a feliratokkal podok érkező adatforgalom engedélyezéséhez *app:webapp, szerepkör: előtérbeli* és minden olyan névtér. Szerkessze az előző *háttér-policy.yaml* fájlt, és adjon hozzá egy *matchLabels* bejövő szabályok, hogy a következő példához hasonlóan jelenik meg a jegyzékfájlban:

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
> Használja a hálózati házirend- *namespaceSelector* és a egy *podSelector* elem a bejövő szabály. A YAML szintaxisa a következő fontos lehet a bejövő szabályok a additív vagy sem. Mindkét elem ebben a példában meg kell egyeznie a alkalmazni bejövő szabály. Kubernetes terméknél korábbi verziókat *1.12* előfordulhat, hogy ezek az elemek értelmezi helyesen és nem a hálózati forgalom korlátozására a várt módon. További információkért lásd: [viselkedését, és a választók][policy-rules].

A frissített hálózati csoportházirend használatával alkalmazza az [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzék nevét:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Most ütemezése egy pod van jelölve, hogy *app = webapp, szerepkör = előtérbeli* és a egy terminál-munkamenetben csatlakoztassa:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Egyszer shell parancssornál használata `wget` hozzáférhet-e el az alapértelmezett NGINX-weblap megtekintéséhez:

```console
wget -qO- http://backend
```

A bemeneti pontjaként szabály engedélyezi a forgalmat a podok, amelyek rendelkeznek a címkék *app: webalkalmazásban, szerepkör: előtérbeli*, engedélyezi a forgalmat a az előtér-pod. Az alábbi példa kimenetében látható, az alapértelmezett NGINX-weblap adott vissza:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Lépjen ki a csatolt terminál-munkamenetben. A pod automatikusan törölve:

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>A pod megfelelő címke nélkül tesztelése

A hálózati házirend engedélyezi a forgalmat a címkével ellátott podok *app: webapp, szerepkör: előtérbeli*, azonban célszerű tiltani az összes többi forgalom. Most tesztelje, hogy ezek a címkék nélkül egy másik pod nem férhetnek hozzá a háttérrendszer NGINX pod. Hozzon létre egy másik teszt pod, és csatlakoztassa egy terminál-munkamenetben:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Egyszer shell parancssornál használata `wget` hozzáférhet-e el az alapértelmezett NGINX-weblap megtekintéséhez. A hálózati házirend blokkolja a bejövő forgalmat, ezért a lap nem tölthető be, az alábbi példában látható módon:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Lépjen ki a csatolt terminál-munkamenetben. A teszt pod automatikusan törölve:

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Egy meghatározott névtéren belül csak érkező adatforgalom engedélyezéséhez

Az előző lépéseknél hozott létre egy hálózati házirend, amely megtagadja összes traffic, majd frissíteni a szabályzat adott címkével rendelkező podok érkező adatforgalom engedélyezéséhez. Egy közös kell, hogy csak egy adott névtéren belül a forgalmat. Ha az előző példák a forgalom egy *fejlesztési* névtér, érdemes lehet majd létrehozni, amely megakadályozza, hogy a forgalom egy másik névtér, például a hálózati házirend *éles*, elérése a podok.

Először hozzon létre egy új névteret szimulálása a production névtérből:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

A teszt podot ütemezése a *éles* névtér van jelölve, hogy az *app = webapp, szerepkör = előtérbeli*. Csatolhat egy terminál-munkamenetben:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Egyszer shell parancssornál használata `wget` hozzáférhet az alapértelmezett NGINX-weblap megerősítéséhez:

```console
wget -qO- http://backend.development
```

A pod címkéit megegyezik, mi jelenleg engedélyezett a hálózati házirend, mivel engedélyezi a forgalmat. A hálózati házirend nem tekintse meg a névterek, csak a pod-címkéket. Az alábbi példa kimenetében látható, az alapértelmezett NGINX-weblap adott vissza:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Lépjen ki a csatolt terminál-munkamenetben. A teszt pod automatikusan törölve:

```console
exit
```

### <a name="update-the-network-policy"></a>A hálózati házirend frissítése

Most frissítsük a bejövő szabály *namespaceSelector* szakaszban csak a belül érkező adatforgalom engedélyezéséhez a *fejlesztési* névtér. Szerkessze a *háttér-policy.yaml* jegyzékfájl, az alábbi példában látható módon:

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

Az összetettebb példában, meghatározhat több bejövő szabályt, például a használandó egy *namespaceSelector* , majd egy *podSelector*.

A frissített hálózati csoportházirend használatával alkalmazza az [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzék nevét:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>A frissített hálózati házirend tesztelése

Most már ütemezése az egy másik pod a *éles* névteret és a egy terminál-munkamenetben csatlakoztassa:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Egyszer shell parancssornál használata `wget` a hálózati házirend, most már a forgalom megtagadásához megtekintéséhez:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Lépjen ki a teszt pod:

```console
exit
```

A tiltott a forgalom a *éles* névtér, most ütemezés teszt podot biztonsági a *fejlesztési* névtér és a egy terminál-munkamenetben csatlakoztassa:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Egyszer shell parancssornál használata `wget` megtekintheti a hálózati házirend engedélyezi a forgalmat:

```console
wget -qO- http://backend
```

A pod ütemezve van, a névtér, amely megfelel a hálózati házirend megengedett, mivel engedélyezi a forgalmat. Az alábbi kimeneti példa látható az alapértelmezett NGINX-weblap adott vissza:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Lépjen ki a csatolt terminál-munkamenetben. A teszt pod automatikusan törölve:

```console
exit
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben hozunk létre két névtérrel, és a hálózati házirend alkalmazva. Ezek az erőforrások törléséhez használja a [kubectl törlése] [ kubectl-delete] parancsot, majd adja meg az erőforrások nevei a következők szerint:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>További lépések

Hálózati erőforrások kapcsolatos további információkért lásd: [hálózati alkalmazások az Azure Kubernetes Service (AKS) fogalmai][concepts-network].

Szabályzatokkal kapcsolatos további információkért lásd: [Kubernetes hálózati házirendek][kubernetes-network-policies].

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
