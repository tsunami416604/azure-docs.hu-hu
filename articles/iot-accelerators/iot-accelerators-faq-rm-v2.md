---
title: Távoli figyelési megoldás – gyorssegéd – GYIK – Azure | Microsoft Docs
description: Ez a cikk a távoli figyelési megoldási gyorssegédekkel kapcsolatos gyakori kérdésekre ad választ.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c410ac7f41adb623a7198320a1edced097778569
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "73826238"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Gyakori kérdések a távoli monitorozási megoldás gyorsítása terén

Lásd még az általános [gyakori kérdéseket](iot-accelerators-faq.md)is.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Mennyibe kerül az új távoli figyelési megoldás kiépítése?

Az új megoldás-gyorsító két telepítési lehetőséget kínál:

* Egy olyan *alapszintű* megoldás, amely a fejlesztők számára tervezett alacsonyabb fejlesztési költségeket, illetve a bemutatót vagy a koncepciót igazoló ügyfeleket keres.
* Egy *szabványos* beállítás, amely olyan vállalatok számára készült, akik termelésre kész infrastruktúrát kívánnak üzembe helyezni.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Hogyan biztosíthatom a költségeket a megoldásom fejlesztése során?

Amellett, hogy két különböző központi telepítést biztosít, az új távoli figyelési megoldásnak van egy olyan beállítása, amellyel engedélyezheti vagy letilthatja az összes szimulált eszközt igény szerint. A szimuláció letiltása csökkenti a megoldásban betöltött adatmennyiséget, így a teljes díjat.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Mi a különbség az alapszintű és a standard telepítési lehetőségek között? Hogyan dönteni a két telepítési lehetőség közül?

Az egyes központi telepítési lehetőségek különböző igényekre reagálnak. Az alapszintű üzembe helyezés a PoC és a kis pilóták megkezdéséhez és fejlesztéséhez készült. A minimálisan szükséges erőforrásokkal és alacsonyabb díjszabással könnyített architektúrát biztosít. A normál üzembe helyezést úgy terveztük, hogy éles használatra kész megoldást hozzon létre és szabja testre, és a szükséges elemek használatával üzembe helyezést biztosítson. A megbízhatóság és a méretezés érdekében az Application Service-szolgáltatások a Docker-tárolóként vannak felépítve, és a Orchestrator (Kubernetes by default) használatával telepíthetők. A Orchestrator feladata az alkalmazás üzembe helyezése, skálázása és felügyelete. Az aktuális igények alapján válasszon egy lehetőséget. A projekt szintjétől függően használhatja az egyiket, a másikat vagy a kettő kombinációját is.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Hogyan konfiguráljon egy dinamikus térképet az irányítópulton?

További információ: a [Térkép kulcsának frissítése az eszközök dinamikus térképen való megjelenítéséhez](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Hol találhatok információt a távoli figyelési megoldás előző verziójáról?

A távoli figyelési megoldáshoz tartozó gyorssegéd előző verzióját a IoT Suite távoli figyelési előre konfigurált megoldásnak hívták. Az archivált dokumentációt itt találja: [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/) .

### <a name="next-steps"></a>További lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [A távoli figyelési megoldás gyorsító képességeinek megismerése](quickstart-remote-monitoring-deploy.md)
* [A prediktív karbantartási megoldásgyorsító áttekintése](iot-accelerators-predictive-overview.md)
* [Csatlakoztatott gyári megoldás-gyorsító üzembe helyezése](quickstart-connected-factory-deploy.md)
* [IoT-biztonság létrehozása az alapoktól](/azure/iot-fundamentals/iot-security-ground-up)
