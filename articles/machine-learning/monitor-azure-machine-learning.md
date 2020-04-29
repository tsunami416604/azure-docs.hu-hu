---
title: Figyelési Azure Machine Learning | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure Monitor a metrikák megtekintésére, elemzésére és riasztások létrehozásához Azure Machine Learningokból.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78399111"
---
# <a name="monitoring-azure-machine-learning"></a>Figyelés Azure Machine Learning

Ez a cikk a Azure Machine Learning által generált figyelési információkat ismerteti. Azt is leírja, hogyan használható a Azure Monitor az adatelemzéshez és a riasztások definiálásához.

> [!TIP]
> A jelen dokumentumban található információk elsősorban rendszergazdák számára készültek, mivel a Azure Machine Learning figyelését ismerteti. Ha Ön egy adattudós vagy fejlesztő, és szeretné figyelni a modell-oktatóprogramokra vonatkozó információkat, tekintse meg a következő dokumentumokat:
>
> * [Betanítási futtatások indítása, figyelése és megszakítása](how-to-manage-runs.md)
> * [A betanítási futtatások mérőszámainak naplózása](how-to-track-experiments.md)
> * [Kísérletek nyomon követése az MLflow használatával](how-to-use-mlflow.md)
> * [Futtatások megjelenítése a TensorBoard használatával](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

A Azure Machine Learning a Azure Monitor használatával naplózza az adatfigyelési szolgáltatásokat, amely az Azure-ban teljes verem-figyelési szolgáltatás. Azure Monitor az Azure-erőforrások figyelésére szolgáló funkciók teljes készletét biztosítja. Más felhőkben és a helyszínen is figyelheti az erőforrásokat.

A [Azure monitor áttekintése](/azure/azure-monitor/overview)című cikkből megtudhatja, hogyan tekintheti át a figyelési képességeket. Az alábbi fejezetek ezen információkra épülnek, ha a Azure Monitor és a Azure Machine Learning használatával kapcsolatos konkrét adatokat biztosít.

A Azure Monitorhez kapcsolódó költségek megismeréséhez tekintse meg a [használati és becsült költségeket](/azure/azure-monitor/platform/usage-estimated-costs). Ha meg szeretné tudni, hogy mire van szüksége ahhoz, hogy az adatai megjelenjenek Azure Monitorban, tekintse meg az [adatfeldolgozási idő naplózása](/azure/azure-monitor/platform/data-ingestion-time)című témakört.

## <a name="monitoring-data-from-azure-machine-learning"></a>Adatok figyelése Azure Machine Learningról

A Azure Machine Learning ugyanolyan típusú figyelési adatokat gyűjt, mint a többi Azure-erőforrást, amelyeket az [Azure-erőforrások adatainak monitorozása](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)című témakörben talál. A Azure Machine Learning által létrehozott naplók és metrikák részletes ismertetését lásd: [Azure Machine learning monitorozási adatok referenciája](monitor-resource-reference.md) .

## <a name="analyzing-metric-data"></a>Metrikus adatok elemzése

Azure Machine Learning metrikáinak elemzéséhez nyissa meg a **metrikákat** a **Azure monitor** menüből. Az eszköz használatával kapcsolatos részletekért lásd: az [Azure Metrikaböngésző használatának első lépései](/azure/azure-monitor/platform/metrics-getting-started) .

Azure Machine Learning összes mérőszáma a névtér **Machine learning szolgáltatás munkaterületen**található.

![Metrikaböngésző a Machine Learning szolgáltatás munkaterületének kiválasztásával](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Szűrés és felosztás

A dimenziókat támogató metrikák esetében a dimenzió érték használatával szűrőket alkalmazhat. Például az **aktív magok** szűrése a **fürt nevénél** `cpu-cluster`. 

A mérőszámokat dimenzió alapján is feloszthatja, hogy megjelenítse, hogy a metrika különböző szakaszai hogyan hasonlítanak össze egymással. Például feloszthatja a folyamat **lépésének típusát** , hogy megtekintse a folyamat során használt lépések számát.

További információ a szűrésről és a felosztásról: [Azure monitor speciális szolgáltatásai](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Riasztások

Azure Machine Learning riasztásait a **Azure monitor** menüből származó **riasztások** megnyitásával érheti el. A riasztások létrehozásával kapcsolatos részletekért tekintse meg a [metrikus riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](/azure/azure-monitor/platform/alerts-metric) című témakört.

A következő táblázat a Azure Machine Learning vonatkozó gyakori és javasolt metrikai szabályokat sorolja fel:

| Riasztástípus | Állapot | Leírás |
|:---|:---|:---|
| Modell-üzembehelyezés sikertelen | Összesítés típusa: Total, operátor: nagyobb, mint, küszöbérték: 0 | Ha egy vagy több modell telepítése meghiúsult |
| Kvóta kihasználtsága (%) | Összesítés típusa: átlag, operátor: nagyobb, mint, küszöbérték: 90| Ha a kvóta kihasználtsági aránya nagyobb, mint 90% |
| Használhatatlan csomópontok | Összesítés típusa: Total, operátor: nagyobb, mint, küszöbérték: 0 | Ha egy vagy több használhatatlan csomópont van |

## <a name="configuration"></a>Configuration

> [!IMPORTANT]
> A __Azure Machine learning metrikáit nem kell konfigurálni__, a rendszer automatikusan gyűjti azokat, és az Metrikaböngészőban elérhetők a figyeléshez és a riasztáshoz.

A következő funkciók konfigurálásához diagnosztikai beállítást adhat hozzá:

* Archiválja a napló és a metrikák adatait egy Azure Storage-fiókba.
* Stream-napló és metrikák adatai egy Azure Event hub-hoz.
* A napló és a metrikák adatainak elküldése Azure Monitor Log Analytics.

Ezen beállítások engedélyezéséhez további Azure-szolgáltatások (Storage-fiók, Event hub vagy Log Analytics) szükségesek, ami növelheti a költségeket. A becsült költségek kiszámításához tekintse meg az [Azure díjszabási számológépét](https://azure.microsoft.com/pricing/calculator).

A diagnosztikai beállítások létrehozásával kapcsolatos további információkért lásd: [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák gyűjtéséhez az Azure-ban](/azure/azure-monitor/platform/diagnostic-settings).

A következő naplókat konfigurálhatja Azure Machine Learninghoz:

| Kategória | Leírás |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning számítási fürtök eseményei. |
| AmlComputeClusterNodeEvent | Azure Machine Learning számítási fürt csomópontjain belüli események. |
| AmlComputeJobEvent | Azure Machine Learning számításon futó feladatok eseményei. |

> [!NOTE]
> Ha a mérőszámokat egy diagnosztikai beállításban engedélyezi, a dimenzió adatai jelenleg nem szerepelnek a Storage-fiókba, az Event hub-ba vagy a log analyticsbe továbbított adatok részeként.

## <a name="analyzing-log-data"></a>Naplózási adatok elemzése

A Azure Monitor Log Analytics használatával diagnosztikai konfigurációt kell létrehoznia, és engedélyeznie __kell az adatok küldését a log Analyticsnak__. További információ: [konfiguráció](#configuration) szakasz.

Azure Monitor naplókban lévő, az egyes táblákban található, egyedi tulajdonságokkal rendelkező táblázatokban tárolt adathalmazok. A Azure Machine Learning az alábbi táblázatokban tárolja az adattárolást:

| Tábla | Leírás |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning számítási fürtök eseményei. |
| AmlComputeClusterNodeEvent | Azure Machine Learning számítási fürt csomópontjain belüli események. |
| AmlComputeJobEvent | Azure Machine Learning számításon futó feladatok eseményei. |

> [!IMPORTANT]
> Amikor kijelöli a **naplók** elemet a Azure Machine learning menüben, a rendszer megnyit egy log Analytics a jelenlegi munkaterületre beállított lekérdezési hatókörrel. Ez azt jelenti, hogy a naplók lekérdezése csak az adott erőforrás adatait fogja tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más adatbázisokból vagy más Azure-szolgáltatásoktól származó adatokból származó adatokkal is rendelkezik, válassza a **naplók** lehetőséget a **Azure monitor** menüből. Részletekért lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](/azure/azure-monitor/log-query/scope/) .

A naplók és a metrikák részletes ismertetését lásd: [Azure Machine learning monitorozási adatok referenciája](monitor-resource-reference.md).

### <a name="sample-queries"></a>Mintalekérdezések

A következő lekérdezések segítségével figyelheti Azure Machine Learning erőforrásait: 

+ Sikertelen feladatok beolvasása az elmúlt öt napban:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Rekordok beolvasása egy adott feladattípushoz:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ A fürt eseményeinek beolvasása az elmúlt öt napban azon fürtök esetében, amelyeknél a virtuális gép mérete Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Az elmúlt nyolc napban lefoglalt csomópontok beolvasása:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>További lépések

- A naplók és a metrikák ismertetését lásd: [Azure Machine learning monitorozási adatok referenciája](monitor-resource-reference.md).
- További információ a Azure Machine Learninghoz kapcsolódó kvóták használatáról: az [Azure-erőforrások kezelése és kvóták igénylése](how-to-manage-quotas.md).
- Az Azure-erőforrások monitorozásával kapcsolatos további információkért lásd: [Azure-erőforrások figyelése Azure monitorokkal](/azure/azure-monitor/insights/monitor-azure-resource).
