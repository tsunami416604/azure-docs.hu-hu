---
title: Biztonságos podok hálózati szabályzatokkal az Azure Kubernetes szolgáltatásban (AKS)
description: Ismerje meg, hogyan biztosíthat a podokba be- és kiáramló forgalmat a Kubernetes hálózati szabályzatainak használatával az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 37b6ebd1c8b147db0a9cead4678a0b2bb4ed234d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473608"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Biztonságos forgalom a podok között az Azure Kubernetes-szolgáltatás (AKS) hálózati szabályzatai használatával

Amikor modern, mikroszolgáltatásokon alapuló alkalmazásokat futtat a Kubernetes-ben, gyakran szeretné szabályozni, hogy mely összetevők kommunikálhatnak egymással. A minimális jogosultság elvet kell alkalmazni, hogy a forgalom az Azure Kubernetes-fürt (AKS) fürtpodok között. Tegyük fel, hogy valószínűleg közvetlenül a háttéralkalmazásokba szeretné blokkolni a forgalmat. A Kubernetes *hálózati házirend* szolgáltatása lehetővé teszi a fürtpodok közötti bejövő és kimenő forgalom szabályait.

Ez a cikk bemutatja, hogyan telepítheti a hálózati házirend-motort, és kubernetes hálózati házirendek létrehozása a podok közötti forgalom az AKS-ben. A hálózati házirendet csak Linux-alapú csomópontokhoz és podok az AKS-ben kell használni.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.61-es vagy újabb verziójára van szükség telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

