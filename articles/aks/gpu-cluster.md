---
title: GPU-k használata az Azure Kubernetes szolgáltatásban (AKS)
description: Megtudhatja, hogyan használhatja a GPU-kat nagy teljesítményű számítási vagy nagy teljesítményű számítási feladatokhoz az Azure Kubernetes szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 03/27/2020
ms.openlocfilehash: 242fefb3b153d11e23d66f26049d0b68c0a4bf4a
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383990"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>GPU-k használata nagy számítási feladatokhoz az Azure Kubernetes szolgáltatás (AKS) szolgáltatásban

Grafikus feldolgozási egységek (GPU-k) gyakran használják a számítási nagy munkaterhelések, például a grafikus és vizualizációs számítási feladatok. Az AKS támogatja a GPU-kompatibilis csomópontkészletek létrehozását a kubernetes-i nagy számítási nagy számítási feladatok futtatásához. A rendelkezésre álló GPU-kompatibilis virtuális gépekről további információt az [Azure-ban optimalizált VIRTUÁLISGÉPEK-méretek című témakörben talál.][gpu-skus] AKS-csomópontok esetén azt javasoljuk, hogy a *Standard_NC6*minimális mérete.

> [!NOTE]
> A GPU-kompatibilis virtuális gépek olyan speciális hardvereket tartalmaznak, amelyek magasabb díjszabási és régióelérhetőségi szintűek. További információt az [árképzési][azure-pricing] eszköz és a [régió elérhetősége című témakörben talál.][azure-availability]

Jelenleg a GPU-kompatibilis csomópontkészletek használata csak Linux-csomópontkészletekhez érhető el.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürt gpusokat támogató csomópontokkal. Az AKS-fürtnek kubernetes 1.10-es vagy újabb futtatást kell futtatnia. Ha olyan AKS-fürtre van szüksége, amely megfelel ezeknek a követelményeknek, tekintse meg a cikk első részét [az AKS-fürt létrehozásához.](#create-an-aks-cluster)

Az Azure CLI 2.0.64-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Ha olyan AKS-fürtre van szüksége, amely megfelel a minimális követelményeknek (GPU-kompatibilis csomópont és Kubernetes 1.10-es vagy újabb verziója), hajtsa végre az alábbi lépéseket. Ha már rendelkezik olyan AKS-fürttel, amely megfelel ezeknek a követelményeknek, [ugorjon a következő szakaszra.](#confirm-that-gpus-are-schedulable)

Először hozzon létre egy erőforráscsoportot a fürt számára az [az csoport létrehozása][az-group-create] paranccsal. A következő példa létrehoz egy sajka *erőforráscsoport* nevet az *eastus* régióban:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy AKS-fürtöt az [aks create][az-aks-create] paranccsal. A következő példa egyetlen csomópontméretű `Standard_NC6`fürtöt hoz létre:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Az AKS-fürt hitelesítő adatainak beszereznie az [aks get-credentials][az-aks-get-credentials] paranccsal:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-drivers"></a>NVIDIA-illesztőprogramok telepítése

Mielőtt a csomópontokban lévő GPU-k használhatók lennének, telepítenie kell egy DaemonSet-et az NVIDIA eszköz beépülő modulhoz. Ez a DaemonSet minden csomóponton futtat egy podot, hogy biztosítsa a GPU-k szükséges illesztőprogramjait.

Először hozzon létre egy névteret a [kubectl create névtér][kubectl-create] paranccsal, például *gpu-resources:*

```console
kubectl create namespace gpu-resources
```

Hozzon létre egy fájlt nevű *nvidia-device-plugin-ds.yaml* és illessze be a következő YAML jegyzékfájl. Ez a jegyzékfájl a [Kubernetes projekt NVIDIA eszközbővítményének][nvidia-github]részeként érhető el.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Most használja a [kubectl alkalmazni][kubectl-apply] parancsot, hogy megteremtse a DaemonSet és erősítse meg az NVIDIA eszköz plugin sikeresen létre, amint az a következő példa kimenet:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>Annak ellenőrzése, hogy a GPU-k schedulable

Az AKS-fürt létrehozása, győződjön meg arról, hogy gpu-k schedulable a Kubernetes. Először sorolja fel a csomópontokat a fürtben a [kubectl get csomópontok][kubectl-get] paranccsal:

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Most használja a [kubectl írja le a csomópont parancsot][kubectl-describe] annak megerősítéséhez, hogy a GPU-k schedulable. A *Kapacitás* szakaszban a GPU-nak a . `nvidia.com/gpu:  1`

A következő sűrített példa azt mutatja, hogy a GPU elérhető a csomópont nevű *aks-nodepool1-18821093-0*:

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>GPU-kompatibilis számítási feladatok futtatása

A GPU működés közbeni megtekintéséhez ütemezzen egy GPU-kompatibilis számítási feladatot a megfelelő erőforrás-kérelemmel. Ebben a példában futtassunk egy [Tensorflow-feladatot](https://www.tensorflow.org/) az [MNIST adatkészleten.](http://yann.lecun.com/exdb/mnist/)

Hozzon létre egy *mintát-tf-mnist-demo.yaml* nevű fájlt, és illessze be a következő YAML-jegyzékfájlt. A következő feladatjegyzék a `nvidia.com/gpu: 1`következő erőforráskorlátot tartalmazza:

> [!NOTE]
> Ha verzióeltérési hibaüzenetet kap az illesztőprogramok behívásakor, például a CUDA illesztőprogram verziója nem elegendő a CUDA futásidejű verziójához, tekintse át az NVIDIA illesztőprogram mátrixkompatibilitási táblázatát -[https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

A feladat futtatásához használja a [kubectl apply parancsot.][kubectl-apply] Ez a parancs elemzi a jegyzékfájl, és létrehozza a definiált Kubernetes objektumok:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>A GPU-kompatibilis munkaterhelés állapotának és kimenetének megtekintése

Figyelje a feladat előrehaladását a [kubectl][kubectl-get] `--watch` get jobs paranccsal az argumentummal. Eltarthat néhány percig, amíg először lekéri a lemezképet, és feldolgozza az adatkészletet. Ha a *KIEGÉSZÍTÉSEK* *oszlopban 1/1*látható, a feladat sikeresen befejeződött. Kilépés `kubetctl --watch` a parancsból a *Ctrl-C billentyűkombinációval:*

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

A GPU-kompatibilis számítási feladatok kimenetének megtekintéséhez először a [kubectl get pods][kubectl-get] paranccsal kapja meg a podok nevét:

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Most használja a [kubectl naplók parancsot][kubectl-logs] a pod naplók megtekintéséhez. A következő példa pod naplók megerősítik, hogy `Tesla K80`a megfelelő GPU-eszköz fedezte fel, . Adja meg a saját pod nevét:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikkben létrehozott társított Kubernetes-objektumok eltávolításához használja a [kubectl delete feladatparancsot][kubectl delete] az alábbiak szerint:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>További lépések

Az Apache Spark-feladatok futtatásáról az [Apache Spark-feladatok futtatása az AKS-en.][aks-spark]

A Kubernetes gépi tanulási (ML) számítási feladatai futtatásáról a [Kubeflow Labs című témakörben][kubeflow-labs]talál további információt.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
