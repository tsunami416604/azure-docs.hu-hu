---
title: Biztonsági modul az Azure RTOS beépített & testreszabható riasztások és javaslatok
description: Ismerje meg a biztonsági riasztásokat és a javasolt szervizelést az Azure IoT biztonsági modul – RTOS használatával.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: ebb4edf10433cce981b4718835676d0a5d76ba40
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514884"
---
# <a name="security-module-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Biztonsági modul Azure RTOS – biztonsági riasztások és javaslatok (előzetes verzió)

Az Azure RTOS biztonsági modulja folyamatosan elemzi a IoT-megoldást a fejlett Analitika és a fenyegetések felderítése révén, hogy figyelmeztesse Önt a lehetséges kártékony tevékenységekre és a gyanús rendszermódosításokra. Egyéni riasztásokat is létrehozhat a várt eszköz viselkedésének és alapértékének ismerete alapján.

Az Azure RTOS-riasztás biztonsági modulja a lehetséges kompromisszumok jelzése működik, és meg kell vizsgálni és szervizelni kell. Az Azure RTOS ajánlott biztonsági modulja a gyenge biztonsági helyzet javítását és frissítését azonosítja. 

Ebben a cikkben a beépített riasztások és javaslatok listáját találja, amelyeket az alapértelmezett tartományok alapján indít el, és testreszabható a saját értékekkel a várt vagy az alapkonfiguráció alapján. 

További információ arról, hogyan működik a riasztások testreszabása a IoT szolgáltatás Azure Security Centerban: [testreszabható riasztások](concept-customizable-security-alerts.md). Az Azure RTOS biztonsági moduljának használatakor elérhető konkrét riasztások és javaslatok az alábbi táblázatokban vannak részletezve. 

## <a name="security-module-for-azure-rtos-supported-security-alerts"></a>Security-modul az Azure RTOS-ben – támogatott biztonsági riasztások

### <a name="device-related-security-alerts"></a>Eszközhöz kapcsolódó biztonsági riasztások

|Eszközhöz kapcsolódó biztonsági riasztási tevékenység  |Riasztás neve  |
|---------|---------|
|IP-cím| A rendszer gyanús IP-címmel folytatott kommunikációt észlelt|
|X. 509 eszköz tanúsítványának ujjlenyomata|Az X. 509 eszköz tanúsítványának ujjlenyomata nem egyezik|
|X. 509 tanúsítvány| Az X. 509 tanúsítvány lejárt|
|SAS-token| Lejárt SAS-jogkivonat|
|SAS-token| Érvénytelen SAS-jogkivonat aláírása|

### <a name="iot-hub-related-security-alerts"></a>IoT Hub kapcsolatos biztonsági riasztások

|IoT Hub biztonsági riasztási tevékenység  |Riasztás neve  |
|---------|---------|
|Tanúsítvány hozzáadása    |  A rendszer sikertelen kísérletet észlelt a tanúsítvány hozzáadásához egy IoT Hub       |
|Diagnosztikai beállítások hozzáadása vagy szerkesztése    | A rendszer egy IoT Hub diagnosztikai beállításainak hozzáadására vagy szerkesztésére tett kísérletet észlelt.      |
|Tanúsítvány törlése    |  A rendszer sikertelen kísérletet észlelt a tanúsítvány törlésére egy IoT Hub       |
|Diagnosztikai beállítások törlése    |  A rendszer a diagnosztikai beállítások törlését észlelte egy IoT Hub      |
|Tanúsítvány törölve    | Tanúsítvány észlelése egy IoT Hubból        |
|Új tanúsítvány     |  Új tanúsítvány hozzáadásának észlelése egy IoT Hub       |

## <a name="security-module-for-azure-rtos-supported-customizable-alerts"></a>Az Azure RTOS biztonsági modulja által támogatott testreszabható riasztások

### <a name="device-related-customizable-alerts"></a>Eszközhöz kapcsolódó testreszabható riasztások

|Eszközhöz kapcsolódó tevékenység |Riasztás neve  |
|---------|---------|
|Aktív kapcsolatok|Az aktív kapcsolatok száma nem a megengedett tartományba esik|
|Felhőbeli eszközök üzenetei a **MQTT** protokollban|A **MQTT** -protokollban lévő üzenetek Felhőbeli üzeneteinek száma nem a megengedett tartományba esik.|
|Kimenő kapcsolatok| Nem engedélyezett IP-címhez való kimenő kapcsolódás|

### <a name="hub-related-customizable-alerts"></a>Hubhoz kapcsolódó testreszabható riasztások 

|Hubhoz kapcsolódó tevékenység  |Riasztás neve  |
|---------|---------|
|A parancssori törlések     |  A várólista-kiürítések száma az engedélyezett tartományon kívül       |
|Felhőbeli eszközök üzenetei a **MQTT** protokollban    |  A **MQTT** protokollban az engedélyezett tartományon kívül eső Felhőbeli üzenetek száma       |
|Eszközről a felhőbe irányuló üzenetek a **MQTT** protokollban    | Az eszköz és a felhő üzeneteinek száma az **MQTT** protokollban az engedélyezett tartományon kívül        |
|A Direct metódus meghívja     |  Az engedélyezett tartományon kívülre meghívó közvetlen metódusok száma       |
|Elutasította a felhőből az eszközök üzeneteit a **MQTT** protokollban     |   Elutasított Felhőbeli üzenetek száma az **MQTT** protokollban az engedélyezett tartományon kívül      |
|A Twin modulok frissítései     |  Az engedélyezett tartományon kívüli Twin modulok frissítéseinek száma       |
|Jogosulatlan műveletek    |  Az engedélyezett tartományon kívüli jogosulatlan műveletek száma       |

## <a name="security-module-for-azure-rtos-supported-recommendations"></a>Biztonsági modul az Azure RTOS-hez – támogatott javaslatok

### <a name="device-related-recommendations"></a>Eszközhöz kapcsolódó javaslatok

|Eszközhöz kapcsolódó tevékenység  |Javaslat neve |
|---------|---------|
|Hitelesítő adatok    |  Több eszköz által használt azonos hitelesítési hitelesítő adatok       |

### <a name="hub-related-recommendations"></a>Hubhoz kapcsolódó javaslatok

|IoT Hub kapcsolódó tevékenység  |Javaslat neve |
|---------|---------|
|IP-szűrési házirend   |  Az alapértelmezett IP-szűrési házirendet a **Megtagadás** értékre kell állítani  |
|IP-szűrési szabály| Az IP-szűrési szabály nagyméretű IP-címtartományt tartalmaz|
|Diagnosztikai naplók|A diagnosztikai naplók engedélyezésének javaslata IoT Hub|

### <a name="all-azure-security-center-for-iot-alerts-and-recommendations"></a>A IoT-riasztások és-javaslatok összes Azure Security Center

A IoT szolgáltatással kapcsolatos riasztások és javaslatok összes Azure Security Center teljes listájáért lásd: IoT [biztonsági riasztások](concept-security-alerts.md), IoT biztonsági [javaslatok](concept-recommendations.md).

## <a name="next-steps"></a>Következő lépések

- [Gyors útmutató: biztonsági modul az Azure RTOS](quickstart-azure-rtos-security-module.md)
- [Az Azure RTOS biztonsági moduljának konfigurálása és testreszabása](how-to-azure-rtos-security-module.md)
- Tekintse meg az [Azure RTOS API biztonsági modulját](azure-rtos-security-module-api.md)