---
title: A nyomon követési adatok hivatkozása | Microsoft dokumentumok
titleSuffix: Azure Machine Learning
description: Ismerje meg az Azure Machine Learninghez gyűjtött és az Azure Monitorban elérhető adatokat és erőforrásokat. Az Azure Monitor adatokat gyűjt és felszínel az Azure Machine Learning-munkaterületről, és lehetővé teszi a metrikák megtekintését, riasztások beállítását és a naplózott adatok elemzését.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/06/2020
ms.openlocfilehash: 958794cda60d0ce1b0d223b9b5a6c03283022a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927564"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Azure gépi tanulási figyelési adatok hivatkozás

Ismerje meg az Azure Monitor által az Azure Machine Learning-munkaterületről gyűjtött adatokat és erőforrásokat. A figyelési adatok gyűjtésével és elemzésével kapcsolatos részletekért tekintse meg az [Azure Machine Learning figyelése](monitor-azure-machine-learning.md) című témakört.

## <a name="resource-logs"></a>Erőforrásnaplók

Az alábbi táblázat felsorolja az Azure Machine Learning-erőforrás-naplók tulajdonságait, amikor az Azure Monitor naplók vagy az Azure Storage-ban gyűjtik őket.

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents tábla

| Tulajdonság | Leírás |
|:--- |:---|
| TimeGenerated | A naplóbejegyzés létrehozásának időpontja |
| OperationName | A naplóeseményhez társított művelet neve |
| Kategória | A naplóesemény neve, AmlComputeClusterNodeEvent |
| JobId | A beküldött feladat azonosítója |
| ExperimentId | A kísérlet azonosítója |
| Kísérletneve | A kísérlet neve |
| CustomerSubscriptionId azonosító | Előfizetésazonosító, ahol a kísérlet és a feladat elküldve |
| Munkaterület neve | A gépi tanulási munkaterület neve |
| ClusterName | A fürt neve |
| ProvisioningState (KiépítésÁllapota) | Az álláspályázat állapota |
| ResourceGroupName | Az erőforráscsoport neve |
| Feladatneve | A feladat neve |
| Fürtazonosító | A fürt azonosítója |
| EventType (Eseménytípus) | A feladat esemény típusa, pl.: JobSubmitted, JobRunning, JobFailed, JobSucceeded stb. |
| ExecutionState | A feladat állapota (a Futtatás), pl.: Várólistán, Futó, Sikeres, Sikertelen |
| ErrorDetails | A feladathiba részletei |
| CreationApiVersion | A feladat létrehozásához használt Api-verzió |
| ClusterResourceGroupName | A fürt erőforráscsoport neve |
| TFworkerCount (TFworkerCount) | A tf-dolgozók száma |
| TFParameterServerCount | TF paraméterkiszolgáló száma |
| Eszköztípus | A használt eszköz típusa |
| RuninContainer tároló | A feladat tárolón belüli futtatásának jelzője |
| JobErrorMessage | a feladathiba részletes üzenete |
| NodeId | A feladat futtatásához létrehozott csomópont azonosítója |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents tábla

