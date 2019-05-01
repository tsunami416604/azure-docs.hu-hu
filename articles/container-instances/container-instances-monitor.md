---
title: Tárolók figyelése az Azure Container Instances-ben
description: Hogyan lehet a számítási erőforrások, például a Processzor- és az Azure Container Instances a tárolók által a használat figyeléséhez.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2019
ms.author: danlep
ms.openlocfilehash: 7b46ea0518038eeb908591b8438acc2a9095242c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570900"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Tároló-erőforrások figyelése az Azure Container Instances-ben

[Az Azure Monitor] [ azure-monitoring] a tárolók példányok által használt számítási erőforrások betekintést nyújt. Az erőforrás-használati adatok segít meghatározni, hogy a legjobb erőforrás-beállításai a tárolócsoportok. Az Azure Monitor mérőszámok, amelyek nyomon követik a tárolópéldányok hálózati tevékenységet is tartalmaz.

Ez a dokumentum részletesen gyűjtése az Azure Monitor metrikáinak container Instances szolgáltatásban az Azure Portalon és az Azure CLI használatával.

> [!IMPORTANT]
> Az Azure Container Instances szolgáltatásban az Azure Monitor-metrikák jelenleg előzetes verzióban érhető el, és néhány [korlátozások érvényesek a](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

Jelenleg az Azure Monitor-metrikák érhetők el csak Linux-tárolók esetén.

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

Az Azure Monitor biztosítja a következő [mérőszámok az Azure Container Instances][supported-metrics]. Ezeket a metrikákat egy tárolócsoport és az egyes tárolók érhetők el.

* **CPU-használat** – mérve **millicore**. Egy millicore: 1/1000th, a CPU-magot, így 500 millicore (vagy 500 millió) a CPU-magot 50 %-os használatát jelöli. Összesítjük **átlagos használat** összes magon.

* **Memóriahasználat** – mint összesített **bájtok átlagos**.

* **Hálózati bájtok másodpercenként fogadott** és **hálózati bájtok továbbított másodpercenként** – mint összesített **bájtok másodpercenkénti átlagos**. 

## <a name="get-metrics---azure-portal"></a>Metrika beolvasása – Azure Portal

Tárolócsoport létrehozásakor az Azure Monitor adatai elérhetők az Azure Portalon. A tárolócsoport metrikák megtekintéséhez nyissa meg a **áttekintése** a tárolócsoport lap. Itt láthatja el az egyes elérhető metrikáinak előre létrehozott diagramok.

![kettős diagram][dual-chart]

Tárolócsoport, amely több tárolót tartalmaz, használjon egy [dimenzió] [ monitor-dimension] metrikák a tároló nyújtjuk. Az egyes tárolómetrikák diagramjának létrehozásához hajtsa végre az alábbi lépéseket:

1. Az a **áttekintése** lapon válassza ki a metrikus diagramok egyikének például **CPU**. 
1. Válassza ki a **alkalmazni a felosztás** gombra, és válassza **Tárolónév**.

![dimenzió][dimension]

## <a name="get-metrics---azure-cli"></a>Metrika beolvasása – Azure CLI

Container Instances metrikák az Azure CLI használatával is megtörtént. Első lépésként olvassa be a csoport azonosítóját az alábbi paranccsal. Cserélje le a `<resource-group>` kifejezést az erőforráscsoport-nevével, a `<container-group>` kifejezést pedig a tárolócsoport nevével.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Használja az alábbi parancsot a **CPU**-használati metrikák lekérésére.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

Módosítsa a `--metric` paramétert a parancsba beolvasni a többi [támogatott mérőszámok][supported-metrics]. Például a következő parancs használatával első **memória** metrikáit. 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

Többtárolós csoport a `containerName` dimenzió lehet hozzáadni tárolónként metrikákat adja vissza.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>További lépések

Az Azure-alapú figyelésről további információt az [Azure-alapú figyelés áttekintése][azure-monitoring] szakaszban talál.

Ismerje meg, hogyan hozhat létre [metrikákhoz kapcsolódó riasztások] [ metric-alert] , értesítést kaphat, amikor az Azure Container Instances egy metrika átlépi a küszöbértéket.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
