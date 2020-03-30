---
title: Az Azure Security Center ismertetése a C | Microsoft dokumentumok
description: Ismerje meg az Azure Security Center ügynök helyi konfigurációk C.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 2725a824da26dafcbc215e4c302ec38ad4b5a699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600539"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>A LocalConfiguration.json fájl ismertetése – C-ügynök

Az Azure Security Center for IoT biztonsági ügynök konfigurációkat használ egy helyi konfigurációs fájlból.
A biztonsági ügynök egyszer olvassa be a konfigurációt az ügynök indításakor.
A helyi konfigurációs fájlban található konfiguráció hitelesítési konfigurációt és más ügynökkel kapcsolatos konfigurációkat tartalmaz.
A fájl "Kulcs-érték" párokkonfigurációit tartalmazza JSON jelölésben, és a konfigurációk feltöltődnek az ügynök telepítésekor. 

Alapértelmezés szerint a fájl a következő helyen található: /var/ASCIoTAgent/LocalConfiguration.json

A konfigurációs fájl módosításaaz ügynök újraindításakor történik. 

## <a name="security-agent-configurations-for-c"></a>Biztonsági ügynök konfigurációk C
| Konfiguráció neve | Lehetséges értékek | Részletek | 
|:-----------|:---------------|:--------|
| Ügynökazonosító | GUID | Az ügynök egyedi azonosítója |
| TriggerdEventsInterval | ISO8601 karakterlánc | Az aktivált események gyűjteményének ütemezői időköze |
| ConnectionTimeout (Kapcsolatidő-túllépés) | ISO8601 karakterlánc | Az IoThub-szolgáltatással való kapcsolat időbeli túltöltése előtt |
| Hitelesítés | JsonObject objektum | Hitelesítési konfiguráció. Ez az objektum tartalmazza az IoTHub elleni hitelesítéshez szükséges összes információt |
| Identitás | "DPS", "SecurityModule", "Device" | Hitelesítési identitás – DPS, ha a hitelesítés DPS protokollon keresztül történik, A SecurityModule, ha a hitelesítés a biztonsági modul hitelesítő adataival vagy eszközével történik, ha a hitelesítés eszközhitelesítő adatokkal történik |
| AuthenticationMethod (Hitelesítési módszer) | "SasToken", "SelfSignedCertificate" | a felhasználó titkos hitelesítése - Válassza a SasToken lehetőséget, ha a használati titok szimmetrikus kulcs, válassza az önaláírt tanúsítványt, ha a titkos kulcs egy önaláírt tanúsítvány  |
| FilePath (Fájlelérési út) | Fájl elérési útja (karakterlánc) | A hitelesítési titkot tartalmazó fájl elérési útja |
| HostName | sztring | Az azure iot hub gazdagépneve. általában <my-hub>.azure-devices.net |
| DeviceId | sztring | Az eszköz azonosítója (az Azure IoT Hubban regisztrált) |
| Dps | JsonObject objektum | DPS-hez kapcsolódó konfigurációk |
| Idscope | sztring | A DPS azonosító hatóköre |
| Regisztrációs azonosító | sztring  | DPS-eszköz regisztrációs azonosítója |
| Naplózás | JsonObject objektum | Ügynöknaplózóval kapcsolatos konfigurációk |
| SystemLoggerMinimumSeverity | 0 <= <szám = 4 | az ilyen súlyosságú és annál magasabb naplóüzenetek et a /var/log/syslog könyvtárba naplózza a rendszer (a 0 a legalacsonyabb súlyosság) |
| DiagnosticEventMinimumSeverity | 0 <= <szám = 4 | az ilyen súlyosságú és annál magasabb naplóüzenetek diagnosztikai eseményekként lesznek elküldve (a 0 a legalacsonyabb súlyosság) |

## <a name="security-agent-configurations-code-example"></a>Példa a biztonsági ügynök konfigurációinak kódjára
```JSON
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>További lépések
- Olvassa el az Azure Security Center for IoT szolgáltatás [áttekintését](overview.md)
- További információ az Azure Security Center for IoT [Architecture szolgáltatásról](architecture.md)
- Az Azure Security Center [ioT-szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- Olvassa el az Azure Security Center for IoT szolgáltatás [gyakori kérdések](resources-frequently-asked-questions.md)
- További információ a [nyers biztonsági adatok](how-to-security-data-access.md) eléréséről
- Az [ajánlások megismerése](concept-recommendations.md)
- A biztonsági [riasztások ismertetése](concept-security-alerts.md)