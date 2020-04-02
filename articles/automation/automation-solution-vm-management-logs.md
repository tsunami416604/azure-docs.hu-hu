---
title: Naplók lekérdezése a virtuális gépek indítási/leállítási megoldásából
description: Ez a cikk ismerteti, hogyan lekérdezési napló adatok által generált indítási/leállítási virtuális gépek megoldás az Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 00f6a5f88a66f0a19943ff3a2c722ae1a9938e9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550395"
---
# <a name="how-to-query-logs-from-startstop-vms-solution"></a>Naplók lekérdezése a virtuális gépek indítási/leállítási megoldásából

Az Azure Automation kétféle rekordot továbbít a csatolt Log Analytics-munkaterületre: feladatnaplókat és feladatadatfolyamokat. Ezek az adatok az Azure Monitor [lekérdezéséhez](../azure-monitor/log-query/log-query-overview.md) érhetők el.

## <a name="job-logs"></a>Feladatnaplók

|Tulajdonság | Leírás|
|----------|----------|
|Hívó |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs.|
|CorrelationId | GUID, amely a runbook-feladat korrelációs azonosítója.|
|JobId | GUID, amely a runbook-feladat azonosítója.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az automatizálás esetében az érték A feladat.|
|resourceId | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat állapota. Lehetséges értékek:<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>- Sikeres|
|resultDescription | Ismerteti a runbook-feladat eredményállapotát. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött|
|RunbookName | Megadja a runbook-feladat nevét.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automatizálás esetében az érték OpsManager|
|StreamType | Megadja az esemény típusát. Lehetséges értékek:<br>- Részletes<br>- Kimenet<br>- Hiba<br>- Figyelmeztetés|
|SubscriptionId | Megadja a feladat előfizetési azonosítóját.
|Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

## <a name="job-streams"></a>Feladatstreamek

|Tulajdonság | Leírás|
|----------|----------|
|Hívó |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams.|
|JobId | GUID, amely a runbook-feladat azonosítója.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az automatizálás esetében az érték A feladat.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|resourceId | Megadja az erőforrás-azonosítót az Azure-ban. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat eredménye az esemény létrehozásának időpontjában. A lehetséges érték a következő:<br>- Folyamatban|
|resultDescription | A runbook kimeneti streamjét tartalmazza.|
|RunbookName | A runbook neve.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automatizálás esetében az érték opsmanager.|
|StreamType | A feladatstream típusa. Lehetséges értékek:<br>- Haladás<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes|
|Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

Ha olyan naplókeresést hajt végre, amely a JobLogs vagy **a JobStreams** **kategóriarekordjait** adja vissza, kiválaszthatja a **JobLogs** vagy **a JobStreams nézetet,** amely a keresés által visszaadott frissítéseket összegző csempéket jeleníti meg.

## <a name="sample-log-searches"></a>Naplókeresési minták

A következő táblázat a megoldás által összegyűjtött feladatrekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

|Lekérdezés | Leírás|
|----------|----------|
|Sikeresen befejezett runbook ScheduledStartStop_Parent feladatkeresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Sikertelenül lezárt runbook-ScheduledStartStop_Parent feladatainak keresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Sikeresen befejezett runbook SequencedStartStop_Parent feladatának keresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Sikertelenül lezárt runbook-SequencedStartStop_Parent feladatainak keresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>További lépések

A virtuális gépek indítása/leállítása munkaidőn kívüli megoldás nem tartalmaz előre meghatározott riasztásokkészletét. Tekintse át a [naplóriasztások létrehozása](../azure-monitor/platform/alerts-log.md) az Azure Monitor segítségével megtudhatja, hogyan hozhat létre feladat sikertelen riasztásokat a DevOps vagy az operatív folyamatok és eljárások támogatása érdekében.