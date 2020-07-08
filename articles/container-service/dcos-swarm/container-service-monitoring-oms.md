---
title: ELAVULT Az Azure DC/OS-fürt – Operations Management figyelése
description: Azure Container Service DC/OS-fürt figyelése Log Analyticssal.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76277322"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>ELAVULT Azure Container Service DC/OS-fürt figyelése Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A Log Analytics a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a helyszíni és a Felhőbeli infrastruktúra kezelésében és biztonságában.A Container Solution Log Analytics megoldás, amely segít megtekinteni a tárolók leltározását, teljesítményét és naplóit egyetlen helyen. Naplózhatja a tárolók hibakeresését, ha megtekinti a naplókat a központi helyen, és a gazdagépen megkeresi a felesleges tárolót.

![](media/container-service-monitoring-oms/image1.png)

A tároló megoldással kapcsolatos további információkért tekintse meg a [Container solution log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Log Analytics beállítása a DC/OS-univerzumból


Ez a cikk azt feltételezi, hogy beállított egy DC/OS-t, és üzembe helyezett egy egyszerű webes tároló alkalmazást a fürtön.

### <a name="pre-requisite"></a>Előfeltétel
- [Microsoft Azure előfizetés](https://azure.microsoft.com/free/) – ingyenes előfizetést kaphat.  
- Log Analytics munkaterület beállítása – lásd: "3. lépés"
- A [DC/os CLI](https://docs.mesosphere.com/1.12/cli) telepítve van.

1. A DC/OS irányítópulton kattintson az univerzum elemre, és keressen rá az "OMS" kifejezésre az alábbi ábrán látható módon.

   >[!NOTE]
   >A OMS már Log Analytics néven is ismert.

   ![](media/container-service-monitoring-oms/image2.png)

2. Kattintson a **telepítés**gombra. Ekkor megjelenik egy előugró ablak, amely tartalmazza a verziószámot és a telepítési **csomagot** vagy a **speciális telepítést** . Ha rákattint a **speciális telepítés**lehetőségre, amely a **OMS-specifikus konfigurációs tulajdonságok** lapra mutat.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Itt meg kell adnia a `wsid` (log Analytics munkaterület azonosítóját) és `wskey` (a munkaterület azonosítójának elsődleges kulcsát). Ha mindkettőre `wsid` `wskey` szüksége van, és létre kell hoznia egy fiókot a következő helyen: <https://mms.microsoft.com> .
   A fiók létrehozásához kövesse a lépéseket. Ha elkészült a fiók létrehozásával, be kell szereznie és a `wsid` `wskey` **Beállítások**, majd a **csatlakoztatott források**, majd a Linux- **kiszolgálók**elemre kell kattintania az alább látható módon.

   ![](media/container-service-monitoring-oms/image5.png)

4. Válassza ki a kívánt példányok számát, és kattintson a felülvizsgálat és telepítés gombra. Általában azt szeretné, hogy a példányok száma megegyezzen az ügynök fürtjében lévő virtuális gépek számával. A Linux rendszerhez készült Log Analytics-ügynök különálló tárolóként települ minden virtuális gépen, amely információkat szeretne gyűjteni a figyelési és naplózási adatokhoz.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Egyszerű Log Analytics irányítópult beállítása

Miután telepítette a Linux rendszerhez készült Log Analytics-ügynököt a virtuális gépeken, a következő lépés az Log Analytics irányítópult beállítása. Az irányítópultot Azure Portal használatával állíthatja be.

### <a name="azure-portal"></a>Azure Portal 

Jelentkezzen be Azure Portal a következő címen: <https://portal.microsoft.com/> . Lépjen a **piactérre**, válassza a **figyelés + felügyelet** elemet, majd kattintson **az összes**megjelenítése elemre. Ezután írja be `containers` a keresést. A keresési eredmények között megjelenik a "tárolók". Válassza a **tárolók** lehetőséget, majd kattintson a **Létrehozás**gombra.

![](media/container-service-monitoring-oms/image9.png)

Ha a **Létrehozás**gombra kattint, a rendszer megkérdezi a munkaterületet. Válassza ki a munkaterületet, vagy ha még nem rendelkezik ilyennel, hozzon létre egy új munkaterületet.

![](media/container-service-monitoring-oms/image10.PNG)

Miután kiválasztotta a munkaterületet, kattintson a **Létrehozás**gombra.

![](media/container-service-monitoring-oms/image11.png)

A Log Analytics Container megoldással kapcsolatos további információkért tekintse meg a [Container solution log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Log Analytics-ügynök méretezése az ACS DC/OS-vel 

Ha a csomópontok tényleges száma vagy a virtuálisgép-méretezési csoport méretezése több virtuális gép hozzáadásával történik, akkor ezt a szolgáltatás skálázásával teheti meg `msoms` . log Analytics

A Marathon vagy a DC/OS felhasználói felületi szolgáltatások lapon megtekintheti a csomópontok számának felskálázását.

![](media/container-service-monitoring-oms/image12.PNG)

Ez olyan csomópontokon fog települni, amelyek még nem telepítették a Log Analytics-ügynököt.

## <a name="uninstall-ms-oms"></a>MS-OMS eltávolítása

Az MS OMS eltávolításához írja be a következő parancsot:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Tudassa velünk!!!
Mi működik? Mi hiányzik? Mire van szüksége ahhoz, hogy hasznos legyen az Ön számára? Tudassa velünk a következő címen: <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>További lépések

 Most, hogy beállította Log Analytics a tárolók figyelésére,[tekintse meg a tároló irányítópultját](../../azure-monitor/insights/containers.md).
