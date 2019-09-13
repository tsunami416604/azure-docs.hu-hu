---
title: A IoT-megoldási esemény összesítésének Azure Security Center ismertetése | Microsoft Docs
description: Ismerje meg, hogy az események hogyan legyenek összesítve Azure Security Center a IoT szolgáltatásban.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2019
ms.author: mlottner
ms.openlocfilehash: b8100fc78eced9aa26fe185a8d68244d8f665ff2
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933901"
---
# <a name="security-agent-event-aggregation"></a>Biztonsági ügynök eseményének összesítése

A IoT biztonsági ügynökök Azure Security Center a helyi eszközről gyűjtenek adatokat és rendszereseményeket, és ezeket az adatokat az Azure-felhőbe küldik feldolgozásra és elemzésre. A biztonsági ügynök számos típusú eseményt gyűjt, beleértve az új folyamatot és az új kapcsolatok eseményeit. Az új folyamat és az új kapcsolódási események is jogszerűen fordulhatnak elő egy másodpercen belül egy eszközön, és fontos, hogy a robusztus és az átfogó biztonság szempontjából milyen mennyiségű üzenetnek kell megtörténnie, ami kényszeríti az elküldött biztonsági ügynökök gyors elérését vagy túllépését IoT Hub kvóta-és költséghatékonysági korlátok. Ezek az események azonban rendkívül értékes biztonsági információkat tartalmaznak, amelyek elengedhetetlenek az eszköz védelméhez.

Ha csökkenteni szeretné a további kvótákat és költségeket az eszközök védelmének fenntartása mellett, Azure Security Center IoT-ügynökök összesítik az ilyen típusú eseményeket.

Az esemény összesítése alapértelmezés szerint **be** van kapcsolva, és bár nem ajánlott, a manuálisan **kikapcsolható** .

Az Összesítés jelenleg a következő típusú eseményekhez érhető el:
* ProcessCreate
* ConnectionCreate
* ProcessTerminate (csak Windows)

## <a name="how-does-event-aggregation-work"></a>Hogyan működik az események összesítése?
Ha az esemény összesítése **továbbra**is fennáll, Azure Security Center a IoT-ügynökök összesített eseményei az intervallum vagy az idő ablakban.
Az intervallum lejárta után az ügynök további elemzés céljából elküldi az összesített eseményeket az Azure-felhőbe.
Az összesített eseményeket a rendszer a memóriában tárolja, amíg el nem érkezik az Azure-felhőbe.

Az ügynök memória-lábnyomának csökkentése érdekében, ha az ügynök egy olyan eseményt gyűjt, amely már a memóriában van, akkor az ügynök növeli az adott esemény találati számát. Az összesítési idő ablakának átadásakor az ügynök elküldi a találatok számát minden egyes eseménynél. Az események összesítése egyszerűen az egyes összegyűjtött események találati számának összesítése.

Az események csak akkor tekinthetők azonosnak, ha teljesülnek a következő feltételek: 

* ProcessCreate események – ha a **parancssor**, a **végrehajtható fájl**, a **Felhasználónév** és a **userid** megegyezik
* ConnectionCreate események – ha a **commandLine**, a **userId**, az **Direction**, a **Local címet**, a **távoli címet**, a **protokollt** és a **célport** azonos
* ProcessTerminate események – ha a **végrehajtható fájl** és a **kilépési állapot** megegyezik

### <a name="working-with-aggregated-events"></a>Összesített események használata

Az összesítés során a rendszer elveti a nem összesített esemény-tulajdonságokat, és a log Analyticsben 0 értékkel jelenik meg. 
* ProcessCreate-események – a **folyamatazonosító**és a **parentProcessId** értéke 0
* ConnectionCreate-események – a **folyamatazonosító**és a **forrásport** értéke 0

## <a name="event-aggregation-based-alerts"></a>Esemény-összesítési alapú riasztások 
Az elemzést követően a IoT Azure Security Center biztonsági riasztásokat hoz létre a gyanús összesített eseményekhez. Az összesített eseményekből létrehozott riasztások csak egyszer jelennek meg minden összesített eseménynél.

Az Összesítés kezdési időpontja, a befejezési idő és a találatok száma az egyes eseményekhez Log Analytics az Event **ExtraDetails** mezőben, a vizsgálatok során való használatra. 

Minden összesített esemény a begyűjtött riasztások 24 órás időszakát jelenti. Az egyes események bal felső sarkában az Event Options (esemény beállításai) menü használatával **elvetheti** az egyes összesített eseményeket.    

## <a name="event-aggregation-twin-configuration"></a>Esemény-összesítési kettős konfiguráció
Módosítsa Azure Security Center konfigurációját az IoT esemény összesítéséhez az **azureiotsecurity** modul modul- [konfigurációs objektumán](how-to-agent-configuration.md) belül.

| Konfiguráció neve | Lehetséges értékek | Részletek | Megjegyzések |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | Események összesítésének engedélyezése/letiltása folyamat-létrehozási eseményekhez |
| aggregationIntervalProcessCreate | ISO8601 TimeSpan karakterlánca | A folyamat-létrehozási események összesítési időköze |
| aggregationEnabledConnectionCreate | boolean| Esemény-összesítés engedélyezése/letiltása a kapcsolat létrehozási eseményeihez |
| aggregationIntervalConnectionCreate | ISO8601 TimeSpan karakterlánca | Kapcsolódási időköz a kapcsolatok létrehozási eseményeihez |
| aggregationEnabledProcessTerminate | boolean | Események összesítésének engedélyezése/letiltása a folyamatok megszakításához | Csak Windows|
| aggregationIntervalProcessTerminate | ISO8601 TimeSpan karakterlánca | Az összesítési időköz a folyamat megszakításához | Csak Windows|
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

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan Azure Security Center a IoT biztonsági ügynök összesítése és az elérhető esemény-konfigurációs beállítások.

A IoT-telepítés Azure Security Centerának folytatásához használja a következő cikkeket:

- A [biztonsági ügynök hitelesítési módszereinek](concept-security-agent-authentication-methods.md) megismerése
- [Biztonsági ügynök](how-to-deploy-agent.md) kiválasztása és központi telepítése
- A IoT [szolgáltatás előfeltételeinek](service-prerequisites.md) áttekintése Azure Security Center
- Megtudhatja, hogyan [engedélyezheti Azure Security Center a IoT szolgáltatáshoz a IoT hub](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról a [Azure Security Center IoT – gyakori kérdések](resources-frequently-asked-questions.md)
