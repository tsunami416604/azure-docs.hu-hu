---
title: Eszköz szimulálása áttekintése – Azure |} A Microsoft Docs
description: Az Eszközszimuláció megoldásgyorsító és annak leírását.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 173963470a77932186b3c51f17ccb406b32a13f3
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452224"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Az eszközszimulációs megoldásgyorsító áttekintése

Egy felhőalapú IoT-megoldás az eszközök csatlakozzon a felhőbeli végpont küldhet telemetriát gyűjthet, például a hőmérsékletet, helyét és állapotát. A megoldás ezt a telemetriát, lehetővé téve a műveleteket, vagy abból insights használ fel.

IoT-megoldások fejlesztésekor a Kísérletezési és tesztelése a folyamat alapvető részből áll. Szimuláció Ez a folyamat során fontos eszköz. Eszközszimuláció segítségével:

* Gyorsan egy prototípust, majd futtassa, és beállításával majd újrafuttathatja a szimulált eszköz viselkedésének menet közben. Ez a folyamat lehetővé teszi ki a cél előtt költséges hardverberuházás igazolásához. Egyéni eszközét a webes felhasználói felületen, a prototípus-eszköz létrehozása másodpercek alatt hozhat létre.
* Valós eszközviselkedést szimulálva ellenőrizheti, hogy a megoldás a vártnak megfelelően működik-e minden szinten. JavaScript használatával való valósághű szimulált telemetria jöjjön létre összetett eszközműködés is parancsfájlt.
* Méretezési csoport teszteléséhez megoldását, szimuláló normál, maximális és a maximális betöltési feltételek mellett. Méretezési csoport tesztek is segítenek a megfelelő méretű a megoldás futtatásához szükséges Azure-erőforrások.

![Minta drónos szimuláció](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Az Eszközszimuláció meghatározhatja eszközmodellek a valódi eszközön szimulálásához. Ez a modell üzenetformátumok ikertulajdonságok és módszereket tartalmazza. Összetett eszközműködés JavaScript emellett szimulálhatja is.

Több ezer bármely IoT hubhoz csatlakozó eszközök esetében is futtassa a szimulációkat. Annak érdekében, a tesztelés, igény szerint telepítheti egy IoT hubot, valamint Eszközszimuláció, egy különálló környezetben.

Eszközszimuláció díjmentes. Azonban Eszközszimuláció üzembe helyezi az Azure-előfizetéshez a felhőben, és Azure-erőforrások felhasználását. Eszközszimuláció nem felel meg a követelményeket, ha a [forráskódja is elérhető a Githubon](https://github.com/Azure/device-simulation-dotnet) , másolása és módosítása.

## <a name="sample-simulations"></a>Minta szimulációk

Eszközszimuláció üzembe helyezése, ha megjelenik néhány minta szimulációkat és a mintául szolgáló eszközök. Ezek a minták segítségével megtudhatja, hogyan használhatja az Eszközszimuláció. Első lépésként futtassa egy [szimuláció, amely szimulálja 10 látható minta](quickstart-device-simulation-deploy.md). Emellett [hozzon létre saját szimuláció, az egyik megadott számos minta eszköz](iot-accelerators-device-simulation-create-simulation.md).

![Szimuláció konfigurálása](media/iot-accelerators-device-simulation-overview/SampleSimulation.png)

## <a name="custom-simulated-devices"></a>Egyéni szimulált eszközök

Használhatja az Eszközszimuláció való [egyéni modelleket hozhat létre](iot-accelerators-device-simulation-create-custom-device.md) használata a szimulációkat. Ha például meghatározhat egy új hűtőszekrények eszköz modellje, amely hőmérséklettel és páratartalommal kapcsolatos telemetriai adatokat küld. Egyéni szimulált eszközök a véletlenszerű egyszerű eszközműködés, növekvő vagy csökkenő telemetriaértékeket ideális megoldást jelentenek.

![Eszközmodell létrehozása](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Speciális szimulált eszközök

Ha több felügyeleti eszközt küld telemetriai értékek van szüksége, egy speciális eszközmodell is használhatja. Speciális eszközök segítségével kezelheti az elküldött telemetriai értékeket a JavaScript-támogatás engedélyezése. Például a belső hőmérséklet parkoltatott autó sikerült szimulálása, a gyakori elérésű napsütéses nap – mint a külső hőmérséklet növekedése, exponenciálisan növeli a belső hőmérséklet.

Speciális eszközök lehetővé teszik [létrehozása és feltöltése a saját eszköz modellek](iot-accelerators-device-simulation-advanced-device.md) eszköz JSON-definíció állnak, amelyek fájl- és a kapcsolódó JavaScript-fájlok.

Speciális eszközök lehetővé teszik:

* Adja meg a telemetriai adatok típusa mellett az eszközről küldött az üzenet formátuma.
* Egyéni parancsfájl segítségével hozza létre, amely az eszköz állapotának karbantartásához telemetriaértékeket.
* Egyéni parancsfájl használatával adja meg, hogyan válaszol a szimulált eszköz módszerek.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az Eszközszimuláció megoldásgyorsító és annak képességeit. Ismerkedés a megoldásgyorsító használatával, folytassa a rövid útmutató:

> [!div class="nextstepaction"]
> [Helyezhet üzembe és futtathat egy IoT-eszköz szimulálása az Azure-ban](quickstart-device-simulation-deploy.md)
