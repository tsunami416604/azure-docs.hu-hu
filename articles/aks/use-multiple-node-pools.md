---
title: Több csomópontkészlet használata az Azure Kubernetes-szolgáltatásban (AKS)
description: Megtudhatja, hogyan hozhat létre és kezelhet több csomópontkészletet egy fürthöz az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 607419787bc0bab243d6cc2b8cbaa0ec22921e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422313"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Fürt több csomópontkészletének létrehozása és kezelése az Azure Kubernetes szolgáltatásban (AKS)

Az Azure Kubernetes-szolgáltatásban (AKS) az azonos konfigurációs csomópontok *csomópontkészletekbe*vannak csoportosítva. Ezek a csomópontkészletek tartalmazzák az alkalmazásokat futtató mögöttes virtuális gépeket. A csomópontok kezdeti száma és méretük (SKU) az AKS-fürt létrehozásakor van definiálva, amely *alapértelmezett csomópontkészletet*hoz létre. A különböző számítási vagy tárolási igényekkel bíró alkalmazások támogatásához további csomópontkészleteket hozhat létre. Használja például ezeket a további csomópontkészleteket gpu-k biztosításához a nagy számítási igényű alkalmazásokhoz, vagy a nagy teljesítményű SSD-tárolóhoz való hozzáféréshez.

> [!NOTE]
> Ez a szolgáltatás lehetővé teszi, hogy több csomópontkészlet létrehozása és kezelése jobban szabályozhatja. Ennek eredményeképpen külön parancsok szükségesek a létrehozáshoz/frissítéshez/törléshez. Korábban fürtműveletek `az aks create` `az aks update` a felügyeltfürt API-n keresztül vagy használták, és ez volt az egyetlen lehetőség a vezérlősík és egyetlen csomópontkészlet módosítására. Ez a szolgáltatás az ügynökkészlet-készletek számára külön műveletkészletet tesz `az aks nodepool` elérhetővé az agentPool API-n keresztül, és a parancskészlet használatát igényli az egyes csomópontkészleteken végrehajtott műveletek végrehajtásához.

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet több csomópontkészletet egy AKS-fürtben.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.2.0-s vagy újabb verzióját telepíteni és konfigurálni kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="limitations"></a>Korlátozások

A következő korlátozások vonatkoznak a több csomópontkészletet támogató AKS-fürtök létrehozásakor és kezelésében:

* Lásd: [Kvóták, virtuálisgép-méretkorlátozások és a régió elérhetősége az Azure Kubernetes szolgáltatásban (AKS).][quotas-skus-regions]
* A rendszercsomópont-készlet alapértelmezés szerint nem törölhető az első csomópontkészlet.
* Az AKS-fürtnek a standard termékváltozat terheléselosztót kell használnia több csomópontkészlet használatához, a szolgáltatás nem támogatott az alapszintű termékváltozat terheléselosztóival.
* Az AKS-fürtnek virtuálisgép-méretezési készleteket kell használnia a csomópontokhoz.
* A csomópontkészlet neve csak kisalfanumerikus karaktereket tartalmazhat, és kisbetűvel kell kezdődnie. Linux-csomópontkészletek esetén a hossznak 1 és 12 karakter között kell lennie, a Windows-csomópontkészletek esetében a hossznak 1 és 6 karakter között kell lennie.
* Minden csomópontkészletnek ugyanabban a virtuális hálózatban kell lennie.
* Ha fürtlétrehozási időben több csomópontkészletet hoz létre, a csomópontkészletek által használt összes Kubernetes-verziónak meg kell egyeznie a vezérlősíkhoz beállított verzióval. Ez frissíthető a fürt csomópontonkénti készletműveletek használatával történő kiépítése után.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Első lépésekhez hozzon létre egy AKS-fürtöt egyetlen csomópontkészlettel. A következő példa az [az csoport létrehozása][az-group-create] parancsot használja egy *myResourceGroup* nevű erőforráscsoport létrehozásához az *eastus* régióban. Ezután létrejön egy *myAKSCluster* nevű AKS-fürt az [az aks create][az-aks-create] paranccsal. A *--kubernetes-verzió* *1.15.7* segítségével mutatja be, hogyan frissítheti a csomópontkészlet egy következő lépésben. A támogatott [Kubernetes-verziót megadhatja.][supported-versions]

