---
title: Eszközszimuláció áttekintése - Azure | Microsoft dokumentumok
description: Az Eszközszimulációi megoldásgyorsító és annak képességeinek leírása.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: f58eb05ed582cf18157a76f4d637d72a228f4e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65464866"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Az eszközszimulációs megoldásgyorsító áttekintése

Egy felhőalapú IoT-megoldás, az eszközök egy felhővégponthoz csatlakozik, hogy telemetriai adatokat, például hőmérsékletet, helyet és állapotot küldjön. A megoldás felhasználja ezt a telemetriai adatokat, lehetővé téve, hogy műveleteket, vagy elemzéseket nyerjen belőle.

IoT-megoldás kifejlesztésekor a kísérletezés és a tesztelés a folyamat alapvető részét képezik. A szimuláció fontos eszköz ebben a folyamatban. Az eszközszimulációsegítségével:

* Gyorsan működésbe hozhatja a prototípust, majd menet közben módosíthatja a szimulált eszköz viselkedését. Ez a folyamat lehetővé teszi, hogy bizonyítsa ki az ötletet, mielőtt költséges hardverbe fektetne be. Egyéni eszközöket hozhat létre a webes felhasználói felületen keresztül, hogy másodpercek alatt létrehozzon egy prototípus eszközt.
* Ellenőrizze, hogy a megoldás az eszközről a megoldásra várt módon működik a valós eszközök viselkedésének szimulálásával. A JavaScript használatával összetett eszközviselkedéseket parancsfájlálhat reális szimulált telemetriai adatok létrehozásához.
* A méretezési teszt az oldat szimulálásával normál, csúcs, és túl csúcsterhelési feltételeket. A tesztek méretezése is segít a megfelelő méretű a megoldás futtatásához szükséges Azure-erőforrásokat.

![Minta drón szimuláció](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Az Eszközszimuláció segítségével eszközmodelleket határozhat meg a valódi eszközök szimulálásához. Ez a modell üzenetformátumokat, ikertulajdonságokat és metódusokat tartalmaz. Összetett eszközviselkedéseket is szimulálhat JavaScript-tel.

Szimulációkat futtathat egy-ezer, bármely IoT-központhoz csatlakozó eszközhöz. A tesztelés hez szükség esetén üzembe helyezhet egy IoT-központot az eszközszimulációval együtt egy önálló környezetben.

Eszköz szimuláció ingyenes. A Device Simulation azonban üzembe helyezi az Azure-előfizetését a felhőben, és azure-erőforrásokat használ fel. Ha az Eszközszimuláció nem felel meg az Ön igényeinek, a [forráskód a GitHubon is elérhető,](https://github.com/Azure/device-simulation-dotnet) hogy másolhassa és módosíthassa.

## <a name="sample-simulations"></a>Mintaszimulációk

Az Eszközszimuláció telepítésekor mintaszimulációkat és mintaeszközöket kap. Ezekkel a mintákkal megtudhatja, hogyan használhatja az Eszközszimulációt. A kezdéshez futtasson egy [mintaszimulációt, amely 10 teherautót szimulál.](quickstart-device-simulation-deploy.md) Saját szimulációt is [létrehozhat a számos mintaeszköz egyikével.](iot-accelerators-device-simulation-create-simulation.md)

![Szimuláció konfigurálása](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Egyéni szimulált eszközök

Az Eszközszimuláció segítségével [egyéni eszközmodelleket hozhat létre](iot-accelerators-device-simulation-create-custom-device.md) a szimulációkban való használatra. Például definiálhat egy új hűtőszekrény eszköz modell, amely elküldi a hőmérséklet és a páratartalom telemetriai adatokat. Az egyéni szimulált eszközök ideálisak az egyszerű eszközviselkedésekhez véletlenszerű, növekmény- vagy telemetriai értékekkel.

![Eszközmodell létrehozása](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Speciális szimulált eszközök

Ha az eszköz által küldött telemetriai értékek további szabályozására van szüksége, használhat egy speciális eszközmodellt. A speciális eszközmodellek lehetővé teszik a JavaScript-támogatás engedélyezését az elküldött telemetriai értékek kezeléséhez. Például, akkor szimulálja a belső hőmérséklet egy parkoló autó egy forró napsütéses napon - a külső hőmérséklet emelkedése, a belső hőmérséklet exponenciálisan emelkedik.

A speciális eszközmodellek lehetővé teszik, hogy [saját eszközmodelleket hozzon létre és töltsön fel,](iot-accelerators-device-simulation-advanced-device.md) amelyek Egy JSON-eszközdefiníciós fájlból és a megfelelő JavaScript-fájlokból állnak.

A speciális eszközmodellek lehetővé teszik:

* Adja meg az eszközről küldött üzenetformátumot a telemetriai típusokmellett.
* Egyéni parancsfájlok használatával telemetriai értékeket hozhat létre, amelyek az eszköz állapotát az idő múlásával fenntartják.
* Egyéni parancsfájlok használatával megadhatja, hogy a szimulált eszköz hogyan reagáljon a metódusokra.

## <a name="next-steps"></a>További lépések

Ebben a cikkben az Eszközszimuláció megoldásgyorsítóról és annak képességeiről szerzett tudomást. A megoldásgyorsító használatának megkezdéséhez folytassa a rövid útmutatóval:

> [!div class="nextstepaction"]
> [IoT-eszközszimuláció üzembe helyezése és futtatása az Azure-ban](quickstart-device-simulation-deploy.md)
