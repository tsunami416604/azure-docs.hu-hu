---
title: Alsóbb rétegbeli eszközök – Azure IoT Edge-átjárókat |} A Microsoft Docs
description: Az Azure IoT Edge segítségével átlátható, átlátszatlan vagy proxy átjáróeszköz létrehozása, amely adatokat küld a több alárendelt eszközről a felhőbe vagy helyi feldolgozza azt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ec8b6cf61f9fb92f888642d1de7d4d1b9b7ac3df
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456652"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>IoT Edge-eszköz használata átjáróként

Az IoT Edge-megoldások átjárói az eszköz kapcsolatát és az Edge Analytics szolgáltatást biztosítják, hogy IoT azokat az eszközöket, amelyek egyébként nem rendelkeznek ilyen képességekkel. Az Azure IoT Edge segítségével IoT-átjáró, függetlenül attól, hogy azok kapcsolódó kapcsolat, identity vagy peremhálózati elemzés futtatása a szükségletét. Ez a cikk az átjáró minták csak tekintse meg jellemzői alsóbb rétegbeli eszközök csatlakoztatása és az eszközidentitással, nem hogyan eszköz adatok feldolgozása az átjárón.

## <a name="patterns"></a>Minták

Nincsenek az IoT Edge-eszköz használata átjáróként a három minták: transzparens, protokoll, fordítás, és identitás fordítási:
* **Transzparens** – azok az eszközök, amelyek elméletileg csatlakozhatnak IoT hub csatlakozhatnak egy átjáró-eszközhöz. Az alsóbb rétegbeli eszközök a saját IoT Hub-identitásokat, és az MQTT, AMQP vagy HTTP-protokollokat használja. Az átjáró egyszerűen továbbítja a kommunikációt az eszközök és az IoT Hub között. Az eszközök nem biztosak abban, hogy átjárón keresztül kommunikálnak a felhővel, és a IoT Hub eszközeit használó felhasználó nem ismeri a köztes átjáró eszközét. Így az átjáró az átlátható. A IoT Edge-eszköz transzparens átjáróként való használatával kapcsolatos részletekért lásd: [transzparens átjáró létrehozása](how-to-create-transparent-gateway.md) .
* **Protokoll fordítása** – más néven átlátszatlan átjáró mintája, a MQTT, a AMQP vagy a http-t nem támogató eszközök egy átjáró-eszköz használatával küldhetnek adatIoT Hubt a nevükben. Az átjáró megérti az alárendelt eszközök által használt protokollt, és az egyetlen olyan eszköz, amely IoT Hub identitással rendelkezik. Minden információt tűnik, hogy egy eszközt, az átjáró érkezik. Alsóbb rétegbeli eszközök kell beágyazása az üzenetek további információkra, ha felhőalapú alkalmazások elemezheti az adatokat az eszközönkénti alapon. Emellett az IoT Hub primitívek, például a párok, és módszereket csak az átjáróeszköz nem alsóbb rétegbeli eszközök számára érhető el.
* **Identitás fordítása** – az IoT hubhoz nem csatlakoztatható eszközök nem tudnak csatlakozni az átjáró-eszközökhöz. Az átjáró biztosítja az IoT Hub identitás- és protokoll fordítási az alsóbb rétegbeli eszközök nevében. Az átjáró nem elég intelligensek ahhoz az alsóbb rétegbeli eszközök által használt protokollt, identitást biztosíthat, és az IoT Hub primitívek fordítása. Alsóbb rétegbeli eszközök első osztályú eszközökön twins és módszerek az IoT Hub jelennek meg. Egy felhasználó kezelheti az eszközöket az IoT Hubban, és nem észleli a köztes átjáróeszköz.

