---
title: (ELAVULT) A figyelő Azure DC/OS fürt - műveletek kezelése
description: Egy Azure Container Service DC/OS fürt megfigyelése a Log Analytics.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 290141136672729060f5156d645c47ac303fa0c3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110970"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(ELAVULT) Egy Azure Container Service DC/OS fürt megfigyelése a Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A log Analytics a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúrára. Tároló megoldás egy olyan megoldás a Log Analyticsben, így a segítségével egyetlen helyen, a tároló készlet, a teljesítmény és a naplók megtekintéséhez. Naplózási, tárolók hibaelhárítása központi helyen a naplóinak megtekintésével és zajos felhasználása felesleges tároló-gazdagépen található.

![](media/container-service-monitoring-oms/image1.png)

Tároló megoldásról további információkért lásd: a [tároló megoldás a Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>A DC/OS universe rendszerben a Log Analytics beállítása


Ez a cikk feltételezi, hogy beállította-e egy DC/OS és egyszerű webes tároló alkalmazást a fürtön telepített.

### <a name="pre-requisite"></a>Előfeltétel
- [A Microsoft Azure-előfizetés](https://azure.microsoft.com/free/) -előfizetés ingyenes beszerzése.  
- Log Analytics-munkaterület beállítása – lásd: "3. lépés" alatt
- [DC/OS parancssori felület](https://docs.mesosphere.com/1.12/cli) telepítve.

1. A DC/OS irányítópultján kattintson a Universe, és keressen a "OMS" alább látható módon.

   >[!NOTE]
   >OMS most már a Log Analytics nevezik.

   ![](media/container-service-monitoring-oms/image2.png)

2. Kattintson az **Install** (Telepítés) gombra. Megjelenik egy előugró ablak, és a fájlverzió-információkat és a egy **telepítőcsomag** vagy **speciális telepítési** gombra. Amikor rákattint **speciális telepítési**, amely vezet, hogy a **OMS konfigurációs tulajdonságok** lap.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Itt meg kell adnia a `wsid` (a Log Analytics-munkaterület Azonosítójára) és `wskey` (a munkaterület-azonosító elsődleges kulcsát). Mindkét beolvasásához `wsid` és `wskey` szeretne létrehozni egy fiókot a <https://mms.microsoft.com>.
   A lépésekkel hozzon létre egy fiókot. Ha elkészült hoz létre a fiókot, be kell szereznie a `wsid` és `wskey` kattintva **beállítások**, majd **csatlakoztatott források**, majd **Linux-kiszolgálók**lent látható módon.

   ![](media/container-service-monitoring-oms/image5.png)

4. Válassza ki a példányok, hogy szeretné, és kattintson a "Áttekintése és telepítése" gombra. Általában érdemes szeretné, hogy a virtuális gép már van az ügynök fürtben száma egyenlő a példányok számát. Linuxhoz készült log Analytics-ügynök telepítése, az egyes virtuális Gépeken, amely figyelési adatait és a naplózási információk gyűjtéséhez szeretné az egyes tárolók.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Egy egyszerű Log Analytics-irányítópult beállítása

Amint telepítette a Linuxhoz készült Log Analytics-ügynököt a virtuális gépeken, a következő lépés az a Log Analytics-irányítópult beállítása. Beállíthatja az irányítópultot az Azure Portalon keresztül.

### <a name="azure-portal"></a>Azure Portal 

Jelentkezzen be az Azure Portalra a <https://portal.microsoft.com/>. Lépjen a **Marketplace**válassza **figyelés + felügyelet** kattintson **tekintse meg az összes**. Írja be `containers` kifejezést a keresőmezőbe. "Tárolók" a keresési eredmények között megjelenik. Válassza ki **tárolók** kattintson **létrehozás**.

![](media/container-service-monitoring-oms/image9.png)

Miután rákattint **létrehozás**, a munkaterület fog kérni. Válassza ki a munkaterületet, vagy ha nem rendelkezik egy, hozzon létre egy új munkaterületet.

![](media/container-service-monitoring-oms/image10.PNG)

Miután kiválasztotta a munkaterületet, kattintson a **létrehozás**.

![](media/container-service-monitoring-oms/image11.png)

A Log Analytics megoldás kapcsolatos további információkért tekintse meg a [tároló megoldás a Log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Log Analytics-ügynököket az ACS DC/OS méretezése 

Abban az esetben kell a tényleges csomópontok száma nem éri el a Log Analytics-ügynök telepítve van, vagy, a rendszer több virtuális gép hozzáadásával virtuálisgép-méretezési vertikális felskálázása, megteheti felskálázásával a `msoms` szolgáltatás.

Látogasson el a Marathon vagy a DC/OS felhasználói felület lapon, és növelheti a csomópontok száma.

![](media/container-service-monitoring-oms/image12.PNG)

Ez más csomópontokhoz, amelyek még nem helyezte üzembe a Log Analytics-ügynököket telepíti.

## <a name="uninstall-ms-oms"></a>Távolítsa el az MS OMS

Távolítsa el az MS OMS adja meg a következő parancsot:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Tudassa velünk, hogy!!!
Mi működik? Mi a hiányzó? Mit kell ehhez az Ön számára hasznos lehet? Tudassa velünk <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>További lépések

 Most, hogy úgy állította be a Log Analytics szolgáltatással figyelheti a tárolókat[tekintse meg a tároló irányítópultján](../../azure-monitor/insights/containers.md).
