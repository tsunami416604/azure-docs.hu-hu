---
title: Több Node-készlet használata az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan hozhat létre és kezelhet több Node-készletet egy fürthöz az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: 9c8bae879c5e28914981eec34afb0759dd963004
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928981"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Fürthöz tartozó több Node-készlet létrehozása és kezelése az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes szolgáltatásban (ak) az azonos konfiguráció csomópontjai a *csomópont-készletekbe*vannak csoportosítva. Ezek a csomópont-készletek az alkalmazásokat futtató mögöttes virtuális gépeket tartalmazzák. A csomópontok kezdeti száma és a mérete (SKU) definiálása akkor történik meg, amikor létrehoz egy *alapértelmezett csomópont-készletet*, amely létrehoz egy AK-fürtöt. A különböző számítási vagy tárolási igényekkel rendelkező alkalmazások támogatásához további csomópont-készleteket is létrehozhat. Ezekkel a további csomópont-készletekkel például GPU-ket biztosíthat a számítási igényű alkalmazások számára, vagy hozzáférhet a nagy teljesítményű SSD-tárolóhoz.

> [!NOTE]
> Ez a funkció lehetővé teszi a több csomópontos készlet létrehozását és kezelését. Ennek eredményeképpen külön parancsok szükségesek a létrehozás/frissítés/törlés számára. Korábban a fürt műveletei `az aks create` vagy `az aks update` használták a managedCluster API-t, és az egyetlen lehetőség a vezérlési sík és egyetlen csomópontos készlet módosítására. Ez a funkció az agentPool API-n keresztül egy külön műveleti készletet tesz elérhetővé az ügynökök számára, és a `az aks nodepool` parancs használatát igényli egy adott csomópont-készlet műveleteinek végrehajtásához.

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet több Node-készletet egy AK-fürtben.

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.76 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="limitations"></a>Korlátozások

