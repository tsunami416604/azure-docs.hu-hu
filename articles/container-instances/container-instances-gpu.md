---
title: GPU-kompatibilis tárolópéldány telepítése
description: Ismerje meg, hogyan telepítheti az Azure-tárolópéldányokat a nagy számítási igényű tárolóalkalmazások GPU-erőforrások használatával történő futtatásához.
ms.topic: article
ms.date: 02/19/2020
ms.openlocfilehash: 0f1d21c62be5d7ae099faa2c6fcc440829bb451f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525286"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>GPU-erőforrásokat használó tárolópéldányok üzembe helyezése

Bizonyos nagy számítási igényű számítási feladatok futtatásához az Azure Container Instances, telepítse a [tárolócsoportok](container-instances-container-groups.md) *GPU-erőforrásokkal.* A csoport tárolópéldányai hozzáférhetnek egy vagy több NVIDIA Tesla GPU-hoz, miközben tárolószámítási feladatokat futtatnak, például a CUDA-t és a deep learning-alkalmazásokat.

Ez a cikk azt ismerteti, hogyan adhat hozzá GPU-erőforrásokat, amikor [YAML-fájl](container-instances-multi-container-yaml.md) vagy [Erőforrás-kezelő sablon](container-instances-multi-container-group.md)használatával telepít egy tárolócsoportot. GPU-erőforrásokat is megadhat, ha egy tárolópéldányt telepít az Azure Portalhasználatával.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek.](#preview-limitations) Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előnézeti korlátozások

Az előzetes verzióban a következő korlátozások érvényesek a GPU-erőforrások tárolócsoportokban való használatakor. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

A további régiók támogatása idővel hozzáadódik.

**Támogatott operációsrendszer-típusok**: Csak Linux

**További korlátozások:** A GPU-erőforrások nem használhatók, ha tárolócsoportot telepít [egy virtuális hálózatba.](container-instances-vnet.md)

## <a name="about-gpu-resources"></a>GPU-erőforrások – be

> [!IMPORTANT]
> A GPU-erőforrások csak kérésre állnak rendelkezésre. A GPU-erőforrásokhoz való hozzáférés kéréséhez nyújtson be egy [Azure-támogatási kérelmet.][azure-support]

### <a name="count-and-sku"></a>Számlálás és termékváltozat

Gpu-k tárolópéldányban való használatához adjon meg egy *GPU-erőforrást* a következő információkkal:

* **Szám** - A GPU-k száma: **1**, **2**, vagy **4**.
* **SKU** - A GPU Termékváltozat: **K80**, **P100**vagy **V100**. Minden termékváltozat leképezi az NVIDIA Tesla GPU-t a következő Azure GPU-kompatibilis virtuálisgép-családokban:

  | SKU | Virtuálisgép-család |
  | --- | --- |
  | 80.00. | [NC](../virtual-machines/nc-series.md) |
  | 100. p. | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

GPU-erőforrások üzembe helyezésekor állítsa be a számítási feladatnak megfelelő CPU- és memória-erőforrásokat az előző táblázatban látható maximális értékekig. Ezek az értékek jelenleg nagyobbak, mint a GPU-erőforrások nélküli tárolócsoportokban elérhető processzor- és memória-erőforrások.  

### <a name="things-to-know"></a>Tudnivalók

* **Telepítési idő** – A GPU-erőforrásokat tartalmazó tárolócsoport létrehozása akár **8–10 percet is**igénybe vesz. Ez annak köszönhető, hogy a további időt a kiépítése és konfigurálása a GPU virtuális gép az Azure-ban. 

* **Díjszabás** – A GPU-erőforrások nélküli tárolócsoportokhoz hasonlóan az Azure-beli erőforrásokat a GPU-erőforrásokkal rendelkező tárolócsoport *időtartama alatt* felhasznált erőforrásokért számlázza. Az időtartam kiszámítása attól az időponttól kezdve, hogy lekéri az első tároló rendszerkép, amíg a tárolócsoport leáll. Nem tartalmazza a tárolócsoport üzembe helyezésének idejét.

  Lásd [az árképzés részleteit](https://azure.microsoft.com/pricing/details/container-instances/).

* **CUDA-illesztőprogramok** – A GPU-erőforrásokkal rendelkező tárolópéldányok előre ki vannak építve NVIDIA CUDA-illesztőprogramokkal és tároló-run-időkkel, így a CUDA-számítási feladatokhoz kifejlesztett tárolórendszerképeket is használhat.

  Ebben a szakaszban támogatjuk a CUDA 9.0-t. A Docker-fájlhoz például a következő alaprendszerképeket használhatja:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Példa YAML

A GPU-erőforrások hozzáadásának egyik módja egy tárolócsoport telepítése [YAML-fájl](container-instances-multi-container-yaml.md)használatával. Másolja a következő YAML-fájlt egy *gpu-deploy-aci.yaml*nevű új fájlba, majd mentse a fájlt. Ez a YAML egy *gpucontainergroup* nevű tárolócsoportot hoz létre, amely egy K80 GPU-val rendelkező tárolópéldányt ad meg. A példány egy minta CUDA vektor összeadási alkalmazást futtat. Az erőforrás-kérelmek elegendőek a számítási feladat futtatásához.

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

Telepítse a tárolócsoportot az [az container create][az-container-create] paranccsal, és `--file` adja meg a paraméter YAML-fájlnevét. Meg kell adnia egy erőforráscsoport nevét és a tárolócsoport helyét, például *az Eastus,* amely támogatja a GPU-erőforrásokat.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Az üzembe helyezés több percet is igénybe vehet. Ezután a tároló elindul, és futtatja a CUDA vektor összeadási művelet. Futtassa az [az container logs parancsot][az-container-logs] a naplókimenet megtekintéséhez:

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

## <a name="resource-manager-template-example"></a>Példa az Erőforrás-kezelő sablonjára

Egy másik módja annak, hogy egy tárolócsoportot GPU-erőforrásokkal telepítsen, egy [Resource Manager sablon](container-instances-multi-container-group.md)használatával. Először hozzon `gpudeploy.json`létre egy nevű fájlt, majd másolja a következő JSON-t. Ez a példa egy V100-as GPU-val rendelkező tárolópéldányt telepít, amely [TensorFlow-betanítási](https://www.tensorflow.org/) feladatot futtat az MNIST adatkészlettel. Az erőforrás-kérelmek elegendőek a számítási feladat futtatásához.

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

Telepítse a sablont az [az csoport telepítési létrehozási][az-group-deployment-create] parancsával. Meg kell adnia egy erőforráscsoport nevét, amely egy olyan régióban jött létre, például *eastus,* amely támogatja a GPU-erőforrásokat.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

Az üzembe helyezés több percet is igénybe vehet. Ezután a tároló elindul, és futtatja a TensorFlow-feladatot. Futtassa az [az container logs parancsot][az-container-logs] a naplókimenet megtekintéséhez:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
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

Mivel a GPU-erőforrások használata költséges lehet, győződjön meg arról, hogy a tárolók nem futnak váratlanul hosszú ideig. Figyelheti a tárolókaz Azure Portalon, vagy ellenőrizze a tárolócsoport állapotát az [az container show][az-container-show] paranccsal. Példa:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Ha végzett a létrehozott tárolópéldányokkal, törölje őket a következő parancsokkal:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>További lépések

* További információ a tárolócsoport [YAML-fájl](container-instances-multi-container-yaml.md) vagy [Erőforrás-kezelő sablon](container-instances-multi-container-group.md)használatával történő központi telepítéséről.
* További információ a [GPU-ra optimalizált virtuálisgép-méretekről](../virtual-machines/linux/sizes-gpu.md) az Azure-ban.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
