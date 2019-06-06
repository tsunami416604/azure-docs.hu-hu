---
title: Több csomópont készletet az Azure Kubernetes Service (AKS) használatához
description: Ismerje meg, hogyan hozhat létre és kezelhet egy fürtöt az Azure Kubernetes Service (AKS) több csomópontkészletei
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: iainfou
ms.openlocfilehash: a295dfa1f7f2c58b3e45036212434837ac4bfb4d
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475448"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Előzetes verzió – létrehozása és az Azure Kubernetes Service (AKS) fürt több csomópontja készletek kezelése

Az Azure Kubernetes Service (aks) Szolgáltatásban, ugyanazt a konfigurációt csomópontján vannak besorolva *csomópontkészletek*. Ezek csomópontkészletek tartalmaznak az alapul szolgáló virtuális gépek, amelyek az alkalmazások futtatásához. A csomópontok és a méretét (Termékváltozat) kezdeti száma definiált létrehozott egy AKS-fürtöt, amely létrehoz egy *csomópontkészlet alapértelmezett*. Az alkalmazásokat, különböző számítási és tárolási igényeinek kielégítése érdekében létrehozhat további csomópontkészletek. Például ezek további csomópont készletek segítségével gpu-k biztosítanak a nagy számítási igényű alkalmazásokhoz, vagy nagy teljesítményű SSD-tárterülettel hozzáférni.

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet az AKS-fürtben több csomóponton készletet. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

