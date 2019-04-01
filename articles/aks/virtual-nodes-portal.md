---
title: Hozzon létre a portálról az Azure Kubernetes-szolgáltatások (AKS) virtuális csomópontok
description: Ismerje meg, hogyan hozható létre egy Azure Kubernetes Services-(AKS-) fürt által használt virtuális csomópontok podok futtatásához az Azure portal használatával.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: fd538ce6821b35dc6e3932256090afdf70b4b232
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755258"
---
# <a name="preview---create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Előzetes verzió – létrehozása és konfigurálása az Azure Kubernetes szolgáltatások (AKS)-fürt virtuális csomópontok használata az Azure Portalon

Számítási feladatok az Azure Kubernetes Service (AKS)-fürt gyors üzembe helyezéséhez használhatja a virtuális csomópontok. Virtuális csomópont esetén podok gyors kiépítés rendelkezik, és csak a végrehajtási idejű másodpercenként kell fizetnie. Egy méretezési esetben nem kell az újabb podok futtatandó virtuális gép számítási csomópontok üzembe helyezéséhez a Kubernetes fürt méretező várja. Ez a cikk bemutatja, hogyan hozhat létre és konfigurálja a virtuális hálózati erőforrásokat és a egy AKS-fürt virtuális csomópontok engedélyezve van.

> [!IMPORTANT]
> Az AKS előzetes verziójú funkciók a következők: az önkiszolgáló és vehetnek részt. Visszajelzés és hibák gyűjtsön közösségünkhöz előzetes verziók vannak megadva. Azonban nem támogatja őket az Azure műszaki támogatást. Hozzon létre egy fürtöt, vagy adja hozzá ezeket a funkciókat a meglévő fürtökre, ha a fürt nem támogatott, mindaddig, amíg a funkció már nem előzetes verzióban érhető el és hallgatóknak az általánosan elérhető (GA).
>
> Ha az előzetes verziójú szolgáltatásaihoz is problémák merülnek fel [nyisson egy problémát a AKS GitHub-adattárat a] [ aks-github] az előzetes verziójú funkció a bejelentett hiba címét nevére.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A következő régiók virtuális csomópont központi telepítések támogatottak:

* Kelet-Ausztrália (Kelet-Ausztrália)
* USA keleti RÉGIÓJA (USA keleti régiója)
* USA nyugati középső Régiója (régiója)
* Nyugat-Európa (westeurope)
* USA nyugati RÉGIÓJA (westus)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Az Azure Portal bal felső sarkában válassza az **Erőforrás létrehozása** > **Kubernetes Service** lehetőséget.

Az a **alapjai** lapon, a következő beállításokat:

- *A PROJECT DETAILS*: Válassza ki az Azure-előfizetéssel, majd válassza ki vagy hozzon létre egy Azure-erőforráscsoportot, mint például *myResourceGroup*. Adja meg a **Kubernetes-fürt nevét**, például *myAKSCluster*.
- *FÜRT RÉSZLETES ADATAI*: Válassza ki a régió, a Kubernetes-verziót és a DNS-név előtagja az AKS-fürtöt.
- *MÉRETEZÉSI CSOPORT*: Válassza ki az AKS-csomópontok Virtuálisgép-méretét. A virtuálisgép-méret az AKS-fürt telepítését követően **nem** módosítható.
    - Adja meg a fürtre telepítendő csomópontok számát. Ez a cikk beállítása **csomópontok száma** való *1*. A csomópontok száma a fürt telepítése után is **módosítható**.
    - A **virtuális csomópontok**válassza *engedélyezve*.

![AKS-fürt létrehozása és engedélyezése a virtuális csomópontok](media/virtual-nodes-portal/enable-virtual-nodes.png)

Alapértelmezés szerint egy Azure Active Directory egyszerű szolgáltatás létrehozása. Egyszerű szolgáltatás szolgál a fürt és más Azure szolgáltatásokkal való integrációt.

A fürt is speciális hálózati van konfigurálva. A virtuális csomópontok a saját Azure virtuális hálózat alhálózatának használatára vannak konfigurálva. Ez az alhálózat rendelkezik delegált engedélyek való csatlakozáshoz az Azure-erőforrások közötti az AKS-fürtöt. Ha még nem rendelkezik delegált alhálózathoz, az Azure Portalon hoz létre, és az Azure virtuális hálózatot és alhálózatot használatra konfigurálja a virtuális csomópontok.

Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejezése után jelölje ki a **létrehozás**.