> [!TIP]
> Ha az előzetes verzióban használta a hálózati házirend-szolgáltatást, javasoljuk, hogy [hozzon létre egy új fürtöt.](#create-an-aks-cluster-and-enable-network-policy)
> 
> Ha továbbra is használni szeretné a meglévő tesztfürtöket, amelyek az előzetes verzió során hálózati házirendet használtak, frissítse a fürtöt egy új Kubernetes-verzióra a legújabb GA-kiadáshoz, majd telepítse a következő YAML-jegyzékfájlt az összeomló metrikakiszolgáló és a Kubernetes javításához Műszerfal. Ez a javítás csak a Calico hálózati házirendmotort használó fürtök esetében szükséges.
>
> Biztonsági ajánlott eljárásként [tekintse át a YAML-jegyzékfájl tartalmát,][calico-aks-cleanup] hogy megértse, mi van telepítve az AKS-fürtben.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>A hálózati házirend áttekintése

Az AKS-fürtösszes podja alapértelmezés szerint korlátozás nélkül küldhet és fogadhat forgalmat. A biztonság növelése érdekében olyan szabályokat határozhat meg, amelyek szabályozzák a forgalom áramlását. A háttéralkalmazások gyakran csak a szükséges előtér-szolgáltatásoknak vannak kitéve. Vagy az adatbázis-összetevők csak a hozzájuk kapcsolódó alkalmazásszintek számára érhetők el.

A hálózati házirend egy Kubernetes-specifikáció, amely meghatározza a podok közötti kommunikáció hozzáférési szabályzatait. A hálózati házirendek használatával megkell határoznia egy rendezett szabálykészletet a forgalom küldéséhez és fogadásához, és alkalmazhatja őket egy vagy több címkeválasztónak megfelelő podok gyűjteményére.

Ezek a hálózati házirend-szabályok YAML-jegyzékfájlként vannak definiálva. A hálózati házirendek egy szélesebb jegyzékfájl részeként is szerepelhetnek, amely központi telepítést vagy szolgáltatást is létrehoz.

### <a name="network-policy-options-in-aks"></a>Hálózati házirend-beállítások az AKS-ben

Az Azure kétféleképpen valósítható meg a hálózati szabályzatok megvalósításához. AKS-fürt létrehozásakor hálózati házirend-beállítást választ. A házirend-beállítás nem módosítható a fürt létrehozása után:

* Az Azure saját implementációja, az *Azure hálózati szabályzatai.*
* *Calico Network Policies*, egy nyílt forráskódú hálózati és hálózati biztonsági megoldás által alapított [Tigera][tigera].

Mindkét implementáció Linux *IPTables* használatával kényszeríti ki a megadott házirendeket. A házirendek engedélyezett és nem engedélyezett IP-párok készleteivé lesznek lefordítva. Ezek a párok ezután programozott IPTable szűrő szabályokat.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Az Azure és a Calico szabályzatai és képességeik közötti különbségek

| Képesség                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Támogatott platformok                      | Linux                      | Linux                       |
| Támogatott hálózati beállítások             | Azure CNI                  | Azure CNI és kubenet       |
| A Kubernetes specifikációnak való megfelelés | Minden támogatott házirendtípus |  Minden támogatott házirendtípus |
| További funkciók                      | None                       | Kiterjesztett házirendmodell, amely globális hálózati házirendből, globális hálózati készletből és állomásvégpontból áll. A CLI-nek `calicoctl` a kiterjesztett szolgáltatások kezeléséhez való használatáról a [calicoctl felhasználói kézikönyvben talál][calicoctl]további információt. |
| Támogatás                                  | Az Azure támogatási és mérnöki csapata támogatja | Calico közösségi támogatás. A további fizetett támogatásról a [Project Calico támogatási lehetőségei][calico-support]című témakörben talál további információt. |
| Naplózás                                  | Az IP-táblázatokban hozzáadott / törölt szabályok a */var/log/azure-npm.log* fájl ban minden állomásra bejelentkeznek. | További információ: [Calico komponensnaplók][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS-fürt létrehozása és hálózati házirend engedélyezése

A hálózati házirendek működés közbeni megtekintéséhez hozzunk létre, majd bontsunk ki egy forgalmat meghatározó házirendet:

* Minden forgalmat megtagadnak a pod-hoz.
* Pod-címkéken alapuló forgalom engedélyezése.
* A forgalom engedélyezése névtér alapján.

Először hozzon létre egy AKS-fürtöt, amely támogatja a hálózati házirendet. 

> [!IMPORTANT]
>
> A hálózati házirend-szolgáltatás csak a fürt létrehozásakor engedélyezhető. Meglévő AKS-fürtön nem engedélyezhető a hálózati házirend.

Az Azure Network Policy használatához az [Azure CNI beépülő modult][azure-cni] kell használnia, és meg kell határoznia a saját virtuális hálózatát és alhálózatait. A szükséges alhálózati tartományok megtervezéséről a Speciális [hálózatkonfigurálás című][use-advanced-networking]témakörben talál további információt. A Calico hálózati szabályzata ugyanezzel az Azure CNI beépülő modullal vagy a Kubenet CNI beépülő modullal használható.

A következő példaparancsfájl:

* Egy virtuális hálózatot és alhálózatot hoz létre.
* Létrehoz egy Azure Active Directory (Azure AD) egyszerű szolgáltatás az AKS-fürthöz.
* *Közreműködői* engedélyek hozzárendelése az AKS fürtegyszerűszolgáltatás egyszerű szolgáltatásához a virtuális hálózaton.
* Létrehoz egy AKS-fürtöt a definiált virtuális hálózatban, és engedélyezi a hálózati házirendet.
    * Az *azure* hálózati házirend beállítás használatos. Ha a Calico-t szeretné hálózati `--network-policy calico` házirend-beállításként használni, használja a paramétert. Megjegyzés: A Calico használható `--network-plugin azure` `--network-plugin kubenet`a vagy a.

Adja meg saját biztonságos *SP_PASSWORD.* A *RESOURCE_GROUP_NAME* és *CLUSTER_NAME* változókat lecserélheti:

```azurecli-interactive
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
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

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
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

A fürt létrehozása néhány percet vesz igénybe. Amikor a fürt készen `kubectl` áll, konfigurálja a Kubernetes-fürthöz való csatlakozást az [az aks get-credentials][az-aks-get-credentials] paranccsal. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t azok használatára:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Pod ba irányuló összes bejövő forgalom megtagadása

Mielőtt szabályokat definiálna az adott hálózati forgalom engedélyezéséhez, először hozzon létre egy hálózati házirendet az összes forgalom megtagadásához. Ez a házirend ad egy kiindulási pontot, hogy megkezdje a whitelist csak a kívánt forgalmat. Azt is tisztán láthatja, hogy a rendszer a hálózati házirend alkalmazásakor elesik.

A mintaalkalmazás-környezet és a forgalmi szabályok, először hozzon létre egy névteret nevű *fejlesztés* a példa podok futtatásához:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Hozzon létre egy példát az NGINX-et futtató háttérpodra. Ez a háttérpod egy minta háttéralapú webalapú alkalmazás szimulálására használható. Hozza létre ezt a podot a *fejlesztési* névtérben, és nyissa meg a *80-as* portot a webes forgalom kiszolgálásához. Címkézze fel a podot *app=webapp,role=backend* segítségével, hogy a következő szakaszban egy hálózati házirenddel célozhassuk meg:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Hozzon létre egy másik podot, és csatoljon egy terminálmunkamenetet annak teszteléséhez, hogy sikeresen elérhesse az alapértelmezett NGINX weblapot:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

A rendszerhéj parancssorában ellenőrizze, `wget` hogy elérhető-e az alapértelmezett NGINX weblap:

```console
wget -qO- http://backend
```

A következő mintakimenet azt mutatja, hogy az alapértelmezett NGINX weblap visszatért:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Kilépés a csatlakoztatott terminálmunkamenetből. A tesztegység automatikusan törlődik.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Hálózati házirend létrehozása és alkalmazása

Most, hogy megerősítette, használhatja az alapvető NGINX weblapa a minta háttérpod, hozzon létre egy hálózati házirendet, hogy megtagadja az összes forgalmat. Hozzon létre `backend-policy.yaml` egy nevű fájlt, és illessze be a következő YAML-jegyzékfájlt. Ez a jegyzékfájl egy *podSelector* csatolja a szabályzatot a podok, amelyek az *app:webapp,role:backend* címke, például a minta NGINX pod. Nincsenek szabályok a *bejövő forgalom*alatt, így a podba irányuló összes bejövő forgalom megtagadva:

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

Nyissa [https://shell.azure.com](https://shell.azure.com) meg az Azure Cloud Shellt a böngészőjében.

Alkalmazza a hálózati házirendet a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>A hálózati házirend tesztelése

Lássuk, hogy használhatja-e újra az NGINX weboldalt a háttérpodon. Hozzon létre egy másik tesztegységet, és csatoljon egy terminálmunkamenetet:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

A parancsértelmező `wget` parancssorában ellenőrizheti, hogy elérhető-e az alapértelmezett NGINX-weblap. Ez alkalommal állítsa az időtúlértéket *2* másodpercre. A hálózati házirend most blokkolja az összes bejövő forgalmat, így a lap nem tölthető be, ahogy az a következő példában látható:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Kilépés a csatlakoztatott terminálmunkamenetből. A tesztegység automatikusan törlődik.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Bejövő forgalom engedélyezése podcímke alapján

Az előző szakaszban egy háttér-NGINX pod volt ütemezve, és egy hálózati házirend jött létre, hogy megtagadja az összes forgalmat. Hozzon létre egy előtér-pod, és frissítse a hálózati házirendet, hogy az előtér-podok forgalmat.

Frissítse a hálózati házirendet, hogy engedélyezze a podok ból érkező forgalmat az *app:webapp, role:frontend* és bármely névtérben. Az előző *háttérrendszer-policy.yaml fájl szerkesztése,* és add *matchLabels* ingress szabályokat, hogy a jegyzékfájl a következő példához hasonlóan néz ki:

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
> Ez a hálózati házirend *egy namespaceselectort* és egy *podSelector* elemet használ a be- és tárhelyszabályhoz. A YAML szintaxis fontos a be- éshozamszabályok additív. Ebben a példában mindkét elemnek meg kell egyeznie a bejövő beviteli szabály alkalmazásához. Előfordulhat, hogy a Kubernetes *1.12* előtti verziói nem megfelelően értelmezik ezeket az elemeket, és nem korlátozzák a hálózati forgalmat a várt módon. Erről a viselkedésről a [Választók viselkedése és kiválasztóinak viselkedése.][policy-rules]

Alkalmazza a frissített hálózati házirendet a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f backend-policy.yaml
```

Ütemezzen egy podot, amely *app=webapp,role=előtérként* van megcímkézve, és csatoljon egy terminálmunkamenetet:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

A parancsértelmező `wget` parancssorában ellenőrizheti, hogy elérhető-e az alapértelmezett NGINX-weblap:

```console
wget -qO- http://backend
```

Mivel a be- és a be- és áttétszabály lehetővé teszi a forgalmat a címkék *alkalmazás: webapp, szerepkör: előtér,* az előtér-pod forgalma engedélyezett. A következő példa kimenetaz alapértelmezett NGINX-weblapot mutatja:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Kilépés a csatlakoztatott terminálmunkamenetből. A pod automatikusan törlődik.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Pod tesztelése megfelelő címke nélkül

A hálózati házirend lehetővé teszi a podok címkével ellátott *alkalmazás: webapp, szerepkör: előtér,* de meg kell tagadnia az összes többi forgalmat. Teszteljük, hogy egy másik pod nélkül ezeket a címkéket férhetnek hozzá a háttér-end NGINX pod. Hozzon létre egy másik tesztegységet, és csatoljon egy terminálmunkamenetet:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

A parancsértelmező `wget` parancssorában ellenőrizheti, hogy elérhető-e az alapértelmezett NGINX-weblap. A hálózati házirend blokkolja a bejövő forgalmat, így a lap nem tölthető be, ahogy az a következő példában látható:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Kilépés a csatlakoztatott terminálmunkamenetből. A tesztegység automatikusan törlődik.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Csak egy meghatározott névtérből engedélyezi a forgalmat

Az előző példákban létrehozott egy hálózati házirendet, amely megtagadta az összes forgalmat, majd frissítette a házirendet, hogy engedélyezze a podok egy adott címkével rendelkező forgalmat. Egy másik gyakori igény, hogy korlátozza a forgalmat csak egy adott névtérben. Ha az előző példák egy *fejlesztési* névtér forgalmára voltak, hozzon létre egy hálózati házirendet, amely megakadályozza, hogy egy másik névtérből, például *éles területről*érkező forgalom elérje a podokat.

Először hozzon létre egy új névteret a termelési névtér szimulálására:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Ütemezzen egy tesztpodot az *éles* névtérben, amely *app=webapp,role=frontend*címkével van ellátva. Terminálmunkamenet csatolása:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

A rendszerhéj parancssorában ellenőrizze, `wget` hogy elérhető-e az alapértelmezett NGINX weblap:

```console
wget -qO- http://backend.development
```

Mivel a pod címkéi megfelelnek a hálózati házirendben jelenleg engedélyezett címkéknek, a forgalom engedélyezett. A hálózati házirend nem a névterek, csak a pod címkéket. A következő példa kimenetaz alapértelmezett NGINX-weblapot mutatja:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Kilépés a csatlakoztatott terminálmunkamenetből. A tesztegység automatikusan törlődik.

```console
exit
```

### <a name="update-the-network-policy"></a>A hálózati házirend frissítése

Frissítsük a be- éselőszolgáltatási *szabály névtérválasztó* szakaszát, hogy csak a fejlesztési névtérből származó forgalmat *engedélyezze.* A *háttérrendszer-policy.yaml* jegyzékfájl szerkesztése a következő példában látható módon:

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

Összetettebb példákban több bejövő házirendet is definiálhat, például egy *namespaceselectort,* majd egy *podSelectort.*

Alkalmazza a frissített hálózati házirendet a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>A frissített hálózati házirend tesztelése

Ütemezzen egy másik podot az *éles* névtérben, és csatoljon egy terminálmunkamenetet:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

A rendszerhéj parancssorába használja `wget` annak megtekintését, hogy a hálózati házirend mostantól megtagadja a forgalmat:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Kilépés a tesztegységből:

```console
exit
```

Ha a forgalom nem szerepel az *éles* névtérből, ütemezzen vissza egy tesztegységet a *fejlesztési* névtérbe, és csatoljon egy terminálmunkamenetet:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

A rendszerhéj parancssorban annak megtekintéséhez használja, `wget` hogy a hálózati házirend engedélyezi-e a forgalmat:

```console
wget -qO- http://backend
```

A forgalom engedélyezett, mert a pod van ütemezve a névtérben, amely megfelel a hálózati házirendben engedélyezett. A következő mintakimenet az alapértelmezett NGINX-weblapot mutatja:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Kilépés a csatlakoztatott terminálmunkamenetből. A tesztegység automatikusan törlődik.

```console
exit
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben két névteret hoztunk létre, és hálózati házirendet alkalmaztunk. Az erőforrások karbantartásához használja a [kubectl delete parancsot,][kubectl-delete] és adja meg az erőforrásneveket:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>További lépések

A hálózati erőforrásokról további információkért lásd: [Hálózati fogalmak alkalmazások hoz az Azure Kubernetes Service (AKS)][concepts-network].

A házirendekről a [Kubernetes hálózati házirendjei][kubernetes-network-policies]ben olvashat bővebben.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
