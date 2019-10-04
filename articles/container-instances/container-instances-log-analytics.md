---
title: Tároló-példányok naplózása Azure Monitor naplókkal
description: Ismerje meg, hogyan küldhet naplókat az Azure Container instances szolgáltatásból Azure Monitor naplókba.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 09/02/2019
ms.author: danlep
ms.openlocfilehash: 1c4846414036e86d460d9abe0bd93e785e710395
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258463"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Tároló-példányok naplózása Azure Monitor naplókkal

Log Analytics-munkaterületek központi helyet biztosítanak a naplófájlok adatainak tárolásához és lekérdezéséhez nem csak az Azure-erőforrások, hanem a helyszíni erőforrások és erőforrások között más felhőkben is. A Azure Container Instances beépített támogatást biztosít a naplók és az események Azure Monitor naplókba küldéséhez.

Ha a tárolók csoportjának naplóját és az eseményeket Azure Monitor naplókba kívánja küldeni, meg kell adnia egy Log Analytics-munkaterület AZONOSÍTÓját és a munkaterület kulcsát egy tároló-csoport létrehozásakor. A következő szakaszok egy naplózható tárolócsoport létrehozásának és a naplók lekérdezésének menetét ismertetik.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Jelenleg csak a Linux Container instances szolgáltatásból küldhetnek az események adatait Log Analytics.

## <a name="prerequisites"></a>Előfeltételek

A tárolópéldányokban való naplózás engedélyezéséhez a következőkre van szükség:

