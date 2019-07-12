---
title: Az Azure Monitor naplóira tároló példány naplózása
description: Ismerje meg, hogyan lehet elküldeni a naplókat az Azure container instances Azure Monitor naplóira.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 07/09/2019
ms.author: danlep
ms.openlocfilehash: cab0bc4d2d0491c70a1d2f11f3a5d5d831ade6cf
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722644"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Az Azure Monitor naplóira tároló példány naplózása

Log Analytics-munkaterületek adjon meg egy központi helyre a tároló és lekérdező nem csak Azure-erőforrásokat, de is a helyszíni erőforrásokhoz és erőforrások más felhőkben naplóadatait. Az Azure Container Instances szolgáltatás beépített módon támogatja az Azure Monitor naplóira történő adatküldés.

Tároló példány adatokat küldeni az Azure Monitor naplóira, meg kell adnia egy Log Analytics munkaterület Azonosítóját és a munkaterület kulcsát, amikor egy tárolócsoportot hoz létre. A következő szakaszok egy naplózható tárolócsoport létrehozásának és a naplók lekérdezésének menetét ismertetik.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

A tárolópéldányokban való naplózás engedélyezéséhez a következőkre van szükség:

* [Log Analytics-munkaterület](../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (vagy [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>A Log Analytics hitelesítő adatainak lekérése

Az Azure Container Instancesnek engedélyre van szüksége ahhoz, hogy adatokat küldhessen a Log Analytics-munkaterületre. Ennek az engedélynek a megadásához és a naplózás engedélyezéséhez a tárolócsoport létrehozásakor meg kell adnia a Log Analytics-munkaterület azonosítóját és egy ahhoz tartozó kulcsot (az elsődleges vagy a másodlagos kulcsot).

A log analytics-munkaterület Azonosítójára és az elsődleges kulcs beszerzése:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. A **beállítások**válassza **speciális beállítások**
1. Válassza ki az **Összekapcsolt források** > **Windows-kiszolgálók** lehetőséget (vagy **Linux-kiszolgálók** lehetőséget – mindkettőhöz ugyanaz az azonosító és kulcs tartozik)
1. Jegyezze fel a következőket:
   * **MUNKATERÜLET AZONOSÍTÓJA**
   * **ELSŐDLEGES KULCS**

## <a name="create-container-group"></a>Tárolócsoport létrehozása

Most, hogy a log analytics-munkaterület Azonosítójára és az elsődleges kulcs, készen áll naplózás engedélyezése tárolócsoport létrehozásához.

Az alábbi példák bemutatják, kétféle módon hozhat létre egy tárolócsoport egyetlen [fluentd][fluentd] tároló: Az Azure CLI és az Azure CLI-t egy YAML-sablont. A Fluentd tároló az alapértelmezett konfiguráció szerint több sornyi kimenetet hoz létre. Mivel ez a kimenet a Log Analytics-munkaterületre lesz elküldve, jól használható a naplók megtekintésének és lekérdezésének bemutatásához.

### <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

Adja meg az Azure CLI-vel üzembe helyezéséhez a `--log-analytics-workspace` és `--log-analytics-workspace-key` paramétereket a [az tároló létrehozása][az-container-create] parancsot. Cserélje le a két munkaterület-értéket az előző lépésben lekért értékekre (és frissítse az erőforráscsoport nevét) a következő parancs futtatása előtt.

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

Ezután hajtsa végre a következő parancsot a tároló-csoport központi telepítése. Cserélje le `myResourceGroup` egy erőforrással rendelkező csoportot az előfizetésében (vagy először hozzon létre egy erőforráscsoportot "myResourceGroup" nevű):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Röviddel a parancs kiadását követően választ kell kapnia az Azure-tól, amely az üzemelő példány részleteit taglalja.

## <a name="view-logs-in-azure-monitor-logs"></a>Naplók megtekintése az Azure Monitor naplóira

A tárolócsoport üzembe helyezése után néhány percbe telik (legfeljebb 10 percbe), hogy az Azure Portalon megjelenjenek az első naplóbejegyzések. A tárolócsoport naplók megtekintése:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. A **általános**válassza **naplók**  
1. Írja be a következő lekérdezést: `search *`
1. Válassza ki **futtatása**

Ekkor számos eredményt kell megjelenítenie a `search *` lekérdezésnek. Ha először eredményt nem látja, várjon néhány percet, majd válassza a **futtatása** gomb újra végrehajtani a lekérdezést. Alapértelmezés szerint a naplóbejegyzések megjelennek **tábla** formátumban. Ezután a sorokat kibontva tekintheti meg az egyes naplóbejegyzések tartalmát.

![Naplókeresési találatok az Azure Portalon][log-search-01]

## <a name="query-container-logs"></a>Tárolónaplók lekérdezése

Az Azure Monitor naplóira tartalmaz egy [lekérdezési nyelvet][query_lang] a napló kimeneti sor több ezer adatainak lekérése.

Az Azure Container Instances naplózó ügynöke a bejegyzéseket a Log Analytics-munkaterület `ContainerInstanceLog_CL` táblájába küldi. A lekérdezések alapvető szerkezete a következő: a forrástábla (`ContainerInstanceLog_CL`) után több operátor következik, a függőleges vonal (`|`) karakterrel elválasztva. Több operátor sorba állításával finomíthatja az eredményeket, és speciális funkciókat végezhet el.

A példa lekérdezés eredményeinek megtekintéséhez illessze be a következő lekérdezést a lekérdezési szövegmezőbe (az „Örökölt nyelvi átalakító megjelenítése” alatt), majd kattintson a **FUTTATÁS** gombra a lekérdezés végrehajtásához. Ez a lekérdezés megjeleníti az összes olyan naplóbejegyzést, amelynek „Message” (Üzenet) mezője tartalmazza a „warn” (figyelmeztetés) szót:

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

Összetettebb lekérdezések használata is támogatott. Például ez a lekérdezés csak a „mycontainergroup001” tárolócsoporthoz az elmúlt egy órában létrejött naplóbejegyzéseket jeleníti meg:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>További lépések

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

Naplók lekérdezése és riasztások konfigurálása az Azure Monitor naplóira kapcsolatos további információkért lásd:

* [Naplókereséseit ismertető az Azure Monitor naplóira](../log-analytics/log-analytics-log-search.md)
* [Egyesített riasztások az Azure Monitorban](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Tárolók processzor- és memóriahasználatának monitorozása

A tárolópéldányok processzor- és memória-erőforrásainak monitorozásával kapcsolatos bővebb információkért lásd:

* [Tároló-erőforrások monitorozása az Azure Container Instancesben](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create