Az AKS-fürt létrehozása és a használatra való előkészítése néhány percet vesz igénybe.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Kubernetes-fürtök kezeléséhez használja a [kubectl][kubectl] eszközt, a Kubernetes parancssori ügyfelét. A `kubectl` ügyfél előzetesen már telepítve van az Azure Cloud Shellben.

A Cloud Shell megnyitásához válassza **kipróbálás** a kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/bash](https://shell.azure.com/bash) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálhatja a `kubectl` ügyfelet a Kubernetes-fürthöz való csatlakozásra. A következő példa lekéri a *myResourceGroup* erőforrásban lévő *myAKSCluster* fürtnév hitelesítő adatait:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

Az alábbi példa kimenetében látható a létrehozott egyetlen virtuális gép csomópont, majd a virtuális csomópont Linux rendszeren *virtuális-csomópont-aci-linux*:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Egy mintaalkalmazás üzembe helyezése

Az Azure Cloud shellben hozzon létre egy fájlt `virtual-node.yaml` másolja be a következő yaml-kódot. A tároló a csomóponton, ütemezni egy [nodeSelector] [ node-selector] és [toleration] [ toleration] vannak definiálva. Ezek a beállítások engedélyezése a virtuális csomópont ütemezhető, és győződjön meg arról, hogy a szolgáltatás sikeresen engedélyezve van a pod.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Futtassa az alkalmazást a [a kubectl a alkalmazni] [ kubectl-apply] parancsot.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Használja a [kubectl get pods] [ kubectl-get] parancsot a `-o wide` argumentum a kimenetben podok és az ütemezett csomópont listáját. Figyelje meg, hogy a `virtual-node-helloworld` pod van ütemezve: a `virtual-node-linux` csomópont.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A pod belső IP-cím van hozzárendelve az Azure virtuális hálózat alhálózatról a virtuális csomópontok segítségével delegált.

> [!NOTE]
> Ha az Azure Container Registryben tárolt rendszerképek [konfigurálhatja és használhatja a Kubernetes titkos][acr-aks-secrets]. A jelenlegi korlátozás miatt a virtuális csomópontok előzetes, hogy nem használható az Azure AD egyszerű szolgáltatás hitelesítése integrálva. Ha egy titkos kulcsot nem használja, a virtuális csomópontok ütemezett podok sikertelen elindításához, és jelentse a hibát `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>A virtuális csomópont pod tesztelése

A pod, a virtuális csomóponton futó teszteléséhez, keresse meg a webes ügyféllel bemutató alkalmazás. A pod belső IP-cím van hozzárendelve, akkor gyorsan tesztelheti e kapcsolat az AKS-fürtöt egy másik podján. Hozzon létre egy teszt pod, és egy terminál-munkamenetben csatlakoztatása:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Telepítés `curl` be a pod `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Most már elérhető a cím a pod használatának `curl`, mint például *http://10.241.0.4*. Adja meg a saját belső IP-címét, ahogyan az előző `kubectl get pods` parancsot:

```azurecli-interactive
curl -L http://10.241.0.4
```

A bemutató alkalmazás jelenik meg, ahogyan az a következő sűrített példához kimenet:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zárja be a terminál-munkamenetben, a teszt rendelkező podot `exit`. Ha a munkamenet véget ér, a törölt a pod.

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy pod lett ütemezve a virtuális csomópont, és privát, belső IP-címet. Ehelyett létrehozhat egy szolgáltatás üzembe helyezésének és irányíthatja a forgalmat a terheléselosztó vagy a bejövőforgalom-vezérlőt a pod. További információkért lásd: [az aks-ben hozzon létre egy alapszintű bejövőforgalom-vezérlőjéhez][aks-basic-ingress].

Virtuális csomópontok használata egy méretezési megoldás az aks-ben egyik összetevője. Méretezési megoldásokra további információkért tekintse meg a következő cikkeket:

- [A Kubernetes-podok horizontális méretező használatához][aks-hpa]
- [A Kubernetes-fürt méretező használatához][aks-cluster-autoscaler]
- [Tekintse meg az automatikus skálázási minta virtuális csomópontok][virtual-node-autoscale]
- [További információ a Virtual Kubelet nyílt forráskódú kódtár][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
