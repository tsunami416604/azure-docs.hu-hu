---
title: Átjárók alsóbb rétegbeli eszközökhöz – Azure IoT Edge | Microsoft Docs
description: A Azure IoT Edge használatával olyan transzparens, átlátszatlan vagy proxy átjárót hozhat létre, amely több alsóbb rétegbeli eszközről küld adatokat a felhőbe, vagy helyileg dolgozza fel azokat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 916eeaa60bc054301af039164ce1c14e77ceb91a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733517"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>IoT Edge-eszköz használata átjáróként

Az IoT Edge-megoldások átjárói az eszköz kapcsolatát és az Edge Analytics szolgáltatást biztosítják, hogy IoT azokat az eszközöket, amelyek egyébként nem rendelkeznek ilyen képességekkel. Azure IoT Edge a IoT-átjárók bármilyen igényének kielégítésére használható, függetlenül attól, hogy kapcsolódik-e a kapcsolathoz, az identitáshoz vagy a peremhálózati elemzésekhez. A cikkben szereplő átjárói minták csak az alsóbb rétegbeli eszköz kapcsolatának és az eszköz identitásának jellemzőire vonatkoznak, nem az eszköz adatainak feldolgozására az átjárón.

## <a name="patterns"></a>Minták

Az IoT Edge eszköz átjáróként való használatának három mintája van: transzparens, protokoll fordítása és az identitás fordítása:

* **Transzparens** – azok az eszközök, amelyek elméletileg csatlakozhatnak IoT hub csatlakozhatnak egy átjáró-eszközhöz. Az alsóbb rétegbeli eszközök saját IoT Hub identitással rendelkeznek, és a MQTT, a AMQP vagy a HTTP protokollt használják. Az átjáró egyszerűen továbbítja az eszközök és a IoT Hub közötti kommunikációt. Az eszközök és a velük kommunikáló felhasználók IoT Hub nem biztos, hogy az átjáró közvetíti a kommunikációt. Ez a tájékoztatás hiánya azt jelenti, hogy az átjáró *transzparensnek*tekintendő. A IoT Edge-eszköz transzparens átjáróként való használatával kapcsolatos részletekért lásd: [transzparens átjáró létrehozása](how-to-create-transparent-gateway.md) .
* **Protokoll fordítása** – más néven átlátszatlan átjáró mintája, a MQTT, a AMQP vagy a http-t nem támogató eszközök egy átjáró-eszköz használatával küldhetnek adatIoT Hubt a nevükben. Az átjáró megérti az alárendelt eszközök által használt protokollt, és az egyetlen olyan eszköz, amely IoT Hub identitással rendelkezik. Minden információ úgy tűnik, hogy egy eszközről, az átjáróról származik. Az alárendelt eszközöknek további azonosítási információkat kell tartalmazniuk az üzeneteiben, ha a Felhőbeli alkalmazások eszközönkénti alapon szeretnék elemezni az adatokat. Emellett a IoT Hub primitívek, például az ikrek és a metódusok csak az átjáró-eszközhöz érhetők el, nem az alsóbb rétegbeli eszközökhöz.
* **Identitás fordítása** – az IoT hubhoz nem csatlakoztatható eszközök nem tudnak csatlakozni az átjáró-eszközökhöz. Az átjáró IoT Hub identitást és a protokoll fordítását biztosítja az alárendelt eszközök nevében. Az átjáró elég intelligens ahhoz, hogy tisztában legyen az alsóbb rétegbeli eszközök által használt protokollal, megismertesse azokat az identitással, és lefordítsa IoT Hub primitíveket. Az alsóbb szintű eszközök IoT Hub az ikrek és a metódusok első osztályú eszközeiként jelennek meg. A felhasználók kezelhetik IoT Hub eszközeit, és nem ismerik a köztes átjáró eszközét.

