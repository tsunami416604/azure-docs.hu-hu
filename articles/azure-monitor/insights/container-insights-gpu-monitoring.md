---
title: GPU-figyelés konfigurálása a Azure Monitor for containers szolgáltatáshoz | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a figyelési Kubernetes-fürtöket NVIDIA és AMD GPU-t használó csomópontokkal a Azure Monitor for containers használatával.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 4c6044d407dc4abd0e69bac0190cc19c901022c3
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569696"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>A GPU-figyelés konfigurálása a Azure Monitor for containers szolgáltatással

Az ügynök verziójának *ciprod03022019*kezdődően az Azure monitor for containers Integrated Agent mostantól támogatja a GPU-kompatibilis Kubernetes-fürt csomópontjain a monitoring GPU-t (grafikus feldolgozási egységek), valamint a GPU-erőforrásokat kérő és használó hüvelyek/tárolók figyelését.

## <a name="supported-gpu-vendors"></a>Támogatott GPU-szállítók

A tárolók Azure Monitor támogatja a GPU-fürtök figyelését a következő GPU-szállítóktól:

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

A tárolók Azure Monitor automatikusan elindítja a GPU-használat figyelését a csomópontokon, valamint a hüvelyeket és a munkaterheléseket kérő GPU-t a következő metrikák 60sec időközönkénti gyűjtésével és a **InsightMetrics** táblában való tárolásával.

>[!NOTE]
>A fürt GPU-csomópontokkal való üzembe helyezését követően győződjön meg arról, hogy a GPU- [illesztő](../../aks/gpu-cluster.md) a GPU-munkaterhelések futtatásához szükséges az AK-ban. A tárolók Azure Monitor GPU-metrikákat gyűjtenek a csomóponton futó GPU-illesztőn keresztül. 

|Metrika neve |Metrikus dimenzió (címkék) |Description |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Az elmúlt mintavételi időszakban (60 másodpercben) az idő százalékos aránya, amely alatt a GPU foglalt/aktívan feldolgozás alatt áll a tárolóban. A Duty ciklus 1 és 100 közötti szám. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Minden tároló megadhatja a határértékeket egy vagy több GPU-ként. A GPU töredékét nem lehet lekérni vagy korlátozni. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Az egyes tárolók egy vagy több GPU-t is igényelhetnek. A GPU töredékét nem lehet lekérni vagy korlátozni.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Egy adott tároló számára elérhető, bájtban használható GPU-memória mennyisége. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Egy adott tároló által használt GPU memória mennyisége bájtban megadva. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Azon GPU-k száma egy csomóponton, amelyeket a Kubernetes használhat. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Egy csomóponton található GPU-k száma összesen |

## <a name="gpu-performance-charts"></a>GPU-teljesítményű diagramok 

A tárolók Azure Monitor tartalmaz előre konfigurált diagramokat a táblázatban korábban felsorolt metrikák számára a minden fürthöz tartozó GPU-munkafüzetként. A GPU-munkafüzet **csomópontjának GPU** -t közvetlenül egy AK-fürtből is megtalálhatja, ha a bal oldali ablaktáblán a **munkafüzetek** elemre kattint, és az elemzés **munkafüzetek megtekintése** legördülő listájában található.

## <a name="next-steps"></a>Következő lépések

- Lásd: [a GPU használata nagy számítási igényű munkaterhelésekhez az Azure Kubernetes szolgáltatásban](../../aks/gpu-cluster.md) (ak), amelyből megtudhatja, hogyan helyezhet üzembe egy GPU-t támogató csomópontokat tartalmazó AK-fürtöt.

- További információ a [GPU-ra optimalizált VM SKU-ról Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Tekintse át a [GPU támogatását a Kubernetes-ben](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) , és ismerkedjen meg a Kubernetes kísérleti támogatásával a GPU-k egy vagy több csomóponton való kezeléséhez.
