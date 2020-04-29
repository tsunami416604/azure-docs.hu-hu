---
title: Biztonsági ügynök helyi konfigurációja (C)
description: Tudnivalók a C-hez készült Agent helyi konfigurációk Azure Security Centeráról.
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
ms.openlocfilehash: cd344b9bebb69af210c482f46af6b2dd7edf7816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311707"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>A LocalConfiguration.json fájl ismertetése – C-ügynök

A IoT biztonsági ügynök Azure Security Center konfigurációkat használ egy helyi konfigurációs fájlból.
A biztonsági ügynök egyszer beolvassa a konfigurációt az ügynök indításakor.
A helyi konfigurációs fájlban található konfiguráció hitelesítési konfigurációt és más ügynökkel kapcsolatos konfigurációkat tartalmaz.
A fájl a "kulcs-érték" párokban található konfigurációkat JSON-jelöléssel tartalmazza, a konfigurációk pedig az ügynök telepítésekor kerülnek feltöltésre.

Alapértelmezés szerint a fájl a következő helyen található:/var/ASCIoTAgent/LocalConfiguration.json

A konfigurációs fájl módosításai az ügynök újraindításakor lépnek életbe.

## <a name="security-agent-configurations-for-c"></a>A C biztonsági ügynök konfigurációi

| Konfiguráció neve | Lehetséges értékek | Részletek |
|:-----------|:---------------|:--------|
| Ügynökazonosító | GUID | Az ügynök egyedi azonosítója |
| TriggerdEventsInterval | ISO8601 karakterlánc | Az aktivált események gyűjtésének ütemező időköze |
| ConnectionTimeout | ISO8601 karakterlánc | Túllépte az időkorlátot a IoThub való kapcsolódás előtt. |
| Hitelesítés | JsonObject | Hitelesítési konfiguráció. Ez az objektum tartalmazza az IoTHub-hitelesítéshez szükséges összes információt |
| Identitás | "DPS", "SecurityModule", "eszköz" | Hitelesítési identitás – DPS if hitelesítés a DPS-n keresztül történik, SecurityModule, ha a hitelesítés biztonsági modul hitelesítő adataival vagy eszközével történik, ha az eszköz hitelesítő adataival történik a hitelesítés. |
| AuthenticationMethod | "SasToken", "SelfSignedCertificate" | a hitelesítő felhasználói titok – válassza a SasToken lehetőséget, ha a titok használata szimmetrikus kulcs, válassza az önaláírt tanúsítvány lehetőséget, ha a titok egy önaláírt tanúsítvány  |
| FilePath | Fájl elérési útja (karakterlánc) | A hitelesítési titkot tartalmazó fájl elérési útja |
| HostName | sztring | Az Azure IOT hub állomásneve. általában <My-hub>. azure-devices.net |
| DeviceId | sztring | Az eszköz azonosítója (az Azure IoT Hubban regisztrálva) |
| DPS | JsonObject | DPS-hez kapcsolódó konfigurációk |
| IDScope | sztring | A DPS azonosító hatóköre |
| Regisztrációban | sztring  | DPS-eszköz regisztrációs azonosítója |
| Naplózás | JsonObject | Ügynök-naplózó kapcsolódó konfigurációk |
| SystemLoggerMinimumSeverity | 0 <= szám <= 4 | a/var/log/syslog (0 a legalacsonyabb súlyosságú) üzeneteket a rendszer naplózza. |
| DiagnosticEventMinimumSeverity | 0 <= szám <= 4 | a rendszer a súlyossági szinttel egyenlő vagy annál nagyobb naplózási üzeneteket küld diagnosztikai eseményként (a 0 a legalacsonyabb súlyosságú). |

## <a name="security-agent-configurations-code-example"></a>Biztonsági ügynök konfigurációjának kódja – példa

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

- A IoT-szolgáltatás [áttekintésének](overview.md) Azure Security Center olvasása
- További információ a IoT- [architektúra](architecture.md) Azure Security Center
- A IoT [szolgáltatás](quickstart-onboard-iot-hub.md) Azure Security Centerának engedélyezése
- A IoT szolgáltatással kapcsolatos [Gyakori kérdések](resources-frequently-asked-questions.md) Azure Security Center beolvasása
- Ismerje meg, hogyan érheti el a [nyers biztonsági információkat](how-to-security-data-access.md)
- [Javaslatok](concept-recommendations.md) ismertetése
- Biztonsági [riasztások](concept-security-alerts.md) ismertetése
