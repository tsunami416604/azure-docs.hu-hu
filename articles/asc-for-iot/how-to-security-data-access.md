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
ms.openlocfilehash: d81a8973772879f4f4b143701a1f4be3ecad95d9
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576639"
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

1. Válasszon ki egy riasztás vagy javaslat az ASC az IoT. 
2. Kattintson a **további vizsgálatra**, majd kattintson a **ezt a riasztást, kattintson ide, és tekintse meg a DeviceId oszlopot, mely eszközök vannak**.

További információ a Log Analyticsből származó adatok lekérdezése: [Ismerkedés a Log Analytics lekérdezéseinek](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Biztonsági riasztások

Biztonsági riasztások tárolt _AzureSecurityOfThings.SecurityAlert_ a Log Analytics-munkaterületen konfigurált IoT-megoldás az ASC táblájában.

Úgy döntöttünk, hogy a megadott hasznos lekérdezések segítségével számos első lépések a biztonsági riasztások feltárása.

### <a name="sample-records"></a>Minta rekordok

Válassza pár véletlenszerű rekord

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

| TimeGenerated           | IoTHubId                                                                                                       | Eszközazonosító      | AlertSeverity | Megjelenítendő név                           | Leírás                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Találgatásos támadás sikeres volt           | Az eszközön egy találgatásos kényszerítése a támadás sikeres volt        |    {"Forrás teljes cím": "[\"10.165.12.18:\"]", "Felhasználónév": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Az eszköz sikeres helyi bejelentkezés      | Egy sikeres helyi bejelentkezés az eszközön észlelt     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Az eszközön a helyi bejelentkezési próbálkozás sikertelen volt  | Egy helyi sikertelen bejelentkezési kísérlet után az eszközön észlelt |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Eszköz összegzésének

Válassza ki az IoT Hub, az eszköz, a riasztás súlyossága, a riasztási típus által az elmúlt hét észlelt olyan biztonsági riasztások számát.

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

| IoTHubId                                                                                                       | Eszközazonosító      | AlertSeverity | Megjelenítendő név                           | Darabszám |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Találgatásos támadás sikeres volt           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes        | Az eszközön a helyi bejelentkezési próbálkozás sikertelen volt  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | Az eszköz sikeres helyi bejelentkezés      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes        | Titkosítási érme Miner                     | 4   |

### <a name="iot-hub-summary"></a>Az IoT hub összefoglalás

Válassza ki a különböző eszközök, amelyek korábban riasztások az elmúlt héten, az IoT Hub, a riasztás súlyosságát, a riasztástípus számát

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

| IoTHubId                                                                                                       | AlertSeverity | Megjelenítendő név                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Magas          | Találgatásos támadás sikeres volt           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Közepes        | Az eszközön a helyi bejelentkezési próbálkozás sikertelen volt  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Magas          | Az eszköz sikeres helyi bejelentkezés      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Közepes        | Titkosítási érme Miner                     | 1          |

## <a name="security-recommendations"></a>Biztonsági javaslatok

Biztonsági javaslatok tárolt _AzureSecurityOfThings.SecurityRecommendation_ a Log Analytics-munkaterületen konfigurált IoT-megoldás az ASC táblájában.

Biztosítottunk egy hasznos lekérdezések, amelyek felfedezése a biztonsági javaslatok kezdő száma.

### <a name="sample-records"></a>Minta rekordok

Válassza pár véletlenszerű rekord

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | IoTHubId | Eszközazonosító | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Leírás | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes | Aktív | A bemeneti lánc megengedő tűzfalszabály található. | A rendszer olyan szabályt talált a tűzfalon, amely IP-címek vagy portok széles tartományára vonatkozó megengedő mintát tartalmaz | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes | Aktív | A bemeneti lánc megengedő tűzfalszabály található. | A rendszer olyan szabályt talált a tűzfalon, amely IP-címek vagy portok széles tartományára vonatkozó megengedő mintát tartalmaz | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Eszköz összegzésének

Válassza ki a különböző aktív biztonsági javaslatok számát az IoT Hub, eszköz, ajánlás súlyosság és típus szerint.

```
// Select number of distinct active security recommendations by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | Eszközazonosító      | RecommendationSeverity | Darabszám |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Magas          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Közepes        | 4   |


## <a name="next-steps"></a>További lépések

- Olvassa el az ASC IOT [áttekintése](overview.md)
- További információk az ASC az IoT [architektúra](architecture.md)
- Ismertetés és felfedezés [ASC IoT-riasztások](concept-security-alerts.md)
- Ismertetés és felfedezés [ASC IoT-javaslat](concept-recommendations.md)