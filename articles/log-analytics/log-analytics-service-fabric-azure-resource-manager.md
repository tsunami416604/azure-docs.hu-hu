---
title: Mérje fel a Service Fabric-alkalmazásokat a Log Analytics az Azure portal használatával |} A Microsoft Docs
description: A Service Fabric-megoldás a Log Analytics az Azure portal segítségével felmérheti a kockázatait és állapotát a Service Fabric alkalmazások, a mikroszolgáltatások, az csomópontokat és fürtöket is használhatja.
services: log-analytics
documentationcenter: ''
author: niniikhena
manager: jochan
editor: ''
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: nini
ms.component: na
ms.openlocfilehash: 9bc1f7d9eab73a086e664dcc520ecf26befbbf0e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432891"
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Mérje fel a Service Fabric-alkalmazásokat és a mikroszolgáltatások és az Azure portal

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![A Service Fabric szimbólum](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

Ez a cikk ismerteti, hogyan használható a Service Fabric megoldást a Log Analytics azonosítása és elhárítása a Service Fabric-fürt között.

A Service Fabric-megoldás által az adatok gyűjtése az Azure WAD táblából a Service Fabric virtuális gépek az Azure Diagnostics adatait használja. A log Analytics majd beolvassa a Service Fabric keretrendszer eseményeket, többek között **Reliable Service-események**, **szereplő események**, **működési eseményeit**, és **egyéni ETW-események**. A megoldás irányítópultján, az Ön jelentős problémák és kapcsolódó eseményeket megtekintheti a Service Fabric-környezetben.

Első lépések a megoldás, a Service Fabric-fürthöz csatlakozhat a Log Analytics-munkaterület szükséges. Az alábbiakban három forgatókönyvet kell figyelembe venni:

1. Ha nem telepítette a Service Fabric-fürthöz, kövesse ***egy Log Analytics-munkaterülethez csatlakozik egy Service Fabric-fürt üzembe helyezése*** helyezhet üzembe egy új fürtöt, és nincs úgy konfigurálva, hogy a jelentés a Log Analytics szolgáltatásba.
1. Ha más felügyeleti megoldásokkal, például biztonsági használata a Service Fabric-fürtön a gazdagépekről a teljesítményszámlálók adatainak összegyűjtése van szüksége, kövesse a ***Virtuálisgép-bővítménnyel Log Analytics-munkaterület csatlakoztatva egy Service Fabric-fürt üzembe helyezése telepítve van.***
1. Ha már telepítette a Service Fabric-fürt és a Log Analytics csatlakozni szeretne, kövesse a ***hozzáadása egy meglévő tárfiókot a Log Analytics szolgáltatásba.***

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Csatlakoztatott Log Analytics-munkaterület Service Fabric-fürt üzembe helyezése.
Ez a sablon a következőket teszi:

1. Üzembe helyez egy Azure Service Fabric-fürtön a Log Analytics-munkaterülethez már csatlakoztatott. Hozzon létre egy új munkaterületet a sablon üzembe helyezése során lehetősége van, vagy adjon meg egy már meglévő Log Analytics-munkaterület nevét.
1. A diagnosztikai tárfiók hozzáadása a Log Analytics-munkaterületet.
1. Lehetővé teszi, hogy a Service Fabric-megoldás a Log Analytics-munkaterület.

[![Üzembe helyezés az Azure-ban](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Miután a telepítés gombot választja, megnyílik az Azure portal szerkesztését a paraméterekkel. Ügyeljen arra, hogy hozzon létre egy új erőforráscsoportot, ha, adjon meg egy új Log Analytics-munkaterület neve:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Fogadja el a jogi feltételeket, és kattintson a **létrehozás** a üzembe helyezésének megkezdéséhez. Ha a telepítés befejeződött, megjelenik az új munkaterületet és a létrehozott fürt, és a WADServiceFabric * hozzáadott esemény, WADWindowsEventLogs és WADETWEvent táblák:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Csatlakoztatott Log Analytics-munkaterület Virtuálisgép-bővítmény telepítve van a Service Fabric-fürt üzembe helyezése.

Ez a sablon a következőket teszi:

1. Üzembe helyez egy Azure Service Fabric-fürtön a Log Analytics-munkaterülethez már csatlakoztatott. Hozzon létre egy új munkaterületet, vagy használjon egy meglévőt.
1. A diagnosztikai tárfiókok hozzáadása a Log Analytics-munkaterületet.
1. Lehetővé teszi, hogy a Service Fabric-megoldás a Log Analytics-munkaterületen.
1. Telepíti az MMA-ügynök bővítmény beállítása Service Fabric-fürtben az egyes virtuálisgép-méretezési csoport. Az MMA-ügynök telepítve van, az Ön megtekintheti a csomópontok kapcsolatos teljesítmény-mérőszámon.

[![Üzembe helyezés az Azure-ban](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Ugyanazokat a lépéseket a fenti adjon meg a szükséges paramétereket a elindít egy központi telepítést. Ismét megjelenik az új munkaterületet, a fürt és a minden létrehozott WAD táblák:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Teljesítményadatok megtekintéséhez

Teljesítményadatok megjelenítése a csomópontokról:


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Nyissa meg a Log Analytics-munkaterületet az Azure Portalról.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- Nyissa meg a bal oldali panelen beállításait, és válassza az adatok >> Windows-teljesítményszámlálók >> "A kijelölt teljesítményszámlálók felvétele": ![Service Fabric](./media/log-analytics-service-fabric/7.png)
- A Naplókeresés használja a következő lekérdezéseket elmélyedne a csomópontok fontosabb metrikákat:

    a. Hasonlítsa össze az átlagos processzorhasználat az összes csomópont az elmúlt egy órában, melyik csomópontokon problémák léptek fel, és milyen időközönként csomópont kellett ugrásszerű:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Hasonló vonaldiagramok a rendelkezésre álló memória megtekintéséhez a lekérdezéshez minden egyes csomóponton:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Összes csomópontot, a pontos átlagos érték megjelenítése a rendelkezésre álló memória az egyes csomópontok listájának megtekintéséhez használja a lekérdezés:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. A részletesebb adatainak megjelenítése egy adott csomópont a óránkénti átlagos, minimális, maximális és 75 – PERCENTILIS CPU-használat megvizsgálásával kívánt esetben Ön tudja végrehajtani ezt a lekérdezést (cserélje le a Computer mezőre) használatával:

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

További információt szeretne a Log Analytics-, teljesítmény-mérőszámokat a [Operations Management Suite blog](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>A Log Analytics hozzáadása egy meglévő tárfiókot

Ez a sablon egyszerűen ad a meglévő tárfiókok egy új vagy meglévő Log Analytics-munkaterületet.

[![Üzembe helyezés az Azure-ban](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Featured egy erőforráscsoportot, ha dolgozik egy már meglévő Log Analytics-munkaterületet, jelölje be "Meglévő használata", és keresse meg a Log Analytics-munkaterületet tartalmazó erőforráscsoportot. Hozzon létre egy új egy esetén más módon.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

Ez a sablon üzembe helyezését követően lesz tekintse meg a tárfiókot, a Log Analytics-munkaterülethez csatlakozik. Ebben a példában egy további tárfiókot a fent létrehozott Exchange-munkaterülethez hozzáadott.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>A Service Fabric-események megtekintése

A központi telepítések végezhető el, és a Service Fabric-megoldás engedélyezve van a munkaterületen, válassza ki a **Service Fabric** csempéje a Log Analytics-portál a Service Fabric-irányítópult elindításához szükséges. Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Mindegyik oszlop felsorolja a 10 leggyakoribb események a megadott időtartományban adott oszlop feltételeknek megfelelő száma szerint. Egy naplókeresést, amely a teljes listáját jeleníti meg kattintva futtathatja **összes** alján jobb oldalon az egyes oszlopok, vagy kattintson az oszlop fejlécére.

| **A Service Fabric-esemény** | **description** |
| --- | --- |
| Jelentős problémák |Megjeleníti a problémákat, például RunAsyncFailures RunAsynCancellations és csomópont olyan plusz lehetőségeket. |
| A működési események |Például az alkalmazás frissítése és központi telepítések jelentős működési eseményeit. |
| Reliable Services eseményei |Reliable Services jelentős események egy ilyen Runasyncinvocations. |
| Actors-események |A mikroszolgáltatások, például-aktormetódus, actor aktiválások és deactivations és így tovább által okozott kivételek feldolgozását által generált jelentős szereplő események. |
| Alkalmazásesemények |Minden egyéni ETW által előállított eseményeket az alkalmazások. |

![A Service Fabric-irányítópult](./media/log-analytics-service-fabric/sf3.png)

![A Service Fabric-irányítópult](./media/log-analytics-service-fabric/sf4.png)

Az alábbi táblázat adatgyűjtési módszerek és egyéb hogyan adatgyűjtés a Service fabric részleteit.

| Platform | Közvetlen ügynök | Operations Manager-ügynök | Azure Storage | Az Operations Manager szükséges? | A felügyeleti csoport Operations Manager-ügynök adatok küldött | Gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 perc |

> [!NOTE]
> Ezek az események a Service Fabric-megoldásban hatókörének kattintva módosíthatja **adatok alapján az elmúlt 7 napban** az irányítópult tetején. Az elmúlt hét nap, egy nap vagy 6 órán belül létrehozott eseményeket is megjelenítheti. Másik lehetőségként kiválaszthatja **egyéni** egyéni dátumtartomány megadása.
>
>

## <a name="next-steps"></a>További lépések

* Használat [Naplókeresések a Log Analytics](log-analytics-log-searches.md) Service Fabric-esemény részletes adatainak megtekintéséhez.
