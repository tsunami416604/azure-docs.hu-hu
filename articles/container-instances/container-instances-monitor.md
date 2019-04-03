---
title: Tárolók figyelése az Azure Container Instances-ben
description: Részletesen ismerteti, hogyan lehet figyelni a számítási erőforrások, mint a CPU és memória fogyasztását az Azure Container Instances tárolóiban.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: danlep
ms.openlocfilehash: 950d8b4b5ec1a55e2054039a01d6807915b5c714
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58848231"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Tároló-erőforrások figyelése az Azure Container Instances-ben

Az Azure Monitor betekintést biztosít a tárolópéldányok által használt számítási erőforrásokba. Az Azure Monitor segítségével nyomon követheti a tárolócsoportok és tárolóik CPU- és memóriafelhasználását. Ezek az erőforrás-használati adatok segítenek meghatározni a tárolócsoportok számára legjobb CPU- és memóriabeállításokat.

Ez a dokumentum részletesen ismerteti a tárolópéldányok CPU- és memóriahasználatának az Azure Portalon és az Azure CLI-vel történő gyűjtését.

> [!IMPORTANT]
> Jelenleg erőforrás-használati metrikák csak Linux-tárolókhoz állnak rendelkezésre.
>

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

Az Azure Monitor metrikák biztosít a **CPU-** és **memória**használat méréséhez az Azure Container Instances-ben. Mindkét metrika rendelkezésre áll tárolócsoporthoz és különálló tárolókhoz is.

A CPU-metrikák kifejezése **millicore-ban** történik. Egy millicore a processzormag 1/1000 része, tehát 500 millicore (vagy 500 m) a CPU-mag 50%-os kihasználtságát jelenti.

A memóriametrikák kifejezése **bájtban** történik.

## <a name="get-metrics---azure-portal"></a>Metrika beolvasása – Azure Portal

Tárolócsoport létrehozásakor az Azure Monitor adatai elérhetők az Azure Portalon. Tárolócsoport metrikáinak megtekintéséhez jelölje ki az erőforráscsoportot, majd a tárolócsoport. Itt előre létrehozott diagramokat talál a CPU- és a memóriahasználathoz is.

![kettős diagram][dual-chart]

Ha több tárolót tartalmazó tárolócsoporttal rendelkezik, használjon [dimenziót] [ monitor-dimension] az egyes tárolók metrikáinak bemutatásához. Az egyes tárolómetrikák diagramjának létrehozásához hajtsa végre az alábbi lépéseket:

1. Válassza a **Monitor** lehetőséget a bal oldali navigációs menüből.
2. Válasszon tárolócsoportot és egy metrikát (CPU vagy memória).
3. Kattintson a zöld dimenzió gombra, majd válassza a **Tárolónév** lehetőséget.

![dimenzió][dimension]

## <a name="get-metrics---azure-cli"></a>Metrika beolvasása – Azure CLI

A tárolópéldányok a CPU- és memóriahasználat is begyűjthető az Azure CLI használatával. Első lépésként olvassa be a csoport azonosítóját az alábbi paranccsal. Cserélje le a `<resource-group>` kifejezést az erőforráscsoport-nevével, a `<container-group>` kifejezést pedig a tárolócsoport nevével.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Használja az alábbi parancsot a **CPU**-használati metrikák lekérésére.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

Az alábbi parancsot pedig a **memória**használati metrikák lekérésére.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

Több tárolót tartalmazó csoportnál a `containerName` dimenziót fel lehet venni ezeknek az adatoknak a tárolónkénti visszaadására.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>További lépések

Az Azure-alapú figyelésről további információt az [Azure-alapú figyelés áttekintése][azure-monitoring] szakaszban talál.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
