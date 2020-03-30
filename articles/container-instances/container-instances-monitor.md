---
title: Tárolópéldányok figyelése
description: Hogyan figyelheti a számítási erőforrások, például a PROCESSZOR és a memória felhasználását az Azure Container Instances tárolókban.
ms.topic: article
ms.date: 04/24/2019
ms.openlocfilehash: b4a66254c18d7e01b6d56e64e6b62721b620d499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250032"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Tároló-erőforrások figyelése az Azure Container Instances-ben

[Az Azure Monitor][azure-monitoring] betekintést nyújt a tárolópéldányok által használt számítási erőforrásokba. Ez az erőforrás-használati adatok segítenek meghatározni a legjobb erőforrás-beállításokat a tárolócsoportok számára. Az Azure Monitor olyan metrikákat is biztosít, amelyek nyomon követik a hálózati tevékenységeket a tárolópéldányokban.

Ez a dokumentum részletezi az Azure Monitor metrikák összegyűjtését az Azure Portalon és az Azure CLI használatával a tárolópéldányok számára.

> [!IMPORTANT]
> Az Azure Monitor metrikák az Azure Container-példányok jelenleg előzetes verzióban, és bizonyos [korlátozások vonatkoznak.](#preview-limitations) Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előnézeti korlátozások

Ebben az időben az Azure Monitor metrikák csak Linux-tárolók érhetők el.

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

Az Azure Monitor a következő [metrikákat biztosítja az Azure Container-példányok számára.][supported-metrics] Ezek a metrikák egy tárolócsoporthoz és az egyes tárolókhoz érhetők el.

* **CPU-használat** - **millimagban mérve.** Egy millimag a CPU mag 1/1000-ed része, így az 500 millimag (vagy 500 m) a CPU mag 50%-os használatát jelenti. **Összesítve az összes** mag átlagos használataként.

* **Memóriahasználat** – **átlagos bájtként összesítve.**

* **Másodpercenként fogadott hálózati bájtok** és **másodpercenként küldött hálózati bájtok** – **másodpercenként átlagos bájtként**összesítve . 

## <a name="get-metrics---azure-portal"></a>Metrika beolvasása – Azure Portal

Tárolócsoport létrehozásakor az Azure Monitor adatai elérhetők az Azure Portalon. Egy tárolócsoport metrikáit tekintse meg, nyissa meg a tárolócsoport **áttekintése** lapot. Itt láthatja az egyes elérhető mutatók hoz előre létrehozott diagramokat.

![kettős diagram][dual-chart]

Több tárolót tartalmazó tárolócsoportban használjon [dimenziót][monitor-dimension] a metrikák tárolószerint történő bemutatásához. Az egyes tárolómetrikák diagramjának létrehozásához hajtsa végre az alábbi lépéseket:

1. Az **Áttekintés** lapon válasszon ki egy metrikadiagramot, például a **CPU-t.** 
1. Jelölje ki a **Felosztás alkalmazása** gombot, majd a Tároló **neve parancsot.**

![dimenzió][dimension]

## <a name="get-metrics---azure-cli"></a>Metrika beolvasása – Azure CLI

A tárolópéldányok metrikák is gyűjthető az Azure CLI használatával. Első lépésként olvassa be a csoport azonosítóját az alábbi paranccsal. Cserélje le a `<resource-group>` kifejezést az erőforráscsoport-nevével, a `<container-group>` kifejezést pedig a tárolócsoport nevével.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Használja az alábbi parancsot a **CPU**-használati metrikák lekérésére.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table
```

```output
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

Módosítsa a `--metric` paraméter értékét a parancsban, hogy más [támogatott mutatókat kapjon.][supported-metrics] Például használja a következő parancsot **a memóriahasználati** metrikák lekérni. 

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table
```

```output
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

Többtárolós csoport esetén `containerName` a dimenzió hozzáadható a tárolónkénti metrikák visszaadásához.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table
```

```output
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

Megtudhatja, hogyan hozhat létre [metrikariasztásokat,][metric-alert] hogy értesítést kapjon, ha az Azure Container Instances metrikája átlép egy küszöbértéket.

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
