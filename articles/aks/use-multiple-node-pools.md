---
title: Több Node-készlet használata az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan hozhat létre és kezelhet több Node-készletet egy fürthöz az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: 514098368c38c6d61bc192f5ba0f0450dc05776c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533474"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Előzetes verzió – több Node-készlet létrehozása és kezelése az Azure Kubernetes Service-ben (ak)

Az Azure Kubernetes szolgáltatásban (ak) az azonos konfiguráció csomópontjai a *csomópont*-készletekbe vannak csoportosítva. Ezek a csomópont-készletek az alkalmazásokat futtató mögöttes virtuális gépeket tartalmazzák. A csomópontok kezdeti száma és a mérete (SKU) definiálása akkor történik meg, amikor létrehoz egy *alapértelmezett csomópont*-készletet, amely létrehoz egy AK-fürtöt. A különböző számítási vagy tárolási igényekkel rendelkező alkalmazások támogatásához további csomópont-készleteket is létrehozhat. Ezekkel a további csomópont-készletekkel például GPU-ket biztosíthat a számítási igényű alkalmazások számára, vagy hozzáférhet a nagy teljesítményű SSD-tárolóhoz.

> [!NOTE]
> Ez a funkció lehetővé teszi a több csomópontos készlet létrehozását és kezelését. Ennek eredményeképpen külön parancsok szükségesek a létrehozás/frissítés/törlés számára. Korábban a managedCluster API `az aks create` - `az aks update` val vagy annak használatával végzett fürtműveleteket, és az egyetlen lehetőség, hogy megváltoztassa a vezérlő síkot és egyetlen csomópontos készletet. Ez a funkció az agentPool API-n keresztül egy külön műveleti készletet tesz elérhetővé az ügynökök számára `az aks nodepool` , és a parancs használatát igényli a műveletek végrehajtásához egy adott csomópont-készleten.

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet több Node-készletet egy AK-fürtben. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.61 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

Több Node-készlet használatához szüksége lesz az *AK-* előnézeti CLI-bővítmény 0.4.1 vagy újabb verziójára. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg az elérhető frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-multiple-node-pool-feature-provider"></a>Több csomópontos készlet szolgáltatás-szolgáltató regisztrálása

Ha olyan AK-fürtöt szeretne létrehozni, amely több Node-készletet is használhat, először engedélyezzen két szolgáltatás jelölőt az előfizetésben. A többcsomópontos készlet fürtök virtuálisgép-méretezési csoport (VMSS) használatával kezelik a Kubernetes-csomópontok központi telepítését és konfigurációját. Regisztrálja a *MultiAgentpoolPreview* és a *VMSSPreview* funkció jelzőit az az [Feature Register][az-feature-register] paranccsal az alábbi példában látható módon:

> [!CAUTION]
> Ha regisztrál egy szolgáltatást egy előfizetéshez, jelenleg nem tudja regisztrálni a szolgáltatást. Az előzetes verziójú funkciók engedélyezése után az alapértelmezett beállítások az előfizetésben létrehozott összes AK-fürthöz használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> A *MultiAgentpoolPreview* sikeres regisztrálását követően létrehozott AK-fürtök ezt az előzetes verziójú fürtöt használják. A rendszeres, teljes mértékben támogatott fürtök létrehozásához ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Az előzetes verzió funkcióinak teszteléséhez használjon külön tesztelési vagy fejlesztési Azure-előfizetést.

Néhány percet vesz igénybe, amíg az állapot *regisztrálva*jelenik meg. A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Korlátozások

