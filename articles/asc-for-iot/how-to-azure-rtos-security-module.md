---
title: Az Azure RTOS biztonsági moduljának konfigurálása és testreszabása
description: Ismerje meg, hogyan konfigurálhatja és testreszabhatja az Azure RTOS biztonsági modulját.
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
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: eed0422f574e54ff6d7df486b4929850a26418fa
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514869"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Az Azure RTOS biztonsági moduljának konfigurálása és testreszabása (előzetes verzió)

A következő fájl segítségével konfigurálhatja az eszköz viselkedését.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/Inc/asc_port. h

 Az egyes konfigurációk alapértelmezett viselkedését az alábbi táblázatokban ismertetjük: 

### <a name="general"></a>Általános kérdések

| Név | Típus | Alapértelmezett | Részletek |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Sztring | --- | Az eszköz egyedi azonosítója  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Szám | 300 | A biztonsági modul ideje másodpercben. Ha az idő meghaladja az állapot-módosítás felfüggesztését. |

#### <a name="collection"></a>Gyűjtemény

| Név | Típus | Alapértelmezett | Részletek |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Szám | 10 | A gyűjtők magas prioritású csoportjának időköze másodpercben. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Szám | 30 | Adatgyűjtők közepes prioritású csoportjának időköze másodpercben. |
| ASC_LOW_PRIORITY_INTERVAL | Szám | 145 440  | A gyűjtők alacsony prioritású csoportjának időköze másodpercben. |

#### <a name="collector-network-activity"></a>Adatgyűjtő hálózati tevékenység

A gyűjtő hálózati tevékenység konfigurációjának testreszabásához használja a következőt:

| Név | Típus | Alapértelmezett | Részletek |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Logikai | hamis | `TCP`Hálózati tevékenység szűrése |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Logikai | hamis | `UDP`Hálózati tevékenység eseményeinek szűrése |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Logikai | hamis | `ICMP`Hálózati tevékenység eseményeinek szűrése |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Logikai | true | Csak az egyedi küldésű bejövő csomagok rögzítése, ha hamis rögzítésre van beállítva, a szórás és a csoportos küldés is |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Szám | 64 | A memóriában tárolandó IPv4-hálózati események maximális száma |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Szám | 64  | A memóriában tárolandó IPv6-alapú hálózati események maximális száma |


## <a name="compile-flags"></a>Jelölők fordítása
A fordítási jelzők lehetővé teszik az előre definiált konfigurációk felülbírálását.

### <a name="collectors"></a>Naplógyűjtők
| Név | Típus | Alapértelmezett | Részletek |
| - | - | - | - |
| collector_heartbeat_enabled | Logikai | ON | A szívverés-gyűjtő engedélyezése |
| collector_network_activity_enabled | Logikai | ON | Hálózati tevékenység-gyűjtő engedélyezése |
| collector_system_information_enabled | Logikai | ON | Rendszerinformáció-gyűjtő engedélyezése |

## <a name="supported-security-alerts-and-recommendations"></a>Támogatott biztonsági riasztások és javaslatok

Az Azure RTOS biztonsági modulja támogatja a konkrét biztonsági riasztásokat és javaslatokat. Győződjön meg arról, hogy a szolgáltatásra [vonatkozó riasztási és ajánlási értékek áttekintése és testreszabása](concept-rtos-security-alerts-recommendations.md) megtörténjen.

## <a name="log-analytics-optional"></a>Log Analytics (nem kötelező)

Ha nem kötelező és nem szükséges, a Log Analytics engedélyezése és konfigurálása hasznos lehet, ha további vizsgálatot szeretne végezni az eszközök eseményeiről és tevékenységeiről. További információ a Log Analytics beállításáról és használatáról [a IoT szolgáltatás Azure Security Centerával](how-to-security-data-access.md#log-analytics) . 

## <a name="next-steps"></a>Következő lépések

- Az Azure RTOS [biztonsági riasztások és javaslatok](concept-rtos-security-alerts-recommendations.md) biztonsági moduljának áttekintése és testreszabása
- Szükség szerint tekintse meg az [Azure RTOS API biztonsági modulját](azure-rtos-security-module-api.md) .