* [Log Analytics-munkaterület](../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (vagy [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>A Log Analytics hitelesítő adatainak lekérése

Az Azure Container Instancesnek engedélyre van szüksége ahhoz, hogy adatokat küldhessen a Log Analytics-munkaterületre. Ennek az engedélynek a megadásához és a naplózás engedélyezéséhez a tárolócsoport létrehozásakor meg kell adnia a Log Analytics-munkaterület azonosítóját és egy ahhoz tartozó kulcsot (az elsődleges vagy a másodlagos kulcsot).

A log Analytics-munkaterület AZONOSÍTÓjának és elsődleges kulcsának beszerzése:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. A **Beállítások**területen válassza a **Speciális beállítások** lehetőséget.
1. Válassza ki az **Összekapcsolt források** > **Windows-kiszolgálók** lehetőséget (vagy **Linux-kiszolgálók** lehetőséget – mindkettőhöz ugyanaz az azonosító és kulcs tartozik)
1. Jegyezze fel a következőket:
   * **MUNKATERÜLET AZONOSÍTÓJA**
   * **ELSŐDLEGES KULCS**

## <a name="create-container-group"></a>Tárolócsoport létrehozása

Most, hogy megkapta a log Analytics-munkaterület AZONOSÍTÓját és az elsődleges kulcsot, készen áll egy naplózásra képes tároló csoport létrehozására.

Az alábbi példák két módszert mutatnak be egy olyan tároló-csoport létrehozására, amely egyetlen [Fluent][fluentd] -tárolóval rendelkezik: Az Azure CLI és az Azure CLI egy YAML sablonnal. A Fluentd tároló az alapértelmezett konfiguráció szerint több sornyi kimenetet hoz létre. Mivel ez a kimenet a Log Analytics-munkaterületre lesz elküldve, jól használható a naplók megtekintésének és lekérdezésének bemutatásához.

### <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

Az Azure CLI-vel való üzembe helyezéshez `--log-analytics-workspace` a `--log-analytics-workspace-key` és a paramétereket az az [Container Create][az-container-create] paranccsal adhatja meg. Cserélje le a két munkaterület-értéket az előző lépésben lekért értékekre (és frissítse az erőforráscsoport nevét) a következő parancs futtatása előtt.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Üzembe helyezés a YAML használatával

Ezt a módszert akkor használja, ha a YAML használatával szeretne tárolócsoportokat üzembe helyezni. Az alábbi YAML egy tárolócsoportot határoz meg, benne egyetlen tárolóval. Másolja a YAML-t egy új fájlba, majd cserélje le a `LOG_ANALYTICS_WORKSPACE_ID` és `LOG_ANALYTICS_WORKSPACE_KEY` helyőrzőket az előző lépésben lekért értékekre. Mentse a fájlt **deploy-aci.yaml** néven.

```yaml
apiVersion: 2018-10-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Ezután hajtsa végre a következő parancsot a tároló csoport üzembe helyezéséhez. Cserélje `myResourceGroup` le az kifejezést az előfizetéshez tartozó erőforráscsoporthoz (vagy először hozzon létre egy "myResourceGroup" nevű erőforráscsoportot):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Röviddel a parancs kiadását követően választ kell kapnia az Azure-tól, amely az üzemelő példány részleteit taglalja.

## <a name="view-logs"></a>Naplók megtekintése

A tárolócsoport üzembe helyezése után néhány percbe telik (legfeljebb 10 percbe), hogy az Azure Portalon megjelenjenek az első naplóbejegyzések. A tároló csoport naplóinak megtekintése a `ContainerInstanceLog_CL` táblában:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. Az **általános**területen válassza a **naplók** lehetőséget.  
1. Írja be a következő lekérdezést:`ContainerInstanceLog_CL | limit 50`
1. **Futtatás** kiválasztása

A lekérdezésnek számos találatot kell megjelennie. Ha először nem lát eredményt, várjon néhány percet, majd kattintson a **Futtatás** gombra a lekérdezés újbóli végrehajtásához. Alapértelmezés szerint a naplóbejegyzések **táblázatos** formátumban jelennek meg. Ezután a sorokat kibontva tekintheti meg az egyes naplóbejegyzések tartalmát.

![Naplókeresési találatok az Azure Portalon][log-search-01]

## <a name="view-events"></a>Események megtekintése

Megtekintheti a Azure Portal tároló példányainak eseményeit is. Az események közé tartozik a példány létrehozásának és indításának időpontja. A `ContainerEvent_CL` táblázatban szereplő esemény-adatértékek megtekintése:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. Az **általános**területen válassza a **naplók** lehetőséget.  
1. Írja be a következő lekérdezést:`ContainerEvent_CL | limit 50`
1. **Futtatás** kiválasztása

A lekérdezésnek számos találatot kell megjelennie. Ha először nem lát eredményt, várjon néhány percet, majd kattintson a **Futtatás** gombra a lekérdezés újbóli végrehajtásához. Alapértelmezés szerint a bejegyzések **táblázatos** formátumban jelennek meg. Ezután kibonthat egy sort az egyes bejegyzések tartalmának megtekintéséhez.

![Az események keresési eredményei a Azure Portal][log-search-02]

## <a name="query-container-logs"></a>Tárolónaplók lekérdezése

Azure Monitor naplók széles körű [lekérdezési nyelvet][query_lang] foglalnak magukban, hogy az információk a naplófájlok esetlegesen több ezer sorából legyenek kihúzva.

A lekérdezés alapstruktúrája a forrástábla (ebben a cikkben vagy `ContainerInstanceLog_CL` `ContainerEvent_CL`), majd a cső karakterrel (`|`) elválasztott operátorok sorozata. Több operátor sorba állításával finomíthatja az eredményeket, és speciális funkciókat végezhet el.

A lekérdezés eredményeinek megtekintéséhez illessze be a következő lekérdezést a lekérdezés szövegmezőbe, majd kattintson a **Futtatás** gombra a lekérdezés végrehajtásához. Ez a lekérdezés megjeleníti az összes olyan naplóbejegyzést, amelynek „Message” (Üzenet) mezője tartalmazza a „warn” (figyelmeztetés) szót:

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

Összetettebb lekérdezések használata is támogatott. Például ez a lekérdezés csak a „mycontainergroup001” tárolócsoporthoz az elmúlt egy órában létrejött naplóbejegyzéseket jeleníti meg:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>További lépések

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A naplók lekérdezéséről és a riasztások Azure Monitor naplókban való konfigurálásáról további információt a következő témakörben talál:

* [A naplók Azure Monitor naplókban való keresésének ismertetése](../log-analytics/log-analytics-log-search.md)
* [Egyesített riasztások az Azure Monitorban](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Tárolók processzor- és memóriahasználatának monitorozása

A tárolópéldányok processzor- és memória-erőforrásainak monitorozásával kapcsolatos bővebb információkért lásd:

* [Tároló-erőforrások monitorozása az Azure Container Instancesben](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create