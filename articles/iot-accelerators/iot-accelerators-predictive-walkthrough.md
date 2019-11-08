---
title: A prediktív karbantartási megoldásgyorsító áttekintése – Azure | Microsoft Docs
description: Az Azure IoT prediktív karbantartási megoldásának gyorsítása, amely azt jelzi, hogy a hiba várhatóan egy üzleti forgatókönyv esetén valószínűleg előfordul.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827415"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>A prediktív karbantartási megoldásgyorsító áttekintése

A prediktív karbantartási megoldásgyorsító olyan teljes körű megoldást nyújt az üzleti forgatókönyvekben, amely előrejelzi a meghibásodás várható idejét. Ezt a megoldásgyorsítót proaktív módon használhatja olyan tevékenységekhez, mint a karbantartás optimalizálása. A megoldás kombinálja a fő Azure IoT-megoldás-gyorsító szolgáltatásokat, például a IoT Hub és egy [Azure Machine learning][lnk-machine-learning] munkaterületet. Ezen a munkaterületen egy modell található a repülőmotorok maradék hasznos élettartamának (RUL-jének) előrejelzéséhez egy nyilvános minta adatkészlet alapján. A megoldás az IoT üzleti forgatókönyv teljes megvalósítását biztosítja kiindulópontként, amellyel megtervezheti és megvalósíthatja ezt a megoldást a saját üzleti követelményeinek megfelelően.

