---
title: Az Azure biztonsági adatok exportálása SIEM - folyamat konfigurációs [előzetes verzió] |} A Microsoft Docs
description: Ez a cikk dokumentálja a Bevezetés az Azure security center naplókat a siem-nek a termék
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: barclayn
ms.openlocfilehash: aede60a729fe9c0594ded485e189c0b467e34271
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298233"
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Az Azure biztonsági adatok exportálása SIEM - folyamat konfigurációs [előzetes verzió]

Ez a dokumentum az Azure Security Center biztonsági adatok exportálásával egy siem-be részletesen.

Az Azure Security Center által feldolgozott események az Azure-ban közzétett [tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), a napló egyik Azure monitoron keresztül elérhető típusokat. Az Azure Monitor egy konszolidált folyamatot a figyelési adatokat egy SIEM-eszközével történő útválasztáshoz kínál. Ez történik, a streamelési adatokat egy eseményközpontba, ahol azt is majd le kell kérnie egy partner eszközt.

A függőleges vonal használja a [egyetlen folyamat Azure Monitoring](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) hozzáférés a monitorozási adatok lekérése az Azure-környezet számára. Ez lehetővé teszi, hogy könnyedén létrehozhasson siem-EK és figyelési eszközök az adatok felhasználásához.

A következő szakaszok ismertetik, hogyan konfigurálható egy eseményközpontba is streamelhetők adatokat. A lépések feltételezik, hogy már rendelkezik az Azure Security Center az Azure-előfizetéshez konfigurált.

Áttekintés

![Magas szintű áttekintése](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Mi az az Azure biztonsági adatok siem-be közzétett?

Az előzetes verzióban elérhetővé tesszük a [biztonsági riasztásokat.](../security-center/security-center-managing-and-responding-alerts.md) A jövőbeli kiadásokban az adatkészlet, a biztonsági javaslatok azt fogja bővítését.

## <a name="how-to-setup-the-pipeline"></a>Hogyan állíthatja be a folyamat? 

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása 

Mielőtt elkezdené, kell [Event Hubs-névtér létrehozása](../event-hubs/event-hubs-create.md). A névtér és Eseményközpont az összes monitorozási adatot célját.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Az Event hubs Azure tevékenységnapló Stream

Tekintse meg a következő cikkben [tevékenységnapló streamelése az Event hubs szolgáltatásba](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)

### <a name="install-a-partner-siem-connector"></a>Partneri SIEM-összekötő telepítése 

A figyelési adatok útválasztást egy eseményközpontba, és az Azure Monitor lehetővé teszi könnyen integrálhatja a partneri SIEM és figyelési eszközöket.

Tekintse meg a következő hivatkozásra kattintva megtekintheti [támogatott siem-ektől](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

## <a name="example-for-querying-data"></a>Például az adatok lekérdezése 

Íme néhány Splunk lekérdezések, amelyek segítségével kérje le a riasztási adatokat:

| **Lekérdezés leírása**                                | **Lekérdezés**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Minden riasztás                                              | index fő Microsoft.Security/locations/alerts =                                                                                         |
| Műveletek száma összefoglalója neve szerint             | index = fő forrás típusa = "amal: biztonsági" \| tábla operationName \| statisztikák szerint operationName száma                                |
| Riasztások adatainak beolvasása: idő, neve, állam, ID és az előfizetés | index = fő Microsoft.Security/locations/alerts \| tábla \_idő, properties.eventName, állam, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>További lépések

- [Támogatott siem-EK](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Tevékenységnapló streamelése az Event Hubs szolgáltatásba](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Biztonsági riasztások.](../security-center/security-center-managing-and-responding-alerts.md)