---
title: Naplók lekérdezése a virtuális gépek indítása/leállítása megoldásból
description: Ez a cikk azt ismerteti, hogyan lehet lekérdezni a virtuális gépek indítási/leállítási megoldásával létrehozott naplófájlokat a Azure Monitorról.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 472f3762ca18f71ba95053576daf025d8477fee9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604727"
---
# <a name="how-to-query-logs-from-startstop-vms-solution"></a>Naplók lekérdezése a virtuális gépek indítása/leállítása megoldásból

Azure Automation két típusú rekordot továbbít a csatolt Log Analytics munkaterületre: a feladatok naplóit és a feladatok folyamait. Ezek az adatAzure Monitor [lekérdezésekhez](../azure-monitor/log-query/log-query-overview.md) érhetők el.

## <a name="job-logs"></a>Feladatnaplók

|Tulajdonság | Leírás|
|----------|----------|
|Hívó |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs.|
|CorrelationId | GUID, amely a runbook-feladatokhoz tartozó korrelációs azonosító.|
|JobId | A runbook-feladatokhoz tartozó GUID azonosító.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték a job.|
|resourceId | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat állapota. Lehetséges értékek:<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>- Sikeres|
|resultDescription | Ismerteti a runbook-feladat eredményállapotát. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött|
|RunbookName | Megadja a runbook-feladat nevét.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automation esetében az érték a OpsManager|
|StreamType | Megadja az esemény típusát. Lehetséges értékek:<br>- Részletes<br>- Kimenet<br>- Hiba<br>- Figyelmeztetés|
|SubscriptionId | Megadja a feladat előfizetési azonosítóját.
|Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

## <a name="job-streams"></a>Feladatstreamek

|Tulajdonság | Leírás|
|----------|----------|
|Hívó |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams.|
|JobId | A runbook-feladatokhoz tartozó GUID azonosító.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték a job.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|resourceId | Megadja az erőforrás-azonosítót az Azure-ban. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat eredménye az esemény létrehozásának időpontjában. Lehetséges érték:<br>- Folyamatban|
|resultDescription | A runbook kimeneti streamjét tartalmazza.|
|RunbookName | A runbook neve.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automation esetében az érték a OpsManager.|
|StreamType | A feladatstream típusa. Lehetséges értékek:<br>– Folyamat<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes|
|Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

Ha olyan naplóbeli keresést hajt végre, amely a **JobLogs** vagy a **JobStreams**kategóriába tartozó rekordokat ad vissza, akkor kiválaszthatja a **JobLogs** vagy a **JobStreams** nézetet, amely a keresés által visszaadott frissítések összegzését jeleníti meg.

## <a name="sample-log-searches"></a>Naplókeresési minták

A következő táblázat a megoldás által összegyűjtött feladatrekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

|Lekérdezés | Leírás|
|----------|----------|
|A sikeresen befejezett runbook ScheduledStartStop_Parent feladatok keresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Azon runbook ScheduledStartStop_Parent feladatok keresése, amelyek nem fejeződött be sikeresen | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|A sikeresen befejezett runbook SequencedStartStop_Parent feladatok keresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Azon runbook SequencedStartStop_Parent feladatok keresése, amelyek nem fejeződött be sikeresen | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>További lépések

A **Start/Stop VMS During off-hours** megoldás nem tartalmaz előre meghatározott riasztásokat. Tekintse át a [naplók létrehozása](../azure-monitor/platform/alerts-log.md) a Azure monitor használatával című témakört, amelyből megtudhatja, hogyan hozhat létre sikertelen riasztásokat a DevOps vagy működési folyamatainak és eljárásainak támogatásához.