---
title: "Az Azure biztonsági adatok exportálása a SIEM - feldolgozási folyamat konfigurációs [előzetes verzió] |} Microsoft Docs"
description: "Ez a cikk dokumentálja a termék az első Azure biztonsági központ naplói a siem-rendszerébe"
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: barclayn
ms.openlocfilehash: aef623f047bd7e14cb5bd17fb2a2c18e3c5d42b9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Az Azure biztonsági adatok exportálása a SIEM - feldolgozási folyamat konfigurációs [előzetes verzió]

Ez a dokumentum részletesen az Azure Security Center biztonsági adatok exportálásával egy siem-rendszerébe.

Az Azure Security Center által feldolgozott esemény közzé lesz téve, az Azure-bA [tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), a napló egyikét meg kell adnia Azure figyelő keresztül érhető el. A figyelő az Azure útválasztási a figyelési adatok olyan SIEM eszközt egy olyan összevont folyamatot biztosít. Egy partner eszközt, hogy az adatok egy Eseményközpontot, ahol tudja majd kell kért le adatfolyam ehhez.

Ez az adatcsatorna használja a [Azure figyelése egyetlen feldolgozási sorként](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) a hozzáférést a figyelési adatok lekérése az Azure környezetben. Ez lehetővé teszi, hogy egyszerűen állítsa be az adatokat a siem-ektől és figyelési eszközök.

A következő szakaszok ismertetik, hogyan konfigurálhat egy eseményközpontba közzétett adatokat. A lépések azt feltételezik, hogy már rendelkezik az Azure Security Center az Azure-előfizetéshez konfigurált.

Áttekintés

![Magas szintű áttekintése](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Mi az az Azure biztonsági adatok érhető el az SIEM?

Az előzetes verzióban elérhetővé kell tenni a [biztonsági riasztásokat.](../security-center/security-center-managing-and-responding-alerts.md) A jövőbeli kiadásokban azt fogja kiegészítése az adathalmaz a biztonsági javaslatok.

## <a name="how-to-setup-the-pipeline"></a>A telepítő a folyamat hogyan? 

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása 

Kezdés előtt kell [az Event Hubs-névtér létrehozása](../event-hubs/event-hubs-create.md). A névtér és az Event Hubs a figyelési adatok célját.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Az Event hubs Azure tevékenységnapló adatfolyam

Tekintse meg a következő cikk [adatfolyam tevékenységnapló Event hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)

### <a name="install-a-partner-siem-connector"></a>Egy partner SIEM-összekötő telepítése 

A figyelési adatokat az Azure-megfigyelővel Eseményközpontba útválasztási lehetővé teszi, könnyen integrálható a partner SIEM és figyelési eszközök.

Tekintse meg a következő hivatkozásra kattintva megtekintheti a listája [támogatott siem-ektől](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

## <a name="example-for-querying-data"></a>Például az adatok lekérdezése 

Íme néhány Splunk lekérdezések, amelyek segítségével olvasnak be riasztási adatokat:

| **Lekérdezés leírása**                                | **Lekérdezés**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Minden riasztás                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| Műveletek száma a nevük összefoglalója             | **Riasztások** index fő forrástípus = = "amal: biztonsági" \| tábla operationName \| operationName lépésköz statisztikák                                |
| Riasztások adatok beolvasása: idő, neve, állapota, azonosítója és előfizetés | index fő Microsoft.Security/locations/alerts = \| tábla \_idő, properties.eventName, állapot, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>További lépések

- [Támogatott siem-ektől](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Tevékenységnapló streamelése az Event Hubs szolgáltatásba](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Biztonsági riasztások.](../security-center/security-center-managing-and-responding-alerts.md)