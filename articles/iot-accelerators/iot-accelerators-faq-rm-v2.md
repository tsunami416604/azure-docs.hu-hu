---
title: Távfigyelési megoldásgyorsító – gyakori kérdések – Azure | Microsoft dokumentumok
description: Ez a cikk választ ad a távoli figyelési megoldásgyorsítókkal kapcsolatos gyakori kérdésekre.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c410ac7f41adb623a7198320a1edced097778569
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826238"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Gyakori kérdések a távfigyelési megoldásgyorsítóhoz

Lásd még, az általános [GYIK](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Mennyibe kerül az új távoli figyelési megoldás kiépítése?

Az új megoldásgyorsító két telepítési lehetőséget kínál:

* Egy *alapvető* lehetőség, amelyet az alacsonyabb fejlesztési költségeket kereső fejlesztők vagy olyan ügyfelek számára terveztek, akik demót vagy koncepcióigazolást szeretnének készíteni.
* Egy *szabványos* beállítás, amelyet olyan vállalatok számára terveztek, amelyek termelésre kész infrastruktúrát kívánnak telepíteni.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Hogyan biztosíthatom, hogy a megoldás kidolgozása közben is lent tartsam a költségeimet?

Amellett, hogy két differenciált központi telepítést biztosít, az új távoli figyelési megoldás rendelkezik egy beállítással az összes szimulált eszköz igény szerinti engedélyezéséhez vagy letiltásához. A szimuláció letiltása csökkenti a megoldásban bevitt adatokat, és így a teljes költséget.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Mi a különbség az alapszintű és a szabványos üzembe helyezési lehetőségek között? Hogyan dönthetek a két telepítési lehetőség között?

Minden egyes telepítési lehetőség különböző igényekre válaszol. Az alapvető telepítés célja, hogy az induláshoz és fejlesztése PoC és a kis pilóták. Ez biztosítja az áramvonalas architektúra a minimálisan szükséges erőforrások és alacsonyabb költségek. A szabványos központi telepítés célja, hogy egy éles használatra kész megoldást hozzon létre és szabjon testre, és biztosítja a telepítést a szükséges elemekkel ennek megvalósításához. A megbízhatóság és a méretezés érdekében az alkalmazás mikroszolgáltatások Docker-tárolókként épülnek fel, és egy orchestrator (alapértelmezés szerint Kubernetes) használatával vannak telepítve. Az orchestrator felelős az alkalmazás üzembe helyezéséért, méretezéséért és felügyeletéért. A jelenlegi igényei alapján válasszon egy lehetőséget. Használhatja az egyiket, a másikat vagy a kettő kombinációját a projektszakasztól függően.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Hogyan konfigurálhatok dinamikus térképet az irányítópulton?

További információ: [Frissítési térképkulcs az eszközök dinamikus térképen való megtekintéséhez.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Hol találhatok információt a távfigyelési megoldás előző verziójáról?

A távoli figyelési megoldásgyorsító korábbi verziója az IoT Suite távoli figyelési előre konfigurált megoldásnéven volt ismert. Az archivált dokumentációt [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)a következő helyen találja: a.

### <a name="next-steps"></a>További lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [A távfigyelési megoldásgyorsító képességeinek megismerése](quickstart-remote-monitoring-deploy.md)
* [A prediktív karbantartási megoldásgyorsító áttekintése](iot-accelerators-predictive-overview.md)
* [Csatlakoztatott gyári megoldásgyorsító telepítése](quickstart-connected-factory-deploy.md)
* [IoT-biztonság létrehozása az alapoktól](/azure/iot-fundamentals/iot-security-ground-up)
