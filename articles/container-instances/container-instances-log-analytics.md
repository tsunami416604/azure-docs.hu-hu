---
title: Tárolópéldány-naplózás az Azure Log Analytics használatával
description: Megtudhatja, hogyan küldheti el tárolók kimenetét (STDOUT és STDERR) az Azure Log Azure Log Analytics szolgáltatásba.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 06/06/2018
ms.author: marsma
ms.openlocfilehash: a0772d1009021ca64b448710c5353407a5492fae
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809869"
---
# <a name="container-instance-logging-with-azure-log-analytics"></a>Tárolópéldány-naplózás az Azure Log Analytics használatával

A Log Analytics munkaterületek központi helyet biztosítanak a naplóadatok tárolásához és lekérdezéséhez nem csak az Azure-erőforrásokból, hanem a helyszíni erőforrásokból és az egyéb felhőkben található erőforrásokból is. Az Azure Container Instances beépített módon támogatja az adatok elküldését a Log Analytics számára.

A tárolópéldányok adatainak a Log Analytics szolgáltatásba való elküldéséhez létre kell hozni egy tárolócsoportot az Azure CLI (vagy Cloud Shell) és egy YAML-fájl használatával. A következő szakaszok egy naplózható tárolócsoport létrehozásának és a naplók lekérdezésének menetét ismertetik.

## <a name="prerequisites"></a>Előfeltételek

A tárolópéldányokban való naplózás engedélyezéséhez a következőkre van szükség:

* [Log Analytics-munkaterület](../log-analytics/log-analytics-quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (vagy [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>A Log Analytics hitelesítő adatainak lekérése

Az Azure Container Instancesnek engedélyre van szüksége ahhoz, hogy adatokat küldhessen a Log Analytics-munkaterületre. Ennek az engedélynek a megadásához és a naplózás engedélyezéséhez a tárolócsoport létrehozásakor meg kell adnia a Log Analytics-munkaterület azonosítóját és egy ahhoz tartozó kulcsot (az elsődleges vagy a másodlagos kulcsot).

A Log Analytics-munkaterület azonosítóját és elsődleges kulcsát a következő módon kérheti le:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. A **BEÁLLÍTÁSOK** területen válassza ki a **Speciális beállítások** elemet
1. Válassza ki az **Összekapcsolt források** > **Windows-kiszolgálók** lehetőséget (vagy **Linux-kiszolgálók** lehetőséget – mindkettőhöz ugyanaz az azonosító és kulcs tartozik)
1. Jegyezze fel a következőket:
   * **MUNKATERÜLET AZONOSÍTÓJA**
   * **ELSŐDLEGES KULCS**

## <a name="create-container-group"></a>Tárolócsoport létrehozása

Most, hogy lekérte a Log Analytics-munkaterület azonosítóját és elsődleges kulcsát, készen áll arra, hogy létrehozzon egy naplózható tárolócsoportot. Az alábbi példa létrehoz egy tárolócsoportot, amelyben egyetlen [fluentd][fluentd] tároló található. A Fluentd tároló az alapértelmezett konfiguráció szerint több sornyi kimenetet hoz létre. Mivel ez a kimenet a Log Analytics-munkaterületre lesz elküldve, jól használható a naplók megtekintésének és lekérdezésének bemutatásához.

Először is másoljuk egy új fájlba az alábbi YAML-t, amely meghatároz egy tárolócsoportot, benne egyetlen tárolóval. Cserélje le a `LOG_ANALYTICS_WORKSPACE_ID` és `LOG_ANALYTICS_WORKSPACE_KEY` helyőrzőket az előző lépésben lekért értékekre, majd mentse a fájlt **deploy-aci.yaml** néven.

```yaml
apiVersion: 2018-06-01
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

Ezután futtassa az alábbi parancsokat a tárolócsoport üzembe helyezéséhez. Az `myResourceGroup` helyőrzőt cserélje le egy, az előfizetésében található erőforráscsoport nevére (vagy először hozzon létre egy „myResourceGroup” nevű erőforráscsoportot):

```azurecli-interactive
az container create -g myResourceGroup -n mycontainergroup001 -f deploy-aci.yaml
```

Röviddel a parancs kiadását követően választ kell kapnia az Azure-tól, amely az üzemelő példány részleteit taglalja.

## <a name="view-logs-in-log-analytics"></a>Naplók megtekintése a Log Analyticsben

A tárolócsoport üzembe helyezése után néhány percbe telik (legfeljebb 10 percbe), hogy az Azure Portalon megjelenjenek az első naplóbejegyzések. A tárolócsoport naplóinak megtekintéséhez nyissa meg a Log Analytics-munkaterületet, majd:

1. Az **OMS-munkaterület** áttekintésénél válassza ki a **Naplók keresése** lehetőséget
1. A **További kipróbálható lekérdezések** között válassza ki **Az összes összegyűjtött adat** hivatkozást

Ekkor számos eredményt kell megjelenítenie a `search *` lekérdezésnek. Ha elsőre nem lát eredményeket, várjon néhány percet, majd kattintson a **FUTTATÁS** gombra a lekérdezés újbóli végrehajtásához. Alapértelmezés szerint a naplóbejegyzések „Lista” nézetben jelennek meg. Válassza a **tábla** lehetőséget a tömörebb formában való megjelenítéshez. Ezután a sorokat kibontva tekintheti meg az egyes naplóbejegyzések tartalmát.

![Naplókeresési találatok az Azure Portalon][log-search-01]

## <a name="query-container-logs"></a>Tárolónaplók lekérdezése

A Log Analytics egy kiterjedt [lekérdezési nyelvet][query_lang] is tartalmaz, amely akár több ezer sornyi naplókimenetből is képes kivonni információkat.

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

### <a name="log-analytics"></a>Log Analytics

További tudnivalók a naplók lekérdezéséről és a riasztások beállításáról az Azure Log Analyticsben:

* [A Log Analytics naplókereséseinek ismertetése](../log-analytics/log-analytics-log-search.md)
* [Egyesített riasztások az Azure Monitorban](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)

### <a name="monitor-container-cpu-and-memory"></a>Tárolók processzor- és memóriahasználatának monitorozása

A tárolópéldányok processzor- és memória-erőforrásainak monitorozásával kapcsolatos bővebb információkért lásd:

* [Tároló-erőforrások monitorozása az Azure Container Instancesben](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://docs.loganalytics.io/

<!-- LINKS - Internal -->