![Diagram – transzparens, protokoll és identitás átjáró minták](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Használati esetek
Az összes átjáró-minták a következő előnyöket nyújtják:
* **Elemzések az Edge** -ben – a mesterséges intelligenciát használó eszközök használatával helyileg dolgozhat fel adatokat, anélkül, hogy teljes hűségű telemetria küldene a felhőbe. Keresse meg és helyileg insights reagálhat rájuk, és csak IoT hubnak küldi az adatok egy részét. 
* **Alsóbb rétegbeli eszközök elkülönítése** – az átjáró eszköz az összes alsóbb rétegbeli eszközt megvédi az internet felé. Azt is Dőljön között egy Zásoktól hálózati kapcsolat nem rendelkező és a egy IT-hálózat, amely a webes hozzáférést biztosít. 
* **Kapcsolat – többszörös** használat – az IoT hub IoT Edge átjárón keresztül csatlakozó összes eszköz ugyanazt az alapul szolgáló kapcsolatot használja.
* **Forgalom simítása** – a IoT Edge eszköz automatikusan végrehajtja az exponenciális leállítási, ha IoT hub szabályozza a forgalmat, miközben az üzeneteket helyileg tartja fenn. Ezzel az értékelemmel teszi adatforgalmának rugalmas megoldását.
* **Offline támogatás** – az átjáró eszköz olyan üzeneteket és kettős frissítéseket tárol, amelyek nem továbbíthatók IoT hubba.

Olyan átjáró, amely fordítási protokollt is elvégezheti, peremhálózati elemzést, eszköz elkülönítés, forgalom simítás és offline támogatja a meglévő eszközökkel és új eszközöket, amelyek korlátozott erőforráshoz. Több meglévő eszközt állít elő, amelyek az üzleti elemzések; működtethet adatok azonban nem tervezték őket felhőalapú összefüggő szem előtt. Az átlátszatlan átjárók lehetővé teszik, hogy ezeket az adatfájlokat egy IoT-megoldásban feloldják és használják.

Egy átjáró, amely identitás fordítási protokollfordítás előnyét, és emellett lehetővé teszi, hogy a felhőből alsóbb rétegbeli eszközök teljes kezelhetőségét. Minden eszközét az IoT-megoldás jelennek meg az IoT Hub függetlenül a protokollt használják.

## <a name="cheat-sheet"></a>Hasznos tanácsok
Íme egy gyors Adatlap, amely összehasonlítja az IoT Hub primitívek használatakor transzparens, átlátszatlan (protokoll), és proxy-átjárók.

| &nbsp; | Transzparens átjáró | Protokollfordítás | Identitás-fordítás |
|--------|-------------|--------|--------|
| Az IoT Hub-identitásjegyzék tárolja identitások | Az összes csatlakoztatott eszközön identitások | Csak az átjáró eszköz identitása | Az összes csatlakoztatott eszközön identitások |
| Ikereszközök | Minden csatlakoztatott eszközhöz tartozik a saját ikereszköz | Az átjáró csak egy eszköz- és modul twins rendelkezik | Minden csatlakoztatott eszközhöz tartozik a saját ikereszköz |
| Közvetlen metódusok és a felhőből az eszközre irányuló üzenetek | A felhő külön-külön kezelheti minden csatlakoztatott eszköz | A felhő csak kezelheti az átjáró eszköz | A felhő külön-külön kezelheti minden csatlakoztatott eszköz |
| [IoT Hub szabályozások és kvóták](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Egyes eszközök a alkalmazni | Az átjáró eszköz a alkalmazni | Egyes eszközök a alkalmazni |

Egy nem átlátszó átjáró (protokollfordítás) minta használatakor, hogy az átjárón keresztül csatlakozó összes eszközök megosztása azonos felhőből az eszközre beolvasása, amely legfeljebb 50 üzenetet is tartalmazhat. Ebből az következik, hogy az átjáró nem átlátszó minta kell használni, csak akkor, amikor minden mező-átjárón keresztül kapcsolódik néhány olyan eszközt, és a felhőből az eszközre irányuló forgalom értéke alacsony.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan állíthat be transzparens átjárót: 

* [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
* [Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon](how-to-authenticate-downstream-device.md)
* [Lefelé irányuló eszköz csatlakoztatása Azure IoT Edge-átjáróhoz](how-to-connect-downstream-device.md)