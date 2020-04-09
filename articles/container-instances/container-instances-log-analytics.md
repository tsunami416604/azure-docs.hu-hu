---
title: Erőforrásnaplók gyűjtése & elemzése
description: Megtudhatja, hogy miként küldhet erőforrásnaplókat és eseményadatokat az Azure Container-példányok tárolócsoportjaiból az Azure Monitor-naplókba
ms.topic: article
ms.date: 04/07/2020
ms.author: danlep
ms.openlocfilehash: bd21a511641d5ea027c18bedb4dce47749110bcb
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892393"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Tárolócsoport- és példánynaplózás az Azure Monitor-naplókkal

A Log Analytics-munkaterületek központi helyet biztosítanak a naplóadatok tárolására és lekérdezésére nem csak az Azure-erőforrásokból, hanem a helyszíni erőforrásokból és más felhőkben lévő erőforrásokból is. Az Azure Container Instances beépített támogatást tartalmaz a naplók és eseményadatok Azure Monitor-naplókba küldéséhez.

Ha tárolócsoport-naplót és eseményadatokat szeretne küldeni az Azure Monitor naplóiba, adjon meg egy meglévő Log Analytics-munkaterület-azonosítót és munkaterületi kulcsot egy tárolócsoport konfigurálásakor. 

A következő szakaszok ismertetik, hogyan hozhat létre egy naplózást engedélyező tárolócsoportot, és hogyan lehet lekérdezni a naplókat. A naplózás engedélyezéséhez munkaterület-azonosítóval és munkaterületi kulccsal is frissítheti a [tárolócsoportot.](container-instances-update.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Jelenleg csak a Linux-tárolópéldányok eseményadatait küldheti el a Log Analytics szolgáltatásba.

## <a name="prerequisites"></a>Előfeltételek

A tárolópéldányokban való naplózás engedélyezéséhez a következőkre van szükség:

* [Log Analytics-munkaterület](../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (vagy [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>A Log Analytics hitelesítő adatainak lekérése

Az Azure Container Instancesnek engedélyre van szüksége ahhoz, hogy adatokat küldhessen a Log Analytics-munkaterületre. Ennek az engedélynek a megadásához és a naplózás engedélyezéséhez a tárolócsoport létrehozásakor meg kell adnia a Log Analytics-munkaterület azonosítóját és egy ahhoz tartozó kulcsot (az elsődleges vagy a másodlagos kulcsot).

A naplóelemzési munkaterület-azonosító és az elsődleges kulcs beszerzése:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. A **Beállítások csoportban**válassza a **Speciális beállítások lehetőséget.**
1. Válassza a **Csatlakoztatott források** > **Windows-kiszolgálók** (vagy **Linux-kiszolgálók**-- az azonosító és a kulcsok azonosak mindkét)
1. Jegyezze fel a következőket:
   * **MUNKATERÜLET AZONOSÍTÓJA**
   * **ELSŐDLEGES KULCS**

## <a name="create-container-group"></a>Tárolócsoport létrehozása

Most, hogy rendelkezik a log analytics munkaterület-azonosító és az elsődleges kulcs, készen áll egy naplózást engedélyező tárolócsoport létrehozására.

A következő példák bemutatják két módon hozhat létre egy tárolócsoportot, amely egyetlen [folyékony tárolóból][fluentd] áll: Azure CLI és az Azure CLI egy YAML-sablonnal. A folyékonyan kiújult tároló több sorkimenetet hoz létre az alapértelmezett konfigurációban. Mivel ez a kimenet a Log Analytics-munkaterületre lesz elküldve, jól használható a naplók megtekintésének és lekérdezésének bemutatásához.

### <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül

Az Azure CLI üzembe helyezéséhez adja meg a `--log-analytics-workspace` és a `--log-analytics-workspace-key` paramétereket az [az container create][az-container-create] parancsban. Cserélje le a két munkaterület-értéket az előző lépésben lekért értékekre (és frissítse az erőforráscsoport nevét) a következő parancs futtatása előtt.

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

Ezután hajtsa végre a következő parancsot a tárolócsoport telepítéséhez. Cserélje `myResourceGroup` le egy erőforráscsoportra az előfizetésben (vagy először hozzon létre egy "myResourceGroup" nevű erőforráscsoportot):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Röviddel a parancs kiadását követően választ kell kapnia az Azure-tól, amely az üzemelő példány részleteit taglalja.

## <a name="view-logs"></a>Naplók megtekintése

A tárolócsoport üzembe helyezése után néhány percbe telik (legfeljebb 10 percbe), hogy az Azure Portalon megjelenjenek az első naplóbejegyzések. A tárolócsoport naplóinak megtekintéséhez `ContainerInstanceLog_CL` a táblázatban:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. Az **Általános**csoportban válassza **a Naplók lehetőséget.**  
1. Írja be a következő lekérdezést:`ContainerInstanceLog_CL | limit 50`
1. Válassza a **Futtatás** lehetőséget

A lekérdezés több eredményt is megjelenít. Ha először nem lát eredményt, várjon néhány percet, majd kattintson a **Futtatás** gombra a lekérdezés ismételt végrehajtásához. Alapértelmezés szerint a naplóbejegyzések **Táblázat** formátumban jelennek meg. Ezután a sorokat kibontva tekintheti meg az egyes naplóbejegyzések tartalmát.

![Naplókeresési találatok az Azure Portalon][log-search-01]

## <a name="view-events"></a>Események megtekintése

Az Azure Portalon is megtekintheti a tárolópéldányok eseményeit. Az események tartalmazzák a példány létrehozásának és elindításának idejét. A táblázatban szereplő `ContainerEvent_CL` eseményadatok megtekintése:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. Az **Általános**csoportban válassza **a Naplók lehetőséget.**  
1. Írja be a következő lekérdezést:`ContainerEvent_CL | limit 50`
1. Válassza a **Futtatás** lehetőséget

A lekérdezés több eredményt is megjelenít. Ha először nem lát eredményt, várjon néhány percet, majd kattintson a **Futtatás** gombra a lekérdezés ismételt végrehajtásához. Alapértelmezés szerint a bejegyzések **Táblázat** formátumban jelennek meg. Ezután kibontva kibonthatja a sort, hogy láthassa az egyes bejegyzések tartalmát.

![Eseménykeresési eredmények az Azure Portalon][log-search-02]

## <a name="query-container-logs"></a>Tárolónaplók lekérdezése

Az Azure Monitor naplók tartalmaz egy kiterjedt [lekérdezési nyelv][query_lang] potenciálisan több ezer sornyi naplókimeneti információkat.

A lekérdezés alapszerkezete a forrástábla (ebben `ContainerInstanceLog_CL` `ContainerEvent_CL`a cikkben vagy ), amelyet a`|`csőkarakter ( ) által elválasztott operátorok sorozata követ. Több operátor sorba állításával finomíthatja az eredményeket, és speciális funkciókat végezhet el.

A lekérdezés eredményére példa megtekintéséhez illessze be a következő lekérdezést a lekérdezés szövegmezőjébe, és a Lekérdezés végrehajtásához kattintson a **Futtatás** gombra. Ez a lekérdezés megjeleníti az összes olyan naplóbejegyzést, amelynek „Message” (Üzenet) mezője tartalmazza a „warn” (figyelmeztetés) szót:

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

A naplók lekérdezéséről és a riasztások azure monitornaplókban való konfigurálásáról a következő témakörökben talál további információt:

* [A naplókeresések ismertetése az Azure Monitor naplóiban](../log-analytics/log-analytics-log-search.md)
* [Egyesített riasztások az Azure Monitorban](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Tárolók processzor- és memóriahasználatának monitorozása

A tárolópéldányok processzor- és memória-erőforrásainak monitorozásával kapcsolatos bővebb információkért lásd:

* [Tároló-erőforrások figyelése az Azure Container-példányokban.](container-instances-monitor.md)

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create