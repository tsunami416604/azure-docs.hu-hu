---
title: Az Azure IoT Edge bemutatása | Microsoft Docs
description: Az Azure IoT Edge szolgáltatás áttekintése
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 10/28/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: 1ba133acda414d9779e2fb10150bbdd57285e9a5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76511212"
---
# <a name="what-is-azure-iot-edge"></a>Az Azure IoT Edge bemutatása

Az Azure IoT Edge szolgáltatás a felhőbeli elemzéseket és egyéni üzleti logikákat eszközökre helyezi át, hogy a szervezet az üzleti elemzésekre tudjon koncentrálni az adatkezelés helyett. Az IoT-megoldás horizontális felskálázása az üzleti logika szabványos tárolókba csomagolásával, majd telepítheti ezeket a tárolókat bármelyik eszközére, és figyelheti az egészet a felhőből.

Az elemzések jelentős üzleti értékeket teremthetnek az IoT-megoldásokban, de nem szükséges minden elemzést a felhőben végezni. Ha a lehető leggyorsabban szeretne reagálni a vészhelyzetekre, futtathat anomáliadetektálási számítási feladatokat a peremhálózaton. Ha csökkenteni szeretné a sávszélesség-költségeket, és el szeretné kerülni a terabájtnyi nyers adat átvitelét, akkor az adatokat helyileg tisztíthatja és összesítheti, majd csak elemzésre küldje el az elemzéseket a felhőbe.

Az Azure IoT Edge három összetevőből áll:

* **Az IoT Edge-modulok olyan tárolók,** amelyek Azure-szolgáltatásokat, külső szolgáltatásokat vagy saját kódot futtatnak. A modulokat IoT Edge-eszközökön lehet üzembe helyezni, és a végrehajtásuk is helyileg, az eszközökön zajlik.
* Az **IoT Edge futásidejű** fut minden IoT Edge-eszközön, és kezeli az egyes eszközökre telepített modulokat.
* A felhőalapú felület lehetővé teszi az IoT **Edge-eszközök** távoli figyelése és kezelése.

>[!NOTE]
>Az Azure IoT Edge az IoT Hub ingyenes és standard szintű csomagjaiban érhető el. Az ingyenes szintű csomag csak tesztelési és értékelési célokra használható. Az alapszintű és standard csomagokról további információt [a megfelelő IoT Hub-csomag kiválasztását](../iot-hub/iot-hub-scaling.md) ismertető cikkben talál.

## <a name="iot-edge-modules"></a>IoT Edge-modulok

Az IoT Edge-modulok olyan végrehajtási egységek, amelyek Docker-kompatibilis tárolókként vannak megvalósítva, és amelyek az üzleti logikát a peremhálózaton futtatják. Több modul is konfigurálható az egymás közötti kommunikációra, így a modulok egy adatfeldolgozó folyamatot alkothatnak. Kifejleszthet egyedi modulokat, vagy egy modulba csomagolhat bizonyos Azure-szolgáltatásokat is, amelyek elemzéseket nyújtanak offline és a peremhálózaton is.

### <a name="artificial-intelligence-at-the-edge"></a>Mesterséges intelligencia a szélén

Az Azure IoT Edge lehetővé teszi, hogy összetett eseményfeldolgozást, gépi tanulást, képfelismerést és más nagy értékű AI-t üzembe helyezze anélkül, hogy házon belül írnák. Az Azure-szolgáltatások, például az Azure Functions, az Azure Stream Analytics és az Azure Machine Learning mind a helyszínen futtathatók az Azure IoT Edge-en keresztül. Nem korlátozódik az Azure-szolgáltatások, mégis. Bárki létrehozhat AI-modulokat, és elérhetővé teheti azokat a közösség számára az Azure Marketplace-en keresztül történő használatra.

### <a name="bring-your-own-code"></a>Saját kód használata

Ha a saját kódját szeretné üzembe helyezni az eszközein, az Azure IoT Edge ezt is támogatja. Az IoT Edge ugyanazt a programozási modellt használja, mint a többi Azure IoT-szolgáltatás. Ugyanazt a kódot futtathatja egy eszközön vagy a felhőben. Az Azure IoT Edge a Linux és Windows rendszereket is támogatja, így szabadon kiválasztható a kódolás platformja. Támogatja a Java, .NET Core 2.0, Node.js, C és Python, így a fejlesztők kódot egy nyelvet már ismerik, és használja a meglévő üzleti logika.

## <a name="iot-edge-runtime"></a>IoT Edge-futtatókörnyezet

Az Azure IoT Edge-futtatókörnyezet egyéni és felhőbeli logikák is használatát is lehetővé teszi az IoT Edge-eszközökön. A futásidejű az IoT Edge-eszközön ül, és felügyeleti és kommunikációs műveleteket hajt végre. A futtatókörnyezet számos funkciót lát el:

* A számítási feladatok telepítése és frissítése az eszközön.
* Fenntartja Azure IoT Edge biztonsági szabványait az eszközön.
* Biztosítja az IoT Edge-modulok folyamatos futását.
* Jelenti a modulok állapotát a felhőnek a távoli monitorozáshoz.
* Az alsóbb rétegbeli eszközök és az IoT Edge-eszközök közötti kommunikáció, az IoT Edge-eszközön lévő modulok, valamint az IoT Edge-eszköz és a felhő közötti kommunikáció kezelése.

![Az IoT Edge-futtatókörnyezet elemzéseket és jelentéseket küld az IoT Hub felé](./media/about-iot-edge/runtime.png)

Az Azure IoT Edge-eszközök használatának önön múlik. A futásidejű gyakran használják a AI átjáróeszközök, amelyek összesítik és feldolgozzák az adatokat más helyszíni eszközökről, de ez a telepítési modell csak egy lehetőség.

Az Azure IoT Edge futásidejű fut egy nagy ioT-eszközök, amely lehetővé teszi a használatát a legkülönbözőbb módokon. Támogatja mind a Linux és a Windows operációs rendszerek és absztrakt hardver részleteket. A Raspberry Pi 3-nál kisebb eszközt használjon, ha nem dolgoz fel sok adatot, vagy használjon egy ipari kiszolgálót az erőforrás-igényes számítási feladatok futtatásához.

## <a name="iot-edge-cloud-interface"></a>Az IoT Edge felhőalapú interfésze

Nehéz kezelni a szoftver életciklusát több millió IoT-eszközök, amelyek gyakran különböző gyártmányok és modellek vagy földrajzilag szétszórt. A számítási feladatokat az adott eszköztípus sajátosságai szerint hozhatja létre és konfigurálhatja, az összes eszközön üzembe helyezheti, és megfigyelés útján minden helytelenül működő eszközt észlelhet. Ezeket a tevékenységeket nem lehet eszközönként végrehajtani, ezért nagy léptékben kell elvégezni őket.

Az Azure IoT Edge zökkenőmentesen integrálható az Azure IoT-megoldásgyorsítókkal, hogy a megoldás igényeit egyetlen felületről lehessen kezelni. A felhőszolgáltatások lehetővé teszik a következőket:

* Számítási feladatok létrehozása és konfigurálása egy adott típusú eszközön való futtatáshoz.
* Számítási feladatok küldése több eszközre.
* A használatban lévő eszközökön futó számítási feladatok monitorozása.

![Az eszköztelemetria és a műveletek egyeztetve vannak a felhővel](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>További lépések

A fogalmak gyakorlati alkalmazását is kipróbálhatja, ha [üzembe helyezi az IoT Edge szolgáltatást egy szimulált eszközön](quickstart.md).
