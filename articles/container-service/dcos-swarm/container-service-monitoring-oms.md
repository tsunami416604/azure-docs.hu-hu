---
title: A figyelő Azure DC/OS fürtben - műveletek kezelése
description: Egy Azure tároló szolgáltatás DC/OS fürtben Naplóelemzési a figyelheti.
services: container-service
author: keikhara
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: ba76f8480dedb37326505f7ed756eb51a41ee0fe
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>Egy Azure tároló szolgáltatás DC/OS fürtben, a Naplóelemzési figyelése

A Naplóelemzési a Microsoft felhőalapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra. Tároló egy olyan megoldás a Naplóelemzési, amely segít a tároló szoftverleltár, a teljesítmény és a naplók megtekintéséhez egyetlen helyen. Naplózási, tárolók hibaelhárítás a naplók megtekintése központi helyen, és zajos fel felesleges tároló egy gazdagépen található.

![](media/container-service-monitoring-oms/image1.png)

A tároló megoldásról további információkért tekintse meg a [tároló megoldás Naplóelemzési](../../log-analytics/log-analytics-containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>A DC/OS universe a Naplóelemzési beállítása


Ez a cikk feltételezi, hogy a DC/OS beállítását, és egyszerű webes tároló alkalmazást a fürtön telepített.

### <a name="pre-requisite"></a>Előfeltétel
- [A Microsoft Azure-előfizetés](https://azure.microsoft.com/free/) -szabad szerezni ez.  
- Napló Analytics-munkaterület beállítása – lásd a "3. lépés" alatt
- [DC/OS parancssori felület](https://dcos.io/docs/1.8/usage/cli/install/) telepítve.

1. A DC/OS-irányítópultot kattintson a Universe, és keressen a "OMS" alább látható módon.

![](media/container-service-monitoring-oms/image2.png)

2. Kattintson az **Install** (Telepítés) gombra. Látni fogja pop fel a fájlverzió-információkat, és egy **csomag telepítése** vagy **speciális telepítési** gombra. Elemre **speciális telepítési**, amely vezet, hogy a **OMS konfigurációs tulajdonságok** lap.

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. Itt kérni fogja írni a `wsid` (a Naplóelemzési munkaterület azonosítója) és `wskey` (elsődleges kulcsát a munkaterület azonosítója). Mindkét beolvasandó `wsid` és `wskey` fiók létrehozásához szükséges <https://mms.microsoft.com>.
Kérjük, kövesse a lépéseket egy fiók létrehozásához. Ha elkészült a fiók létrehozását, be kell szereznie a `wsid` és `wskey` kattintva **beállítások**, majd **csatlakoztatott források**, majd **Linux kiszolgálók**lent látható módon.

 ![](media/container-service-monitoring-oms/image5.png)

4. Válassza ki a számos esetben szeretne, és kattintson a "Áttekintése és telepítése" gombra. Általában érdemes a virtuális gép van az ügynök fürt száma egyenlő példányok száma rendelkezik. Linux OMS-ügynököt telepíti, minden egyes virtuális gépen, amely a figyelés és naplózás adatokat gyűjthet kíván egyedi tárolóként.

## <a name="setting-up-a-simple-oms-dashboard"></a>Egy egyszerű OMS irányítópult beállítása

Miután telepítette az OMS-ügynököt a virtuális gépeken Linux, tovább állíthatja be az OMS-irányítópult. Ehhez két módja van: OMS-portálon vagy az Azure portálon.

### <a name="oms-portal"></a>OMS-portálon 

Jelentkezzen be az OMS-portálon (<https://mms.microsoft.com>), és navigáljon a **megoldás gyűjtemény**.

![](media/container-service-monitoring-oms/image6.png)

Miután belépett a **megoldás gyűjtemény**, jelölje be **tárolók**.

![](media/container-service-monitoring-oms/image7.png)

Miután a tároló megoldás kijelölt, látni fogja a csempe az OMS áttekintése irányítópult-oldalon. Miután a feldolgozott adatai egy indexelt, megjelenik a csempe a megoldás nézet csempék adatokkal feltöltve.

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>Azure Portal 

Az Azure portálon, a bejelentkezés <https://portal.microsoft.com/>. Nyissa meg a **piactér**, jelölje be **figyelés + felügyeleti** kattintson **tekintse meg az összes**. Írja be `containers` keresésben. A keresési eredmények "tárolók" jelenik meg. Válassza ki **tárolók** kattintson **létrehozása**.

![](media/container-service-monitoring-oms/image9.png)

Miután rákattintott **létrehozása**, akkor kérni fogja a munkaterületen. A munkaterület kiválasztása, vagy ha nem rendelkezik ilyennel, hozzon létre egy új munkaterületet.

![](media/container-service-monitoring-oms/image10.PNG)

A munkaterület kijelölése után kattintson **létrehozása**.

![](media/container-service-monitoring-oms/image11.png)

A napló Analytics tároló megoldásról további információkért tekintse meg a [tároló megoldás Naplóelemzési](../../log-analytics/log-analytics-containers.md).

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>Az ACS a DC/OS OMS-ügynököt méretezése 

Abban az esetben kell a tényleges csomópontok száma kevés OMS-ügynököt telepítette, vagy adja hozzá a további VM skálázás be VMSS, ehhez skálázással a `msoms` szolgáltatás.

Nyissa meg a Marathon vagy a DC/OS felhasználói felületének Services lapra, és növelheti a csomópontok száma.

![](media/container-service-monitoring-oms/image12.PNG)

Ez fog üzembe helyezni, más csomópontok, amelyek még nem telepítették az OMS-ügynököt.

## <a name="uninstall-ms-oms"></a>MS OMS eltávolítása

MS OMS eltávolításához adja meg a következő parancsot:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Ossza meg velünk!!!
Mi működik? Mi az a hiányzó? Milyen hiba van szüksége a lehet hasznos, ha Ön? Ossza meg velünk <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>További lépések

 Most, hogy beállítása Naplóelemzési a tárolók figyelése[tekintse meg a tároló irányítópult](../../log-analytics/log-analytics-containers.md).
