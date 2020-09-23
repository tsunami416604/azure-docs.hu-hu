---
title: Eseményösszesítés
description: Ismerje meg a Defender for IoT esemény összesítését.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: aec750d246ce99fa65431e23ef68e70418db0017
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940941"
---
# <a name="defender-for-iot-event-aggregation"></a>Defender a IoT esemény összesítéséhez

A Defender for IoT biztonsági ügynökök adatokat és rendszereseményeket gyűjtenek a helyi eszközről, és ezeket az adatokat az Azure-felhőbe küldik feldolgozásra és elemzésre. A biztonsági ügynök számos típusú eseményt gyűjt, beleértve az új folyamatot és az új kapcsolatok eseményeit. Az új folyamat és az új kapcsolódási események is jogszerűen előfordulhatnak egy adott eszközön egy másodpercen belül, és noha fontos a robusztus és teljes biztonság szempontjából, az üzenetek biztonsági ügynökei számára a küldésre kényszerített mennyiség gyorsan elérheti vagy meghaladhatja a IoT Hub kvótát és a költségeket. Ezek az események azonban rendkívül értékes biztonsági információkat tartalmaznak, amelyek elengedhetetlenek az eszköz védelméhez.

Ha csökkenteni szeretné a további kvótát és költségeket az eszközök védelmének fenntartása mellett, a Defender for IoT-ügynökök összesítik az ilyen típusú eseményeket.

Az esemény összesítése alapértelmezés szerint **be** van kapcsolva, és bár nem ajánlott, a manuálisan **kikapcsolható** .

Az Összesítés jelenleg a következő típusú eseményekhez érhető el:

* ProcessCreate
* ConnectionCreate
* ProcessTerminate (csak Windows)

## <a name="how-does-event-aggregation-work"></a>Hogyan működik az események összesítése?

Ha az esemény összesítése **továbbra**is fennáll, a Defender a IoT-ügynökök összesített eseményeket az intervallum vagy az idő ablakban.
Az intervallum lejárta után az ügynök további elemzés céljából elküldi az összesített eseményeket az Azure-felhőbe.
Az összesített eseményeket a rendszer a memóriában tárolja, amíg el nem érkezik az Azure-felhőbe.

Az ügynök memória-lábnyomának csökkentése érdekében, ha az ügynök egy olyan eseményt gyűjt, amely már a memóriában van, akkor az ügynök növeli az adott esemény találati számát. Az összesítési idő ablakának átadásakor az ügynök elküldi a találatok számát minden egyes eseménynél. Az események összesítése egyszerűen az egyes összegyűjtött események találati számának összesítése.

Az események csak akkor tekinthetők azonosnak, ha teljesülnek a következő feltételek:

* ProcessCreate-események – ha a **parancssor**, a **végrehajtható fájl**, a **Felhasználónév**és a **userid** megegyezik
* ConnectionCreate események – ha a **commandLine**, a **userId**, az **Direction**, a **Local címet**, a **távoli címet**, a * * protokollt és a **célport** azonos
* ProcessTerminate események – ha a **végrehajtható fájl** és a **kilépési állapot** megegyezik

### <a name="working-with-aggregated-events"></a>Összesített események használata

Az összesítés során a rendszer elveti a nem összesített esemény-tulajdonságokat, és a log Analyticsben 0 értékkel jelenik meg.

* ProcessCreate-események – a **folyamatazonosító**és a **parentProcessId** értéke 0
* ConnectionCreate-események – a **folyamatazonosító**és a **forrásport** értéke 0

## <a name="event-aggregation-based-alerts"></a>Esemény-összesítési alapú riasztások

Az elemzést követően a Defender for IoT biztonsági riasztásokat hoz létre a gyanús összesített eseményekhez. Az összesített eseményekből létrehozott riasztások csak egyszer jelennek meg minden összesített eseménynél.

Az egyes események összesítésének kezdési és befejezési időpontja, valamint az egyes eseményekhez tartozó találatok száma a vizsgálatok során való használat érdekében a Log Analytics **ExtraDetails** mezőjében lesz naplózva.

Minden összesített esemény a begyűjtött riasztások 24 órás időszakát jelöli. Az egyes események bal felső sarkában az Event Options (esemény beállításai) menü használatával **elvetheti** az egyes összesített eseményeket.

## <a name="event-aggregation-twin-configuration"></a>Esemény-összesítési kettős konfiguráció

Módosítsa a Defender konfigurációját a IoT esemény összesítéséhez az **azureiotsecurity** modul moduljának kettős identitásának [ügynök konfigurációs objektumán](how-to-agent-configuration.md) belül.

| Konfiguráció neve | Lehetséges értékek | Részletek | Megjegyzések |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | Események összesítésének engedélyezése/letiltása folyamat-létrehozási eseményekhez |
| aggregationIntervalProcessCreate | ISO8601 TimeSpan karakterlánca | A folyamat-létrehozási események összesítési időköze |
| aggregationEnabledConnectionCreate | boolean| Esemény-összesítés engedélyezése/letiltása a kapcsolat létrehozási eseményeihez |
| aggregationIntervalConnectionCreate | ISO8601 TimeSpan karakterlánca | Kapcsolódási időköz a kapcsolatok létrehozási eseményeihez |
| aggregationEnabledProcessTerminate | boolean | Események összesítésének engedélyezése/letiltása a folyamatok megszakításához | Csak Windowson|
| aggregationIntervalProcessTerminate | ISO8601 TimeSpan karakterlánca | Az összesítési időköz a folyamat megszakításához | Csak Windowson|
|

## <a name="default-configurations-settings"></a>Alapértelmezett konfigurációk beállításai

| Konfiguráció neve | Alapértelmezett értékek |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | PT1H|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | PT1H|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | PT1H|
|

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan IoT a Defender a biztonsági ügynök összesítését, és az elérhető esemény-konfigurációs beállításokat.

A Defender IoT-telepítéssel való használatának folytatásához használja a következő cikkeket:

- A [biztonsági ügynök hitelesítési módszereinek](concept-security-agent-authentication-methods.md) megismerése
- [Biztonsági ügynök](how-to-deploy-agent.md) kiválasztása és központi telepítése
- A Defender áttekintése a IoT [szolgáltatás előfeltételeihez](service-prerequisites.md)
- Ismerje meg, hogyan [engedélyezheti a Defender for IoT szolgáltatást a IoT hub](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról a [Defender for IoT – gyakori kérdések](resources-frequently-asked-questions.md)
