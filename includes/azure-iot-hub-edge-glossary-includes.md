---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: 0b478775aa572ce89f3dcdc6f28ee0418e818d99
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376407"
---
## <a name="automatic-device-management-in-azure-iot-hub"></a>Automatikus eszközkezelés az Azure-ban IoT Hub
Az Azure-ban az automatikus eszközkezelés IoT Hub automatizálja a nagyméretű eszközök flottáinak teljes életciklusával kapcsolatos ismétlődő és összetett feladatokat. Az automatikus eszközkezelés lehetővé teszi, hogy a tulajdonságok alapján csoportosítsa az eszközöket, Definiáljon egy kívánt konfigurációt, és hagyja IoT Hub frissíteni az eszközöket, amikor azok hatókörbe kerülnek.  [Automatikus eszköz-konfigurációkból](../articles/iot-hub/iot-hub-auto-device-config.md) és [IoT Edge automatikus központi telepítésből](../articles/iot-edge/how-to-deploy-at-scale.md)áll.

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge lehetővé teszi az Azure-szolgáltatások és a megoldás-specifikus kódok felhőalapú üzembe helyezését a helyszíni eszközökön. IoT Edge eszközök más eszközökről származó adatokat összesítenek, hogy az adatok a felhőbe való küldése előtt számítási és elemzési műveleteket végezzenek. További információ: [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>IoT Edge ügynök
A modulok üzembe helyezéséhez és figyeléséhez felelős IoT Edge futtatókörnyezetének része.

## <a name="iot-edge-device"></a>IoT Edge-eszköz
IoT Edge eszközökön telepítve van a IoT Edge futtatókörnyezet, és **IoT Edge eszközként** vannak megjelölve az eszköz részleteiben. Megtudhatja, hogyan [helyezhet üzembe Azure IoT Edge egy szimulált eszközön Linuxon – előzetes](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)verzió.

## <a name="iot-edge-automatic-deployment"></a>Automatikus központi telepítés IoT Edge
Az IoT Edge automatikus központi telepítése IoT Edge eszközök egy meghatározott készletét konfigurálja IoT Edge modulok futtatásához. Az egyes központi telepítések folyamatosan biztosítják, hogy a célként megadott feltételnek megfelelő összes eszköz futtatja a modulok adott készletét, még akkor is, ha új eszközök jönnek létre vagy módosulnak a cél feltételnek megfelelően. Minden IoT Edge eszköz csak a legmagasabb prioritású telepítést kapja, amelynek a célja megfelel. További információ a [IoT Edge automatikus központi telepítéséről](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>IoT Edge üzembe helyezési jegyzék
Egy vagy több IoT Edge-eszközbe másolt adatokat tartalmazó JSON-dokumentum, amely modulokat, útvonalakat és társított modul kívánt tulajdonságokat helyez üzembe.

## <a name="iot-edge-gateway-device"></a>IoT Edge átjáró eszköz
IoT Edge-eszköz alsóbb rétegbeli eszközzel. Az alsóbb rétegbeli eszköz lehet IoT Edge vagy nem IoT Edge eszköz.

## <a name="iot-edge-hub"></a>IoT Edge hub
A modul kommunikációs, felsőbb rétegbeli (IoT Hub) és alsóbb rétegbeli (IoT Hub) kommunikációjának IoT Edgei futtatókörnyezetének része. 

## <a name="iot-edge-leaf-device"></a>IoT Edge Leaf eszköz
Egy IoT Edge, alsóbb rétegbeli eszköz nélküli eszköz. 

## <a name="iot-edge-module"></a>IoT Edge modul
Az IoT Edge modul egy Docker-tároló, amelyet IoT Edge eszközökön telepíthet. Egy adott feladatot hajt végre, például egy eszközön lévő üzenet betöltését, egy üzenet átalakítását vagy egy üzenet küldését egy IoT hubhoz. Más modulokkal kommunikál, és adatokat küld a IoT Edge futtatókörnyezetnek. [Ismerje meg a IoT Edge modulok fejlesztésének követelményeit és eszközeit](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>IoT Edge modul identitása
A IoT Hub modul identitásának beállításjegyzékében szereplő egyik rekord részletezi, hogy a modul milyen létezési és biztonsági hitelesítő adatokat használ, hogy a hitelesítő adatok egy peremhálózati hubhoz vagy IoT Hubhoz legyenek használva.

## <a name="iot-edge-module-image"></a>IoT Edge modul képe
A IoT Edge futtatókörnyezet által a modul példányainak létrehozásához használt Docker-rendszerkép.

## <a name="iot-edge-module-twin"></a>IoT Edge modul Twin
Egy olyan JSON-dokumentum, amely egy modul példányának állapotinformációkat tároló IoT Hubban található.

## <a name="iot-edge-priority"></a>IoT Edge prioritás
Ha két IoT Edge üzemelő példány ugyanazt az eszközt célozza, a magasabb prioritású üzemelő példány lesz alkalmazva. Ha két üzemelő példány azonos prioritással rendelkezik, a rendszer alkalmazza a későbbi létrehozási dátummal rendelkező üzemelő példányt. További információ a [prioritásról](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>IoT Edge-futtatókörnyezet
IoT Edge Runtime tartalmaz mindent, amit a Microsoft terjeszt IoT Edge eszközre való telepítésre. Ide tartozik az Edge Agent, az Edge hub és a IoT Edge biztonsági démon.

## <a name="iot-edge-set-modules-to-a-single-device"></a>Modulok beállítása egyetlen eszközre IoT Edge
Egy művelet, amely egy IoT Edge jegyzékfájl tartalmát másolja egy különálló eszközre. Az alapul szolgáló API egy általános "konfiguráció alkalmazása", amely egyszerűen egy IoT Edge-jegyzékfájl bemenetként való megadását is elvégzi.

## <a name="iot-edge-target-condition"></a>IoT Edge cél feltétele
Egy IoT Edge üzemelő példányban a célként megadott feltétel bármely logikai feltétel az eszközök ikrek címkéjén, hogy kiválassza a központi telepítés céljának eszközeit, például: **tag. environment = Prod**. A célként megadott feltétel folyamatosan ki van értékelve, hogy tartalmazza a követelményeknek megfelelő új eszközöket, vagy távolítsa el azokat az eszközöket, amelyek már nem rendelkeznek. További információ a [cél feltétellel](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition) kapcsolatban