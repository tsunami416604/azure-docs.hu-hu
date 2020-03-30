---
title: Az Azure Machine Learning figyelése | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az Azure Monitort az Azure Machine Learning metrikákra vonatkozó riasztások megtekintéséhez, elemzéséhez és létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399111"
---
# <a name="monitoring-azure-machine-learning"></a>Az Azure Machine Learning figyelése

Ez a cikk ismerteti az Azure Machine Learning által létrehozott figyelési adatok. Azt is ismerteti, hogyan használhatja az Azure Monitor az adatok elemzéséhez és riasztások meghatározásához.

> [!TIP]
> A jelen dokumentumban szereplő információk elsősorban a rendszergazdák, az Azure Machine Learning figyelése. Ha Ön adatelemző vagy fejlesztő, és szeretné figyelni a modellbetanítási futtatásokra jellemző információkat, tekintse meg az alábbi dokumentumokat:
>
> * [Betanítási futtatások indítása, figyelése és megszakítása](how-to-manage-runs.md)
> * [A betanítási futtatások mérőszámainak naplózása](how-to-track-experiments.md)
> * [Kísérletek nyomon követése az MLflow használatával](how-to-use-mlflow.md)
> * [Futtatások megjelenítése a TensorBoard használatával](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Az Azure Machine Learning naplózza az adatok figyelését az Azure Monitor használatával, amely egy teljes veremfigyelési szolgáltatás az Azure-ban. Az Azure Monitor az Azure-erőforrások figyeléséhez teljes körű funkciókat biztosít. Más felhőkben és a helyszínen is figyelheti az erőforrásokat.

Kezdje az [Azure Monitor áttekintése](/azure/azure-monitor/overview)című cikkel, amely áttekintést nyújt a figyelési képességekről. A következő szakaszok ezen információk alapján az Azure Monitor azure Machine Learning használatával kapcsolatos sajátosságait biztosítják.

Az Azure Monitorhoz kapcsolódó költségek megismeréséhez olvassa el [a Használat és a becsült költségek témakört.](/azure/azure-monitor/platform/usage-estimated-costs) Ha meg szeretné tudni, hogy az adatok milyen ideig jelennek meg az Azure Monitorban, olvassa el [az Adatok betöltési idejének naplózása című témakört.](/azure/azure-monitor/platform/data-ingestion-time)

## <a name="monitoring-data-from-azure-machine-learning"></a>Az Azure Machine Learning adatainak figyelése

Az Azure Machine Learning ugyanolyan típusú figyelési adatokat gyűjt, mint más Azure-erőforrások, amelyeket az [Azure-erőforrásokból származó adatok figyelése](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)ismertet. Tekintse meg az [Azure Machine Learning figyelési adatok hivatkozása](monitor-resource-reference.md) az Azure Machine Learning által létrehozott naplók és metrikák részletes hivatkozását.

## <a name="analyzing-metric-data"></a>Metrikaadatok elemzése

Az Azure Machine Learning metrikák elemzéséhez nyissa meg a **metrikákat** az **Azure Monitor** menüből. Az [azure metrics-kezelő használatának megkezdéséről](/azure/azure-monitor/platform/metrics-getting-started) az eszköz használatával kapcsolatos részletekről.

Az Azure Machine Learning összes metrikája a **Machine Learning Service Workspace**névtérben található.

![Metrikakezelő a Machine Learning Szolgáltatás munkaterületének kijelölésével](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Szűrés és felosztás

A dimenziókat támogató mutatók esetében dimenzióérték használatával alkalmazhat szűrőket. Például az **aktív magok szűrése** a `cpu-cluster` **fürt nevéhez.** 

A metrikát dimenzió szerint is feloszthatja, hogy vizualizálja, hogyan viszonyulnak egymáshoz a metrika különböző szegmensei. Például a **folyamatlépés típusának** felosztásával megtekintheti a folyamatban használt lépések számát.

A szűrésről és a felosztásról az [Azure Monitor speciális szolgáltatásai](/azure/azure-monitor/platform/metrics-charts)című témakörben talál további információt.

## <a name="alerts"></a>Riasztások

Az Azure Machine Learning riasztásait az **Azure Monitor** **menüből** származó riasztások megnyitásával érheti el. A riasztások létrehozásával kapcsolatos részletekért [olvassa el A metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával](/azure/azure-monitor/platform/alerts-metric) című témakört.

Az alábbi táblázat az Azure Machine Learning általános és ajánlott metrikariasztási szabályait sorolja fel:

| Riasztástípus | Állapot | Leírás |
|:---|:---|:---|
| A modell telepítése sikertelen | Összesítés típusa: Összes, Operátor: Nagyobb, mint, Küszöbérték: 0 | Ha egy vagy több modell központi telepítése sikertelen volt |
| Kvótakihasználtság százaléka | Összesítés típusa: Átlag, Operátor: Nagyobb, Küszöbérték: 90| Ha a kvótakihasználási százalék nagyobb, mint 90% |
| Használhatatlan csomópontok | Összesítés típusa: Összes, Operátor: Nagyobb, mint, Küszöbérték: 0 | Ha egy vagy több használhatatlan csomópont van |

## <a name="configuration"></a>Konfiguráció

> [!IMPORTANT]
> __Metrikák az Azure Machine Learning nem kell konfigurálni,__ azokat automatikusan gyűjtik, és elérhetők a Metrikák Intézőben a figyelés és riasztás.

A következő funkciók konfigurálásához diagnosztikai beállításokat adhat hozzá:

* Archív napló és metrikák információkat egy Azure-tárfiókba.
* Napló- és metrikaadatokat továbbítanak egy Azure Event Hubba.
* Napló- és metrikaadatok küldése az Azure Monitor Log Analytics szolgáltatásba.

A beállítások engedélyezéséhez további Azure-szolgáltatásokra (tárfiókra, eseményközpontra vagy Log Analyticsre) van szükség, ami növelheti a költségeket. A becsült költségek kiszámításához keresse fel az [Azure díjkalkulátorát.](https://azure.microsoft.com/pricing/calculator)

A diagnosztikai beállítások létrehozásáról további információt a [Diagnosztikai beállítás létrehozása platformnaplók és metrikák azure-beli gyűjtéséhez című témakörben talál.](/azure/azure-monitor/platform/diagnostic-settings)

Az Azure Machine Learning következő naplóit konfigurálhatja:

| Kategória | Leírás |
|:---|:---|
| AmlComputeClusterEvent | Események az Azure Machine Learning számítási fürtök. |
| AmlComputeClusterNodeEsemény | Események egy Azure Machine Learning számítási fürt csomópontokból. |
| AmlComputeJobEsemény | Az Azure Machine Learning-számításon futó feladatok eseményei. |

> [!NOTE]
> Ha diagnosztikai környezetben engedélyezi a metrikákat, a dimenzióadatok jelenleg nem szerepelnek a tárfiókba, eseményközpontba vagy naplóelemzésbe küldött adatok részeként.

## <a name="analyzing-log-data"></a>Naplóadatok elemzése

Az Azure Monitor Log Analytics használatával diagnosztikai konfigurációt kell létrehoznia, és engedélyeznie kell __az Adatok küldése a Log Analytics szolgáltatásba__című szolgáltatást. További információt a [Konfiguráció](#configuration) című szakaszban talál.

Az Azure Monitor naplók ban tárolt adatok táblák, minden tábla saját egyedi tulajdonságokkal rendelkezik. Az Azure Machine Learning az alábbi táblázatokban tárolja az adatokat:

| Tábla | Leírás |
|:---|:---|
| AmlComputeClusterEvent | Események az Azure Machine Learning számítási fürtök. |
| AmlComputeClusterNodeEsemény | Események egy Azure Machine Learning számítási fürt csomópontokból. |
| AmlComputeJobEsemény | Az Azure Machine Learning-számításon futó feladatok eseményei. |

> [!IMPORTANT]
> Ha az Azure Machine Learning menü naplók parancsát **választja,** a Log Analytics megnyílik az aktuális munkaterületre beállított lekérdezési hatókörrel. Ez azt jelenti, hogy a naplólekérdezések csak az adott erőforrásból származó adatokat tartalmazzák. Ha olyan lekérdezést szeretne futtatni, amely más adatbázisokból vagy más Azure-szolgáltatásokból származó adatokat tartalmaz, válassza az **Azure Monitor** menü **Naplók parancsát.** A részleteket az [Azure Monitor Log Analytics lekérdezési naplótartományának naplózása és időtartománya.](/azure/azure-monitor/log-query/scope/)

A naplók és metrikák részletes hivatkozását az [Azure Machine Learning figyelési adatok hivatkozása című témakörben található.](monitor-resource-reference.md)

### <a name="sample-queries"></a>Mintalekérdezések

A következőkben az Azure Machine Learning-erőforrások figyeléséhez használható lekérdezések: 

+ Sikertelen feladatok beszereznie az elmúlt öt napban:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Adott feladatnevének rekordjainak beszerezni:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Fürtesemények beszereznie az elmúlt öt napban olyan fürtökhöz, ahol a virtuális gép mérete Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Az elmúlt nyolc napban kiosztott csomópontok lefoglalása:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>További lépések

- A naplók és metrikák hivatkozása: [Azure Machine Learning figyelési adatok hivatkozása.](monitor-resource-reference.md)
- Az Azure Machine Learninghez kapcsolódó kvótákkal kapcsolatos munka az [Azure-erőforrások kvótáinak kezelése és kérése](how-to-manage-quotas.md)című témakörben található.
- Az Azure-erőforrások figyelésével kapcsolatos részletekért tekintse [meg az Azure-erőforrások figyelése az Azure Monitor segítségével.](/azure/azure-monitor/insights/monitor-azure-resource)
