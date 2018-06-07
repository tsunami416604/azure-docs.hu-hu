---
title: Az Azure IoT Edge bemutatása | Microsoft Docs
description: Az Azure IoT Edge szolgáltatás áttekintése
author: kgremban
manager: timlt
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 52956e9888d67e0502a9ea82657acb9728216111
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630697"
---
# <a name="what-is-azure-iot-edge---preview"></a>Az Azure IoT Edge bemutatása – előzetes verzió

Az Azure IoT Edge szolgáltatás a felhőbeli elemzéseket és egyéni üzleti logikákat eszközökre helyezi át, hogy a szervezet az üzleti elemzésekre tudjon koncentrálni az adatkezelés helyett. Tegye lehetővé megoldása valódi méretezhetőségét az IoT-szoftver konfigurálásával, amelyet standard tárolók használatával helyezhet üzembe az eszközökön, és a felhőn keresztül monitorozhatja is.

>[!NOTE]
>Az Azure IoT Edge az IoT Hub ingyenes és standard szintű csomagjaiban érhető el. Az ingyenes szintű csomag csak tesztelési és értékelési célokra használható. Az alapszintű és standard csomagokról további információt [a megfelelő IoT Hub-csomag kiválasztását](../iot-hub/iot-hub-scaling.md) ismertető cikkben talál.

Az elemzések jelentős üzleti értékeket teremthetnek az IoT-megoldásokban, de nem szükséges minden elemzést a felhőben végezni. Ha azt szeretné, hogy az eszköze a lehető leggyorsabban reagálni tudjon a vészhelyzetekre, akkor magán az eszközön is végezhet rendellenesség-észlelést. Hasonlóképpen, ha csökkenteni szeretné a sávszélesség-költségeket, és el szeretné kerülni több terabájtnyi nyers adat átvitelét, akkor helyileg is elvégezheti az adatok törlését és összesítését. Ezután elküldheti az elemzéseket a felhőbe. 

Az Azure IoT Edge három összetevőből áll:
* Az IoT Edge-modulok olyan tárolók, amelyek Azure-szolgáltatásokat, külső felektől származó szolgáltatásokat vagy az Ön saját kódjait is futtathatják. A modulok IoT Edge-eszközökön vannak üzembe helyezve, és a végrehajtásuk is helyileg, az eszközökön zajlik. 
* Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön fut, és ez kezeli az adott eszközön üzembe helyezett modulokat. 
* Egy felhőalapú interfész lehetővé teszi az IoT Edge-eszközök távoli monitorozását és kezelését.

## <a name="iot-edge-modules"></a>IoT Edge-modulok

Az IoT Edge-modulok végrehajtási egységek, amelyek jelenleg Docker-kompatibilis tárolókként vannak megvalósítva, és a peremhálózaton futtatják az Ön üzleti logikáját. Több modul is konfigurálható az egymás közötti kommunikációra, így a modulok egy adatfeldolgozó folyamatot alkothatnak. Kifejleszthet egyedi modulokat, vagy egy modulba csomagolhat bizonyos Azure-szolgáltatásokat is, amelyek elemzéseket nyújtanak offline és a peremhálózaton is. 

### <a name="artificial-intelligence-on-the-edge"></a>Mesterséges intelligencia a peremhálózaton

Az Azure IoT Edge lehetővé teszi összetett eseményfeldolgozások, gépi tanulás, képfelismerés és egyéb értékes mesterséges intelligenciák üzembe helyezését anélkül, hogy ezeket házon belül kellene megírni. Az Azure IoT Edge-en keresztül olyan Azure-szolgáltatásokat lehet helyileg futtatni, mint az Azure Functions, az Azure Stream Analytics vagy az Azure Machine Learning, de nem csak Azure-szolgáltatások használatára van lehetőség. Bárki létrehozhat mesterségesintelligencia-modulokat, és elérhetővé teheti azokat a közösség számára. 

### <a name="bring-your-own-code"></a>Saját kód használata

