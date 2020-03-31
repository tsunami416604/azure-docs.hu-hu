---
title: GPU-figyelés konfigurálása az Azure Monitor tárolókhoz való használatával | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan konfigurálhatja a Kubernetes-fürtök figyelését az NVIDIA és az AMD GPU-kompatibilis csomópontokkal az Azure Monitor tárolókhoz.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373309"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>GPU-figyelés konfigurálása az Azure Monitor tárolókhoz szolgáltatásával

A *ciprod03022019*ügynökverziójától kezdve az Azure-figyelő a tárolók integrált ügynöke számára mostantól támogatja a GPU (grafikus feldolgozó egységek) használatát a GPU-barát Kubernetes fürtcsomópontokon, valamint a GPU-erőforrásokat kérő és használó podok/tárolók figyelését.

## <a name="supported-gpu-vendors"></a>Támogatott GPU-gyártók

Az Azure Monitor tárolókhoz támogatja a következő GPU-gyártók GPU-fürtjeit figyelő:

- [Nvidia](https://developer.nvidia.com/kubernetes-gpu)

- [Amd](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Az Azure Monitor a tárolók automatikusan elindítja a FIGYELÉS GPU-használat csomópontokon, és a GPU kérő podok és számítási feladatok a következő metrikák 60 mp-es időközönként, és tárolja őket az **InsightMetrics** táblázatban:

|Metrika neve |Metrikus dimenzió (címkék) |Leírás |
|------------|------------------------|------------|
|konténerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Az elmúlt mintavételi időszak (60 másodperc) azon idejének százalékos aránya, amely alatt a GPU foglalt/aktívan feldolgozott egy tárolót. A terhelhetőség 1 és 100 közötti szám. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Minden tároló korlátokat adhat meg egy vagy több GPU-ként. A GPU töredékét nem lehet kérni vagy korlátozni. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Minden tároló kérhet egy vagy több GPU-kat. A GPU töredékét nem lehet kérni vagy korlátozni.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Egy adott tárolóhoz használható GPU-memória bájtban kifejezett mennyisége. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Egy adott tároló által használt GPU-memória bájtban kifejezett mennyisége. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |A Kubernetes által használható csomópontban lévő GPU-k száma. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |A csomópontok gpu-inak teljes száma. |

## <a name="gpu-performance-charts"></a>GPU teljesítménydiagramok 

Az Azure Monitor tárolók hoz előre konfigurált diagramok a metrikák a táblázatban korábban felsorolt GPU-munkafüzetként minden fürthöz. A GPU-munkafüzet **node GPU-ját** közvetlenül az AKS-fürtből találhatja meg, ha a bal oldali ablaktáblából a Munkafüzeteket, az Insight **Munkafüzetek megtekintése** legördülő listájában választják ki a **munkafüzeteket.**

## <a name="next-steps"></a>További lépések

- A [GPU-k használata gpu-kompatibilis csomópontokat](../../aks/gpu-cluster.md) tartalmazó AKS-fürtök üzembe helyezéséről az Azure Kubernetes-szolgáltatás (AKS) nagy számítási nagy munkaterheléséhez című témakörben olvashat.

- További információ a [GPU-ra optimalizált VM termékkészletekről a Microsoft Azure-ban.](../../virtual-machines/sizes-gpu.md)

- Tekintse át [a GPU-támogatást a Kubernetes-ben,](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) ha többet szeretne megtudni a Kubernetes kísérleti támogatásáról a GPU-k fürt egy vagy több csomópontján történő kezeléséhez.