---
title: Eseményösszesítés
description: Ismerje meg az Azure Security Center for IoT-eseményösszesítést.
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
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: f72ef8cc5161bd6f885249e7d39344a57fa2368e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311417"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Azure Security Center az IoT-események összesítéséhez

Az Azure Security Center for IoT biztonsági ügynökök adatokat és rendszereseményeket gyűjt a helyi eszközről, és elküldi ezeket az adatokat az Azure-felhőbe feldolgozásra és elemzésre. A biztonsági ügynök számos eszközeseményt gyűjt, beleértve az új folyamatot és az új kapcsolati eseményeket. Mind az új folyamat, mind az új kapcsolatesemények egy másodpercen belül gyakran előfordulhatnak egy eszközön, és bár fontos a robusztus és átfogó biztonság szempontjából, a biztonsági ügynökök által küldött üzenetek száma gyorsan elérheti vagy meghaladhatja az IoT Hub kvótáját és a költségkorlátokat. Ezek az események azonban rendkívül értékes biztonsági információkat tartalmaznak, amelyek létfontosságúak az eszköz védelme szempontjából.

A további kvóta és költségek csökkentése érdekében az eszközök védelme érdekében az Azure Security Center for IoT-ügynökök az ilyen típusú eseményeket összesítik.

Az eseményösszesítés alapértelmezés szerint be van **kapcsolva,** és bár nem ajánlott, bármikor **manuálisan kikapcsolható.**

Az összesítés jelenleg a következő típusú eseményekhez érhető el:

* ProcessCreate
* Kapcsolatlétrehozása
* ProcessTerminate (csak Windows)

## <a name="how-does-event-aggregation-work"></a>Hogyan működik az eseményösszesítés?

Ha az eseményösszesítés **bekapcsolva**marad, az Azure Security Center az IoT-ügynökök összesített események az intervallum időszak vagy idő ablak.
Miután az intervallum időköz lejárt, az ügynök elküldi az összesített eseményeket az Azure-felhőtovábbi elemzéscéljából.
Az összesített események a memóriában vannak tárolva, amíg el nem küldik az Azure-felhőbe.

Az ügynök memórialábnyomának csökkentése érdekében, amikor az ügynök egy azonos eseményt gyűjt, amely már a memóriában van, az ügynök növeli az adott esemény találati számát. Amikor az összesítési időablak áthalad, az ügynök elküldi a találati számot az egyes eseménytípusok, amelyek előfordultak. Az eseményösszesítés egyszerűen az egyes összegyűjtött eseménytípusok találati számainak összesítése.

Az események csak akkor tekinthetők azonosnak, ha a következő feltételek teljesülnek:

* ProcessCreate events - when **commandLine**, **executable**, **username**, and **userid** are azonos
* ConnectionCreate events - ha **a parancssor,** **a userId**, **az irány**, a helyi **cím**, a **távoli cím**, a **protokoll és a **célport** azonosak
* ProcessTerminate események - ha **a végrehajtható** és **a kilépési állapot** azonos

### <a name="working-with-aggregated-events"></a>Összesített események kelése

Az összesítés során a nem összesített eseménytulajdonságok at elveti a rendszer, és 0-s értékkel jelennek meg a naplóelemzésben.

* ProcessCreate events - **processId**, és parentProcessId (ProcessCreate események létrehozása - és **a parentProcessId** beállítása 0
* ConnectionCreate events - **processId**, és **a forrásport** 0-ra van állítva

## <a name="event-aggregation-based-alerts"></a>Eseményösszesítésalapú riasztások

Az elemzés után az Azure Security Center for IoT biztonsági riasztásokat hoz létre a gyanús összesített eseményekhez. Az összesített eseményekből létrehozott riasztások minden összesített eseménynél csak egyszer jelennek meg.

Az összesítés kezdési időpontja, befejezési időpontja és a lehívási számláló minden esemény naplózza az esemény **ExtraDetails** mező ben Log Analytics a vizsgálatok során használható.

Minden összesített esemény az összegyűjtött riasztások 24 órás időszakát jelöli. Az egyes események bal felső részén található eseménybeállítások menüben minden egyes összesített eseményt **elvethet.**

## <a name="event-aggregation-twin-configuration"></a>Esemény összesítése ikerkonfiguráció

Módosítsa az Azure Security Center ioT-eseményösszesítésének konfigurációját az **azureiotsecurity** modul modul ikeridentitásának [ügynökkonfigurációs objektumán](how-to-agent-configuration.md) belül.

| Konfiguráció neve | Lehetséges értékek | Részletek | Megjegyzések |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | logikai | Eseményösszesítés engedélyezése/letiltása folyamatlétrehozási eseményekhez |
| aggregationIntervalProcessCreate | ISO8601 Timespan karakterlánc | A folyamatlétrehozási események összesítési időköze |
| aggregationEnabledConnectionCreate | logikai| Eseményösszesítés engedélyezése/letiltása kapcsolatlétrehozási eseményekhez |
| aggregationIntervalConnectionCreate | ISO8601 Timespan karakterlánc | A kapcsolat létrehozási eseményeinek összesítési időköze |
| aggregationEnabledProcessTerminate | logikai | Eseményösszesítés engedélyezése/letiltása folyamatmegszakítási eseményekesetén | Csak Windowson|
| aggregationIntervalProcessTerminate | ISO8601 Timespan karakterlánc | A folyamatmegszakítási események összesítési időköze | Csak Windowson|
|

## <a name="default-configurations-settings"></a>Alapértelmezett konfigurációs beállítások

| Konfiguráció neve | Alapértelmezett értékek |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | igaz |
| aggregationIntervalProcessCreate | "PT1H"|
| aggregationEnabledConnectionCreate | igaz |
| aggregationIntervalConnectionCreate | "PT1H"|
| aggregationEnabledProcessTerminate | igaz |
| aggregationIntervalProcessTerminate | "PT1H"|
|

## <a name="next-steps"></a>További lépések

Ebben a cikkben az Azure Security Center for IoT biztonsági ügynök összesítéséről, valamint a rendelkezésre álló eseménykonfigurációs beállításokról szerzett tudomást.

Az Azure Security Center iot-telepítéshez való használatának megkezdéséhez használja az alábbi cikkeket:

- A [biztonsági ügynökök hitelesítési módszereinek](concept-security-agent-authentication-methods.md) ismertetése
- [Biztonsági ügynök](how-to-deploy-agent.md) kiválasztása és üzembe helyezése
- Tekintse át az Azure Security Centert az [IoT-szolgáltatások előfeltételeihez](service-prerequisites.md)
- Megtudhatja, hogyan engedélyezheti az [Azure Security Center for IoT-szolgáltatást az IoT Hubban](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról az [Azure Security Center for IoT –GYIK-ből](resources-frequently-asked-questions.md)
