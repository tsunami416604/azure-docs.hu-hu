---
title: A prediktív karbantartási megoldásgyorsító áttekintése – Azure | Microsoft Docs
description: Az Azure IoT prediktív karbantartási megoldásgyorsító áttekintése.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: dobett
ms.openlocfilehash: bdb9b48549d24565fdafb9ac1c3215029445793c
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100492"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>A prediktív karbantartási megoldásgyorsító áttekintése

A prediktív karbantartási megoldásgyorsító olyan teljes körű megoldást nyújt az üzleti forgatókönyvekben, amely előrejelzi a meghibásodás várható idejét. Ezt a megoldásgyorsítót proaktív módon használhatja olyan tevékenységekhez, mint a karbantartás optimalizálása. A megoldás kombinálja a fő Azure IoT megoldás megoldásgyorsítók szolgáltatásokat, mint az IoT Hub és a egy [Azure Machine Learning] [ lnk-machine-learning] munkaterületen. Ezen a munkaterületen egy modell található a repülőmotorok maradék hasznos élettartamának (RUL-jének) előrejelzéséhez egy nyilvános minta adatkészlet alapján. A megoldás az IoT üzleti forgatókönyv teljes megvalósítását biztosítja kiindulópontként, amellyel megtervezheti és megvalósíthatja ezt a megoldást a saját üzleti követelményeinek megfelelően.

