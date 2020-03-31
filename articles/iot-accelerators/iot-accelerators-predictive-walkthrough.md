---
title: A prediktív karbantartási megoldásgyorsító áttekintése – Azure | Microsoft Docs
description: Az Azure IoT prediktív karbantartási megoldásgyorsító áttekintése, amely előre jelzi azt a pontot, ahol egy üzleti forgatókönyv valószínűleg hiba következik be.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827415"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>A prediktív karbantartási megoldásgyorsító áttekintése

A prediktív karbantartási megoldásgyorsító olyan teljes körű megoldást nyújt az üzleti forgatókönyvekben, amely előrejelzi a meghibásodás várható idejét. Ezt a megoldásgyorsítót proaktív módon használhatja olyan tevékenységekhez, mint a karbantartás optimalizálása. A megoldás egyesíti a kulcsfontosságú Azure IoT-megoldásgyorsító kondiókat, például az IoT Hubot és az Azure Machine Learning-munkaterületet. [Azure Machine Learning][lnk-machine-learning] Ezen a munkaterületen egy modell található a repülőmotorok maradék hasznos élettartamának (RUL-jének) előrejelzéséhez egy nyilvános minta adatkészlet alapján. A megoldás az IoT üzleti forgatókönyv teljes megvalósítását biztosítja kiindulópontként, amellyel megtervezheti és megvalósíthatja ezt a megoldást a saját üzleti követelményeinek megfelelően.

