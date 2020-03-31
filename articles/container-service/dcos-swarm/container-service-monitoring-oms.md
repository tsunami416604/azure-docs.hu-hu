---
title: (ELAVULT) Az Azure DC/OS-fürt figyelése – Üzemeltetéskezelés
description: Egy Azure Container Service DC/OS-fürt figyelése a Log Analytics segítségével.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277322"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(ELAVULT) Azure Container Service DC/OS-fürt figyelése a Log Analytics szolgáltatással

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A Log Analytics a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a helyszíni és a felhőalapú infrastruktúra kezelésében és védelmében.A Container Solution egy megoldás a Log Analytics szolgáltatásban, amely segít a tárolókészlet, a teljesítmény és a naplók egyetlen helyen való megtekintésében. A tárolók naplózása, a tárolók hibaelhárítása a központi helyen lévő naplók megtekintésével, és zajos fogyasztású felesleges tárolót találhat a gazdagépen.

![](media/container-service-monitoring-oms/image1.png)

A Container Solution szolgáltatásról további információt a [Container Solution Log Analytics című témakörben talál.](../../azure-monitor/insights/containers.md)

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>A Log Analytics beállítása a DC/OS univerzumból


Ez a cikk feltételezi, hogy beállított egy tartományvezérlőt/operációs rendszert, és egyszerű webtároló-alkalmazásokat telepített a fürtön.

### <a name="pre-requisite"></a>Előfeltétel
- [Microsoft Azure-előfizetés](https://azure.microsoft.com/free/) – Ingyenes előfizetést kaphat.  
- Log Analytics-munkaterület beállítása – lásd alább a "3.
- [DC/OS CLI](https://docs.mesosphere.com/1.12/cli) telepítve.

1. A DC/OS irányítópulton kattintson az Univerzumra, és keressen az "OMS" kifejezésre az alábbiak szerint.

   >[!NOTE]
   >Az OMS-t most log analytics-nek nevezzük.

   ![](media/container-service-monitoring-oms/image2.png)

2. Kattintson a **Telepítés gombra.** Megjelenik egy előugró ablak a verzióadatokkal, valamint egy **Telepítési csomag** vagy **speciális telepítés** gomb. Ha a **Speciális telepítés gombra**kattint, amely az **OMS-specifikus konfigurációs tulajdonságok** laphoz vezet.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Itt meg kell adnia `wsid` a (A Log Analytics munkaterület-azonosító) és `wskey` (a munkaterület-azonosító elsődleges kulcsa). Ahhoz, `wsid` `wskey` hogy mindkettőt beszerezd, és létre kell hoznod egy fiókot a(z) szinten. <https://mms.microsoft.com>
   A fiók létrehozásához kövesse a lépéseket. Miután befejezte a fiók létrehozását, be `wsid` `wskey` kell szereznie a **fiókját,** és a Beállítások gombra kattintva , majd **a Csatlakoztatott források**, majd a Linux **szerverek**, az alábbiak szerint.

   ![](media/container-service-monitoring-oms/image5.png)

4. Válassza ki a kívánt példányok számát, és kattintson a "Véleményezés és telepítés" gombra. Általában azt szeretné, hogy a példányok száma megegyezik a virtuális gépek száma van az ügynökfürtben. A Log Analytics-ügynök Linuxhoz minden olyan virtuális gépre egyedi tárolóként települ, amely et adatokat szeretne gyűjteni az adatok figyeléséhez és naplózásához.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Egyszerű Log Analytics-irányítópult beállítása

Miután telepítette a Log Analytics-ügynök Linux a virtuális gépeken, a következő lépés a Log Analytics irányítópult beállítása. Az irányítópultot az Azure Portalon keresztül állíthatja be.

### <a name="azure-portal"></a>Azure portál 

Jelentkezzen be az <https://portal.microsoft.com/>Azure Portalra a itt. Nyissa meg a **Piacteret,** válassza **a Figyelés + kezelés** lehetőséget, és kattintson az Összes **megtekintése**gombra. Ezután `containers` írja be a keresést. A keresési eredmények között megjelenik a "tárolók". Válassza a **Tárolók lehetőséget,** és kattintson a **Létrehozás gombra.**

![](media/container-service-monitoring-oms/image9.png)

Miután a **Létrehozás gombra**kattintott, a munkaterületet fogja kérni. Válassza ki a munkaterületet, vagy ha nem rendelkezik ilyentel, hozzon létre egy új munkaterületet.

![](media/container-service-monitoring-oms/image10.PNG)

Miután kiválasztotta a munkaterületet, kattintson a **Létrehozás gombra.**

![](media/container-service-monitoring-oms/image11.png)

A Log Analytics-tárolómegoldással kapcsolatos további információkért tekintse meg a [Container Solution Log Analytics](../../azure-monitor/insights/containers.md)című.

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>A Log Analytics-ügynök méretezése ACS DC/OS-vel 

Abban az esetben, ha a tényleges csomópontszám tól nem kell telepítenie a Log Analytics-ügynököt, vagy több `msoms` virtuális gép hozzáadásával felskálázza a virtuális gép méretezését, ezt a szolgáltatás méretezésével teheti meg.

A Marathon vagy a DC/OS felhasználói felületi szolgáltatások lapon felskálázhatja a csomópontok számát.

![](media/container-service-monitoring-oms/image12.PNG)

Ez olyan csomópontokra lesz telepítve, amelyek még nem telepítették a Log Analytics-ügynököt.

## <a name="uninstall-ms-oms"></a>MS OMS eltávolítása

Az MS OMS eltávolításához írja be a következő parancsot:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Tudassa velünk!!!
Mi működik? Mi hiányzik? Mi kell még ahhoz, hogy ez hasznos legyen az Ön számára? Tudassa velünk az <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>További lépések

 Most, hogy beállította a Log Analytics a tárolók figyelésére,[tekintse meg a tároló irányítópultját.](../../azure-monitor/insights/containers.md)
