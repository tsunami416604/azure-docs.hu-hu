---
title: Adathozzáférés ASC használata az IoT-előzetes verzió |} A Microsoft Docs
description: További tudnivalók az IoT ASC használatakor biztonsági riasztás és a javaslattételt adatok eléréséhez.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e394f6025f7898aad7dde7b1acefd9f95029a554
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541992"
---
# <a name="access-your-security-data"></a>Biztonsági adatok elérése 

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC az IoT tárolja a biztonsági riasztások, javaslatok és biztonsági nyers adatokat (Ha meg szeretné tartani őket) Log Analytics-munkaterület.

## <a name="log-analytics"></a>Log Analytics

Annak konfigurálása, mely Log Analytics-munkaterületet használja:

1. Nyissa meg az IoT hubnak.
1. Kattintson a **biztonsági**
2. Kattintson a **beállítások**, és módosítsa a Log Analytics-munkaterület konfigurálása.

A Log Analytics-munkaterület elérése a konfigurálás után:

1. Válassza ki a riasztást az ASC az IoT. 
2. Kattintson a **további vizsgálatra**, majd kattintson a **ezt a riasztást, kattintson ide, és tekintse meg a DeviceId oszlopot, mely eszközök vannak**.

További információ a Log Analyticsből származó adatok lekérdezése: [Ismerkedés a Log Analytics lekérdezéseinek](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Biztonsági riasztások

Biztonsági riasztások vannak tárolva a **ASCforIoT.SecurityAlert** konfigurált Log Analytics-munkaterület táblájában.

A következő alapvető kql lekérdezések használatával további biztonsági riasztásokat.

### <a name="sample-records-query"></a>Mintalekérdezés rekordok

Néhány rekordokat véletlenszerű kiválasztása: 

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

#### <a name="sample-query-results"></a>Mintául szolgáló lekérdezés eredményei 

| TimeGenerated           | IoTHubId                                                                                                       | Eszközazonosító      | AlertSeverity | Megjelenítendő név                           | Leírás                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Találgatásos támadás sikeres volt           | Az eszközön egy találgatásos kényszerítése a támadás sikeres volt        |    {"Forrás teljes cím": "[\"10.165.12.18:\"]", "Felhasználónév": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Az eszköz sikeres helyi bejelentkezés      | Egy sikeres helyi bejelentkezés az eszközön észlelt     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Az eszközön a helyi bejelentkezési próbálkozás sikertelen volt  | Egy helyi sikertelen bejelentkezési kísérlet után az eszközön észlelt |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary-query"></a>Eszköz összesítő lekérdezés

Jelölje be egy olyan biztonsági riasztások száma kql lekérdezés észlelt a múlt héten az IoT Hub, az eszköz, a riasztás súlyossága, a riasztási típus használata.

```
// Select number of distinct security alerts detected last week by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

#### <a name="device-summary-query-results"></a>Eszköz összesítő lekérdezés eredményei

| IoTHubId | Eszközazonosító| AlertSeverity| Megjelenítendő név | Darabszám |
|----------|---------|------------------|---------|---------|
|/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Találgatásos támadás sikeres volt           | 9   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes        | Az eszközön a helyi bejelentkezési próbálkozás sikertelen volt  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Az eszköz sikeres helyi bejelentkezés      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes        | Titkosítási érme Miner                     | 4   |
|

### <a name="iot-hub-summary"></a>Az IoT Hub összefoglalás

Ez a lekérdezés kql használatával számos különböző eszközt, akinek a riasztások az elmúlt héten az IoT hub, a riasztás súlyosságát, a riasztástípus kiválasztása:

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

#### <a name="iot-hub-summary-query-results"></a>Az IoT Hub összesítő lekérdezés eredményei

| IoTHubId                                                                                                       | AlertSeverity | Megjelenítendő név                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Magas          | Találgatásos támadás sikeres volt           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Közepes        | Az eszközön a helyi bejelentkezési próbálkozás sikertelen volt  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Magas          | Az eszköz sikeres helyi bejelentkezés      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Közepes        | Titkosítási érme Miner                     | 1          |



## <a name="next-steps"></a>További lépések

- Olvassa el az ASC IOT [áttekintése](overview.md)
- További információk az ASC az IoT [architektúra](architecture.md)
- Ismertetés és felfedezés [ASC IoT-riasztások](concept-security-alerts.md)
