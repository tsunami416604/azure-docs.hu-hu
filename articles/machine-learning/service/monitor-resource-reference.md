---
title: Az adatfigyelés ismertetése | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg a Azure Machine Learning gyűjtött adatokkal és erőforrásokkal, és Azure Monitor elérhető. Azure Monitor gyűjti és felfedi a Azure Machine Learning munkaterületre vonatkozó adatokat, és lehetővé teszi a metrikák megtekintését, a riasztások beállítását és a naplózott adatok elemzését.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/04/2019
ms.openlocfilehash: 1fb16f6ef58efc5c63e743e557558dc60cf52f86
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514237"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Az Azure Machine learning monitorozási adatreferenciája

Ismerje meg az Azure Monitor által gyűjtött adatokat és erőforrásokat az Azure Machine Learning munkaterületről. A figyelési adatok gyűjtésével és elemzésével kapcsolatos részletekért tekintse meg a [figyelési Azure Machine learning](monitor-azure-machine-learning.md) .

## <a name="resource-logs"></a>Erőforrásnaplók

A következő táblázat a Azure Monitor-naplókba vagy az Azure Storage-ba gyűjtött Azure Machine Learning erőforrás-naplók tulajdonságait sorolja fel.

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents táblázat

| Tulajdonság | Leírás |
|:--- |:---|
| TimeGenerated | A naplóbejegyzés létrehozásának ideje |
| OperationName | A naplózási eseményhez társított művelet neve |
| Kategória | A naplózási esemény neve, AmlComputeClusterNodeEvent |
| JobId | A beküldött feladatokhoz tartozó azonosító |
| ExperimentId | A kísérlet azonosítója |
| experimentName | A kísérlet neve |
| customerSubscriptionId | SubscriptionId, ahol a kísérlet és a feladatok elküldve |
| workspaceName | A Machine learning-munkaterület neve |
| clusterName | A fürt neve |
| ProvisioningState | A feladatok beküldésének állapota |
| ResourceGroupName | Az erőforráscsoport neve |
| JobName | A feladattípus neve |
| ClusterId | A fürt azonosítója |
| EventType | A feladatok eseményének típusa, például JobSubmitted, JobRunning, JobFailed, JobSucceeded stb. |
| ExecutionState | A feladatot (a futtatást), például az üzenetsor-kezelést, a futtatást, a sikeres műveletet, nem sikerült |
| ErrorDetails | A feladattal kapcsolatos hibák részletei |
| CreationApiVersion | A feladatok létrehozásához használt API-verzió |
| ClusterResourceGroupName | A fürt erőforráscsoport-neve |
| TFWorkerCount | TF-feldolgozók száma |
| TFParameterServerCount | A TF paraméter-kiszolgáló száma |
| ToolType | A használt eszköz típusa |
| RunInContainer | Az a jelző, amely leírja, hogy a feladatot egy tárolón belül kell-e futtatni |
| JobErrorMessage | a feladattípus részletes üzenete |
| NodeId | A feladatot futtató csomópont azonosítója |

### <a name="amlcomputerclusterevents-table"></a>AmlComputerClusterEvents táblázat

