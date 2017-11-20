---
title: "A prediktív karbantartási megoldás bemutatója – Azure | Microsoft Docs"
description: "Az Azure IoT prediktív karbantartási előre konfigurált megoldás bemutatója."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3c48a716-b805-4c99-8177-414cc4bec3de
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: e9e0024c645d0e04e7cf9b17e440d7d8c10af232
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>A prediktív karbantartási előre konfigurált megoldás bemutatója

A prediktív karbantartási előre konfigurált megoldás olyan teljes körű megoldást nyújt az üzleti forgatókönyvekben, amely előrejelzi a meghibásodás várható idejét. Ezt az előre konfigurált megoldást proaktív módon használhatja olyan tevékenységekhez, mint a karbantartás optimalizálása. A megoldás kombinálja a fő Azure IoT Suite-szolgáltatásokat, mint az IoT Hub, a Stream Analytics, valamint egy [Azure Machine Learning][lnk-machine-learning]-munkaterületet. Ezen a munkaterületen egy modell található a repülőmotorok maradék hasznos élettartamának (RUL-jének) előrejelzéséhez egy nyilvános minta adatkészlet alapján. A megoldás az IoT üzleti forgatókönyv teljes megvalósítását biztosítja kiindulópontként, amellyel megtervezheti és megvalósíthatja ezt a megoldást a saját üzleti követelményeinek megfelelően.

## <a name="logical-architecture"></a>Logikai architektúra

A következő diagram az előre konfigurált megoldás logikai összetevőit vázolja fel:

![][img-architecture]

A kék elemek Azure-szolgáltatások, amelyek az előre konfigurált megoldás üzembe helyezésének régiójában vannak kiépítve. A régiók listája, amelyekben az előre konfigurált megoldás üzembe helyezhető, az [üzembe helyezés oldalon][lnk-azureiotsuite] jelenik meg.

A zöld elem egy repülőmotort jelképező szimulált eszköz. Ezekről a szimulált eszközökről a [szimulált eszközökkel](#simulated-devices) foglalkozó szakaszban tudhat meg többet.

A szürke elemek az *eszközfelügyeleti* képességeket megvalósító összetevőket jelölik. A prediktív karbantartási előre konfigurált megoldás jelenlegi kiadása nem biztosítja ezeket az erőforrásokat. Az eszközfelügyeletről a [távoli monitorozási előre konfigurált megoldás][lnk-remote-monitoring] szakaszban talál további információt.

## <a name="simulated-devices"></a>Szimulált eszközök

Az előre konfigurált megoldásban a szimulált eszközök repülőmotorokat jelképeznek. A megoldás egyetlen repülőhöz tartozó két motorral van kiépítve. Mindegyik motor négy típusú telemetriát bocsát ki: a 9. érzékelő, a 11. érzékelő, a 14. érzékelő és a 15. érzékelő telemetriáját, amelyek a motor fennmaradó hasznos élettartamának (RUL) kiszámítására szolgáló Machine Learning-modellhez szükséges adatokat biztosítják. Mindegyik szimulált eszköz a következő telemetriai üzeneteket küldi el az IoT Hubnak:

*Ciklusszám*. A ciklusok két és tíz óra közti időtartamú befejezett repülőutakat jelölnek. A rendszer félóránként rögzíti a telemetriai adatokat a repülőutak közben.

*Telemetria*. Négy érzékelő jelzi a motorattribútumokat. Az érzékelők általában a 9. érzékelő, 11. érzékelő, 14. érzékelő és 15. érzékelő felirattal rendelkeznek. Ez a négy érzékelő elegendő telemetriai adatot biztosít ahhoz, hogy hasznos eredmények érkezzenek az RUL-modellről. Az előre konfigurált megoldásban használt modell egy nyilvános adatkészletből jön létre, amely valódi motorérzékelők adatait tartalmazza. További információ a modell eredeti adatkészletből való létrehozásáról: [Cortana Intelligence Gallery Predictive Maintenance Template][lnk-cortana-analytics] (A Cortana Intelligence Gallery prediktív karbantartási sablonja).

A szimulált eszközök a megoldás részét képező IoT Hubról küldött következő parancsokat tudják kezelni:

| Parancs | Leírás |
| --- | --- |
| StartTelemetry |A szimuláció állapotát vezérli.<br/>Elindítja a telemetriát küldő eszközt |
| StopTelemetry |A szimuláció állapotát vezérli.<br/>Leállítja a telemetriát küldő eszközt |

Az IoT Hub nyugtázza az eszközparancsokat.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics-feladat

**Feladat: Telemetria** – a bejövő eszköz telemetriastreamjén működik két utasítással.

* Az első kiválasztja az összes telemetriát az eszközökről, és ezeket az adatokat Blob Storage-ba küldi. Itt a webalkalmazásban jelennek meg.
* A második kiszámítja az átlagos érzékelőértékeket egy kétperces csúszóablakban, és ezeket az adatokat az eseményközponton keresztül egy **eseményfeldolgozóba** küldi.

## <a name="event-processor"></a>Eseményfeldolgozó
Az **Event Processor Host** egy Azure-beli webes feladatban fut. Az **eseményfeldolgozó** a befejezett ciklusok átlagos érzékelőértékeit kezeli. Ezeket az értékeket azután egy olyan API-ra küldi, amely közzéteszi a betanított modellt egy motor RUL értékének kiszámításához. Az API-t a megoldás részeként üzembe helyezett Machine Learning-munkaterület teszi közzé.

## <a name="machine-learning"></a>Machine Learning
A Machine Learning összetevő egy olyan modellt használ, amely valódi repülőgépek adataiból jött lére. A Machine Learning-munkaterületet a megoldás csempéjéről érheti el, amely az [azureiotsuite.com][lnk-azureiotsuite] oldalon található. A csempe akkor érhető el, amikor a megoldás **Kész** állapotban van.


## <a name="next-steps"></a>Következő lépések
A prediktív karbantartási előre konfigurált megoldás fő összetevőinek áttekintése után érdemes lehet testre szabni a megoldást. Lásd: [Útmutatás az előre konfigurált megoldások testreszabásáról][lnk-customize].

Megismerheti az IoT Suite előre konfigurált megoldásának egyéb szolgáltatásait és funkcióit is:

* [Gyakran ismételt kérdések az IoT Suite-ról][lnk-faq]
* [IoT-biztonság létrehozása az alapoktól][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/