> [!NOTE]
> Az *alapszintű* terheléselosztó termékváltozat **nem támogatott** több csomópontkészlet használata esetén. Alapértelmezés szerint az AKS-fürtök az Azure CLI és az Azure Portal *standard* terheléselosztó Termékváltozatával jönnek létre.

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
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

A fürt létrehozása néhány percet vesz igénybe.

> [!NOTE]
> Annak érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópontkészletben, mivel az alapvető rendszerszolgáltatások ezen a csomópontkészleten futnak.

Amikor a fürt készen áll, az [az aks get-credentials][az-aks-get-credentials] paranccsal lekéri a fürt hitelesítő adatait a következőhöz: `kubectl`

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Csomópontkészlet hozzáadása

Az előző lépésben létrehozott fürt egyetlen csomópontkészlettel rendelkezik. Adjunk hozzá egy második csomópontkészletet az [az aks nodepool add][az-aks-nodepool-add] paranccsal. A következő példa létrehoz egy *mynodepool* nevű csomópontkészletet, amely *3* csomópontot futtat:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> A csomópontkészlet nevének kisbetűvel kell kezdődnie, és csak alfanumerikus karaktereket tartalmazhat. Linux-csomópontkészletek esetén a hossznak 1 és 12 karakter között kell lennie, a Windows-csomópontkészletek esetében a hossznak 1 és 6 karakter között kell lennie.

A csomópontkészletek állapotának megtekintéséhez használja az [aks csomópontkészletlista][az-aks-nodepool-list] parancsot, és adja meg az erőforráscsoportot és a fürt nevét:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

A következő példa kimenetazt mutatja, hogy *a mynodepool* sikeresen létrejött a csomópontkészlethárom csomópontjával. Amikor az AKS-fürt az előző lépésben jött létre, egy alapértelmezett *nodepool1* jött létre *2*csomópontszámmal.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Ha nincs megadva *VmSize* csomópontkészlet hozzáadásakor, az alapértelmezett méret *Standard_DS2_v3* a Windows-csomópontkészletek és a Linux-csomópontkészletek *Standard_DS2_v2.* Ha nincs *megadva OrchestratorVersion,* akkor alapértelmezés szerint a vezérlősíkkal azonos verziójú.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Csomópontkészlet hozzáadása egyedi alhálózattal (előzetes verzió)

A számítási feladatok a fürt csomópontjainak felosztását külön készletekre lehet szükség a logikai elkülönítés érdekében. Ez az elkülönítés a fürt minden csomópontkészletéhez rendelt külön alhálózattal támogatott. Ez olyan követelményekkel is megoldhatja, mint például a csomópontkészletek között felosztható, nem összefüggő virtuális hálózati címterület.

#### <a name="limitations"></a>Korlátozások

* A csomóponthalmazokhoz rendelt összes alhálózatnak ugyanahhoz a virtuális hálózathoz kell tartoznia.
* A rendszerpodok nak hozzáféréssel kell rendelkezniük a fürt összes csomópontjához, hogy kritikus funkciókat, például DNS-feloldást biztosítsanak a coreDNS-en keresztül.
* Egy egyedi alhálózat csomópontkészletenként hozzárendelése az Azure CNI előzetes verzióban korlátozódik.
* A hálózati házirendek egyedi alhálózattal csomópontonkénti használatával nem támogatott az előzetes verzió.

Ha dedikált alhálózattal rendelkező csomópontkészletet szeretne létrehozni, adja át az alhálózati erőforrás-azonosítót további paraméterként csomópontkészlet létrehozásakor.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Csomópontkészlet frissítése