| Tulajdonság | Leírás |
|:--- |:--- |
| TimeGenerated | A naplóbejegyzés létrehozásának ideje |
| OperationName | A naplózási eseményhez társított művelet neve |
| Kategória | A naplózási esemény neve, AmlComputeClusterNodeEvent |
| ProvisioningState | A fürt kiépítési állapota |
| clusterName | A fürt neve |
| clusterType | A fürt típusa |
| CreatedBy | A fürtöt létrehozó felhasználó |
| CoreCount | A fürtben lévő magok száma |
| vmSize | A fürt virtuálisgép-mérete |
| VmPriority | A fürt dedikált/LowPriority létrehozott csomópontjainak prioritása |
| ScalingType | A fürt méretezésének manuális/automatikus típusa |
| InitialNodeCount | A fürt kezdeti csomópontjának száma |
| MinimumNodeCount | A fürt minimális csomópontjainak száma |
| MaximumNodeCount | A fürt maximális csomópontjainak száma |
| NodeDeallocationOption | A csomópont kiosztásának módja |
| Gyártó/kiadó | A fürt típusának közzétevője |
| Ajánlat | Az ajánlat, amellyel a fürt létrejött |
| Termékváltozat | A fürtön belül létrehozott csomópont/virtuális gép SKU-a |
| Verzió | A csomópont/virtuális gép létrehozásakor használt rendszerkép verziója |
| A netI | A fürt kimutatása |
| AllocationState | Fürt kiosztási állapota |
| CurrentNodeCount | A fürt aktuális csomópontjainak száma |
| TargetNodeCount | A fürt célként megadott csomópontjainak száma a fel/le Méretezés közben |
| EventType | Az esemény típusa a fürt létrehozása során. |
| NodeIdleTimeSecondsBeforeScaleDown | Üresjárati idő másodpercben a fürt skálázása előtt |
| PreemptedNodeCount | A fürt előzik-csomópontjainak száma |
| IsResizeGrow | A fürt felskálázását jelző jelző |
| VmFamilyName | A fürtön belül létrehozható csomópontok virtuálisgép-családjának neve |
| LeavingNodeCount | A fürt csomópontjainak kihagyása |
| UnusableNodeCount | A fürt használhatatlan csomópontok száma |
| IdleNodeCount | A fürt üresjárati csomópontjainak száma |
| RunningNodeCount | A fürt csomópontok számának futtatása |
| PreparingNodeCount | A fürt csomópontjai számának előkészítése |
| QuotaAllocated | Lefoglalt kvóta a fürthöz |
| QuotaUtilized | A fürt kihasználtsági kvótája |
| AllocationStateTransitionTime | Áttérési idő az egyik állapotból a másikba |
| ClusterErrorCodes | A fürt létrehozása vagy skálázása során kapott hibakód |
| CreationApiVersion | A fürt létrehozásakor használt API-verzió |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents táblázat

| Tulajdonság | Leírás |
|:--- |:--- |
| TimeGenerated | A naplóbejegyzés létrehozásának ideje |
| OperationName | A naplózási eseményhez társított művelet neve |
| Kategória | A naplózási esemény neve, AmlComputeClusterNodeEvent |
| clusterName | A fürt neve |
| NodeId | A létrehozott fürtcsomópont azonosítója |
| vmSize | A csomópont virtuális gép mérete |
| VmFamilyName | A virtuális gép családja, amelyhez a csomópont tartozik |
| VmPriority | Dedikált/LowPriority létrehozott csomópont prioritása |
| Gyártó/kiadó | A virtuálisgép-rendszerkép kiadója, például Microsoft-dsvm |
| Ajánlat | A virtuális gép létrehozásával kapcsolatos ajánlat |
| Termékváltozat | A létrehozott csomópont/virtuális gép SKU-jának száma |
| Verzió | A csomópont/virtuális gép létrehozásakor használt rendszerkép verziója |
| ClusterCreationTime | A fürt létrehozásának ideje |
| ResizeStartTime | A fürt vertikális fel-és leskálázásának ideje |
| ResizeEndTime | A fürt vertikális felskálázásának ideje vége |
| NodeAllocationTime | A csomópont lefoglalásának ideje |
| NodeBootTime | A csomópont indításának ideje |
| StartTaskStartTime | A feladat csomóponthoz való hozzárendelésének és elindításának időpontja |
| StartTaskEndTime | A csomóponthoz rendelt feladat befejezésének ideje |
| TotalE2ETimeInSeconds | A teljes idő csomópont aktív volt |

### <a name="metrics"></a>Mérőszámok

A következő táblázatok felsorolják a Azure Machine Learning összes metrikához gyűjtött platform-metrikákat a névtér **Azure Machine learning szolgáltatás munkaterületen**.

