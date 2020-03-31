---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: c95bca125ea70cf32acad0d5ea67c3ad195ed704
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179238"
---
## <a name="automatic-device-management"></a>Automatikus eszközkezelés
Az Azure IoT Hub automatikus eszközkezelése automatizálja a nagy eszközflották teljes életciklusa során végzett ismétlődő és összetett feladatainak nagy feladatainak automatizálását. Az Automatikus eszközkezelés segítségével megcélozhatja az eszközök készletét a tulajdonságaik alapján, meghatározhatja a kívánt konfigurációt, és lehetővé teheti az IoT Hub számára, hogy frissítse az eszközöket, amikor azok hatókörbe kerülnek.  [Automatikus eszközkonfigurációkból](../articles/iot-hub/iot-hub-auto-device-config.md) és [IoT Edge automatikus központi telepítésekből](../articles/iot-edge/how-to-deploy-monitor.md)áll.

## <a name="iot-edge"></a>IoT Edge
Az Azure IoT Edge lehetővé teszi az Azure-szolgáltatások és a megoldásspecifikus kód felhőalapú üzembe helyezését a helyszíni eszközökre. Az IoT Edge-eszközök összesíthetik a más eszközökről származó adatokat a számítási és elemzési műveletek elvégzéséhez, mielőtt az adatokat a felhőbe küldenék. További információ: [Azure IoT Edge.](https://docs.microsoft.com/azure/iot-edge/)

## <a name="iot-edge-agent"></a>IoT edge ügynök
Az IoT Edge-futásidejű modulok üzembe helyezéséért és figyeléséért felelős része.

## <a name="iot-edge-device"></a>IoT Edge-eszköz
Az IoT Edge-eszközök telepítettioT Edge-futásidejű, és az eszköz részleteiben **IoT Edge-eszközként** vannak megjelölve. Ismerje meg, hogyan [telepítheti az Azure IoT Edge-et egy szimulált eszközön Linux alatt – előzetes verzió.](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)

## <a name="iot-edge-automatic-deployment"></a>IoT Edge automatikus üzembe helyezése
Az IoT Edge automatikus központi telepítése konfigurálja az IoT Edge-eszközök célkészletét az IoT Edge-modulok készletének futtatásához. Minden központi telepítés folyamatosan biztosítja, hogy minden eszköz, amely megfelel a célfeltétel fut a megadott modulkészlet, akkor is, ha új eszközök jönnek létre, vagy a célfeltételnek megfelelően módosulnak. Minden IoT Edge-eszköz csak a legmagasabb prioritású központi telepítést kapja, amelynek célfeltétele megfelel. További információ az [IoT Edge automatikus központi telepítéséről.](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)

## <a name="iot-edge-deployment-manifest"></a>IoT Edge központi telepítési jegyzékfájlja
Egy Json-dokumentum, amely egy vagy több IoT Edge-eszköz ikermoduljában másolandó információkat tartalmaz a modulok, útvonalak és a kapcsolódó modul kívánt tulajdonságainak üzembe helyezéséhez.

## <a name="iot-edge-gateway-device"></a>IoT Peremhálózat átjáróeszköz
Egy IoT Edge-eszköz alsóbb rétegbeli eszközzel. Az alsóbb rétegbeli eszköz lehet IoT Edge vagy nem IoT Edge-eszköz.

## <a name="iot-edge-hub"></a>IoT edge hub
Az IoT Edge futásidejű modul kommunikációért felelős része, az upstream (az IoT Hub felé) és az alsóbb rétegbeli (az IoT Hubtól távol) kommunikáció. 

## <a name="iot-edge-leaf-device"></a>IoT Edge levéleszköz
Egy IoT Edge-eszköz alsóbb rétegbeli eszköz nélkül. 

## <a name="iot-edge-module"></a>IoT Edge modul
Az IoT Edge-modul egy Docker-tároló, amely az IoT Edge-eszközök üzembe helyezhető. Egy adott feladatot hajt végre, például egy üzenet betöltése egy eszközről, egy üzenet átalakítása, vagy egy üzenetet küld egy IoT hub. Kommunikál más modulokkal, és adatokat küld az IoT Edge futásidejű. [Ismerje meg az IoT Edge-modulok fejlesztéséhez szükséges követelményeket és eszközöket.](https://docs.microsoft.com/azure/iot-edge/module-development)

## <a name="iot-edge-module-identity"></a>IoT Edge-modul identitása
Az IoT Hub-modul identitásjegyzékében található rekord részletezi a modul által egy peremhálózati hubbal vagy Az IoT Hubbal való hitelesítéshez használandó meglétet és a biztonsági hitelesítő adatokat.

## <a name="iot-edge-module-image"></a>IoT Edge modul képe
A docker-rendszerkép, amely et az IoT Edge futásidejű a modulpéldányok példányai.

## <a name="iot-edge-module-twin"></a>IoT Edge modul iker
Egy Json-dokumentum megőrzött az IoT Hub, amely tárolja a modulpéldány állapotadatait.

## <a name="iot-edge-priority"></a>IoT edge prioritás
Ha két IoT Edge-telepítés ugyanazt az eszközt célozza meg, a nagyobb prioritású központi telepítés lesz alkalmazva. Ha két központi telepítés azonos prioritással rendelkezik, a későbbi létrehozási dátummal rendelkező központi telepítés lesz alkalmazva. További információ a [prioritásról.](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority)

## <a name="iot-edge-runtime"></a>IoT Edge-futtatókörnyezet
Az IoT Edge futásidejű tartalmaz mindent, amelyet a Microsoft az IoT Edge-eszközökön való telepítéshez terjeszt. Ez magában foglalja az Edge-ügynököt, az Edge hubot és az IoT Edge biztonsági démont.

## <a name="iot-edge-set-modules-to-a-single-device"></a>Az IoT Edge modulokat egyetlen eszközre állítja be
Egy művelet, amely az IoT Edge-jegyzékfájl tartalmát másolja az egyik eszköz ikermodulján. Az alapul szolgáló API egy általános "konfiguráció alkalmazása", amely egyszerűen egy IoT Edge-jegyzékfájl bemenetként.

## <a name="iot-edge-target-condition"></a>IoT edge célfeltétel
Az IoT Edge-telepítésben a célfeltétel bármely logikai feltétel az eszköz twins-címkéken a központi telepítés céleszközeinek kiválasztásához, például **tag.environment = prod.** A célfeltétel folyamatosan kiértékelése minden olyan új eszközt tartalmaz, amely megfelel a követelményeknek, vagy eltávolítja azokat az eszközöket, amelyek már nem felelnek meg. További információ a [célállapotról](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)