Ha a saját kódját szeretné üzembe helyezni az eszközein, az Azure IoT Edge ezt is támogatja. Az IoT Edge ugyanazt a programozási modellt használja, mint a többi Azure IoT-szolgáltatás. Egy adott kód futtatható egy eszközön vagy a felhőben is. Az Azure IoT Edge a Linux és Windows rendszereket is támogatja, így szabadon kiválasztható a kódolás platformja. A szolgáltatás a Java, .NET Core 2.0, Node.js, C és Python nyelveket támogatja, így a fejlesztők a már ismert nyelveken írhatják a kódokat, és felhasználhatják a meglévő üzleti logikát, így nem kell az alapoktól megírniuk azt.

## <a name="iot-edge-runtime"></a>IoT Edge-futtatókörnyezet

Az Azure IoT Edge-futtatókörnyezet egyéni és felhőbeli logikák is használatát is lehetővé teszi az IoT Edge-eszközökön. A futtatókörnyezet az IoT Edge-eszközökön fut, ahol felügyeleti és kommunikációs műveleteket hajt végre. A futtatókörnyezet számos funkciót lát el:

* Telepíti és frissíti az eszközökön a számítási feladatokat.
* Fenntartja Azure IoT Edge biztonsági szabványait az eszközön.
* Biztosítja az IoT Edge-modulok folyamatos futását.
* Jelenti a modulok állapotát a felhőnek a távoli monitorozáshoz.
* Kommunikációt létesít az alárendelt levéleszközök és az IoT Edge-eszköz között.
* Kommunikációt létesít a modulok és az IoT Edge-eszköz között.
* Kommunikációt létesít az IoT Edge-eszközök és a felhő között.

![Az IoT Edge-futtatókörnyezet elemzéseket és jelentéseket küld az IoT Hub felé][1]

Teljes mértékben saját belátása szerint használhatja az Azure IoT Edge-eszközöket. A futtatókörnyezetet gyakran használják átjárókon olyan mesterséges intelligenciák üzembe helyezésére, amelyek több helyszíni eszköz adatait összegzik és dolgozzák fel, de ez csak egy felhasználási lehetőség. A levéleszközök is lehetnek Azure IoT Edge-eszközök, függetlenül attól, hogy egy átjáróhoz vagy közvetlenül a felhőhöz csatlakoznak.

Az Azure IoT Edge-futtatókörnyezet nagy mennyiségű IoT-eszközön fut, hogy a futtatókörnyezetet minél többféle módon lehessen használni. A futtatókörnyezet támogatja a Linux és Windows operációs rendszereket, valamint kivonatolja a hardveradatokat. Használjon egy a Raspberry Pi 3-nál kisebb eszközt, ha nem dolgoz fel sok adatot, vagy skálázzon fel egy ipari kiszolgálóra, ha nagy erőforrásigényű számítási feladatokat szeretne végezni.

## <a name="iot-edge-cloud-interface"></a>Az IoT Edge felhőalapú interfésze

A vállalati eszközök szoftveres életciklusainak felügyelete bonyolult feladat. Több millió különböző IoT-eszköz szoftveres életciklusainak felügyelete még bonyolultabb feladat. A számítási feladatokat az adott eszköztípus sajátosságai szerint kell létrehozni és konfigurálni, egyszerre több millió eszközön kell azokat üzembe helyezni a megoldáson belül, és monitorozással ki kell szűrni a rendellenesen működő eszközöket. Ezeket a tevékenységeket nem lehet eszközönként végrehajtani, ezért nagy léptékben kell elvégezni őket.

Az Azure IoT peremhálózati integrálható Azure IoT-megoldás gyorsítók egy vezérlő vezérlősík biztosít a megoldás igényeinek. A felhőszolgáltatások a következőket tesik lehetővé a felhasználók számára:

* Számítási feladatok létrehozása és konfigurálása egy adott típusú eszközön való futtatáshoz.
* Számítási feladatok küldése több eszközre.
* A használatban lévő eszközökön futó számítási feladatok monitorozása.

![A telemetriák, az elemzések és az eszközök műveletei koordinálva vannak a felhővel][2]

## <a name="next-steps"></a>További lépések

Ezen fogalmak gyakorlati alkalmazását is kipróbálhatja, ha [üzembe helyezi az IoT Edge szolgáltatást egy szimulált eszközön][lnk-quickstart].

<!-- Images -->
[1]: ./media/how-iot-edge-works/runtime.png
[2]: ./media/how-iot-edge-works/cloud-interface.png

<!-- Links -->
[lnk-quickstart]: quickstart.md