A több csomópontot támogató AK-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* Az alapértelmezett (első) csomópont-készlet nem törölhető.
* A HTTP-alkalmazás útválasztási bővítménye nem használható.
* Az AK-fürtnek a standard SKU Load balancert kell használnia több Node-készlet használatához, a szolgáltatás alapszintű SKU-terheléselosztó esetén nem támogatott.
* Az AK-fürtnek virtuálisgép-méretezési csoportokat kell használnia a csomópontokhoz.
* A legtöbb művelethez hasonlóan a meglévő Resource Manager-sablonok használatával nem adhat hozzá vagy törölhet csomópont-készleteket. Ehelyett [használjon egy különálló Resource Manager-sablont](#manage-node-pools-using-a-resource-manager-template) , amellyel módosításokat hajthat végre egy AK-fürtben lévő csomópont-készleteken.
* A csomópontok készletének neve csak kisbetűket és kisbetűs karaktereket tartalmazhat. A Linux-csomópontok készletei esetében a hossznak 1 és 12 karakter közöttinek kell lennie, a Windows-csomópontok esetében a hossznak 1 és 6 karakter közöttinek kell lennie.
* Az AK-fürt legfeljebb nyolc csomópont-készletet tartalmazhat.
* Az AK-fürt legfeljebb 400 csomóponttal rendelkezhet a nyolc csomópontos készletben.
* Az összes csomópont-készletnek ugyanabban az alhálózatban kell lennie.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Első lépésként hozzon létre egy AK-fürtöt egyetlen csomópontos készlettel. Az alábbi példa az az [Group Create][az-group-create] paranccsal létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *eastus* régióban. Ezután létrejön egy *myAKSCluster* nevű AK-fürt az az [AK Create][az-aks-create] paranccsal. A *1.13.10* egy *--kubernetes-verziója* a következő lépésben mutatja be, hogyan lehet frissíteni egy csomópont-készletet. Megadhat bármilyen [támogatott Kubernetes-verziót][supported-versions].

> [!NOTE]
> Az *alapszintű* Load Balancer SKU **nem támogatott** több csomópontos készlet használata esetén. Alapértelmezés szerint az AK-fürtök a *standard* Load Balancer SKU-val jönnek létre az Azure CLI-ből és Azure Portal.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.13.10 \
    --load-balancer-sku standard
```

A fürt létrehozása néhány percet vesz igénybe.

> [!NOTE]
> Annak biztosítása érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópont-készletben, mivel az alapvető rendszerszolgáltatások ezen a csomópont-készleten futnak.

Ha a fürt elkészült, használja az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot a fürt hitelesítő adatainak lekéréséhez a `kubectl`hoz való használathoz:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Csomópont-készlet hozzáadása

Az előző lépésben létrehozott fürt egyetlen csomópont-készlettel rendelkezik. Hozzunk létre egy második csomópont-készletet az az [AK nodepool Add][az-aks-nodepool-add] paranccsal. A következő példa egy *mynodepool* nevű csomópont-készletet hoz létre, amely *3* csomópontot futtat:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.12.7
```

> [!NOTE]
> A csomópont-készlet nevének kisbetűvel kell kezdődnie, és csak alfanumerikus karaktereket tartalmazhat. A Linux-csomópontok készletei esetében a hossznak 1 és 12 karakter közöttinek kell lennie, a Windows-csomópontok esetében a hossznak 1 és 6 karakter közöttinek kell lennie.

A csomópont-készletek állapotának megtekintéséhez használja az az [AK Node Pool List][az-aks-nodepool-list] parancsot, és adja meg az erőforráscsoportot és a fürt nevét:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

A következő példa kimenete azt mutatja, hogy a *mynodepool* sikeresen létrejött a csomópont-készletben lévő három csomóponttal. Ha az AK-fürt az előző lépésben lett létrehozva, a rendszer egy alapértelmezett *nodepool1* hozott létre, amelynek a csomópontja *2*.

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.12.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Ha nem ad meg *OrchestratorVersion* vagy *VmSize* a csomópont hozzáadásakor, a csomópontok az AK-fürt alapértelmezett értékei alapján jönnek létre. Ebben a példában ez volt a Kubernetes verziójának *1.13.10* és a csomópont mérete *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Csomópont-készlet frissítése
 
> [!NOTE]
> Fürtön vagy csomóponton található műveletek frissítése és méretezése nem végezhető el egyszerre, ha a rendszer hibát adott vissza. Ehelyett minden Művelettípus a következő, ugyanazon az erőforráson megjelenő kérelem előtt fejeződik be a cél erőforráson. Erről a [hibaelhárítási útmutatóban](https://aka.ms/aks-pending-upgrade)olvashat bővebben.

Ha az AK-fürt eredetileg az első lépésben lett létrehozva, a *1.13.10* `--kubernetes-version` lett megadva. Ez a Kubernetes-verziót adja meg a vezérlési síkon és az alapértelmezett csomópont-készlet esetében is. Az ebben a szakaszban szereplő parancsok azt ismertetik, hogyan lehet frissíteni egy adott csomópont-készletet.

A vezérlési sík és a Kubernetes verziójának frissítése közötti kapcsolatot az [alábbi szakasz](#upgrade-a-cluster-control-plane-with-multiple-node-pools)ismerteti.

> [!NOTE]
> A Node Pool operációsrendszer-rendszerkép verziója a fürt Kubernetes-verziójához van kötve. A fürt frissítését követően csak az operációs rendszer rendszerképének frissítését fogja kérni.

Mivel ebben a példában két csomópont-készlet található, a csomópont-készlet frissítéséhez [az az AK nodepool upgrade][az-aks-nodepool-upgrade] -t kell használnia. Frissítse a *mynodepool* az Kubernetes *1.13.10*. Az az [AK nodepool upgrade][az-aks-nodepool-upgrade] paranccsal frissítse a csomópont-készletet, ahogy az az alábbi példában is látható:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.10 \
    --no-wait
```

Sorolja fel újra a csomópont-készletek állapotát az az [AK Node Pool List][az-aks-nodepool-list] parancs használatával. A következő példa azt mutatja, hogy a *mynodepool* a *1.13.10* *Verziófrissítési* állapotban van:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A csomópontok a megadott verzióra való frissítése néhány percet vesz igénybe.

Ajánlott eljárásként egy AK-fürt összes csomópont-készletét ugyanarra a Kubernetes-verzióra kell frissíteni. A `az aks upgrade` alapértelmezett viselkedése az összes csomópont-készlet és a vezérlő síkja együttes frissítése az igazítás eléréséhez. Az egyes csomópont-készletek verziófrissítése lehetővé teszi a működés közbeni frissítés végrehajtását és a csomópontok közötti ütemezést, hogy a fent említett korlátozásokon belül fenntartsa az alkalmazás üzemidőét.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Fürt vezérlőelem síkja több Node-készlettel

> [!NOTE]
> A Kubernetes a szabványos [szemantikai verziószámozási](https://semver.org/) sémát használja. A verziószám *x. y. z*értékként van kifejezve, ahol az *x* a főverzió, az *y* az alverzió, a *z* pedig a javítás verziója. Például a *1.12.6*verzióban az 1 a főverzió, a 12 a másodlagos verzió, a 6 pedig a javítás verziója. A Kubernetes-verzió és a kezdeti csomópont-készlet is be van állítva a fürt létrehozásakor. Az összes további csomópont-készlet Kubernetes-verziója be van állítva a fürthöz való hozzáadásakor. A Kubernetes-verziók eltérőek lehetnek a csomópont-készletek, valamint a csomópont-készlet és a vezérlő síkja között.

Az AK-fürtök két fürterőforrás-objektummal rendelkeznek, amelyek Kubernetes-verzióval vannak társítva.

1. A fürt vezérlő síkja Kubernetes verziója.
2. Csomópont-készlet Kubernetes-verzióval.

A vezérlő síkja egy vagy több csomópont-készletet képez le. A frissítési művelet viselkedése attól függ, hogy melyik Azure CLI-parancsot használja a rendszer.

Az AK-vezérlési sík frissítéséhez `az aks upgrade`t kell használnia. Ez frissíti a vezérlő síkja és a fürt összes csomópont-készletét. 

A `az aks upgrade` parancsnak a `--control-plane-only` jelzővel való kiállítása csak a fürt vezérlő síkjait frissíti. A fürthöz társított csomópont-készletek egyike sincs módosítva.

Az egyes csomópont-készletek frissítéséhez `az aks nodepool upgrade`t kell használni. Ez csak a cél csomópont-készletet frissíti a megadott Kubernetes-verzióval

### <a name="validation-rules-for-upgrades"></a>A frissítésekre vonatkozó érvényesítési szabályok

A fürt vezérlési síkja vagy a csomópont-készletek által tárolt Kubernetes-verziók érvényes frissítéseit az alábbi szabályok határozzák meg.

* Az érvényes verzióra vonatkozó szabályok a következőre való frissítésre:
   * A csomópont-készlet verziójának *ugyanazzal a* főverzióval kell rendelkeznie, mint a vezérlő síkja.
   * A csomópont-készlet verziója *lehet kisebb,* mint a vezérlési sík verziója.
   * A csomópont-készlet verziója lehet a vezérlő síkja verziónál kisebb *frissítési* verzió.

* A frissítési művelet elküldésére vonatkozó szabályok:
   * A vezérlő síkja vagy a Node Pool Kubernetes verziója nem minősíthető vissza.
   * Ha nincs megadva a csomópont-készlet Kubernetes verziója, a viselkedés a használt ügyféltől függ. A Resource Manager-sablonokban található deklaráció visszaesik a csomópont-készlethez definiált meglévő verzióra, ha a nincs beállítva, a vezérlő síkja a verzióra való visszaeséshez használatos.
   * Egy adott időpontban frissítheti vagy méretezheti a vezérlési síkot vagy a csomópont-készletet, nem küldhet egyszerre több műveletet egyetlen vezérlő síkon vagy Node Pool-erőforráson.

## <a name="scale-a-node-pool-manually"></a>Csomópont-készlet manuális skálázása

Az alkalmazás számítási feladatainak változása miatt szükség lehet a csomópontok számának skálázására. A csomópontok száma növelhető felfelé vagy lefelé.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

A csomópontok számának skálázásához használja az az [AK Node Pool Scale][az-aks-nodepool-scale] parancsot. Az alábbi példa a *mynodepool* csomópontok számát *5*-re méretezi:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Sorolja fel újra a csomópont-készletek állapotát az az [AK Node Pool List][az-aks-nodepool-list] parancs használatával. Az alábbi példa azt mutatja, hogy a *mynodepool* a *skálázási* állapotban van, és új számú *5* csomóponttal rendelkezik:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A skálázási művelet elvégzése néhány percet vesz igénybe.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>A fürt automatikus méretezésének engedélyezésével automatikusan méretezhető egy adott csomópont-készlet

Az AK egy külön funkciót kínál, amellyel automatikusan méretezheti a csomópont-készleteket a [fürt automatikus méretezés](cluster-autoscaler.md)nevű funkciójával. Ez a funkció egy csomópont-készleten engedélyezhető, és a csomópontok egyedi minimális és maximális skálázási száma. Megtudhatja, hogyan [használhatja a fürt automéretezőjét egy csomópontos készleten](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Csomópont-készlet törlése

Ha már nincs szüksége a készletre, törölheti, és eltávolíthatja a mögöttes virtuálisgép-csomópontokat. Egy csomópont-készlet törléséhez használja az az [AK Node Pool delete][az-aks-nodepool-delete] parancsot, és adja meg a csomópont-készlet nevét. A következő példa törli az előző lépésekben létrehozott *mynoodepool* :

> [!CAUTION]
> A csomópontok törlésekor előfordulhat, hogy az adatvesztéshez nincs helyreállítási lehetőség. Ha a hüvely nem ütemezhető más csomópont-készletekre, ezek az alkalmazások nem érhetők el. Győződjön meg arról, hogy nem törli a csomópont-készletet, ha a használatban lévő alkalmazásokban nincsenek biztonsági másolatok, vagy a fürt más csomópontjain is futtathatók.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Az az [AK Node Pool List][az-aks-nodepool-list] parancs kimenete az alábbi példa alapján mutatja , hogy a mynodepool *törlési* állapotban van:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A csomópontok és a csomópont-készlet törlése néhány percet vesz igénybe.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Virtuális gép méretének megadása egy csomópont-készlethez

Az előző példákban egy csomópont-készlet létrehozásához a fürtben létrehozott csomópontok alapértelmezett virtuálisgép-méretét használták. A leggyakoribb forgatókönyv az, hogy a különböző virtuálisgép-méretekkel és képességekkel rendelkező csomópont-készleteket hozzon létre. Létrehozhat például egy olyan csomópont-készletet, amely nagy mennyiségű PROCESSZORral vagy memóriával rendelkező csomópontokat tartalmaz, vagy egy olyan csomópont-készletet, amely GPU-támogatást biztosít. A következő lépésben a [szennyező és a tolerancia használatával](#schedule-pods-using-taints-and-tolerations) közli, hogy a Kubernetes ütemező hogyan korlátozza az ezeken a csomópontokon futtatható hüvelyek elérését.

Az alábbi példában hozzon létre egy GPU-alapú csomópont-készletet, amely a *Standard_NC6* VM-méretet használja. Ezeket a virtuális gépeket az NVIDIA Tesla K80 kártya működteti. A rendelkezésre álló virtuálisgép-méretekkel kapcsolatos információkért lásd: [a Linux rendszerű virtuális gépek méretei az Azure-ban][vm-sizes].

Hozzon létre egy csomópont-készletet az az [AK Node Pool Add][az-aks-nodepool-add] paranccsal. Ezúttal adja meg a *gpunodepool*nevet, és a `--node-vm-size` paraméterrel adja meg a *Standard_NC6* méretét:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Az az [AK Node Pool List][az-aks-nodepool-list] parancs kimenete az alábbi példa alapján mutatja, hogy a *Gpunodepool* csomópontokat *hoz létre* a megadott *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A *gpunodepool* sikeres létrehozása néhány percet vesz igénybe.

## <a name="schedule-pods-using-taints-and-tolerations"></a>A hüvelyek kiosztása a fertőzöttség és a tolerancia használatával

Most már két csomópont-készlet található a fürtben – az alapértelmezett, eredetileg létrehozott Node-készletet és a GPU-alapú csomópont-készletet. A fürt csomópontjainak megtekintéséhez használja a [kubectl Get Nodes][kubectl-get] parancsot. A következő példa kimenete a csomópontokat mutatja:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.10
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.10
```

A Kubernetes-ütemező használatával megtilthatja, hogy milyen számítási feladatok futhatnak a csomópontokon.

* A rendszer egy olyan csomópontra alkalmazza a **megromlást** , amely azt jelzi, hogy csak bizonyos hüvelyek ütemezhetők.
* A **rendszer egy olyan** Pod-ra alkalmazza a betartást, amely lehetővé *teszi a* csomópontok megromlását.

További információ a speciális Kubernetes ütemezett funkcióinak használatáról: [ajánlott eljárások a speciális Scheduler-funkciókhoz az AK-ban][taints-tolerations]

Ebben a példában a [kubectl szennyező csomópont][kubectl-taint] parancs használatával alkalmazzon egy Taint a GPU-alapú csomópontra. Adja meg a GPU-alapú csomópont nevét az előző `kubectl get nodes` parancs kimenetében. A megfertőzés kulcsként van alkalmazva *: érték* , majd egy ütemezési beállítás. A következő példa az *SKU = GPU* párt használja, és a hüvelyek definiálása egyébként a nem *menetrendi* képességgel rendelkezik:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

A következő alapszintű példa YAML a tolerancia használatával lehetővé teszi, hogy a Kubernetes-ütemező egy NGINX Pod-t futtasson a GPU-alapú csomóponton. A Tensorflow-feladatok MNIST-adatkészleten való futtatásának megfelelőbb, de időigényes példáját a [GPU használata nagy számítási igényű munkaterhelésekhez az AK-][gpu-cluster]ban című témakörben tekintheti meg.

Hozzon létre egy `gpu-toleration.yaml` nevű fájlt, és másolja a következő példában szereplő YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

A pod a `kubectl apply -f gpu-toleration.yaml` parancs használatával ütemezhet:

```console
kubectl apply -f gpu-toleration.yaml
```

Eltarthat néhány másodpercig a pod beütemezett és az NGINX-rendszerkép lekéréséhez. A hüvely állapotának megtekintéséhez használja az [kubectl Leírás Pod][kubectl-describe] parancsot. A következő összehasonlított példa kimenete az *SKU = GPU:* a nem ütemezett tolerancia alkalmazását mutatja. Az Events (események) szakaszban az ütemező hozzárendelte a hüvelyt az *AK-gpunodepool-28993262-vmss000000* GPU-alapú csomóponthoz:

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

A *gpunodepool*csomópontjain csak azok a hüvelyek ütemezhetők, amelyeken az adott adatszennyező alkalmazva van. Minden egyéb Pod a *nodepool1* csomópont-készletben lesz ütemezve. Ha további csomópont-készleteket hoz létre, további megterhelések és a megromlások használatával korlátozhatja, hogy milyen hüvelyek ütemezhetők a csomópont-erőforrásokon.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Csomópont-készletek kezelése Resource Manager-sablonnal

Ha Azure Resource Manager sablont használ az erőforrások létrehozásához és kezeléséhez, általában frissíti a sablon beállításait, és újból üzembe helyezi az erőforrás frissítéséhez. Az AK-ban található Node-készletekkel a kezdeti csomópont-készlet profilja nem frissíthető az AK-fürt létrehozása után. Ez azt jelenti, hogy nem frissítheti a meglévő Resource Manager-sablonokat, módosíthatja a csomópont-készleteket, és újból üzembe helyezheti azokat. Ehelyett létre kell hoznia egy különálló Resource Manager-sablont, amely csak a meglévő AK-fürtökhöz tartozó csomópont-készleteket frissíti.

Hozzon létre egy sablont, például `aks-agentpools.json`, és illessze be a következő példa jegyzékfájlt. A példában szereplő sablon a következő beállításokat konfigurálja:

* Frissíti a *myagentpool* nevű *Linux* Node-készletet három csomópont futtatásához.
* Beállítja a csomópont-készlet csomópontjait a Kubernetes-verzió *1.13.10*futtatásához.
* Meghatározza a csomópont méretét *Standard_DS2_v2ként*.

Szükség szerint szerkessze ezeket az értékeket a csomópont-készletek frissítéséhez, hozzáadásához vagy törléséhez:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2019-04-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.13.10"
            }
        }
    ]
}
```

Ezt a sablont az az [Group Deployment Create][az-group-deployment-create] paranccsal telepítheti, ahogy az az alábbi példában is látható. A rendszer kéri a meglévő AK-fürt nevének és helyének megadását:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

A Resource Manager-sablonban definiált csomópont-készlet beállításaitól és műveleteitől függően néhány percet is igénybe vehet az AK-fürt frissítése.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Nyilvános IP-cím társítása egy csomópont-készletben

> [!WARNING]
> A nyilvános IP-címek/csomópontok kiosztásának előnézete során a rendszer nem használhatja a *standard Load BALANCER SKU-t az AK-ban* , mert lehetséges terheléselosztó-szabályok ütköznek a virtuális gépek üzembe helyezésével. Az előzetes verzióban az *Alapszintű Load BALANCER SKU* -t kell használnia, ha csomópontként kell hozzárendelni egy nyilvános IP-címet.

Az AK-csomópontok nem igénylik a saját nyilvános IP-címeiket a kommunikációhoz. Bizonyos esetekben azonban előfordulhat, hogy a csomópontok csomópontjain a saját nyilvános IP-címeiket kell megadni. Egy példa a játékra, ahol a konzolnak közvetlen csatlakoznia kell egy felhőalapú virtuális géphez a ugrások minimalizálásához. Ezt úgy érheti el, ha regisztrál egy külön előzetes verziójú szolgáltatásra, a Node nyilvános IP-címére (előzetes verzió).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

A sikeres regisztráció után helyezzen üzembe egy Azure Resource Manager sablont a [fenti](#manage-node-pools-using-a-resource-manager-template) utasítások alapján, és adja hozzá a Boolean Value (logikai érték) tulajdonságot, `enableNodePublicIP` a agentPoolProfiles. `true` alapértelmezett értékként állítsa be az értéket `false` ha nincs megadva. Ez egy csak létrehozási idő tulajdonság, és legalább 2019-06-01-es API-verziót igényel. Ez a Linux-és a Windows-csomópont-készletekre is alkalmazható.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben létrehozta a GPU-alapú csomópontokat tartalmazó AK-fürtöt. A szükségtelen díjak csökkentése érdekében érdemes törölni a *gpunodepool*vagy a teljes Kabai fürtöt.

A GPU-alapú csomópont-készlet törléséhez használja az az [AK nodepool delete][az-aks-nodepool-delete] parancsot az alábbi példában látható módon:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

A fürt törléséhez használja az az [Group delete][az-group-delete] parancsot az AK-erőforráscsoport törléséhez:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre és kezelhet több Node-készletet egy AK-fürtben. További információ a hüvelyek csomópontok közötti szabályozásáról: [ajánlott eljárások a speciális Scheduler-funkciókhoz az AK-ban][operator-best-practices-advanced-scheduler].

A Windows Server Container Node-készletek létrehozásához és használatához lásd: [Windows Server-tároló létrehozása az AK-ban][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