![Diagram – transzparens, protokoll és Identity Gateway-minták](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Használati esetek

Az átjáró összes mintázata a következő előnyöket biztosítja:

* **Elemzések az Edge** -ben – a mesterséges intelligenciát használó eszközök használatával helyileg dolgozhat fel adatokat, anélkül, hogy teljes hűségű telemetria küldene a felhőbe. Megkeresheti és reagálhat helyi elemzésekre, és csak az adathalmazt küldheti IoT Hubba.
* **Alsóbb rétegbeli eszközök elkülönítése** – az átjáró eszköz az összes alsóbb rétegbeli eszközt megvédi az internet felé. A szolgáltatás olyan hálózati hozzáférési pont között tud ülni, amely nem rendelkezik kapcsolattal és olyan informatikai hálózattal, amely hozzáférést biztosít a webhelyhez.
* **Kapcsolat – többszörös** használat – az IoT hub IoT Edge átjárón keresztül csatlakozó összes eszköz ugyanazt az alapul szolgáló kapcsolatot használja.
* **Forgalom simítása** – a IoT Edge eszköz automatikusan végrehajtja az exponenciális leállítási, ha IoT hub szabályozza a forgalmat, miközben az üzeneteket helyileg tartja fenn. Ezzel a megoldással rugalmasan teheti meg a megoldást a forgalomban lévő csúcsokra.
* **Offline támogatás** – az átjáró eszköz olyan üzeneteket és kettős frissítéseket tárol, amelyek nem továbbíthatók IoT hubba.

A protokollok fordítását végző átjáró a peremhálózat-elemzést, az eszközök elkülönítését, a forgalom simítását és az offline támogatást a meglévő eszközökre és az erőforrás által korlátozott új eszközökre is képes elvégezni. Számos meglévő eszköz olyan adatmennyiséget állít elő, amely az üzleti elemzéseket képes kiszolgálni. azonban nem a Felhőbeli kapcsolattal lettek tervezve. Az átlátszatlan átjárók lehetővé teszik, hogy ezeket az adatfájlokat egy IoT-megoldásban feloldják és használják.

Az identitások fordítását végző átjáró biztosítja a protokollok fordításának előnyeit, továbbá lehetővé teszi a folyamaton kívüli eszközök teljes kezelhetőségét a felhőből. A IoT-megoldásban lévő összes eszköz megjelenik a IoT Hub az általuk használt protokolltól függetlenül.

## <a name="cheat-sheet"></a>Hasznos tanácsok

Itt található egy gyors Cheat-táblázat, amely összehasonlítja IoT Hub primitíveket transzparens, átlátszatlan (protokoll) és proxy átjárók használata esetén.

| &nbsp; | Transzparens átjáró | Protokoll fordítása | Identitás fordítása |
|--------|-------------|--------|--------|
| A IoT Hub Identity registryben tárolt identitások | Az összes csatlakoztatott eszköz identitása | Csak az átjáró eszközének identitása | Az összes csatlakoztatott eszköz identitása |
| Ikereszközök | Minden csatlakoztatott eszközhöz saját eszköz tartozik | Csak az átjáró rendelkezik egy eszköz és egy modul ikrekkel | Minden csatlakoztatott eszközhöz saját eszköz tartozik |
| Közvetlen metódusok és a felhőből az eszközre irányuló üzenetek | A felhő minden egyes csatlakoztatott eszközt külön tud kezelni | A felhő csak az átjáró eszközét tudja kezelni | A felhő minden egyes csatlakoztatott eszközt külön tud kezelni |
| [IoT Hub szabályozások és kvóták](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Alkalmazás minden eszközre | Alkalmazás az átjáró eszközre | Alkalmazás minden eszközre |

Ha átlátszatlan átjáró (protokoll fordítása) mintát használ, az átjárón keresztül csatlakozó összes eszköz ugyanazzal a felhőből az eszközre irányuló várólistára kerül, amely legfeljebb 50 üzenetet tartalmazhat. Ebből következik, hogy az átlátszatlan átjáró mintázatát csak akkor kell használni, ha kevés eszköz csatlakozik az egyes mezők átjáróján, és a felhőből az eszközre irányuló forgalom alacsony.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan állíthat be transzparens átjárót:

* [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
* [Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon](how-to-authenticate-downstream-device.md)
* [Lefelé irányuló eszköz csatlakoztatása Azure IoT Edge-átjáróhoz](how-to-connect-downstream-device.md)
