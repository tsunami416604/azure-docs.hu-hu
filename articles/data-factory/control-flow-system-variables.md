---
title: Rendszerváltozók az Azure Data Factoryban
description: Ez a cikk az Azure Data Factory által támogatott rendszerváltozókat ismerteti. Ezeket a változókat a kifejezésekben használhatja a Data Factory entitások definiálásakor.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 0a5237336530d30c3801b13b910171e236e87a23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679276"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Az Azure Data Factory által támogatott rendszerváltozók
Ez a cikk az Azure Data Factory által támogatott rendszerváltozókat ismerteti. Ezeket a változókat a kifejezésekben használhatja a Data Factory entitások definiálásakor.

## <a name="pipeline-scope"></a>Csővezeték hatóköre
Ezek a rendszerváltozók bárhol hivatkozhatók a JSON-folyamatban.

| Változó neve | Leírás |
| --- | --- |
| @pipeline(). DataFactory (DataFactory) |Annak az adatgyárnak a neve, amelyen belül fut a folyamat. |
| @pipeline(). Csővezeték |A csővezeték neve |
| @pipeline(). Futtatásazonosító | Az adott folyamatfuttatás azonosítója |
| @pipeline(). Eseményindító típusa | A folyamatot meghívó eseményindító típusa (Kézi, Ütemező) |
| @pipeline(). Eseményindító azonosítója| A folyamatot meghívó eseményindító azonosítója |
| @pipeline(). Eseményindító neve| A folyamatot meghívó eseményindító neve |
| @pipeline(). TriggerTime (TriggerTime)| Az az időpont, amikor a folyamatot megmeghívó eseményindító meghívta. Az eseményindító ideje a tényleges tüzelési idő, nem az ütemezett idő. Például `13:20:08.0149599Z` a visszaadott helyett`13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Eseményindító hatókörének ütemezése
Ezek a rendszerváltozók bárhol hivatkozhatók az eseményindító JSON-jában, ha az eseményindító típusa: "ScheduleTrigger".

| Változó neve | Leírás |
| --- | --- |
| @trigger().ütemezettidő |Az az időpont, amikor az eseményindító a folyamat futtatásának meghívására volt ütemezve. Például egy 5 percenként aktiválódó eseményindító esetén `2017-06-01T22:20:00Z`ez `2017-06-01T22:25:00Z` `2017-06-01T22:30:00Z` a változó visszatér , illetve .|
| @trigger().startTime |Az az **időpont,** amikor az eseményindító ténylegesen elindult a folyamat futtatásának meghívásához. Például egy 5 percenként aktiválódó eseményindító esetén ez a `2017-06-01T22:20:00.4061448Z` `2017-06-01T22:25:00.7958577Z`változó `2017-06-01T22:30:00.9935483Z` valami ilyesmit adhat vissza , illetve. (Megjegyzés: Az időbélyeg alapértelmezés szerint ISO 8601 formátumban van)|

## <a name="tumbling-window-trigger-scope"></a>Az ablakeseményindító hatókörének bukdácsolása
Ezek a rendszerváltozók bárhol hivatkozhatók az eseményindító JSON-jában, ha az eseményindító típusa: "TumblingWindowTrigger.".
(Megjegyzés: Az időbélyeg alapértelmezés szerint ISO 8601 formátumban van)

| Változó neve | Leírás |
| --- | --- |
| @trigger().outputs.windowStartTime |Az ablak kezdete, amikor az eseményindító a folyamat futtatásának meghívására lett ütemezve. Ha a bukdácsoló ablak ravaszt van egy gyakorisága "óránkénti" ez lenne az idő elején az óra.|
| @trigger().outputs.windowEndTime |Az ablak vége, amikor az eseményindító a folyamat futtatásának meghívására lett ütemezve. Ha a bukdácsoló ablak ravaszt van egy gyakorisága "óránkénti" ez lenne az idő végén az óra.|
## <a name="next-steps"></a>További lépések
A változók kifejezésekben való kezeléséről a [Kifejezés nyelve & függvények](control-flow-expression-language-functions.md)című témakörben talál tájékoztatást.