A prediktív karbantartási megoldásgyorsító [kódja a Githubon elérhető](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Logikai architektúra

A következő diagram a megoldásgyorsító logikai összetevőit vázolja fel:

![Logikai architektúra][img-architecture]

A kék elemek Azure-szolgáltatások, amelyek a megoldásgyorsító üzembe helyezésének régiójában vannak kiépítve. A régiók listája, amelyekben a megoldásgyorsító üzembe helyezhető, az [üzembehelyezési oldalon][lnk-azureiotsuite] jelenik meg.

A zöld elem egy szimulált repülő motorját. Ezekről a szimulált eszközökről a [szimulált eszközökkel](#simulated-devices) foglalkozó szakaszban tudhat meg többet.

A szürke elemek megvalósító összetevőket *Eszközkezelés* képességeket. A prediktív karbantartási megoldásgyorsító jelenlegi kiadása nem biztosítja ezeket az erőforrásokat. Eszközök felügyeletével kapcsolatos további tudnivalókért tekintse meg a [távoli figyelési megoldásgyorsító][lnk-remote-monitoring].

## <a name="azure-resources"></a>Azure-erőforrások

A kiépített erőforrások megtekintéséhez az Azure Portalon keresse meg a kiválasztott megoldásnévvel rendelkező erőforráscsoportot.

![Gyorsító erőforrások][img-resource-group]

A megoldásgyorsító kiépítésekor egy e-mailt kap, amely tartalmazza a Machine Learning-munkaterületre mutató hivatkozást. A Machine Learning-munkaterületet is elérheti a [a Microsoft Azure IoT-Megoldásgyorsítók] [ lnk-azureiotsuite] lapot. Amikor a megoldás **Kész** állapotban van, elérhetővé válik egy csempe ezen az oldalon.

![Machine learning-modell][img-machine-learning]

## <a name="simulated-devices"></a>Szimulált eszközök

A megoldásgyorsító egy szimulált eszközök repülőmotorokat jelképeznek. A megoldás egyetlen repülőhöz tartozó két motorral van kiépítve. Mindegyik motor négy típusú telemetriát bocsát ki: 11. érzékelő, 14. érzékelő és 15. érzékelő Telemetriáját 9 érzékelője adja meg a motor rul értékének kiszámításához a Machine Learning-modellhez szükséges adatokat. Mindegyik szimulált eszköz a következő telemetriai üzeneteket küldi el az IoT Hubnak:

*Ciklusszám*. A ciklus a következő két és tíz óra közti időtartamú befejezett repülőjegyet. A rendszer félóránként rögzíti a telemetriai adatokat a repülőutak közben.

*Telemetria*. Nincsenek négy érzékelő jelzi a motor attribútumok rögzíti. Az érzékelők általában a 9. érzékelő, 11. érzékelő, 14. érzékelő és 15. érzékelő felirattal rendelkeznek. Ez a négy érzékelő elegendő ahhoz, hogy hasznos eredmények érkezzenek az RUL-modellről telemetriát küldjön. A megoldásgyorsítóban használt modell egy nyilvános adatkészletből jön létre, amely valódi motorérzékelők adatait tartalmazza. További információ a modell eredeti adatkészletből való létrehozásáról: [Cortana Intelligence Gallery Predictive Maintenance Template][lnk-cortana-analytics] (A Cortana Intelligence Gallery prediktív karbantartási sablonja).

A szimulált eszközök a megoldás részét képező IoT Hubról küldött következő parancsokat tudják kezelni:

| Parancs | Leírás |
| --- | --- |
| StartTelemetry |A szimuláció állapotát vezérli.<br/>Elindítja a telemetriát küldő eszközt |
| StopTelemetry |A szimuláció állapotát vezérli.<br/>Leállítja a telemetriát küldő eszközt |

Az IoT Hub nyugtázza az eszközparancsokat.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics-feladat

**Feladat: Telemetria** működik két utasítással használatával a bejövő eszköz telemetriai Stream:

* Az első kiválasztja az összes telemetriát az eszközökről, és ezeket az adatokat Blob Storage-ba küldi. Itt azt a rendszer vizualizálja a webalkalmazásban.
* A második kiszámítja az átlagos érzékelőértékeket egy kétperces csúszóablakban, és ezeket az adatokat az eseményközponton keresztül egy **eseményfeldolgozóba** küldi.

## <a name="event-processor"></a>Eseményfeldolgozó
Az **Event Processor Host** egy Azure-beli webes feladatban fut. Az **eseményfeldolgozó** a befejezett ciklusok átlagos érzékelőértékeit kezeli. Ezután továbbítja ezeket az értékeket egy betanított modellt egy motor rul értékének kiszámítására. Egy API-t a modell a Machine Learning-munkaterület a megoldás részét képező hozzáférést biztosít.

## <a name="machine-learning"></a>Machine Learning
A Machine Learning összetevő egy olyan modellt használ, amely valódi repülőgépek adataiból jött lére. A Machine Learning-munkaterületet a megoldás csempéjéről érheti el, amely az [azureiotsuite.com][lnk-azureiotsuite] oldalon található. A csempe akkor érhető el, amikor a megoldás **Kész** állapotban van.

Machine Learning-modellhez, amely bemutatja, hogyan működik az IoT-megoldás gyorsító szolgáltatásokon keresztül gyűjtött telemetriával sablonként érhető el. A Microsoft felépítette egy [regressziós modell] [ lnk_regression_model] felépítette egy repülőmotor nyilvánosan elérhető adatok alapján<sup>\[1\]</sup>, és lépésenkénti útmutatóját a modell használatának módjáról.

Az Azure IoT prediktív karbantartási megoldásgyorsító az ebből a sablonból létrehozott regressziós modellt használja. A modell az Azure-előfizetésben helyezi üzembe, és a egy automatikusan létrehozott API-n keresztül elérhetővé tett. A megoldás tartalmaz egy részhalmazt a tesztelési (összesen 100-ból) 4 motort és (összesen 21-ből) 4 streamet ábrázol. Ezek az adatok elegendők ahhoz, hogy pontos eredményt biztosítsanak a betanított modellből.

*\[1\] A. Saxena és K. Goebel (2008). „Turbofan Engine Degradation Simulation Data Set”, NASA Ames Prognostics Data Repository (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>További lépések
A prediktív karbantartási megoldásgyorsító fő összetevőinek áttekintése után érdemes lehet testre szabni azt.

Emellett megismerheti az IoT-megoldásgyorsítók egyéb funkcióit:

* [Gyakran ismételt kérdések az IoT-megoldásgyorsítókról][lnk-faq]
* [IoT-biztonság létrehozása az alapoktól][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
