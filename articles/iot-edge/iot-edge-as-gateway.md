---
title: Átjárók alsóbb rétegbeli eszközökhöz – Azure IoT Edge | Microsoft Docs
description: A Azure IoT Edge használatával olyan transzparens, átlátszatlan vagy proxy átjárót hozhat létre, amely több alsóbb rétegbeli eszközről küld adatokat a felhőbe, vagy helyileg dolgozza fel azokat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0589779de2ddb0bc75dde3b57d6444634b879f86
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017022"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>IoT Edge-eszköz használata átjáróként

Az IoT Edge-megoldások átjárói az eszköz kapcsolatát és az Edge Analytics szolgáltatást biztosítják, hogy IoT azokat az eszközöket, amelyek egyébként nem rendelkeznek ilyen képességekkel. Azure IoT Edge a IoT-átjárók bármilyen igényének kielégítésére használható, függetlenül attól, hogy kapcsolódik-e a kapcsolathoz, az identitáshoz vagy a peremhálózati elemzésekhez. A cikkben szereplő átjárói minták csak az alsóbb rétegbeli eszköz kapcsolatának és az eszköz identitásának jellemzőire vonatkoznak, nem az eszköz adatainak feldolgozására az átjárón.

## <a name="patterns"></a>Minták

Az IoT Edge eszköz átjáróként való használatának három mintája van: transzparens, protokoll fordítása és az identitás fordítása.

A mintázatok közötti fő különbség az, hogy egy átlátszó átjáró az alsóbb rétegbeli eszközök és IoT Hub között üzeneteket továbbít, anélkül, hogy további feldolgozásra lenne szükség. A protokollok fordítása és az identitás fordítása azonban az átjárón való feldolgozást igényel a kommunikáció engedélyezéséhez.

Bármely átjáró használhat IoT Edge modulokat az elemzéshez vagy az előzetes feldolgozáshoz a peremen, mielőtt üzeneteket továbbítson az alsóbb rétegbeli eszközökről a IoT Hubba.

![Diagram – transzparens, protokoll és Identity Gateway-minták](./media/iot-edge-as-gateway/edge-as-gateway.png)

### <a name="transparent-pattern"></a>Transzparens minta

*Transzparens* átjáró esetén az eszközök, amelyek elméletileg csatlakozhatnak IoT hub csatlakozhatnak egy átjáró-eszközhöz. Az alsóbb rétegbeli eszközök saját IoT Hub-identitással rendelkeznek, és az MQTT, AMQP vagy HTTP protokoll valamelyikét használják. Az átjáró csupán továbbítja az eszközök és az IoT Hub közötti kommunikációt. Az eszközök és a velük kommunikáló felhasználók IoT Hub nem biztos, hogy az átjáró közvetíti a kommunikációt. Ez a tájékoztatás hiánya azt jelenti, hogy az átjáró *transzparensnek*tekintendő.

A IoT Edge futtatókörnyezet átlátható átjáró-képességeket is tartalmaz. További információ: [IoT Edge eszköz konfigurálása transzparens átjáróként való](how-to-create-transparent-gateway.md)használatra.

### <a name="protocol-translation-pattern"></a>Protokoll fordítási mintája

A *protokoll-fordítási* átjárók más néven *átlátszatlan* átjáróként is ismertek, ellentétben az átlátszó átjáró mintázatával. Ebben a mintában azok az eszközök, amelyek nem támogatják a MQTT, a AMQP vagy a HTTP protokollt, egy átjáró-eszköz használatával küldik el az adatküldés IoT Hub a nevükben. Az átjáró ismeri az alsóbb rétegbeli eszközök által használt protokollt, és ez az egyetlen eszköz, amely identitással rendelkezik az IoT Hubban. Minden információ úgy tűnik, hogy egy eszközről, az átjáróról származik. Az alsóbb réteg eszközeinek további azonosító információkat kell beágyazniuk az üzenetekbe, ha a felhőalkalmazásoknak eszközönként is elemezniük kell az adatokat. Emellett az olyan IoT Hub-primitívek, mint az ikrek és metódusok csak az átjáróeszköz számára érhetők el, az alsóbb rétegbeli eszközök számára nem.

A IoT Edge futtatókörnyezet nem tartalmazza a protokollok fordítási képességeit. Ez a minta olyan egyéni vagy harmadik féltől származó modulokat igényel, amelyek gyakran a használt hardverre és protokollra vonatkoznak. Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) számos protokoll-fordítási modult tartalmaz, amelyek közül választhat.

### <a name="identity-translation-pattern"></a>Identitás-fordítási minta

Az *identitás-fordítási* átjáró mintájában az eszközök, amelyek nem tudnak csatlakozni a IoT hubhoz, csatlakozhatnak egy átjáró-eszközhöz. Az alsóbb rétegbeli eszközök nevében az átjáró biztosítja az IoT Hub-identitást és a protokollfordítást. Az átjáró elég intelligens ahhoz, hogy értelmezze az alsóbb réteg eszközei által használt protokollt, identitást biztosítson au eszközöknek és lefordítsa az IoT Hub-primitíveket. Az alsóbb rétegbeli eszközök az IoT Hub felé első szintű, ikrekkel és metódusokkal rendelkező eszközökként jelennek meg. A felhasználók úgy kezelhetik az eszközöket az IoT Hubban, hogy nem tudnak a köztes átjáróeszközről.