> [!NOTE]
> A fürt vagy csomópontkészlet frissítési és méretezési műveletei nem fordulhatnak elő egyszerre, ha hibát kísérelt meg. Ehelyett minden művelettípusnak be kell fejeződnie a célerőforráson, mielőtt az adott erőforrásra vonatkozó következő kérést végrehajtana. Erről bővebben a [hibaelhárítási útmutatónkban](https://aka.ms/aks-pending-upgrade)olvashat.

Amikor az AKS-fürt eredetileg az első `--kubernetes-version` lépésben jött létre, *1.15.7-es* érték lett megadva. Ez a készlet a Kubernetes verziója mind a vezérlősík és az alapértelmezett csomópont készlet. Az ebben a szakaszban található parancsok bemutatják, hogyan frissíthet egyetlen adott csomópontkészletet.

A vezérlősík Kubernetes verziójának frissítése és a csomópontkészlet frissítése közötti kapcsolatot az [alábbi szakasz](#upgrade-a-cluster-control-plane-with-multiple-node-pools)ismerteti.

> [!NOTE]
> A csomópontkészlet operációsrendszer-lemezképverziója a fürt Kubernetes verziójához van kötve. Csak a fürt frissítése után kap operációs rendszer rendszerkép-frissítéseket.

Mivel ebben a példában két csomópontkészlet van, az [aks nodepool-frissítés][az-aks-nodepool-upgrade] sel kell használnunk a csomópontkészlet frissítését. Frissítsük a *mynodepool-t* Kubernetes *1.15.7-re.* Az [aks nodepool upgrade][az-aks-nodepool-upgrade] paranccsal frissítse a csomópontkészletet, ahogy az a következő példában látható:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Sorolja fel ismét a csomópontkészletek állapotát az [aks csomópontkészletlista][az-aks-nodepool-list] paranccsal. A következő példa azt mutatja, hogy a *mynodepool* *frissítési* állapotban van *1.15.7-re:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
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
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A csomópontok frissítése a megadott verzióra néhány percet vesz igénybe.

Ajánlott eljárásként frissítenie kell az Összes csomópont-készletek egy AKS-fürtben, hogy ugyanazt a Kubernetes-verzió. Az alapértelmezett `az aks upgrade` viselkedés az, hogy az összes csomópontkészletet a vezérlősíkkal együtt frissíti az igazítás elérése érdekében. Az egyes csomópontkészletek frissítésével folyamatos frissítés és ütemezés idáig biztosíthatja a podok ütemezését a csomópontkészletek között, hogy az alkalmazás-uptime a fent említett korlátozásokon belül maradjon.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Fürtvezérlő sík frissítése több csomópontkészlettel

> [!NOTE]
> A Kubernetes a szabványos [szemantikai verziószámozási](https://semver.org/) verziószámozási sémát használja. A verziószám *x.y.z-ben*van kifejezve, ahol *x* a főverzió, *az y* az alverzió, a *z* pedig a javítási verzió. Például az *1.12.6-os*verzióban az 1 a főverzió, a 12 az alverzió, a 6 pedig a javítóverzió. A vezérlősík Kubernetes verziója és a kezdeti csomópontkészlet a fürt létrehozása során van beállítva. Minden további csomópontkészlet rendelkezik a Kubernetes-verzióbeállítással, amikor hozzáadja őket a fürthöz. A Kubernetes verziók eltérőek lehetnek a csomópontkészletek, valamint a csomópontkészlet és a vezérlősík között.

Egy AKS-fürt két fürterőforrás-objektummal rendelkezik, amelyekhez Kubernetes-verziók vannak társítva.

1. Fürtvezérlő sík Kubernetes verziója.
2. Kubernetes-verzióval rendelkező csomópontkészlet.

Egy vezérlősík egy vagy több csomópontkészletre van leképezve. A frissítési művelet viselkedése attól függ, hogy melyik Azure CLI-parancsot használja.

Az AKS vezérlősík `az aks upgrade`frissítését a használatával kell használni. Ez a parancs frissíti a vezérlősík verzióját és a fürt összes csomópontkészletét.

A `az aks upgrade` parancs kiadása `--control-plane-only` a jelzőfrissítéssel csak a fürtvezérlő síkot frissíti. A fürt egyik társított csomópontkészlete sem módosul.

Az egyes csomópontkészletek frissítéséhez a használatához kell használni. `az aks nodepool upgrade` Ez a parancs csak a megadott Kubernetes-verzióval rendelkező célcsomópont-készletet frissíti

### <a name="validation-rules-for-upgrades"></a>A frissítések rekedési szabályai

Az érvényes Kubernetes-frissítéseket a fürt vezérlősíkjára és csomópontkészleteire a következő szabálykészletek ellenőrzik.

* A csomópontkészletek frissítéséhez érvényes verziókra vonatkozó szabályok:
   * A csomópontkészlet verziójának a vezérlősíkévfőverziójával azonos *főverzióval* kell rendelkeznie.
   * A csomópontkészlet *alverziójának* a vezérlősík verziójának két *alverzióján* belül kell lennie.
   * A csomópontkészlet verziója nem lehet `major.minor.patch` nagyobb, mint a vezérlőverzió.

* A frissítési művelet elküldésének szabályai:
   * A vezérlősík vagy a Kubernetes-verzió nem minősíthető vissza.
   * Ha a Csomópont készlet Kubernetes verzió nincs megadva, a viselkedés attól függ, hogy az ügyfél használatban van. Az Erőforrás-kezelő sablonjaiban a deklaráció visszaáll a csomópontkészlethez definiált meglévő verzióra, ha használatban van, ha nincs beállítva, akkor a vezérlősík verziója visszaesik.
   * Egy vezérlősíkot vagy csomópontkészletet egyszerre frissíthet vagy méretezhet, nem küldhet egyszerre több műveletet egyetlen vezérlősíkon vagy csomópontkészlet-erőforráson.

## <a name="scale-a-node-pool-manually"></a>Csomópontkészlet manuális méretezése

Az alkalmazás számítási feladatok módosítása, előfordulhat, hogy egy csomópont készlet ben csomópontok számát kell skáláznia. A csomópontok száma fel- vagy leskálázható.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

A csomópontkészlet csomópontjainak számának méretezéséhez használja az [aks csomópontkészlet méretezési][az-aks-nodepool-scale] parancsát. A következő példa a *mynodepool* csomópontjainak számát *5-re*méretezi:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Sorolja fel ismét a csomópontkészletek állapotát az [aks csomópontkészletlista][az-aks-nodepool-list] paranccsal. A következő példa azt mutatja, hogy a *mynodepool* *méretezési* állapotban *van, 5* csomópontúj számlálóval:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
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
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A méretezési művelet befejezéséhez néhány percet vesz igénybe.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Adott csomópontkészlet automatikus méretezése a fürt automatikus méretezőjének engedélyezésével

Az AKS külön szolgáltatást kínál a csomópontkészletek automatikus méretezéséhez a [fürt automatikus skálázójával.](cluster-autoscaler.md) Ez a funkció csomópontkészletenként engedélyezhető, egyedi minimális és maximális léptékszámmal csomópontkészletenként. Ismerje meg, hogyan [használhatja a fürt automatikus skálázó csomópontkészletenként](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Csomópontkészlet törlése

Ha már nincs szüksége készletre, törölheti azt, és eltávolíthatja az alapul szolgáló virtuálisgép-csomópontokat. Csomópontkészlet törléséhez használja az [aks csomópontkészlet törlési][az-aks-nodepool-delete] parancsát, és adja meg a csomópontkészlet nevét. A következő példa törli az előző lépésekben létrehozott *mynoodepoolt:*

> [!CAUTION]
> Nincsenek helyreállítási lehetőségek adatvesztésre, amelyek a csomópontkészlet törlésekor fordulhatnak elő. Ha a podok nem ütemezhetők más csomópontkészleteken, ezek az alkalmazások nem érhetők el. Győződjön meg arról, hogy nem törli a csomópontkészletet, ha a használatban lévő alkalmazások nem rendelkeznek adatbiztonsági másolatokkal, vagy nem képesek a fürt más csomópontkészletein futtatni.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Az [az aks csomópontkészletlista][az-aks-nodepool-list] parancs következő kimenete azt mutatja, hogy a *mynodepool* *törlési* állapotban van:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
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
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A csomópontok és a csomópontkészlet törlése néhány percet vesz igénybe.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Virtuálisgép méretének megadása csomópontkészlethez

Az előző példákban egy csomópontkészlet létrehozásához egy alapértelmezett virtuális gép mérete volt a fürtben létrehozott csomópontok hoz létre. Egy gyakoribb forgatókönyv, hogy hozzon létre csomópontkészletek különböző virtuálisgép-méreteket és képességeket. Létrehozhat például egy csomópontkészletet, amely nagy mennyiségű PROCESSZORral vagy memóriával rendelkező csomópontokat tartalmaz, vagy egy GPU-támogatást támogató csomópontkészletet. A következő lépésben [a taints és a tolerations](#schedule-pods-using-taints-and-tolerations) segítségével megmondja a Kubernetes ütemező, hogyan korlátozhatja a hozzáférést a podok, amelyek futtathatók ezeken a csomópontokon.

A következő példában hozzon létre egy GPU-alapú csomópontkészletet, amely a virtuális gép *Standard_NC6* használja. Ezek a virtuális gépek powered by nvidia Tesla K80 kártya. A rendelkezésre álló virtuálisgép-méretekről az [Azure-beli Linuxos virtuális gépek méretei című témakörben][vm-sizes]talál további információt.

Hozzon létre egy csomópontkészletet az [aks csomópontkészlet hozzáadása][az-aks-nodepool-add] paranccsal. Ezúttal adja meg a *gpunodepool* `--node-vm-size` nevet, és a paraméterrel adja meg a *Standard_NC6* méretet:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

A következő példa kimenetaz [az aks csomópontkészlet lista][az-aks-nodepool-list] parancs azt mutatja, hogy *gpunodepool* *létrehozása* csomópontok a megadott *VmSize:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
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
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A *gpunodepool* sikeres létrehozása néhány percet vesz igénybe.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Podok ütemezése taints és tolerations használatával

Most már két csomópontkészlet található a fürtben – az eredetileg létrehozott alapértelmezett csomópontkészlet és a GPU-alapú csomópontkészlet. Használja a [kubectl get csomópontok parancsot][kubectl-get] a fürt csomópontjainak megtekintéséhez. A következő példa kimenete a csomópontokat mutatja be:

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

A Kubernetes-ütemező használhat taints és tűrések korlátozhatja, milyen számítási feladatok futtathatók a csomópontokon.

* Egy **taint** egy csomópontra van alkalmazva, amely azt jelzi, hogy csak adott podok ütemezhetők rájuk.
* Ezután **egy tolerálást** alkalmaznak egy podra, amely lehetővé teszi számukra, hogy *tolerálják* a csomópont romlottságát.

A Speciális Kubernetes ütemezett funkciók használatáról az [AKS speciális ütemezőszolgáltatásainak gyakorlati tanácsai című témakörben][taints-tolerations] talál további információt.

Ebben a példában alkalmazzon egy taint a GPU-alapú csomópont a --node-taints paranccsal. Adja meg a GPU-alapú csomópont nevét az `kubectl get nodes` előző parancs kimenetéből. A program key:value, majd ütemezési lehetőségként alkalmazza a taint.The taint is applied as a *key:value,* then a scheduling option. A következő példa az *sku=gpu* párt használja, és meghatározza, hogy a podok egyébként *noschedule* képességgel rendelkeznek:

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

A következő alapvető példa YAML-jegyzékfájl egy tűrést használ, hogy a Kubernetes ütemező egy NGINX-pod futtatásához a GPU-alapú csomóponton. A Tensorflow-feladat MNIST adatkészleten való futtatásához megfelelőbb, de időigényes példát az [AKS-en nagy számítási nagy számítási feladatokhoz című][gpu-cluster]témakörben található.

Hozzon létre `gpu-toleration.yaml` egy elnevezett fájlt, és másolja a következő példa YAML:

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

Ütemezze a `kubectl apply -f gpu-toleration.yaml` podot a következő paranccsal:

```console
kubectl apply -f gpu-toleration.yaml
```

Néhány másodpercet vesz igénybe a hüvely ütemezése és az NGINX-kép lekérése. Használja a [kubectl leírás pod][kubectl-describe] parancsot a pod állapotát. A következő kondenzált példa kimenet az *sku=gpu:NoSchedule* terration alkalmazását mutatja. Az események szakaszban az ütemező hozzárendelte a podot az *aks-gpunodepool-28993262-vmss000000* GPU-alapú csomóponthoz:

```console
kubectl describe pod mypod
```

```output
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

Csak a *gpunodepool*csomópontjain ütemezhetők olyan podok, amelyek ilyen taint-ot alkalmaznak. Minden más pod lenne ütemezve a *nodepool1* csomópont-készlet. Ha további csomópontkészleteket hoz létre, további taints és tűrések használatával korlátozhatja, hogy milyen podok ütemezhetők ezeken a csomópont-erőforrásokon.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Csomópontkészlet értékének, címkéjének vagy címkéjének megadása

Csomópontkészlet létrehozásakor hozzáadhat taints, címkék vagy címkék, hogy a csomópont készlet. Ha hozzáad egy romlottat, címkét vagy címkét, az adott csomópontkészleten belüli összes csomópont is megkapja azt a romlottat, címkét vagy címkét.

Ha egy csomónt is szeretne létrehozni, használja [az aks nodepool add][az-aks-nodepool-add]. Adja meg a *taintnp nevet,* és a `--node-taints` paraméter segítségével adja meg a *sku=gpu:NoSchedule* értéket a taint-hez.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

A következő példa kimenetaz [az aks nodepool list][az-aks-nodepool-list] parancs azt mutatja, hogy *taintnp* *létrehozása* csomópontok a megadott *nodeTaints*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  {
      "sku": "gpu:NoSchedule"
    },
    ...
  },
 ...
]
```

A taint információ látható Kubernetes kezelésére ütemezési szabályok csomópontok kezelésére.

Címkéket is hozzáadhat egy csomópontkészlethez a csomópontkészlet létrehozása során. A csomópontkészletben beállított címkék hozzáadódnak a csomópontkészlet minden csomópontjához. Ezek [a címkék a Kubernetes-ben láthatók][kubernetes-labels] a csomópontok ütemezési szabályainak kezeléséhez.

Ha címkét is rendelkező csomópontkészletet szeretne létrehozni, használja [az aks nodepool add][az-aks-nodepool-add]. Adja meg a *name labelnp nevet,* és a `--labels` paraméter rel adja meg *az dept=IT* és a *costcenter=9999* értéket a címkékhez.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Címke csak a csomópontkészletek létrehozása során állítható be. A címkéknek kulcs-érték párnak is kell lenniük, és [érvényes szintaxissal][kubernetes-label-syntax]kell rendelkezniük.

Az [az aks nodepool list][az-aks-nodepool-list] parancs következő kimenete azt mutatja, hogy a *labelnp* a megadott *csomópontcímkékkel*rendelkező csomópontok *létrehozása:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

Az AKS-fürt csomópontkészleteire azure-címkét alkalmazhat. A csomópontkészletre alkalmazott címkék a csomópontkészlet minden csomópontjára vonatkoznak, és a frissítések en keresztül megmaradnak. Címkék et is alkalmaznak a csomópontkészlethez a kibővített műveletek során hozzáadott új csomópontokra. Címke hozzáadása segíthet az olyan feladatokban, mint a házirendek nyomon követése vagy a költségbecslés.

Hozzon létre egy csomópontkészletet az [aks nodepool add][az-aks-nodepool-add]használatával. Adja meg a *tagnodepool nevet,* és a paraméter segítségével adja meg `--tag` *az dept=IT* és a *costcenter=9999* értéket a címkékhez.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> A paramétert `--tags` az [aks nodepool update][az-aks-nodepool-update] parancs használatakor, valamint a fürt létrehozása során is használhatja. A fürt létrehozása `--tags` során a paraméter a címkét alkalmazza a fürttel létrehozott kezdeti csomópontkészletre. Minden címkenévnek meg kell felelnie az [Azure-erőforrások rendszerezéséhez a Címkék használata][tag-limitation]című területen található korlátozásoknak. A csomópontkészlet frissítése a `--tags` paraméterrel frissíti a meglévő címkeértékeket, és hozzáfűzi az új címkéket. Ha például a csomópontkészlet rendelkezett *dept=IT* és *costcenter=9999* címkék, és frissítette a *team=dev* és *costcenter=111* címkék, akkor nodepool volna *dept=IT*, *costcenter=111*, és *team=dev* címkékhez.

Az [az aks nodepool list][az-aks-nodepool-list] parancs következő példája azt mutatja, hogy a *tagnodepool* a megadott *címkével*rendelkező csomópontok *létrehozása:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Csomópontkészletek kezelése Erőforrás-kezelő sablonnal

Ha egy Azure Resource Manager-sablon használatával hoz létre és kezelhet erőforrásokat, általában frissítheti a beállításokat a sablonban, és újratelepítheti az erőforrás frissítéséhez. Az AKS csomópontkészleteivel a kezdeti csomópontkészlet-profil nem frissíthető az AKS-fürt létrehozása után. Ez a viselkedés azt jelenti, hogy nem frissíthet egy meglévő Erőforrás-kezelő sablont, nem módosíthatja a csomópontkészleteket, és nem helyezheti üzembe újra. Ehelyett létre kell hoznia egy külön Erőforrás-kezelő sablont, amely csak egy meglévő AKS-fürt csomópontkészleteit frissíti.

Hozzon létre `aks-agentpools.json` egy sablont, például illessze be a következő példajegyzéket. Ez a példasablon a következő beállításokat konfigurálja:

* Frissíti a *Myagentpool* nevű *Linux-csomópontkészletet* három csomópont futtatásához.
* Beállítja a csomópontkészlet csomópontjait a Kubernetes *1.15.7-es*verziójának futtatásához.
* A csomópont méretét *Standard_DS2_v2.*

Szükség szerint szerkesztheti ezeket az értékeket a csomópontkészletek frissítéséhez, hozzáadásához vagy törléséhez:

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
            "aks": "2020-01-01"
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
            "apiVersion": "2020-01-01",
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
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Telepítse ezt a sablont az [az csoport központi telepítési létrehozási][az-group-deployment-create] parancsával, ahogy az a következő példában látható. A rendszer kéri a meglévő AKS-fürt nevét és helyét:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> A címke hozzáadásával a csomópont készlet hozzáadásával a *címke* tulajdonság a sablonban, ahogy az a következő példában látható.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Az Erőforrás-kezelő sablonban megadott csomópontkészlet-beállításoktól és műveletektől függően eltarthat néhány percig az AKS-fürt frissítése.

## <a name="assign-a-public-ip-per-node-for-a-node-pool-preview"></a>Csomópontonkénti nyilvános IP-cím hozzárendelése csomópontkészlethez (előzetes verzió)

> [!WARNING]
> A nyilvános IP-cím csomópontonkénti hozzárendelésének előzetes verziója során nem használható a *standard terheléselosztó termékváltozattal az AKS-ben* a virtuális gép kiépítésével ütköző lehetséges terheléselosztó szabályok miatt. Ennek a korlátozásnak az eredményeként a Windows-ügynökkészletek nem támogatottak ezzel az előzetes verzióval. Előzetes verzióban az *alapszintű terheléselosztó termékváltozatot* kell használnia, ha csomópontonként nyilvános IP-címet kell hozzárendelnie.

Az AKS-csomópontok nak nincs szükségük saját nyilvános IP-címekre a kommunikációhoz. Előfordulhat azonban, hogy a csomópontok csomópontkészletében lévő csomópontoknak saját dedikált nyilvános IP-címeket kell fogadniuk. Gyakori forgatókönyv a játékszámítási feladatok, ahol a konzol kell, hogy közvetlen kapcsolatot egy felhőalapú virtuális gép az ugrások minimalizálása érdekében. Ez a forgatókönyv érhető el az AKS-ben egy előzetes verziójú funkció, csomópont nyilvános IP (előzetes verzió) regisztrálásával.

Regisztráljon a csomópont nyilvános IP-szolgáltatás a következő Azure CLI-parancs kiadásával.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Sikeres regisztráció után telepítsen egy Azure Resource [above](#manage-node-pools-using-a-resource-manager-template) Manager-sablont a `enableNodePublicIP` fenti utasításokkal megegyező utasításokat követve, és adja hozzá a logikai tulajdonságot az agentPoolProfiles-hoz. Állítsa be `true` az értéket úgy, `false` ahogy alapértelmezés szerint nincs megadva. 

Ez a tulajdonság csak egy létrehozási idejű tulajdonság, és a 2019-06-01 minimális API-verzióját igényli. Ez linuxos és Windows-csomópontkészletekre is alkalmazható.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben létrehozott egy AKS-fürtöt, amely tartalmazza a GPU-alapú csomópontokat. A szükségtelen költségek csökkentése érdekében érdemes törölni a *gpunodepool*, vagy az egész AKS-fürt.

A GPU-alapú csomópontkészlet törléséhez használja az [aks nodepool delete][az-aks-nodepool-delete] parancsot a következő példában látható módon:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Maga a fürt törléséhez az [az csoport törlési][az-group-delete] parancsával törölje az AKS erőforráscsoportot:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozhat létre és kezelhet több csomópontkészletet egy AKS-fürtben. A podok csomópontkészletek közötti vezérléséről az [AKS speciális ütemező szolgáltatásainak gyakorlati tanácsai című témakörben][operator-best-practices-advanced-scheduler]talál további információt.

A Windows Server tárolócsomópont-készletek létrehozásáról és használatáról a [Windows Server-tároló létrehozása az AKS-ben][aks-windows]című témakörben található.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md