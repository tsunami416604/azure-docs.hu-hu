---
title: Távoli figyelés megoldásgyorsító – gyakori kérdések |} Microsoft Docs
description: Gyakori kérdések a megoldásgyorsító távoli figyelése
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: a65f2a90aedf5aa50016a6c5e5019094f202bf55
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Gyakori kérdések a megoldásgyorsító távoli figyelése

Lásd még az általános [gyakran ismételt kérdések](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Nem mennyibe kiépíteni az új távoli figyelési megoldást igényelnek?

Az új megoldásgyorsító két telepítési lehetőségeket nyújtja:

* A *alapvető* lehetőség, a fejlesztők alacsonyabb fejlesztési költségek vagy az ügyfelek keresése bemutató vagy a koncepció igazolása keres.
* A *szabványos* lehetőséget szeretne telepíteni egy éles használatra kész infrastruktúra vállalatok számára készült.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Hogyan tudom arról, hogy I a költséges alacsonyan tartása szeretnék a saját megoldás fejlesztése során?

Csupán két eltérő központi telepítések, az új távoli figyelési megoldást igényelnek engedélyezheti vagy tilthatja le a szimulált eszköz, az igény szerinti beállítás van. A szimuláció letiltása csökkenti az adatokat, a megoldás, és így a teljes költség szempontjából okozhatnak.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Mi a különbség a a basic és standard telepítési lehetőségek között? Hogyan dönt a két telepítési lehetőségek között?

Telepítési lehetőségek válaszol-e a különböző igényeinek. Az alapszintű központi telepítést célja, hogy első lépések és PoC és kisméretű próbakörnyezetek fejlesztéséhez. Biztosít egy egyszerűbb architektúra alacsonyabb költségekkel és a minimálisan szükséges erőforrásokat. Szabványos telepítési célja, hogy létrehozása és testreszabása üzemi használatra kész megoldást, és vegye figyelembe, hogy a szükséges elemeket a központi telepítés biztosít. A megbízhatóság és a skála alkalmazás mikroszolgáltatások Docker tárolóként beépített, és az orchestrator (alapértelmezés szerint Kubernetes) használatával telepíthetők. A koordináló rendszer feladata telepítés, a méretezés és a az alkalmazás felügyelete szempontjából. Válasszon egy beállítást, a jelenlegi igények alapján. Használhat egy, a másik vagy mindkettőt, attól függően, hogy a projekt szakasza.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Hogyan konfigurálhatók a dinamikus térképre az irányítópulton?

További információkért lásd: [frissítési leképezés kulcs eszközök megtekintéséhez a dinamikus térképen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="next-steps"></a>További lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [A távoli felügyeleti megoldásgyorsító lehetőségeinek felfedezése](iot-accelerators-remote-monitoring-explore.md)
* [A prediktív karbantartási megoldásgyorsító áttekintése](../iot-suite/iot-suite-predictive-overview.md)
* [Csatlakoztatott gyári megoldás gyorsító – áttekintés](iot-accelerators-connected-factory-overview.md)
* [Az IoT biztonsági másolatot az alapoktól](../iot-suite/securing-iot-ground-up.md)