A IoT Edge futtatókörnyezet nem tartalmaz identitás-fordítási képességeket. Ez a minta olyan egyéni vagy harmadik féltől származó modulokat igényel, amelyek gyakran a használt hardverre és protokollra vonatkoznak. Az identitás-fordítási mintát használó minta: [Azure IoT Edge LoRaWAN Starter Kit](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="use-cases"></a>Használati esetek

Az átjáró összes mintázata a következő előnyöket biztosítja:

* **Elemzések az Edge** -ben – a mesterséges intelligenciát használó eszközök használatával helyileg dolgozhat fel adatokat, anélkül, hogy teljes hűségű telemetria küldene a felhőbe. Megkeresheti és reagálhat helyi elemzésekre, és csak az adathalmazt küldheti IoT Hubba.
* **Alsóbb rétegbeli eszközök elkülönítése** – az átjáró eszköz az összes alsóbb rétegbeli eszközt megvédi az internet felé. A szolgáltatás olyan hálózati hozzáférési pont között tud ülni, amely nem rendelkezik kapcsolattal és olyan informatikai hálózattal, amely hozzáférést biztosít a webhelyhez.
* **Kapcsolat – többszörös** használat – az IoT hub IoT Edge átjárón keresztül csatlakozó összes eszköz ugyanazt az alapul szolgáló kapcsolatot használja.
* **Forgalom simítása** – a IoT Edge eszköz automatikusan végrehajtja az exponenciális leállítási, ha IoT hub szabályozza a forgalmat, miközben az üzeneteket helyileg tartja fenn. Ezzel a megoldással rugalmasan teheti meg a megoldást a forgalomban lévő csúcsokra.
* **Offline támogatás** – az átjáró eszköz olyan üzeneteket és kettős frissítéseket tárol, amelyek nem továbbíthatók IoT hubba.

A protokollok fordítását végző átjárók képesek támogatni a meglévő eszközöket és az erőforrás által korlátozott új eszközöket. Számos meglévő eszköz olyan adatmennyiséget állít elő, amely az üzleti elemzéseket képes kiszolgálni. azonban nem a Felhőbeli kapcsolattal lettek tervezve. Az átlátszatlan átjárók lehetővé teszik, hogy ezeket az adatfájlokat egy IoT-megoldásban feloldják és használják.

Az identitások fordítását végző átjáró biztosítja a protokollok fordításának előnyeit, továbbá lehetővé teszi a folyamaton kívüli eszközök teljes kezelhetőségét a felhőből. A IoT-megoldásban lévő összes eszköz megjelenik a IoT Hub az általuk használt protokolltól függetlenül.

## <a name="cheat-sheet"></a>Hasznos tanácsok

Itt található egy gyors Cheat-táblázat, amely összehasonlítja IoT Hub primitíveket transzparens, átlátszatlan (protokoll) és proxy átjárók használata esetén.

| Egyszerű | Transzparens átjáró | Protokoll fordítása | Identitás fordítása |
|--------|-------------|--------|--------|
| A IoT Hub Identity registryben tárolt identitások | Az összes csatlakoztatott eszköz identitása | Csak az átjáró eszközének identitása | Az összes csatlakoztatott eszköz identitása |
| Ikereszközök | Minden csatlakoztatott eszközhöz saját eszköz tartozik | Csak az átjáró rendelkezik egy eszköz és egy modul ikrekkel | Minden csatlakoztatott eszközhöz saját eszköz tartozik |
| Közvetlen metódusok és a felhőből az eszközre irányuló üzenetek | A felhő minden egyes csatlakoztatott eszközt külön tud kezelni | A felhő csak az átjáró eszközét tudja kezelni | A felhő minden egyes csatlakoztatott eszközt külön tud kezelni |
| [IoT Hub szabályozások és kvóták](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Alkalmazás minden eszközre | Alkalmazás az átjáró eszközre | Alkalmazás minden eszközre |

Ha átlátszatlan átjáró (protokoll fordítása) mintát használ, az átjárón keresztül csatlakozó összes eszköz ugyanazzal a felhőből az eszközre irányuló várólistára kerül, amely legfeljebb 50 üzenetet tartalmazhat. Ebből következik, hogy az átlátszatlan átjáró mintázatát csak akkor kell használni, ha kevés eszköz csatlakozik az egyes mezők átjáróján, és a felhőből az eszközre irányuló forgalom alacsony.

## <a name="next-steps"></a>Következő lépések

A következő három lépésből megtudhatja, hogyan állíthat be transzparens átjárót:

* [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
* [Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon](how-to-authenticate-downstream-device.md)
* [Lefelé irányuló eszköz csatlakoztatása Azure IoT Edge-átjáróhoz](how-to-connect-downstream-device.md)