A prediktív karbantartási megoldás gyorsító [kódja a githubon érhető el](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Logikai architektúra

A következő diagram a megoldásgyorsító logikai összetevőit vázolja fel:

![Logikai architektúra][img-architecture]

A kék elemek Azure-szolgáltatások, amelyek a megoldásgyorsító üzembe helyezésének régiójában vannak kiépítve. Azon régiók listája, amelyekben a megoldás-gyorsító megjelenik a [kiépítési lapon][lnk-azureiotsolutions].

A zöld elemek szimulált repülőgép-hajtóművek. Ezekről a szimulált eszközökről a [szimulált eszközökkel](#simulated-devices) foglalkozó szakaszban tudhat meg többet.

A szürke elemek olyan összetevők, amelyek implementálják az *eszközök felügyeleti* képességeit. A prediktív karbantartási megoldásgyorsító jelenlegi kiadása nem biztosítja ezeket az erőforrásokat. Az eszközkezelés további megismeréséhez tekintse meg a [távoli figyelési megoldás gyorsító][lnk-remote-monitoring]eszközét.

## <a name="azure-resources"></a>Azure-erőforrások

A kiépített erőforrások megtekintéséhez az Azure Portalon keresse meg a kiválasztott megoldásnévvel rendelkező erőforráscsoportot.

![Gyorssegéd-erőforrások][img-resource-group]

A megoldásgyorsító kiépítésekor egy e-mailt kap, amely tartalmazza a Machine Learning-munkaterületre mutató hivatkozást. A Machine Learning munkaterületet a [Microsoft Azure IoT megoldás-gyorsítók][lnk-azureiotsolutions] lapról is megnyithatja. Amikor a megoldás **Kész** állapotban van, elérhetővé válik egy csempe ezen az oldalon.

![Machine learning-modell][img-machine-learning]

## <a name="simulated-devices"></a>Szimulált eszközök

A megoldás-gyorsító egy szimulált eszköz egy légijármű-motor. A megoldás egyetlen repülőhöz tartozó két motorral van kiépítve. Mindegyik motor négy típusú telemetriát bocsát ki: a 9. érzékelő, a 11. érzékelő, a 14. érzékelő és a 15. érzékelő telemetriáját, amelyek a motor fennmaradó hasznos élettartamának (RUL) kiszámítására szolgáló Machine Learning-modellhez szükséges adatokat biztosítják. Mindegyik szimulált eszköz a következő telemetriai üzeneteket küldi el az IoT Hubnak:

*Ciklusszám*. A ciklus egy befejezett, két és tíz óra közötti időtartamú járat. A rendszer félóránként rögzíti a telemetriai adatokat a repülőutak közben.

*Telemetria*. Négy érzékelő rögzíti a motor attribútumait. Az érzékelők általában a 9. érzékelő, 11. érzékelő, 14. érzékelő és 15. érzékelő felirattal rendelkeznek. Ez a négy érzékelő elegendő telemetria küld, hogy a RUL modellből hasznos eredményeket kapjon. A megoldásgyorsítóban használt modell egy nyilvános adatkészletből jön létre, amely valódi motorérzékelők adatait tartalmazza. További információ a modell eredeti adatkészletből való létrehozásáról: [Cortana Intelligence Gallery prediktív karbantartási sablon][lnk-cortana-analytics].

A szimulált eszközök a megoldás részét képező IoT Hubról küldött következő parancsokat tudják kezelni:

| Parancs | Leírás |
| --- | --- |
| StartTelemetry |A szimuláció állapotát vezérli.<br/>Elindítja a telemetriát küldő eszközt |
| StopTelemetry |A szimuláció állapotát vezérli.<br/>Leállítja a telemetriát küldő eszközt |

Az IoT Hub nyugtázza az eszközparancsokat.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics-feladat

**Feladat: Telemetria** – a bejövő eszköz telemetriastreamjén működik két utasítással.

* Az első kiválasztja az összes telemetriát az eszközökről, és ezeket az adatokat Blob Storage-ba küldi. Itt látható a webalkalmazásban.
* A második kiszámítja az átlagos érzékelőértékeket egy kétperces csúszóablakban, és ezeket az adatokat az eseményközponton keresztül egy **eseményfeldolgozóba** küldi.

## <a name="event-processor"></a>Eseményfeldolgozó
Az **Event Processor Host** egy Azure-beli webes feladatban fut. Az **eseményfeldolgozó** a befejezett ciklusok átlagos érzékelőértékeit kezeli. Ezután átadja ezeket az értékeket egy betanított modellnek, amely kiszámítja a motor RUL. Az API hozzáférést biztosít a modellhez egy Machine Learning munkaterületen, amely a megoldás részét képezi.

## <a name="machine-learning"></a>Machine Learning
A Machine Learning összetevő egy olyan modellt használ, amely valódi repülőgépek adataiból jött lére. A [azureiotsolutions.com][lnk-azureiotsolutions] lapon megnyithatja a megoldás csempéje Machine learning munkaterületét. A csempe akkor érhető el, amikor a megoldás **Kész** állapotban van.

A Machine Learning modell olyan sablonként érhető el, amely bemutatja, hogyan használhatók a IoT-megoldás gyorsító szolgáltatásain keresztül gyűjtött telemetria. A Microsoft egy légijármű-motor [regressziós modelljét][lnk_regression_model] a nyilvánosan elérhető<sup> adat\[1\]</sup>alapján, valamint a modell használatának lépésenkénti útmutatóját is kiépítette.

Az Azure IoT prediktív karbantartási megoldásgyorsító az ebből a sablonból létrehozott regressziós modellt használja. A modell üzembe helyezése az Azure-előfizetésében történik, és egy automatikusan generált API-n keresztül érhető el. A megoldás a 4 (100 összesen) motor és a 4 (21 teljes) érzékelő adatstreamek tesztelési értékének egy részhalmazát tartalmazza. Ezek az adatok elegendők ahhoz, hogy pontos eredményt biztosítsanak a betanított modellből.

*\[1\] A. Saxena és A K. Goebel (2008). "Turbofan Engine romlási szimulációs adatkészlete", NASA Ames-i, az adattárház (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field mező, CA*

## <a name="next-steps"></a>További lépések
A prediktív karbantartási megoldásgyorsító fő összetevőinek áttekintése után érdemes lehet testre szabni azt.

Emellett megismerheti a IoT-megoldási gyorssegédek egyéb funkcióit is:

* [Gyakori kérdések a IoT-megoldási gyorssegédekről][lnk-faq]
* [IoT biztonság az alapoktól][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