**Modell**

| Modell | Unit (Egység) | Leírás |
| ----- | ----- | ----- |
| A modell üzembe helyezése nem sikerült | Mennyiség | A sikertelen telepítési modellek száma. |
| A modell üzembe helyezése elindult | Mennyiség | A modell központi telepítésének száma megkezdődött. |
| A modell telepítése sikerült | Mennyiség | A sikeres modell-telepítések száma. |
| A modell regisztrálása nem sikerült | Mennyiség | A sikertelen modell-regisztrációk száma. |
| A modell regisztrálása sikerült | Mennyiség | A sikeres modell-regisztrációk száma. |

**Kvóta**

A kvóta adatai csak Azure Machine Learning számítási feladatokhoz használhatók.

| Metrika | Unit (Egység) | Leírás |
| ----- | ----- | ----- |
| Aktív magok | Mennyiség | Az aktív számítási magok száma. |
| Aktív csomópontok | Mennyiség | Az aktív csomópontok száma. |
| Üresjárati magok | Mennyiség | Az üresjáratban lévő számítási magok száma. |
| Tétlen csomópontok | Mennyiség | Az üresjáratban lévő számítási csomópontok száma. |
| Magok kihagyása | Mennyiség | A magok elhagyásának száma. |
| Csomópontok elhagyása | Mennyiség | A csomópontok elhagyásának száma. |
| Előzik magok | Mennyiség | A előzik magok száma. |
| Előzik-csomópontok | Mennyiség | A előzik-csomópontok száma. |
| Kvóta kihasználtsága (%) | Százalék | A felhasznált kvóta százalékaránya. |
| Összes mag | Mennyiség | A magok teljes száma. |
| Csomópontok összesen | Mennyiség | Az összes csomópont. |
| Használhatatlan magok | Mennyiség | A használhatatlan magok száma. |
| Használhatatlan csomópontok | Mennyiség | A használhatatlan csomópontok száma. |

A kvóta metrikáinak szűrésére a következő dimenziók használhatók:

| Dimenzió | Elérhető metrikák (k) | Leírás |
| ---- | ---- | ---- |
| Fürt neve | Az összes kvóta mérőszáma | A számítási példány neve. |
| VM-család neve | Kvóta kihasználtsága (%) | A fürt által használt virtuálisgép-család neve. |
| Virtuális gép prioritása | Kvóta kihasználtsága (%) | A virtuális gép prioritása.

**Futtassa**

A képzések futtatásával kapcsolatos információk.

| Metrika | Unit (Egység) | Leírás |
| ----- | ----- | ----- |
| Befejezett futtatások | Mennyiség | A befejezett futtatások száma. |
| Sikertelen futtatások | Mennyiség | A sikertelen futtatások száma. |
| Elindított futtatások | Mennyiség | Az elindított futtatások száma. |

A következő méretek használhatók a futtatási metrikák szűréséhez:

| Dimenzió | Leírás |
| ---- | ---- |
| ComputeType | A futtatáshoz használt számítási típus. |
| PipelineStepType | A Futtatás során használt [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) típusa. |
| PublishedPipelineId | A futtatásban használt közzétett folyamat azonosítója. |
| RunType | A Futtatás típusa. |

A RunType dimenzió érvényes értékei a következők:

| Érték | Leírás |
| ----- | ----- |
| Kísérlet | Nem folyamatban lévő futtatások. |
| PipelineRun | Egy folyamat futtatása, amely egy StepRun szülője. |
| StepRun | Egy folyamat lépésének futtatása. |
| ReusedStepRun | Az előző futtatást használó folyamat lépéseinek futtatása. |

## <a name="see-also"></a>Lásd még:

- A figyelési Azure Machine Learning leírását a [figyelés Azure Machine learning](monitor-azure-machine-learning.md) című részben tekintheti meg.
- Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](/azure/azure-monitor/insights/monitor-azure-resource) .