A több csomópontot támogató AK-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* Több csomópontos készlet csak akkor érhető el az előfizetéshez tartozó *MultiAgentpoolPreview* és *VMSSPreview* funkciók sikeres regisztrálása után létrehozott fürtökhöz. Nem adhat hozzá és nem kezelhet olyan csomópont-készleteket, amelyekhez már létre van hozva a szolgáltatások sikeres regisztrálása előtt.
* Az első csomópont-készlet nem törölhető.
* A HTTP-alkalmazás útválasztási bővítménye nem használható.
* Csomópont-készleteket nem lehet hozzáadni/frissíteni vagy törölni egy meglévő Resource Manager-sablon használatával a legtöbb művelethez hasonlóan. Ehelyett [használjon egy különálló Resource Manager-sablont](#manage-node-pools-using-a-resource-manager-template) , amellyel módosításokat hajthat végre egy AK-fürtben lévő csomópont-készleteken.

Habár ez a funkció előzetes verzióban érhető el, a következő további korlátozások érvényesek:

* Az AK-fürt legfeljebb nyolc csomópont-készletet tartalmazhat.
* Az AK-fürt legfeljebb 400 csomóponttal rendelkezhet a nyolc csomópontos készletben.
* Az összes csomópont-készletnek ugyanabban az alhálózatban kell lennie

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Első lépésként hozzon létre egy AK-fürtöt egyetlen csomópontos készlettel. Az alábbi példa az az [Group Create][az-group-create] paranccsal létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *eastus* régióban. Ezután létrejön egy *myAKSCluster* nevű AK-fürt az az [AK Create][az-aks-create] paranccsal. A *1.13.9* egy *--kubernetes-verziója* a következő lépésben mutatja be, hogyan lehet frissíteni egy csomópont-készletet. Megadhat bármilyen [támogatott Kubernetes-verziót][supported-versions].

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.13.9
```

A fürt létrehozása néhány percet vesz igénybe.

Ha a fürt elkészült, használja az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot a fürt hitelesítő adatainak lekéréséhez a következővel való használatra `kubectl`:

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

A csomópont-készletek állapotának megtekintéséhez használja az az [AK Node Pool List][az-aks-nodepool-list] parancsot, és adja meg az erőforráscsoportot és a fürt nevét:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

A következő példa kimenete azt mutatja, hogy a *mynodepool* sikeresen létrejött a csomópont-készletben lévő három csomóponttal. Ha az AK-fürt az előző lépésben lett létrehozva, akkor egy alapértelmezett *nodepool1* lett létrehozva, amelynek a csomópontjának száma *1*.

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
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.9",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Ha nem ad meg *OrchestratorVersion* vagy *VmSize* a csomópont hozzáadásakor, a csomópontok az AK-fürt alapértelmezett értékei alapján jönnek létre. Ebben a példában ez volt a Kubernetes verziójának *1.13.9* és a *Standard_DS2_v2*csomópontjának mérete.

## <a name="upgrade-a-node-pool"></a>Csomópont-készlet frissítése

> [!NOTE]
> Egy fürtön vagy csomóponton található műveletek frissítése és méretezése kölcsönösen kizárható. Nem lehet egyszerre frissíteni és méretezni a fürt vagy a csomópont készletét. Ehelyett minden Művelettípus a következő, ugyanazon az erőforráson megjelenő kérelem előtt fejeződik be a cél erőforráson. Erről a hibaelhárítási útmutatóban [](https://aka.ms/aks-pending-upgrade)olvashat bővebben.

Ha az AK-fürt az első lépésben lett létrehozva, `--kubernetes-version` a rendszer egy *1.13.9* adott meg. Ezzel beállítja a Kubernetes verzióját a vezérlő síkja és a kezdeti csomópont-készlet esetében is. A vezérlő síkja és a Kubernetes verziójának frissítéséhez különböző parancsok tartoznak. A `az aks upgrade` parancs a vezérlési sík frissítésére szolgál, míg az `az aks nodepool upgrade` egy adott csomópont-készlet frissítésére szolgál.

Frissítse a *mynodepool* az Kubernetes *1.13.9*. Az az [AK Node Pool upgrade][az-aks-nodepool-upgrade] paranccsal frissítse a csomópont-készletet, ahogy az az alábbi példában is látható:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.9 \
    --no-wait
```

> [!Tip]
> A vezérlő síkja *1.14.5*való frissítéséhez futtassa a `az aks upgrade -k 1.14.5`parancsot.

Sorolja fel újra a csomópont-készletek állapotát az az [AK Node Pool List][az-aks-nodepool-list] parancs használatával. A következő példa azt mutatja, hogy a *mynodepool* a *1.13.9* *Verziófrissítési* állapotban van:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.9",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.9",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

A csomópontok a megadott verzióra való frissítése néhány percet vesz igénybe.

Ajánlott eljárásként egy AK-fürt összes csomópont-készletét ugyanarra a Kubernetes-verzióra kell frissíteni. Az egyes csomópont-készletek verziófrissítése lehetővé teszi a működés közbeni frissítés végrehajtását és a csomópontok közötti ütemezést, hogy a fent említett korlátozásokon belül fenntartsa az alkalmazás üzemidőét.

> [!NOTE]
> A Kubernetes a szabványos [szemantikai verziószámozási](https://semver.org/) sémát használja. A verziószám *x. y. z*értékként van kifejezve, ahol az *x* a főverzió, az *y* az alverzió, a *z* pedig a javítás verziója. Például a *1.12.6*verzióban az 1 a főverzió, a 12 az alverzió, a 6 pedig a javítás verziója. A Kubernetes verziója, valamint a kezdeti csomópont-készlet is be van állítva a fürt létrehozásakor. Az összes további csomópont-készlet Kubernetes-verziója be van állítva a fürthöz való hozzáadásakor. A Kubernetes-verziók eltérőek lehetnek a csomópont-készletek és a csomópont-készlet és a vezérlési sík között, de a következő korlátozások érvényesek:
> 
> * A csomópont-készlet verziójának ugyanazzal a főverzióval kell rendelkeznie, mint a vezérlő síkja.
> * A csomópont-készlet verziója lehet kisebb, mint a vezérlő síkja verziója.
> * A csomópont-készlet verziója lehet bármilyen javítási verzió, ha a másik két korlátozást követik.
> 
> A vezérlő síkja Kubernetes-verziójának frissítéséhez használja `az aks upgrade`a következőt:. Ha a fürtnek csak egy csomópont-készlete `az aks upgrade` van, akkor a parancs a Kubernetes verzióját is frissíti.

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
    "orchestratorVersion": "1.13.9",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.9",
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

Az AK egy külön funkciót kínál az előzetes verzióban, amellyel automatikusan méretezheti a csomópont-készleteket a [fürt automatikus méretezés](cluster-autoscaler.md)nevű funkciójával. Ez a funkció egy olyan AK-bővítmény, amely egy csomópont-készletben engedélyezhető, és amelyekben egyedi minimális és maximális méretezési szám adható meg. Megtudhatja, hogyan [használhatja a fürt automéretezőjét egy csomópontos készleten](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

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
    "orchestratorVersion": "1.13.9",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.9",
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

Az előző példákban egy csomópont-készlet létrehozásához a fürtben létrehozott csomópontok alapértelmezett virtuálisgép-méretét használták. A leggyakoribb forgatókönyv az, hogy a különböző virtuálisgép-méretekkel és képességekkel rendelkező csomópont-készleteket hozzon létre. Létrehozhat például egy olyan csomópont-készletet, amely nagy mennyiségű PROCESSZORral vagy memóriával rendelkező csomópontokat tartalmaz, vagy egy olyan csomópont-készletet, amely GPU-támogatást biztosít. A következő lépésben a szennyező [és a tolerancia használatával](#schedule-pods-using-taints-and-tolerations) közli, hogy a Kubernetes ütemező hogyan korlátozza az ezeken a csomópontokon futtatható hüvelyek elérését.

Az alábbi példában hozzon létre egy GPU-alapú *standard nc6* , amely a VM-méretet használja. Ezeket a virtuális gépeket az NVIDIA Tesla K80 kártya működteti. A rendelkezésre álló virtuálisgép-méretekkel kapcsolatos információkért lásd: [a Linux rendszerű virtuális gépek méretei az Azure-ban][vm-sizes].

Hozzon létre egy csomópont-készletet az az [AK Node Pool Add][az-aks-nodepool-add] paranccsal. Ezúttal adja meg a *gpunodepool*nevet, és használja a `--node-vm-size` paramétert a *standard nc6* méretének megadásához:

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
    "orchestratorVersion": "1.13.9",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.9",
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

Most már két csomópont-készlet található a fürtben – az alapértelmezett, eredetileg létrehozott Node-készletet és a GPU-alapú csomópont-készletet. A fürt csomópontjainak megtekintéséhez használja a [kubectl Get Nodes][kubectl-get] parancsot. A következő példa kimenete egy csomópontot mutat be az egyes csomópont-készletekben:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.9
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.9
```

A Kubernetes-ütemező használatával megtilthatja, hogy milyen számítási feladatok futhatnak a csomópontokon.

* A rendszer egy olyan csomópontra alkalmazza a megromlást, amely azt jelzi, hogy csak bizonyos hüvelyek ütemezhetők.
* A rendszer egy olyan Pod-ra alkalmazza a betartást, amely lehetővé teszi a csomópontok megromlását.

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

Ha Azure Resource Manager sablont használ az erőforrások létrehozásához és kezeléséhez, általában frissíti a sablon beállításait, és újból üzembe helyezi az erőforrás frissítéséhez. Az AK-ban található Node-készletekkel a kezdeti csomópont-készlet profilja nem frissíthető az AK-fürt létrehozása után. Ez azt jelenti, hogy nem frissítheti a meglévő Resource Manager-sablonokat, módosíthatja a csomópont-készleteket, és újból üzembe helyezheti azokat. Ehelyett létre kell hoznia egy különálló Resource Manager-sablont, amely csak az ügynök készleteit frissíti egy meglévő AK-fürthöz.

Hozzon létre egy sablont `aks-agentpools.json` , például illessze be a következő példát a jegyzékbe. A példában szereplő sablon a következő beállításokat konfigurálja:

* Frissíti a *myagentpool* nevű *Linux* -ügynök készletét három csomópont futtatásához.
* Beállítja a csomópont-készlet csomópontjait a Kubernetes-verzió *1.13.9*futtatásához.
* Meghatározza a csomópont méretét *Standard_DS2_v2*.

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
            "orchestratorVersion": "1.13.9"
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

> [!NOTE]
> Az előzetes verzió ideje alatt a szolgáltatás a virtuális gépek üzembe helyezésével ütköző lehetséges Load Balancer-szabályok miatt korlátozva van a *standard Load BALANCER SKU-ban (előzetes verzió)* . Az előzetes verzióban az *Alapszintű Load BALANCER SKU* -t használja, ha csomóponthoz kell hozzárendelni egy nyilvános IP-címet.

Az AK-csomópontok nem igénylik a saját nyilvános IP-címeiket a kommunikációhoz. Bizonyos esetekben azonban előfordulhat, hogy a csomópontok csomópontjain a saját nyilvános IP-címeiket kell megadni. Egy példa a játékra, ahol a konzolnak közvetlen csatlakoznia kell egy felhőalapú virtuális géphez a ugrások minimalizálásához. Ezt úgy érheti el, ha regisztrál egy külön előzetes verziójú szolgáltatásra, a Node nyilvános IP-címére (előzetes verzió).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

A sikeres regisztráció után helyezzen üzembe egy Azure Resource Manager sablont a [fenti](#manage-node-pools-using-a-resource-manager-template) utasítások alapján, és adja hozzá a következő Boolean Value tulajdonságot a agentPoolProfiles: "enableNodePublicIP". Állítsa be úgy `true` , hogy az alapértelmezett értékre legyen `false` állítva, ha nincs megadva. Ez egy csak létrehozási idő tulajdonság, és legalább 2019-06-01-es API-verziót igényel. Ez a Linux-és a Windows-csomópont-készletekre is alkalmazható.

```
"agentPoolProfiles":[  
    {  
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]",
      "enableNodePublicIP":true
    }
```

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

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozhat létre és kezelhet több Node-készletet egy AK-fürtben. További információ a hüvelyek csomópontok közötti szabályozásáról: [ajánlott eljárások a speciális Scheduler-funkciókhoz az AK-ban][operator-best-practices-advanced-scheduler].

A Windows Server Container Node-készletek létrehozásához és használatához lásd: [Windows Server-tároló létrehozása az AK-ban][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
