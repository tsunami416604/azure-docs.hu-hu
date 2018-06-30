---
title: Service Fabric-alkalmazások az Azure portál használatával Naplóelemzési értékeléséhez |} Microsoft Docs
description: A Service Fabric-megoldás az Azure portál használata a kockázat és a Service Fabric alkalmazások, a micro-szolgáltatások, az csomópontokat és fürtöket állapotának felmérésére Naplóelemzési használható.
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
ms.openlocfilehash: 8296f0756aef7180efa777795cb361e653c0e4e3
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128013"
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Mérje fel a Service Fabric-alkalmazások és micro-szolgáltatások és az Azure portál

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![A Service Fabric szimbólum](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

A cikkből megtudhatja, hogyan lehet Naplóelemzési a Service Fabric-megoldás segítségével azonosítása és elhárítása a Service Fabric-fürt között.

A Service Fabric-megoldás Azure diagnosztikai adatokat a Service Fabric virtuális gépek által az adatok gyűjtése az Azure ÜVEGVATTA táblát használ. A Naplóelemzési ezután beolvassa a Service Fabric keretrendszer eseményeihez, beleértve a **megbízható szolgáltatás események**, **szereplő események**, **működési eseményeit**, és **egyéni ETW-események**. A megoldás irányítópultja is jelentős problémák és kapcsolódó eseményeket megtekintése a Service Fabric-környezetben.

A kezdéshez a megoldással akkor csatlakoztatni kell a Service Fabric-fürt a Naplóelemzési munkaterület. Az alábbiakban a három forgatókönyv kell figyelembe venni:

1. Ha nem telepítette a Service Fabric-fürt, lépésekkel ***központi telepítése a Service Fabric-fürt csatlakozik a Naplóelemzési munkaterület*** egy új fürt központi telepítése, és jelentést Naplóelemzési konfigurálnia.
2. Ha a teljesítményszámlálók gyűjtése a gazdagépek más felügyeleti megoldások, például biztonsági a Service Fabric-fürt használatára van szüksége, kövesse a ***központi telepítése a Service Fabric-fürt csatlakozik egy Virtuálisgép-bővítmény Naplóelemzési munkaterület telepítve.***
3. Ha már telepítette a Service Fabric-fürt és a kívánt Log Analyticshez való csatlakozáshoz, kövesse a lépéseket ***Naplóelemzési egy meglévő tárfiók hozzáadása.***

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Telepítse a Service Fabric-fürt a Naplóelemzési munkaterület csatlakozik.
Ez a sablon a következőket teszi:

1. Az Azure Service Fabric-fürt már kapcsolódik a Naplóelemzési munkaterület telepíti. Hozzon létre egy új munkaterületet, a sablon telepítése során is, vagy egy már meglévő Naplóelemzési munkaterület nevét.
2. A diagnosztikai tárfiók hozzáadása a Naplóelemzési munkaterület.
3. Lehetővé teszi, hogy a Service Fabric megoldás Naplóelemzési munkaterületet.

[![Üzembe helyezés az Azure-ban](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Miután a telepítés gombot választja, az Azure-portálon szerkeszthetők paramétereinek nyílik meg. Győződjön meg arról, hogy hozzon létre egy új erőforráscsoportot, ha egy új nevet a Naplóelemzési munkaterület:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Fogadja el a jogi feltételeket, és kattintson a **létrehozása** a telepítés elindításához. Ha a telepítés befejeződött, megtekintheti az új munkaterületet, és a fürt létrehozása, és a WADServiceFabric * hozzáadott esemény, WADWindowsEventLogs és WADETWEvent táblák:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>A Naplóelemzési munkaterület csatlakozik Virtuálisgép-bővítmény telepítése a Service Fabric-fürt központi telepítése.

Ez a sablon a következőket teszi:

1. Az Azure Service Fabric-fürt már kapcsolódik a Naplóelemzési munkaterület telepíti. Hozzon létre egy új munkaterületet, vagy használjon egy meglévőt.
2. A diagnosztikai tárfiók hozzáadása a Naplóelemzési munkaterület.
3. Lehetővé teszi, hogy a Service Fabric-megoldás a Naplóelemzési munkaterület.
4. Állítsa be a Service Fabric-fürt minden egyes virtuálisgép-méretezési az MMA ügynök bővítmény telepíti. Telepítve van az MMA ügynök megtekintheti a csomópontokkal kapcsolatos teljesítménymutatók.

[![Üzembe helyezés az Azure-ban](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

A fenti azonos lépéseket adjon meg a szükséges paramétereket, és a központi telepítés indítsa. Ismét megjelenik az új munkaterületet, a fürt és a ÜVEGVATTA táblák összes létrehozott:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Teljesítményadatok

Teljesítményadatok megtekintéséhez a csomópontjáról:


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Indítsa el a Naplóelemzési munkaterület Azure-portálról.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- A bal oldali ablaktáblában válassza a beállítások, és válassza ki az adatok >> Windows teljesítményszámlálók >> "A kijelölt teljesítményszámlálók felvétele": ![Service Fabric](./media/log-analytics-service-fabric/7.png)
- A naplófájl-keresési használja az alábbi lekérdezéseket a csomópontokkal kapcsolatos alapvető metrikákat elmélyedhet:

    a. Hasonlítsa össze az átlagos processzorhasználat minden csomópontra az elmúlt egy órában, mely csomópontok problémát tapasztal, és milyen időközönként csomópont kellett egy csúcs:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Megtekintheti a rendelkezésre álló memória hasonló vonaldiagramok lekérdezéshez minden egyes csomóponton:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Ez a lekérdezés segítségével a csomópontjait, pontos átlagos értékét bemutató a rendelkezésre álló memória (MB) az egyes csomópontok listájának megjelenítése:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. Abban az esetben részletekbe menően tárhatják fel egy adott csomópont a óránkénti átlag, minimális, maximális és 75-PERCENTILIS CPU-használat megvizsgálásával kívánt elvégezheti a lekérdezés (a név felülírandó számítógép mező) használatával teheti:

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

További információt szeretne a metrikák a következő Naplóelemzési a [Operations Management Suite blog](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Log Analytics egy meglévő tárfiók hozzáadása

Ez a sablon egy új vagy meglévő Naplóelemzési munkaterület egyszerűen hozzáadása a meglévő tárfiókok.

[![Üzembe helyezés az Azure-ban](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Csoport kiválasztása egy erőforrást, ha egy már meglévő Naplóelemzési munkaterület dolgozik, válassza ki "Használata meglévő", és keresse meg a Naplóelemzési munkaterület tartalmazó erőforráscsoportot. Hozzon létre egy új egy egyébként.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

Ez a sablon telepítése után lesz tekintse meg a tárfiók a Naplóelemzési munkaterület csatlakozik. Ebben a példában egy további tárfiókot a fentiekben létrehozott Exchange munkaterületre hozzáadott.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>A Service Fabric-események megtekintése

Miután a telepítés befejeződött, és engedélyezve van a Service Fabric-megoldás a munkaterületen, válassza ki a **Service Fabric** csempére elindítani a Service Fabric dashboard Log Analytics-portálról. Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Minden oszlop a felső 10 események száma a megadott időtartományban az adott oszlop feltételeknek megfelelő sorolja fel. Futtathat teljes listáját jeleníti meg, kattintson a napló keresés **láthatja az összes** jobb alsó oszlopok, vagy kattintson az oszlopfejlécre.

| **A Service Fabric-esemény** | **description** |
| --- | --- |
| Jelentős problémák |Megjeleníti a problémák, például RunAsyncFailures RunAsynCancellations és csomópont időszakosan megszakadó. |
| A működési események |Például az alkalmazásfrissítés és központi telepítések figyelmet a jelentősebb működési eseményeit. |
| Megbízható eseményei |Fontos megbízható szolgáltatás események ilyen Runasyncinvocations. |
| Szereplő események |Fontos szereplő eseményeit a micro-szolgáltatások, például egy aktormetódus, szereplő aktiválások és deactivations, és így tovább által okozott kivételeket. |
| Alkalmazás-események |Minden egyéni ETW által előállított eseményeket az alkalmazások. |

![A Service Fabric irányítópult](./media/log-analytics-service-fabric/sf3.png)

![A Service Fabric irányítópult](./media/log-analytics-service-fabric/sf4.png)

A következő táblázat adatgyűjtési módszerek és egyéb adatok gyűjtése hogyan Service Fabric részleteit.

| Platform | Közvetlen ügynök | Operations Manager-ügynök | Azure Storage | Az Operations Manager szükséges? | Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött | Gyűjtemény gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 perc |

> [!NOTE]
> Ezek az események a Service Fabric-megoldásban hatóköre módosítható kattintva **adatok alapján az elmúlt 7 napban** az irányítópult tetején. Akkor is megjelenhet az elmúlt hét napban, egy nap vagy hat órán belül létrehozott eseményeket. Vagy választhat **egyéni** meg egy egyéni dátumtartományt.
>
>

## <a name="next-steps"></a>További lépések

* Használjon [napló megkeresi a Naplóelemzési](log-analytics-log-searches.md) Service Fabric-esemény részletes adatainak megtekintéséhez.
