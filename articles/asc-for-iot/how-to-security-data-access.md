---
title: Hozzáférés biztonsági & javaslatadataihoz
description: Ismerje meg, hogyan érheti el a biztonsági riasztási és javaslati adatokat az Azure Security Center for IoT használatakor.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: bbea0accc79cafb6fea3f1438a71250dc02f4d62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311011"
---
# <a name="access-your-security-data"></a>A biztonsági adatok elérése

Az Azure Security Center for IoT biztonsági riasztásokat, javaslatokat és nyers biztonsági adatokat (ha úgy dönt, hogy menti) a Log Analytics-munkaterületen tárolja.

## <a name="log-analytics"></a>Log Analytics

A Log Analytics-munkaterület konfigurálása:

1. Nyissa meg az IoT-központot.
1. Kattintson az **Áttekintés** panelre a **Biztonság** szakaszban
1. Kattintson **a Beállítások**gombra, és módosítsa a Log Analytics-munkaterület konfigurációját.

A riasztások és javaslatok elérése a Log Analytics-munkaterületen a konfiguráció után:

1. Válasszon egy riasztást vagy javaslatot az Azure Security Center for IoT-ben.
1. Kattintson a **további vizsgálat**gombra, majd kattintson **ide A riasztás megtekintéséhez kattintson ide, és tekintse meg a DeviceId oszlopot.**

A Log Analytics adatainak lekérdezéséről a [Lekérdezések megkezdése a Log Analytics alkalmazásban.](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)

## <a name="security-alerts"></a>Biztonsági riasztások

A biztonsági riasztások az _AzureSecurityOfThings.SecurityAlert_ táblában tárolódnak az Azure Security Center for IoT-megoldáshoz konfigurált Log Analytics-munkaterületben.

Számos hasznos lekérdezést biztosítottunk a biztonsági riasztások feltárásának megkezdéséhez.

### <a name="sample-records"></a>Mintarekordok

Néhány véletlenszerű rekord kijelölése

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | Riasztássúlyossága | DisplayName                           | Leírás                                             | Bővített tulajdonságok                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Magasság          | Brute force támadás sikerült           | A brute force támadás a készülék sikeres volt        |    { "Teljes forráscíme":\""[ 10.165.12.18:\"]", "Felhasználónevek": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Magasság          | Sikeres helyi bejelentkezés az eszközön      | A rendszer sikeres helyi bejelentkezést észlelt az eszközre     | { "Távoli cím": "?", "Távoli port": """, "Helyi port": "","Bejelentkezési rendszerhéj": "/bin/su", "Bejelentkezési folyamat azonosítója": "28207", "Felhasználónév": "támadó", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Magasság          | Sikertelen helyi bejelentkezési kísérlet az eszközön  | A rendszer nem sikerült helyi bejelentkezési kísérletet észlelt az eszközre |    { "Távoli cím": "?", "Távoli port": """, "Helyi port": "","Bejelentkezési rendszerhéj": "/bin/su", "Bejelentkezési folyamat azonosítója": "22644", "Felhasználónév": "támadó", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Eszköz összegzése

Az ioT Hub, az eszköz, a riasztás súlyossága, a riasztás típusa szerint csoportosítva észlelt különböző biztonsági riasztások száma.

```
// Get the number of distinct security alerts detected in the last week, grouped by
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

| IoTHubId                                                                                                       | DeviceId      | Riasztássúlyossága | DisplayName                           | Darabszám |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Magasság          | Brute force támadás sikerült           | 9   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Közepes        | Sikertelen helyi bejelentkezési kísérlet az eszközön  | 242 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Magasság          | Sikeres helyi bejelentkezés az eszközön      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Közepes        | Crypto érme bányász                     | 4   |

### <a name="iot-hub-summary"></a>IoT hub összegzése

Válasszon ki néhány különböző eszközt, amelyek az elmúlt héten riasztásokat kaptak az IoT Hub, a riasztás súlyossága, a riasztás típusa szerint

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

| IoTHubId                                                                                                       | Riasztássúlyossága | DisplayName                           | CntEszközök |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | Magasság          | Brute force támadás sikerült           | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | Közepes        | Sikertelen helyi bejelentkezési kísérlet az eszközön  | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | Magasság          | Sikeres helyi bejelentkezés az eszközön      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | Közepes        | Crypto érme bányász                     | 1          |

## <a name="security-recommendations"></a>Biztonsági javaslatok

A biztonsági javaslatok az _AzureSecurityOfThings.SecurityRecommendation_ táblában tárolódnak az Azure Security Center for IoT-megoldáshoz konfigurált Log Analytics-munkaterületben.

Számos hasznos lekérdezést biztosítottunk a biztonsági javaslatok feltárásának megkezdéséhez.

### <a name="sample-records"></a>Mintarekordok

Néhány véletlenszerű rekord kijelölése

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

| TimeGenerated | IoTHubId | DeviceId | Ajánlássúlyossága | Ajánlásállapota | JavaslatDisplayNeve | Leírás | JavaslatTovábbi adatok |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Közepes | Aktív | A bemeneti láncban egy megengedő tűzfalszabály található | A tűzfalban olyan szabály található, amely ip-címek vagy portok széles körének megengedő mintáját tartalmazza | {"Rules":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\"\"\":\",\"\"DestinationAddress :\", DestinationPort : 1337 }]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Közepes | Aktív | A bemeneti láncban egy megengedő tűzfalszabály található | A tűzfalban olyan szabály található, amely ip-címek vagy portok széles körének megengedő mintáját tartalmazza | {"Rules":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\"\"\":\",\"\"DestinationAddress :\", DestinationPort : 1337 }]"} |

### <a name="device-summary"></a>Eszköz összegzése

Az IoT Hub, az eszköz, a javaslat súlyossága és típusa szerint csoportosított különálló aktív biztonsági javaslatok száma.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | Ajánlássúlyossága | Darabszám |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Magasság          | 2   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Közepes        | 1 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Magasság          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/Microsoft.Devices/IotHubs/<iot_hub> | device_name> < | Közepes        | 4   |

## <a name="next-steps"></a>További lépések

- Olvassa el az Azure Security Center for IoT [áttekintését](overview.md)
- További információ az Azure Security Center for IoT [Architecture szolgáltatásról](architecture.md)
- Az [Azure Security Center for IoT-riasztások megismerése](concept-security-alerts.md) és felfedezése
- Az [Azure Security Center megismerése az IoT-javaslatokhoz](concept-recommendations.md)
