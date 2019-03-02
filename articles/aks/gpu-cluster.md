---
title: A GPU-k használata az Azure Kubernetes Service (AKS)
description: A GPU-k használata a nagy teljesítményű számítási és a magas grafikai igényű számítási feladatok Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: b80eee00f875ba9d7580acfc3340dfa2cec33fbe
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240949"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>A GPU-k használata a nagy számítási igényű számítási feladatokhoz az Azure Kubernetes Service (AKS)

Compute-igényes számítási feladatokhoz, például a grafikus és megjelenítési számítási feladatok gyakran használják a grafikus feldolgozóegység (GPU). Az AKS GPU-kompatibilis csomópontkészleteit a nagy számítási igényű számítási feladatok futtatása a Kubernetesben való létrehozását támogatja. Az elérhető GPU-kompatibilis virtuális gépek további információkért lásd: [GPU-optimalizált Azure-beli Virtuálisgép-méretek][gpu-skus]. Az AKS-csomópontok, javasoljuk, hogy a minimális méret *Standard_NC6*.

> [!NOTE]
> GPU-kompatibilis virtuális gépek speciális hardvereket, amely a készlet erejéig magasabb díjszabással és a régiót tartalmazhat. További információkért lásd: a [díjszabás] [ azure-pricing] eszköz és [régiók rendelkezésre állása][azure-availability].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy gpu-kat támogató csomópontok egy meglévő AKS-fürtöt. 1.10 vagy újabb, az AKS-fürt Kubernetes rendszernek kell futnia. Ha egy AKS-fürtöt, amely megfelel ezeknek a követelményeknek, tekintse meg a jelen cikk első szakaszában [AKS-fürt létrehozása](#create-an-aks-cluster).

Emellett az Azure CLI 2.0.59 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Ha egy AKS-fürtre van szüksége, amely megfelel a minimális követelményeknek (GPU-kompatibilis csomópont és a Kubernetes 1.10 vagy újabb verzió), kövesse az alábbi lépéseket. Ha már rendelkezik egy AKS-fürtöt, amely megfelel ezeknek a követelményeknek, [ugorjon a következő szakaszra](#confirm-that-gpus-are-schedulable).

Először hozzon létre egy erőforráscsoportot a fürt használata a [az csoport létrehozása] [ az-group-create] parancsot. Az alábbi példa létrehoz egy erőforráscsoport-nevet *myResourceGroup* a a *eastus* régió:

```azurecli
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy AKS fürt a [az aks létrehozása] [ az-aks-create] parancsot. Az alábbi példa egy fürtöt hoz létre egy egycsomópontos méretű `Standard_NC6`, és a Kubernetes 1.11.7 verziója fut:

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1 \
    --kubernetes-version 1.11.7
```

Az AKS fürt használatára vonatkozó hitelesítő adatainak lekérése a [az aks get-credentials] [ az-aks-get-credentials] parancsot:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="confirm-that-gpus-are-schedulable"></a>Győződjön meg róla, hogy a GPU-k ütemezhető

Az AKS-fürt létrehozása, és ellenőrizze, hogy a GPU-k ütemezhető a Kubernetesben. Először listázza a fürt használatával a csomópontok a [kubectl get csomópontok] [ kubectl-get] parancsot:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   6m    v1.11.7
```

Mostantól használhatja a [kubectl ismertetik csomópont] [ kubectl-describe] parancs használatával győződjön meg róla, hogy a GPU-k ütemezhető. Alatt a *kapacitás* szakaszban, a grafikus Processzor szerepelnie kell `nvidia.com/gpu:  1`. Ha nem látja a GPU-k, tekintse meg a [hibaelhárítása GPU rendelkezésre állási](#troubleshoot-gpu-availability) szakaszban.

A következő sűrített példához jeleníti meg, hogy egy GPU érhető el a csomóponton nevű *aks-nodepool1-18821093-0*:

```
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 cpu:                6
 ephemeral-storage:  30428648Ki
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             57713780Ki
 nvidia.com/gpu:     1
 pods:               110
Allocatable:
 cpu:                5916m
 ephemeral-storage:  28043041951
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             52368500Ki
 nvidia.com/gpu:     1
 pods:               110
System Info:
 Machine ID:                 9148b74152374d049a68436ac59ee7c7
 System UUID:                D599728C-96F3-B941-BC79-E0B70453609C
 Boot ID:                    a2a6dbc3-6090-4f54-a2b7-7b4a209dffaf
 Kernel Version:             4.15.0-1037-azure
 OS Image:                   Ubuntu 16.04.5 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.11.7
 Kube-Proxy Version:         v1.11.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  gpu-resources              nvidia-device-plugin-97zfc               0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m4s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>GPU-kompatibilis munkaterhelések futtatása

A grafikus Processzor működés közben látni, hogy a megfelelő erőforrás-kérelemmel GPU-kompatibilis munkaterhelések ütemezése. Ebben a példában futtassunk egy [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) feladat ellen a [MNIST adatkészlet](http://yann.lecun.com/exdb/mnist/).

Hozzon létre egy fájlt *minták – tf-mnist-demo.yaml* , és illessze be a következő YAML-jegyzékfájlt. A következő feladat jegyzékfájlt tartalmaz erőforrás legfeljebb `nvidia.com/gpu: 1`:

> [!NOTE]
> Ha egy verzióeltérési hiba hívásakor, illesztőprogramok, például a CUDA-illesztőprogram verziója nem elegendő a CUDA verze modulu runtime, tekintse át az nVidia illesztőprogram mátrix kompatibilitási diagramja – [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

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

Használja a [a kubectl a alkalmazni] [ kubectl-apply] parancsot futtatja a feladatot. Ez a parancs elemzi jegyzékfájlt, és a meghatározott Kubernetes-objektumokat hoz létre:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Állapot és a GPU-kompatibilis számítási feladat kimenetének megtekintése

A feladat előrehaladásának figyeléséhez a [kubectl get feladatok] [ kubectl-get] parancsot a `--watch` argumentum. Előfordulhat, hogy néhány percet vesz igénybe első lekéréses a lemezkép és az adatkészlet feldolgozása. Ha a *BEFEJEZÉSEK* oszlopban látható *1/1*, a feladat sikeresen befejeződött:

```
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Tekintse meg a GPU-kompatibilis számítási feladat kimenetét, először kérje le a podok a nevét a [kubectl get pods] [ kubectl-get] parancsot:

```
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1     Completed   0          3m
```

Mostantól használhatja a [kubectl naplók] [ kubectl-logs] parancsot a pod-naplók megtekintéséhez. A következő példa pod naplók győződjön meg arról, hogy a megfelelő GPU a felderítésük, `Tesla K80`. A saját pod-nevet kell adnia:

```
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-02-28 23:47:34.749013: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-02-28 23:47:34.879877: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 3130:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2019-02-28 23:47:34.879915: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 3130:00:00.0, compute capability: 3.7)
2019-02-28 23:47:39.492532: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
Accuracy at step 40: 0.8685
Accuracy at step 50: 0.8864
Accuracy at step 60: 0.901
Accuracy at step 70: 0.905
Accuracy at step 80: 0.9103
Accuracy at step 90: 0.9126
Adding run metadata for 99
Accuracy at step 100: 0.9176
Accuracy at step 110: 0.9149
Accuracy at step 120: 0.9187
Accuracy at step 130: 0.9253
Accuracy at step 140: 0.9252
Accuracy at step 150: 0.9266
Accuracy at step 160: 0.9255
Accuracy at step 170: 0.9267
Accuracy at step 180: 0.9257
Accuracy at step 190: 0.9309
Adding run metadata for 199
Accuracy at step 200: 0.9272
Accuracy at step 210: 0.9321
Accuracy at step 220: 0.9343
Accuracy at step 230: 0.9388
Accuracy at step 240: 0.9408
Accuracy at step 250: 0.9394
Accuracy at step 260: 0.9412
Accuracy at step 270: 0.9422
Accuracy at step 280: 0.9436
Accuracy at step 290: 0.9411
Adding run metadata for 299
Accuracy at step 300: 0.9426
Accuracy at step 310: 0.9466
Accuracy at step 320: 0.9458
Accuracy at step 330: 0.9407
Accuracy at step 340: 0.9445
Accuracy at step 350: 0.9486
Accuracy at step 360: 0.9475
Accuracy at step 370: 0.948
Accuracy at step 380: 0.9516
Accuracy at step 390: 0.9534
Adding run metadata for 399
Accuracy at step 400: 0.9501
Accuracy at step 410: 0.9552
Accuracy at step 420: 0.9535
Accuracy at step 430: 0.9545
Accuracy at step 440: 0.9533
Accuracy at step 450: 0.9526
Accuracy at step 460: 0.9566
Accuracy at step 470: 0.9547
Accuracy at step 480: 0.9548
Accuracy at step 490: 0.9545
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kapcsolódó Kubernetes-objektumokat a jelen cikkben létrehozott eltávolításához használja a [kubectl törlési feladat] [ kubectl delete] paranccsal a következőképpen:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="troubleshoot-gpu-availability"></a>GPU-rendelkezésre állási hibáinak elhárítása

Ha nem látja a GPU-k, hogy a csomópontokon elérhető, szükség lehet egy DaemonSet nVidia eszköz beépülő modul telepítése. A DaemonSet podot futtat minden csomóponthoz adja meg a szükséges illesztőprogramok a GPU-kkal.

Először is hozzon létre egy névteret a [kubectl névtér létrehozása] [ kubectl-create] parancsot, mint például *gpu-erőforrások*:

```console
kubectl create namespace gpu-resources
```

Hozzon létre egy fájlt *nvidia-device-beépülő modul – ds.yaml* , és illessze be a következő YAML-jegyzékfájlt. Frissítés a `image: nvidia/k8s-device-plugin:1.11` félúton le a jegyzékfájl megfelelően a Kubernetes-verziót. Például, ha az AKS-fürt Kubernetes 1.12 verziója fut, frissítse a címke `image: nvidia/k8s-device-plugin:1.12`.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: gpu-resources
spec:
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
      containers:
      - image: nvidia/k8s-device-plugin:1.11 # Update this tag to match your Kubernetes version
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
      nodeSelector:
        beta.kubernetes.io/os: linux
        accelerator: nvidia
```

Mostantól használhatja a [a kubectl a alkalmazni] [ kubectl-apply] parancsot a DaemonSet létrehozásához:

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

Futtassa a [kubectl ismertetik csomópont] [ kubectl-describe] parancsot ismét ellenőrizze, hogy a GPU most már elérhető a csomóponton.

## <a name="next-steps"></a>További lépések

Az Apache Spark-feladatok futtatásához lásd: [futtassa az Apache Spark-feladatok az aks-en][aks-spark].

Machine learning (gépi tanulás) számítási feladatok futtatása a kubernetes használatával kapcsolatos további információkért lásd: [Kubeflow Labs][kubeflow-labs].

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

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
