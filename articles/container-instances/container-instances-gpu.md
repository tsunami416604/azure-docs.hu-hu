---
title: GPU-kompatibilis az Azure container instances üzembe helyezése
description: Megtudhatja, hogyan futtathatók a GPU-erőforrásokat az Azure container instances üzembe helyezéséhez.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/29/2018
ms.author: danlep
ms.openlocfilehash: d0278a58bfad6f2a4a964ef29fbff8a111b475b5
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856035"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>GPU-erőforrásokat használó tárolópéldányok üzembe helyezése

Bizonyos nagy számítási igényű számítási feladatok futtatásához az Azure Container Instances üzembe helyezése a tárolócsoportok az *GPU-erőforrások*. A container Instances szolgáltatásban tárolókhoz kapcsolódó számítási feladatok, például a CUDA fut, és a deep learning-alkalmazások férhetnek hozzá egy vagy több NVIDIA Tesla gpu-kat.

Ahogyan azt ebben a cikkben, ha telepít egy tárolócsoport használatával adhat hozzá GPU-erőforrások egy [YAML-fájl](container-instances-multi-container-yaml.md) vagy [Resource Manager-sablon](container-instances-multi-container-group.md).

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és néhány [korlátozások érvényesek a](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

Előzetes verzióban elérhető az alábbi korlátozások érvényesek a tárolócsoportok az GPU-erőforrások használata során. 

**Támogatott régiók**:

* USA keleti RÉGIÓJA (USA keleti régiója)
* USA nyugati RÉGIÓJA 2 (westus2)
* USA déli középső Régiója (southcentralus)
* Nyugat-Európa (westeurope)
* Észak-Európa (northeurope)
* Kelet-Ázsia (eastasia)
* Közép-India (centralindia)

Támogatási idővel további régiókban megjelenik.

**Támogatott operációsrendszer-típus**: csak Linux

**További korlátozások**: GPU-erőforrások nem használható, egy tárolócsoport történő üzembe helyezésekor a [virtuális hálózat](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>GPU-erőforrásokról

### <a name="count-and-sku"></a>Száma és a Termékváltozat

Tárolópéldány gpu-k használatához adja meg egy *GPU erőforrás* a következő információkat:

* **Száma** -gpu-k száma: **1**, **2**, vagy **4**.
* **Termékváltozat** – a GPU-Termékváltozat: **K80**, **P100**, vagy **V100**. Minden egyes Termékváltozat az NVIDIA Tesla GPU egy leképezi a következő Azure GPU-kompatibilis Virtuálisgép-család:

  | SKU | Virtuálisgép-család |
  | --- | --- |
  | K80 | [HÁLÓZATI VEZÉRLŐ](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [Az NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

### <a name="cpu-and-memory"></a>Processzor és memória

GPU-erőforrások üzembe helyezésekor, állítsa be a terhelés, akár a maximális értékeket az alábbi táblázatban látható megfelelő CPU és memória-erőforrások. Ezek az értékek jelenleg nagyobb, mint az erőforrások GPU nélkül a container Instances szolgáltatásban a Processzor- és memóriakorlátokkal.  

| GPU-TERMÉKVÁLTOZAT | GPU-száma | CPU |  Memória (GB) |
| --- | --- | --- | --- |
| K80 | 1 | 6 | 56 |
| K80 | 2 | 12 | 112 |
| K80 | 4 | 24 | 224 |
| P100 | 1 | 6 | 112 |
| P100 | 2 | 12 | 224 |
| P100 | 4 | 24 | 448 |
| V100 | 1 | 6 | 112 |
| V100 | 2 | 12 | 224 |
| V100 | 4 | 24 | 448 |

### <a name="things-to-know"></a>Tudnivaló

* **Üzembe helyezési idő** – akár egy grafikus Processzor-erőforrásokat tartalmazó tárolócsoport létrehozása vesz igénybe **8 – 10 percet**. A kiépítése és a egy GPU VM konfigurálása az Azure-ban több idő okozza. 

* **Díjszabás** - tárolócsoportok GPU-erőforrások nélkül, az Azure számlák keresztül használt erőforrások hasonló a *időtartama* egy tárolócsoport GPU-erőforrásokkal. Az időtartam kiszámítása az idő az első tároló rendszerképének lekérni, amíg a tárolócsoport leállításáig számítjuk. Nem tartalmazza a tárolócsoport üzembe is helyezi.

  A tárolócsoportok a GPU-erőforrásokkal, mint a tárolócsoportok nélkül magasabb díjszabás lesz. Lásd: [díjszabás](https://azure.microsoft.com/pricing/details/container-instances/).

* **CUDA illesztőprogramok** – Container instances a GPU-erőforrásokkal előre kiépítve NVIDIA CUDA-illesztőprogramok és tároló-futtatókörnyezet, így használhatja a tárolórendszerképek fejlesztette ki CUDA számítási feladatokhoz.

## <a name="yaml-example"></a>YAML-példa

Másolja a következő yaml-kódot egy új fájlt *gpu üzembe helyezése aci.yaml*, majd mentse a fájlt. A YAML létrehoz egy tárolócsoport nevű *gpucontainergroup* adnak meg egy tárolópéldányt egy K80 gpu-val. A példány egy minta CUDA vektor emellett alkalmazást futtat. Az erőforrás-kérelmek elegendőek a számítási feladat futtatásához.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
    osType: Linux
  restartPolicy: OnFailure
```

Üzembe helyezése az a tárolócsoportot a [az tároló létrehozása] [ az-container-create] a YAML-fájl nevét, a parancsot a `--file` paraméter. Például adjon meg egy erőforráscsoportot és a egy helyet a tárolócsoport nevét kell *eastus* , amely támogatja a grafikus Processzor-erőforrásokat.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Az üzembe helyezés több percet is igénybe vehet. Ezután a tároló elindul, és futtatja a CUDA vektor összeadási művelet. Futtassa a [az tárolónaplók] [ az-container-logs] parancsot a kimenet megtekintéséhez:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Kimenet:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Resource Manager-sablon példa

Először hozzon létre egy fájlt `gpudeploy.json`, majd másolja bele a következő JSON-fájllal. Ebben a példában egy tárolópéldányt egy V100 GPU, amelyen üzembe helyezi egy [TensorFlow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) elleni betanítási feladat a [MNIST adatkészlet](http://yann.lecun.com/exdb/mnist/). Az erőforrás-kérelmek elegendőek a számítási feladat futtatásához.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Helyezze üzembe a sablont a [az csoport központi telepítésének létrehozása] [ az-group-deployment-create] parancsot. Adjon meg egy erőforráscsoportot, mint például az egy régióban létrehozott nevét kell *eastus* , amely támogatja a grafikus Processzor-erőforrásokat.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

Az üzembe helyezés több percet is igénybe vehet. A tárolót, majd elindul, és a TensorFlow-feladatot futtat. Futtassa a [az tárolónaplók] [ az-container-logs] parancsot a kimenet megtekintéséhez:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Kimenet:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
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
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

GPU-erőforrások használata költséges lehet, mert győződjön meg arról, hogy a tárolók váratlanul ne futtassa hosszú ideig. Figyelheti a tárolókat az Azure Portalon, vagy egy tárolócsoportot állapotának ellenőrzéséhez a [az container show] [ az-container-show] parancsot. Példa:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Ha elkészült a container instances használata létrehozott, törölheti őket az alábbi parancsokkal:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>További lépések

* További információ a központi telepítés egy tároló csoport segítségével egy [YAML-fájl](container-instances-multi-container-yaml.md) vagy [Resource Manager-sablon](container-instances-multi-container-group.md).
* Tudjon meg többet [GPU-optimalizált Virtuálisgép-méretek](../virtual-machines/linux/sizes-gpu.md) az Azure-ban.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create