| Tulajdonság | Leírás |
|:--- |:--- |
| TimeGenerated | A naplóbejegyzés létrehozásának időpontja |
| OperationName | A naplóeseményhez társított művelet neve |
| Kategória | A naplóesemény neve, AmlComputeClusterNodeEvent |
| ProvisioningState (KiépítésÁllapota) | A fürt kiépítési állapota |
| ClusterName | A fürt neve |
| Fürttípus | A fürt típusa |
| Készítette: | A fürtet létrehozó felhasználó |
| CoreCount (Magszáma) | A fürt magjainak száma |
| VmMéret | A fürt virtuális gépének mérete |
| VmPrioritás | A fürtön belül létrehozott csomópontok prioritása Dedikált/LowPriority |
| Méretezéstípusa | A fürtméretezés manuális/automatikus típusa |
| InitialNodeCount (MonodeCount) | A fürt kezdeti csomópontszáma |
| MinimumNodeCount (MinimumNodeCount) | A fürt minimális csomópontszáma |
| MaximumNodeCount (Maximális nodeszám) | A fürt maximális csomópontszáma |
| NodeDeallocationOption | A csomópont felosztásának oka |
| Közzétevő | A fürttípus közzétevője |
| Ajánlat | Az ajánlat, amellyel a fürt létrejön |
| SKU | A fürtön belül létrehozott csomópont/virtuális gép skuja |
| Verzió | A csomópont/virtuális gép létrehozása kor használt lemezkép verziója |
| Alhálózatazonosító | A fürt alhálózatazonosítója |
| Felosztásállapota | Fürtkiosztás állapota |
| CurrentNodeCount között | A fürt aktuális csomópontszáma |
| TargetNodeCount között | A fürt célcsomópontszáma fel-fel/lefelé méretezés közben |
| EventType (Eseménytípus) | Az esemény típusa a fürt létrehozása során. |
| NodeIdleTimeSecondsScaleDown előtt | A fürt lekicsinylése előtt másodpercben megadva az tétlenségi idő |
| PreemptedNodeCount (PreemptedNodeCount) | A fürt előjeles csomópontszáma |
| IsResizeGrow (Újraméret | A fürt felskálázását jelző jelző |
| VmFamilyName | A fürtön belül létrehozható csomópontok virtuálisgép-családjának neve |
| LeavingNodeCount (KilépésnodeCount) | A fürt csomópontszámának elhagyása |
| Használhatatlan NodeCount | A fürt használhatatlan csomópontszáma |
| IdleNode-számláló | A fürt tétlen csomószáma |
| RunningNodeCount között | A fürt csomópontszámának futtatása |
| FelkészülésNodeCount | A fürt csomópontszámának előkészítése |
| Kvóta elosztása | A fürthöz rendelt kvóta |
| Kvótaként használt | A fürt felhasznált kvótája |
| AllocationStateTransitionTime | Átmenet ideje egyik állapotból a másikba |
| ClusterErrorKódok | A fürt létrehozása vagy méretezése során fogadott hibakód |
| CreationApiVersion | A fürt létrehozásakor használt Api-verzió |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents tábla

| Tulajdonság | Leírás |
|:--- |:--- |
| TimeGenerated | A naplóbejegyzés létrehozásának időpontja |
| OperationName | A naplóeseményhez társított művelet neve |
| Kategória | A naplóesemény neve, AmlComputeClusterNodeEvent |
| ClusterName | A fürt neve |
| NodeId | A létrehozott fürtcsomópont azonosítója |
| VmMéret | A csomópont virtuális gépmérete |
| VmFamilyName | Vm család, amelyhez a csomópont tartozik |
| VmPrioritás | A dedikált/lowpriority csomópont prioritása |
| Közzétevő | A virtuális gép lemezképének közzétevője, például microsoft-dsvm |
| Ajánlat | A virtuális gép létrehozásához társított ajánlat |
| SKU | A létrehozott csomópont/virtuális gép skuja |
| Verzió | A csomópont/virtuális gép létrehozása kor használt lemezkép verziója |
| ClusterCreationTime | A fürt létrehozásának időpontja |
| ÁtméretezésStartTime | Az az idő, amikor a fürtskálázás fel/le kezdődött |
| ÁtméretezésVégidő | Az az idő, amikor a fürtskálázás fel/le véget ért |
| NodeAllocationTime | A csomópont kiosztásának időpontja |
| NodeBootTime | A Csomópont indításának időpontja |
| StartTaskStartTime | Az az időpont, amikor a tevékenység et hozzárendelték egy csomóponthoz, és elkezdődött |
| StartTaskEndTime | A csomóponthoz rendelt tevékenység befejezténti időpont |
| Totale2ETimeinseconds | A csomópont aktív töltött ideje |

### <a name="metrics"></a>Mérőszámok

Az alábbi táblázatok az Azure Machine Learninghez gyűjtött platformmutatókat sorolják fel, minden metrika az Azure Machine Learning Workspace névtérben **tárolódik.**

**Modell**

| Metrika | Unit (Egység) | Leírás |
| ----- | ----- | ----- |
| A modell telepítése nem sikerült | Darabszám | A sikertelen modelltelepítések száma. |
| A modell üzembe helyezése elindult | Darabszám | Az elindított modelltelepítések száma. |
| A modell telepítése sikerült | Darabszám | A sikeres modelltelepítések száma. |
| A modellregiszter nem sikerült | Darabszám | A sikertelen modellregisztrációk száma. |
| A modellregiszter sikerült | Darabszám | A sikeres modellregisztrációk száma. |

**Kvóta**

A kvótaadatok csak az Azure Machine Learning-számításhoz vonatkoznak.

| Metrika | Unit (Egység) | Leírás |
| ----- | ----- | ----- |
| Aktív magok | Darabszám | Az aktív számítási magok száma. |
| Aktív csomópontok | Darabszám | Az aktív csomópontok száma. |
| Alapjárati magok | Darabszám | Az alapjárati számítási magok száma. |
| Tétlen csomópontok | Darabszám | Az alapjárati számítási csomópontok száma. |
| A magok elhagyása | Darabszám | A kilépő magok száma. |
| Csomópontok elhagyása | Darabszám | A kilépő csomópontok száma. |
| Preempted magok | Darabszám | A preempted magok száma. |
| Preempted csomópontok | Darabszám | A preempted csomópontok száma. |
| Kvótakihasználtság százaléka | Százalék | A felhasznált kvóta százaléka. |
| Magok száma összesen | Darabszám | A teljes magok. |
| Összes csomópont | Darabszám | A teljes csomópontok. |
| Használhatatlan magok | Darabszám | A használhatatlan magok száma. |
| Használhatatlan csomópontok | Darabszám | A használhatatlan csomópontok száma. |

A kvótamutatók szűrésére használható dimenziók a következők:

| Dimenzió | Metrika(k) érhetők el | Leírás |
| ---- | ---- | ---- |
| Fürt neve | Minden kvótamutató | A számítási példány neve. |
| Vm családnév | Kvótakihasználtság százaléka | A fürt által használt virtuálisgép-család neve. |
| Vm prioritás | Kvótakihasználtság százaléka | A virtuális gép prioritása.

**Futtassa a következőt:**

Információ a képzési fut.

| Metrika | Unit (Egység) | Leírás |
| ----- | ----- | ----- |
| Befejezett futtatások | Darabszám | A befejezett futtatások száma. |
| Sikertelen futtatások | Darabszám | A sikertelen futtatások száma. |
| Elindított fut | Darabszám | Az elindított futtatások száma. |

A futtatási mérőszámok szűrésére a következő dimenziók használhatók:

| Dimenzió | Leírás |
| ---- | ---- |
| Számítási típus | A futtatás által használt számítási típus. |
| PipelineStepType típus | A futtatáshoz használt [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) típusa. |
| Közzétett pipelineid | A futtatáskor használt közzétett folyamat azonosítója. |
| RunType (Futtatástípusa) | A futás típusa. |

A RunType dimenzió érvényes értékei a következők:

| Érték | Leírás |
| ----- | ----- |
| Experiment | Nem csővezeték-futtatások. |
| PipelineRun között | Egy folyamatfuttatás, amely egy StepRun szülője. |
| Lépésfuttatás | Egy folyamatlépés futtatása. |
| ÚjrafelhasználtLépésfuttatás | Egy korábbi futtatást újrafelhasználó folyamatlépés futtatása. |

## <a name="see-also"></a>Lásd még:

- Az [Azure Machine Learning figyelése](monitor-azure-machine-learning.md) az Azure Machine Learning figyelése.
- Az [Azure-erőforrások figyelése az Azure Monitorsegítségével](/azure/azure-monitor/insights/monitor-azure-resource) az Azure-erőforrások figyelésével kapcsolatos részletekért tekintse meg.