> [!IMPORTANT]
> Az AKS előzetes verziójú funkciók önkiszolgáló, a rendszer. A biztosított gyűjthet visszajelzéseket és a hibák kapcsolódóan a Közösség részéről. Előzetes verzióban elérhető ezeket a funkciókat nem üzemi használat céljára. Nyilvános előzetes verzióban érhető el "ajánlott beavatkozást" támogatás keretében tartoznak. Az AKS technikai támogatási csapat segítségét munkaidőben csendes-óceáni időzóna (PST) csak alatt érhető el. További információkért tekintse meg a következő cikkek támogatja:
>
> * [Az AKS támogatási házirendek][aks-support-policies]
> * [Az Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.61 verziójára van szükség, vagy később telepített és konfigurált. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Az aks előzetes CLI-bővítmény telepítése

A CLI-parancsok hozhat létre és kezelhet több csomópont készletek érhetők el a *aks előzetes* CLI-bővítményt. Telepítse a *aks előzetes* Azure CLI-bővítmény használata a [az bővítmény hozzáadása] [ az-extension-add] parancsot, az alábbi példában látható módon:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Ha korábban telepítette a *aks előzetes* bővítmény, az elérhető frissítések telepítése használatával a `az extension update --name aks-preview` parancsot.

### <a name="register-multiple-node-pool-feature-provider"></a>Több csomópont készlethez a szolgáltatás-szolgáltató regisztrálása

Több csomópont készletet használó egy AKS-fürtöt létrehozni, először engedélyeznie kell az előfizetésén kát funkció. A készlet több csomópontos fürtök egy virtuálisgép-méretezési csoportot (VMSS) használatával kezelheti, telepítését és konfigurálását, a Kubernetes-csomópontokon. Regisztrálja a *MultiAgentpoolPreview* és *VMSSPreview* funkció jelzők segítségével a [az a funkció regisztrálása] [ az-feature-register] parancs, ahogyan a Példa:

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Bármely AKS-fürtöt hoz létre, miután sikeresen regisztrálta a *MultiAgentpoolPreview* előzetes fürt felület használja. Továbbra is rendszeres, teljes mértékben támogatott fürtök létrehozását, ne engedélyezze az előzetes verziójú funkciók az éles üzemű előfizetéseket. Előzetes verziójú funkciók teszteléséhez használja egy külön teszt- vagy Azure-előfizetés.

Az állapot megjelenítése néhány percet vesz igénybe *regisztrált*. A regisztrációs állapot használatával ellenőrizheti a [az szolgáltatáslistát] [ az-feature-list] parancsot:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a regisztrációját a *Microsoft.ContainerService* erőforrás-szolgáltató használatával a [az provider register] [ az-provider-register] parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Korlátozások

Az alábbi korlátozások érvényesek a felhőszolgáltatásnak, amely támogatja több csomópontkészletek AKS-fürtök kezelése:

* Több csomópont-készletekre csak érhető el, miután sikeresen regisztrálta létrehozott fürtök esetében a *MultiAgentpoolPreview* és *VMSSPreview* funkciók az előfizetéshez. Nem adhat hozzá, és csomópont-készletek kezelése a meglévő AKS-fürt létrehozása előtt ezeket a funkciókat sikeresen regisztrálva.
* Az első node-készlet nem törölhető.
* A HTTP-kérelem útválasztási bővítmény nem használható.
* Nem hozzáadása/frissítése/törlése csomópontkészletek legtöbb művelet az egy meglévő Resource Manager-sablon használatával. Ehelyett [egy külön Resource Manager-sablonnal](#manage-node-pools-using-a-resource-manager-template) csomópontkészletek az AKS-fürtben módosíthatja.
* Csak akkor használható a fürt méretező (jelenleg előzetes verzióban érhető el az aks-ben).

Bár ez a funkció előzetes verzióban érhető el, a következő további korlátozások vonatkoznak:

* Az AKS-fürtöt egy legfeljebb nyolc csomópontkészletek rendelkezhet.
* Az AKS-fürt legfeljebb 400 csomópontok között nyolc csomópont készletekben.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Első lépésként hozzon létre egy AKS-fürt egyetlen csomópont készlethez. Az alábbi példában a [az csoport létrehozása] [ az-group-create] parancs használatával hozzon létre egy erőforráscsoportot *myResourceGroup* a a *eastus* régió. AKS-fürt nevű *myAKSCluster* majd használatával jön létre a [az aks létrehozása] [ az-aks-create] parancsot. A *– kubernetes-verzió* , *1.12.6* frissítése egy következő lépésben csomópontkészletek megjelenítésére használható. Bármely [Kubernetes-verzió támogatott][supported-versions].

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
    --kubernetes-version 1.12.6
```

A fürt létrehozása néhány percet vesz igénybe.

Ha a fürt készen áll, használja a [az aks get-credentials] [ az-aks-get-credentials] parancsot a fürt hitelesítő adatainak lekérése való használatra `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Csomópont készlet hozzáadása

Az előző lépésben létrehozott fürt egyetlen csomópont készlethez rendelkezik. Adjunk hozzá egy második csomópont készlet használatával a [hozzáadása az aks-csomópontkészlet] [ az-aks-nodepool-add] parancsot. A következő példában létrehozunk egy csomópont készletet *mynodepool* futtató *3* csomópontok:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

A csomópontkészletek állapotának megtekintéséhez használja a [az aks csomópontlista készlet] [ az-aks-nodepool-list] parancsot, majd adja meg az erőforrás-csoport és a fürt nevére:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

Az alábbi példa kimenetében látható, amely *mynodepool* három csomóponttal, a csomópont készlet sikeresen létrejött. Az AKS-fürtöt az előző lépésben, egy alapértelmezett létrehozásának *nodepool1* csomópont számával együtt lett létrehozva *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

> [!TIP]
> Ha nincs *OrchestratorVersion* vagy *VmSize* van megadva, amikor felvesz egy csomópontkészlet csomópontjai az AKS-fürtöt az alapértelmezett értéket alapján hozzák létre. Ebben a példában a Kubernetes-verziót, amely volt *1.12.6* és a csomópont méretét *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Csomópontkészletek frissítése

Az első lépésben, az AKS-fürt létrehozásakor egy `--kubernetes-version` , *1.12.6* lett megadva. Frissítsünk a *mynodepool* kubernetes *1.12.7*. Használja a [az aks csomópont készlethez frissítés] [ az-aks-nodepool-upgrade] paranccsal frissíthetünk a csomópont készlethez, a következő példában látható módon:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.12.7 \
    --no-wait
```

A csomópontkészletek újra állapotának listázása a [az aks csomópontlista készlet] [ az-aks-nodepool-list] parancsot. Az alábbi példa azt mutatja, hogy *mynodepool* szerepel a *Upgrading* állapotra *1.12.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

A csomópontok a megadott verzió frissítése néhány percet vesz igénybe.

Ajánlott eljárásként frissítenie kell az AKS-fürt minden csomópont készletek Kubernetes ugyanarra a verzióra. Egyes csomópontkészletek konvertálhatóak lehetővé teszi működés közbeni frissítésének végrehajtása és ütemezése podok között csomópontkészletek fenntartani az alkalmazások hasznos üzemideje.

## <a name="scale-a-node-pool"></a>Csomópontkészletek méretezéséhez

Az alkalmazás számítási feladat igényei, szükség lehet egy csomópont a készletben lévő csomópontok számát. A csomópontok számát felfelé és lefelé skálázhatók.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Méretezheti a csomópont a készletben lévő csomópontok számát, használja a [az aks csomópont készlet méretezési] [ az-aks-nodepool-scale] parancsot. Az alábbi példa bemutatja a csomópontok számát *mynodepool* való *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

A csomópontkészletek újra állapotának listázása a [az aks csomópontlista készlet] [ az-aks-nodepool-list] parancsot. Az alábbi példa azt mutatja, hogy *mynodepool* szerepel a *méretezés* állapot új számával együtt *5* csomópontok:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Scaling              myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

A skálázási művelet végrehajtásához néhány percet vesz igénybe.

## <a name="delete-a-node-pool"></a>Egy csomópont-készlet törlése

Ha már nincs szüksége a készletre, törölje azt, és távolítsa el az alapul szolgáló Virtuálisgép-csomópontok. Csomópontkészletek törléséhez használja a [az aks csomópontkészletek törlése] [ az-aks-nodepool-delete] parancsot, majd adja meg a csomópont-készlet nevét. Az alábbi példával törölhet a *mynoodepool* az előző lépésekben létrehozott:

> [!CAUTION]
> Nem tartoznak az adatvesztés fordulhat elő, ha törli a csomópontkészletek helyreállítási beállítások. Ha más csomópontkészletek podok nem ütemezhetők, ezeknek az alkalmazásoknak nem érhetők el. Ellenőrizze, hogy ne törölje a csomópontkészletek, amikor a használatban lévő alkalmazások nem rendelkezik biztonsági vagy egyéb csomópont-készletek a fürtben futtatására.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Az alábbi példa kimenetében a a [az aks csomópontlista készlet] [ az-aks-nodepool-list] parancs azt mutatja, hogy *mynodepool* szerepel a *törlése* állapota:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Deleting             myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Törli a csomópontok és a csomópont készlethez néhány percet vesz igénybe.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Adja meg a Virtuálisgép-méretének csomópontkészletek

Csomópont-készlet létrehozása a korábbi példákban egy alapértelmezett Virtuálisgép-méretet a csomópontok a fürtben létrehozott használták. Egy gyakori forgatókönyv, hogy a csomópont-készletek létrehozása és a különböző méretű virtuális gépek és a funkciók van. Például előfordulhat, hogy létrehoz egy csomópont-készlet, amely a nagy mennyiségű, a CPU és memória-csomópontokat tartalmaz vagy GPU-támogatással biztosító csomópontkészletek. A következő lépésben, [elkerülésére és tolerations](#schedule-pods-using-taints-and-tolerations) kell tudniuk a Kubernetes-ütemező podok futtatható ezeken a csomópontokon való hozzáférés korlátozása.

A következő példában használó GPU-alapú csomópont-készlet létrehozása a *Standard_NC6* Virtuálisgép-méretet. Ezek a virtuális gépek működteti az NVIDIA Tesla K80 kártyát használják. Elérhető Virtuálisgép-méretekkel kapcsolatos tudnivalókat lásd: [az Azure-ban Linux rendszerű virtuális gépek méretei][vm-sizes].

Hozzon létre egy csomópont készlethez a [az aks csomópontkészletek hozzáadása] [ az-aks-nodepool-add] újra a parancsot. Ebben az esetben adja meg a nevet *gpunodepool*, és használja a `--node-vm-size` paraméterrel adja meg a *Standard_NC6* mérete:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Az alábbi példa kimenetében a a [az aks csomópontlista készlet] [ az-aks-nodepool-list] parancs azt mutatja, hogy *gpunodepool* van *létrehozása* -csomópont a megadott *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.12.6                 100             Linux     Creating             myResourceGroupPools  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Pár percet vesz igénybe a *gpunodepool* sikerült létrehozni.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Ütemezés podok elkerülésére, valamint tolerations használatával

Most már két csomópont-készletek a fürtben – az alapértelmezett csomópontkészletek, először hozza létre, és a GPU-alapú csomópontkészletek. Használja a [kubectl get csomópontok] [ kubectl-get] parancs használatával megtekintheti a csomópontok a fürtben. Az alábbi példa kimenetében látható minden egyes csomópont készlethez egy csomópont:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.12.6
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.12.6
```

A Kubernetes a scheduler használatával elkerülésére, valamint tolerations milyen számítási feladatokat futtathat a csomópontok korlátozhatják.

* A **vágófelülettel** alkalmazott egy csomópont, amely jelzi, hogy csak adott podok ütemezett rajtuk.
* A **toleration** a rendszer ezután alkalmazza, amely lehetővé teszi, hogy podot *lekérés* egy csomópont mellékíz.

A speciális ütemezett Kubernetes-szolgáltatások használatáról további információ: [gyakorlati tanácsok a speciális scheduler funkciók az aks-ben][taints-tolerations]

Ebben a példában a alkalmazni egy mellékíz a GPU-alapú csomópont használatával a [kubectl mellékíz csomópont] [ kubectl-taint] parancsot. Adja meg az előző kimenetéből származó a GPU-alapú csomópont nevét `kubectl get nodes` parancsot. A mellékíz lesz alkalmazva, egy *kulcs: érték* és a egy ütemezési beállítás majd. Az alábbi példában a *termékváltozat = gpu* párosítását, és meghatározza a podok egyébként a *NoSchedule* képessége:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

A következő alapszintű példa YAML-jegyzékfájlt, hogy a Kubernetes-ütemezőt egy NGINX-pod GPU-alapú csomópontján futtatni egy toleration használja. A MNIST adatkészlet Tensorflow-feladatok futtatásához egy megfelelő, de időigényes példa: [nagy számítási igényű számítási feladatokhoz, az aks-en használható gpu-k][gpu-cluster].

Hozzon létre egy fájlt `gpu-toleration.yaml` , és másolja az alábbi példában YAML:

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

A pod ütemezést a `kubectl apply -f gpu-toleration.yaml` parancsot:

```console
kubectl apply -f gpu-toleration.yaml
```

A pod ütemezhet, és az NGINX rendszerképet lekéréses néhány másodpercet vesz igénybe. Használja a [kubectl ismertetik a pod] [ kubectl-describe] parancsot a pod állapotának megtekintéséhez. A következő sűrített példához kimenetet mutat be a *termékváltozat = gpu:NoSchedule* toleration alkalmazza. Az események szakaszban az ütemezőt hozzá van rendelve a pod a *aks-gpunodepool-28993262-vmss000000* GPU-alapú csomópont:

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

Csak a alkalmazni mellékíz rendelkező podok ütemezett csomópontján *gpunodepool*. Bármely más pod szeretné ütemezni a *nodepool1* csomópontkészletek. További csomópont-készleteket hoz létre, ha további elkerülésére is használhat, és milyen podok korlátozása tolerations csomópont erőforrásokat is ütemezhető.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Resource Manager-sablonnal csomópont készletek kezelése

Amikor használhatja az Azure Resource Manager-sablon létrehozása és a felügyelt erőforrások, általában frissítheti a beállításokat a sablont, majd alkalmazza újra az erőforrás frissítése. A csomópontkészletek az aks-ben a kezdeti csomópont-ügynökkészlet profilja nem lehet frissíteni az AKS-fürt létrehozása után. Ez a viselkedés azt jelenti, hogy nem egy meglévő Resource Manager-sablon frissítését, módosítja a csomópont-készleteket, és ismételt üzembe helyezése. Ehelyett hozzon létre egy külön Resource Manager-sablon, amely frissíti a csak az ügynökkészletek meglévő AKS-fürt számára.

Például hozzon létre egy sablont `aks-agentpools.json` , és illessze be a következő példa jegyzékfájlt. Ez a példa a sablon a következő beállítások konfigurálása:

* Frissítések a *Linux* nevű ügynökkészlet *myagentpool* három csomóponttal futtatásához.
* A csomópont készlet futtatása a Kubernetes-verziót állítja be a csomópontok *1.12.8*.
* Meghatározza a csomópont méretét, *Standard_DS2_v2*.

Kell frissíteni, hozzáadása vagy törlése csomópontkészletek szükség szerint módosítsa ezeket az értékeket:

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
            "orchestratorVersion": "1.12.8"
      }
    }
  ]
}
```

Ez a sablon üzembe a [az csoport központi telepítésének létrehozása] [ az-group-deployment-create] parancsot, az alábbi példában látható módon. A meglévő AKS-fürt nevét és helyét kéri:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Attól függően, a csomópont tárolókészlet beállításainak és a műveletek a Resource Manager-sablonnal határoz meg az AKS-fürt frissítése néhány percet igénybe vehet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben létrehozott egy AKS-fürtöt, amely tartalmazza a GPU-alapú csomópontokat. A felesleges költségek csökkentése érdekében előfordulhat, hogy törölni kívánja a *gpunodepool*, vagy az egész AKS-fürtöt.

A GPU-alapú csomópontkészletek törléséhez használja a [az aks nodepool törlése] [ az-aks-nodepool-delete] parancsot az alábbi példában látható módon:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

A fürt, másrészt törléséhez használja a [az csoport törlése] [ az-group-delete] parancsot az AKS erőforráscsoport törléséhez:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozhat létre és kezelhet az AKS-fürtben több csomóponton készletet. Hogyan szabályozható a podok csomópontkészletek között kapcsolatos további információkért lásd: [ajánlott eljárások az aks-ben a scheduler speciális funkciók][operator-best-practices-advanced-scheduler].

Hozzon létre, és a Windows Server-tároló csomópontkészletek használja, lásd: [hozzon létre egy Windows Server-tárolót az aks-ben][aks-windows].

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
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-delete
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