A prediktív karbantartási megoldás [gyorsítókódja elérhető a GitHubon.](https://github.com/Azure/azure-iot-predictive-maintenance)

## <a name="logical-architecture"></a>Logikai architektúra

A következő diagram a megoldásgyorsító logikai összetevőit vázolja fel:

![Logikai architektúra][img-architecture]

A kék elemek Azure-szolgáltatások, amelyek a megoldásgyorsító üzembe helyezésének régiójában vannak kiépítve. A régiók listája, amelyekben a megoldásgyorsító üzembe helyezhető, az [üzembehelyezési oldalon][lnk-azureiotsolutions] jelenik meg.

A zöld elem egy szimulált repülőgép-motor. Ezekről a szimulált eszközökről a [szimulált eszközökkel](#simulated-devices) foglalkozó szakaszban tudhat meg többet.

A szürke elemek olyan összetevők, amelyek *eszközkezelési* képességeket valósítanak meg. A prediktív karbantartási megoldásgyorsító jelenlegi kiadása nem biztosítja ezeket az erőforrásokat. Az eszközkezelésről a [Távfigyelés iszákgyorsítója][lnk-remote-monitoring]című cikkben olvashat bővebben.

## <a name="azure-resources"></a>Azure-erőforrások

A kiépített erőforrások megtekintéséhez az Azure Portalon keresse meg a kiválasztott megoldásnévvel rendelkező erőforráscsoportot.

![Gyorssegéd-erőforrások][img-resource-group]

A megoldásgyorsító kiépítésekor egy e-mailt kap, amely tartalmazza a Machine Learning-munkaterületre mutató hivatkozást. A Machine Learning-munkaterületre a [Microsoft Azure IoT-megoldásgyorsítók][lnk-azureiotsolutions] lapon is navigálhat. Amikor a megoldás **Kész** állapotban van, elérhetővé válik egy csempe ezen az oldalon.

![Gépi tanulási modell][img-machine-learning]

## <a name="simulated-devices"></a>Szimulált eszközök

A megoldásgyorsítóban a szimulált eszköz egy repülőgép-hajtómű. A megoldás egyetlen repülőhöz tartozó két motorral van kiépítve. Mindegyik motor négy típusú telemetriát bocsát ki: a 9. érzékelő, a 11. érzékelő, a 14. érzékelő és a 15. érzékelő telemetriáját, amelyek a motor fennmaradó hasznos élettartamának (RUL) kiszámítására szolgáló Machine Learning-modellhez szükséges adatokat biztosítják. Mindegyik szimulált eszköz a következő telemetriai üzeneteket küldi el az IoT Hubnak:

*Ciklusszám*. A ciklus egy befejezett járat, amelynek időtartama 2 és 10 óra között van. A rendszer félóránként rögzíti a telemetriai adatokat a repülőutak közben.

*Telemetria*. Négy érzékelő rögzíti a motor attribútumait. Az érzékelők általában a 9. érzékelő, 11. érzékelő, 14. érzékelő és 15. érzékelő felirattal rendelkeznek. Ez a négy érzékelő elegendő telemetriai adatokat küld ahhoz, hogy hasznos eredményeket kapjon a RUL-modellből. A megoldásgyorsítóban használt modell egy nyilvános adatkészletből jön létre, amely valódi motorérzékelők adatait tartalmazza. További információ a modell eredeti adatkészletből való létrehozásáról: [Cortana Intelligence Gallery Predictive Maintenance Template][lnk-cortana-analytics] (A Cortana Intelligence Gallery prediktív karbantartási sablonja).

A szimulált eszközök a megoldás részét képező IoT Hubról küldött következő parancsokat tudják kezelni:

| Parancs | Leírás |
| --- | --- |
| StartTelemetry |A szimuláció állapotát vezérli.<br/>Elindítja a telemetriát küldő eszközt |
| StopTelemetry |A szimuláció állapotát vezérli.<br/>Leállítja a telemetriát küldő eszközt |

Az IoT Hub nyugtázza az eszközparancsokat.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics-feladat

**Feladat: Telemetriai** működik a bejövő eszköz telemetriai adatfolyam két utasítás használatával:

* Az első kiválasztja az összes telemetriát az eszközökről, és ezeket az adatokat Blob Storage-ba küldi. Innen a webalkalmazásban jelenik meg.
* A második kiszámítja az átlagos érzékelőértékeket egy kétperces csúszóablakban, és ezeket az adatokat az eseményközponton keresztül egy **eseményfeldolgozóba** küldi.

## <a name="event-processor"></a>Eseményfeldolgozó
Az **Event Processor Host** egy Azure-beli webes feladatban fut. Az **eseményfeldolgozó** a befejezett ciklusok átlagos érzékelőértékeit kezeli. Ezután átadja ezeket az értékeket egy betanított modellnek, amely kiszámítja a motor rul-ját. Egy API hozzáférést biztosít a modell hez egy Machine Learning-munkaterület, amely a megoldás részét képezi.

## <a name="machine-learning"></a>Machine Learning
A Machine Learning összetevő egy olyan modellt használ, amely valódi repülőgépek adataiból jött lére. A Machine Learning-munkaterületre a megoldás csempéjéről navigálhat a [azureiotsolutions.com][lnk-azureiotsolutions] lapon. A csempe akkor érhető el, amikor a megoldás **Kész** állapotban van.

A Machine Learning-modell sablonként érhető el, amely bemutatja, hogyan lehet az IoT-megoldásgyorsító szolgáltatásokon keresztül gyűjtött telemetriai adatokat. A Microsoft a nyilvánosan elérhető<sup>\[adatok 1\]</sup>alapján egy repülőgép-hajtómű [regressziós modelljét][lnk_regression_model] , valamint a modell használatára vonatkozó részletes útmutatást készített.

Az Azure IoT prediktív karbantartási megoldásgyorsító az ebből a sablonból létrehozott regressziós modellt használja. A modell üzembe helyezése az Azure-előfizetésben történik, és egy automatikusan generált API-n keresztül érhető el. A megoldás 4 (összesen 100) motor és 4 (összesen 21) érzékelő adatfolyam vizsgálati adatainak egy részét tartalmazza. Ezek az adatok elegendők ahhoz, hogy pontos eredményt biztosítsanak a betanított modellből.

*\[1\] A. Saxena és K. Goebel (2008). "Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (https://c3.nasa.gov/dashlink/resources/139/)NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>További lépések
A prediktív karbantartási megoldásgyorsító fő összetevőinek áttekintése után érdemes lehet testre szabni azt.

Az IoT-megoldásgyorsítók néhány egyéb funkcióját is megismerheti:

* [Gyakran ismételt kérdések az IoT-megoldásgyorsítókról][lnk-faq]
* [IoT-biztonság létrehozása az alapoktól][lnk-security-groundup]

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
