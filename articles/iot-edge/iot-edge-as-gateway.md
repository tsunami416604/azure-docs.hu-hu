---
title: Átjárók alsóbb rétegbeli eszközökhöz - Azure IoT Edge | Microsoft dokumentumok
description: Az Azure IoT Edge használatával átlátható, átlátszatlan vagy proxyátjáró-eszközt hozhat létre, amely több alsóbb rétegbeli eszközről küld adatokat a felhőbe, vagy helyileg dolgozza fel azt.
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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733517"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>IoT Edge-eszköz használata átjáróként

Az IoT Edge-megoldások átjárói eszközkapcsolatot és peremhálózati elemzést biztosítanak olyan IoT-eszközökhöz, amelyek egyébként nem rendelkeznek ezekkel a képességekkel. Az Azure IoT Edge használható az IoT-átjáró iránti bármilyen igény kielégítésére, függetlenül attól, hogy az kapcsolattal, identitással vagy peremhálózati elemzéssel kapcsolatos. Ebben a cikkben az átjáróminták csak az alsóbb rétegbeli eszközkapcsolat és az eszközidentitás jellemzőire vonatkoznak, az átjárón az eszközadatok feldolgozásának módjára nem.

## <a name="patterns"></a>Minták

Az IoT Edge-eszközök átjáróként való használatának három mintázata létezik: átlátszó, protokollfordítás és identitásfordítás:

* **Átlátszó** – Eszközök, amelyek elméletileg csatlakozhatnak az IoT Hub csatlakozhat egy átjáró eszköz helyett. Az alsóbb rétegbeli eszközök saját IoT Hub-identitással rendelkeznek, és az MQTT, Az AMQP vagy a HTTP protokollok bármelyikét használják. Az átjáró egyszerűen továbbítja az eszközök és az IoT Hub közötti kommunikációt. Az eszközök és a felhasználók az IoT Hubon keresztül kommunikálnak velük, és nincsenek tudatában annak, hogy egy átjáró közvetíti a kommunikációjukat. Ez a tudatosság hiánya azt jelenti, hogy a portál *átláthatónak*tekinthető . Tekintse [meg: Egy transzparens átjáró](how-to-create-transparent-gateway.md) létrehozása a részletek hez egy IoT Edge-eszköz átlátszó átjáróként való használatával.
* **Protokollfordítás** – más néven átlátszatlan átjáróminta, eszközök, amelyek nem támogatják az MQTT, AMQP vagy HTTP segítségével egy átjáró eszköz adatokat küldeni az IoT Hub a nevükben. Az átjáró megérti az alsóbb rétegbeli eszközök által használt protokollt, és az egyetlen olyan eszköz, amely identitással rendelkezik az IoT Hubban. Minden információ úgy néz ki, mintha egy eszközről, az átjáróból érkezne. Az alsóbb rétegbeli eszközöknek további azonosító adatokat kell beágyazniuk az üzeneteikbe, ha a felhőalapú alkalmazások eszközenként szeretnék elemezni az adatokat. Emellett az IoT Hub primitívek, például az ikrek és a módszerek csak az átjáró eszköz, nem alsóbb rétegbeli eszközök.
* **Identitásfordítás** – Az IoT Hubhoz nem tudó eszközök ehelyett csatlakozhatnak egy átjáróeszközhöz. Az átjáró az IoT Hub identitás- és protokollfordítást biztosít az alsóbb rétegbeli eszközök nevében. Az átjáró elég intelligens ahhoz, hogy megértse az alsóbb rétegbeli eszközök által használt protokollt, identitást biztosítson számukra, és lefordítsa az IoT Hub primitívjeit. Az alsóbb rétegbeli eszközök első osztályú eszközökként jelennek meg az IoT Hubban, ikerpárokkal és módszerekkel. A felhasználó az IoT Hub ban kommunikálhat az eszközökkel, és nincs tisztában a köztes átjáróeszközzel.

![Diagram – Átlátszó, protokoll- és identitásátjáró-minták](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Használati esetek

Minden átjáróminta a következő előnyöket biztosítja:

* **Analytics a peremhálózaton** – Az AI-szolgáltatások helyi használatával az alsóbb rétegbeli eszközökről érkező adatok feldolgozásához teljes hűségű telemetriai adatok küldése nélkül a felhőbe. Helyi elemzéseket kereshet és reagálhat, és csak az adatok egy részét küldheti el az IoT Hubnak.
* **Alsóbb rétegbeli eszköz elkülönítése** – Az átjáró eszköz képes megvédeni az összes downstream eszközök az internetnek való kitettség. Ülhet között OT hálózat, amely nem rendelkezik kapcsolattal, és egy informatikai hálózat, amely hozzáférést biztosít az internethez.
* **Kapcsolat multiplexelés** – Az IoT Edge-átjárón keresztül az IoT Hubhoz csatlakozó összes eszköz ugyanazt az alapul szolgáló kapcsolatot használja.
* **Forgalomsimítás** – Az IoT Edge-eszköz automatikusan végrehajtja az exponenciális visszamaradást, ha az IoT Hub szabályozza a forgalmat, miközben helyileg megőrzi az üzeneteket. Ez az előny rugalmassá teszi a megoldást a forgalom csúcsaival szemben.
* **Offline támogatás** – Az átjáróeszköz tárolja az okat az üzeneteket és az ikerfrissítéseket, amelyek nem kézbesíthetők az IoT Hubra.

A protokollfordítást végző átjáró peremhálózati elemzéseket, eszközelkülönítést, forgalomsimítást és offline támogatást is végezhet a meglévő eszközökön és az erőforrások korlátozottan korlátozott eszközein. Számos meglévő eszköz olyan adatokat hoz létre, amelyek üzleti elemzéseket képesek beáramazni; azonban nem a felhőalapú kapcsolatot szem előtt tartva tervezték őket. Az átlátszatlan átjárók lehetővé teszik, hogy ezeket az adatokat feloldják és iot-megoldásban használják.

Az identitásfordítást lehetővé thoz óvó átjáró biztosítja a protokollfordítás előnyeit, és emellett lehetővé teszi a felhőből származó alsóbb rétegbeli eszközök teljes körű kezelhetőségét. Az IoT-megoldásban lévő összes eszköz megjelenik az IoT Hubban, függetlenül attól, hogy az általuk használt protokollt használják.All devices in your IoT solution show up in IoT Hub regardless of the protocol they use.

## <a name="cheat-sheet"></a>Hasznos tanácsok

Itt van egy gyors cheat sheet, amely összehasonlítja az IoT Hub primitívek, ha átlátszó, átlátszatlan (protokoll) és proxy átjárók használata.

| &nbsp; | Átlátszó átjáró | Protokoll fordítása | Identitás fordítása |
|--------|-------------|--------|--------|
| Az IoT Hub identitásbeállítás-nyilvántartásában tárolt identitások | Az összes csatlakoztatott eszköz identitása | Csak az átjáróeszköz identitása | Az összes csatlakoztatott eszköz identitása |
| Ikereszközök | Minden csatlakoztatott eszköz saját eszköz iker | Csak az átjáró rendelkezik egy eszköz és modul ikrek | Minden csatlakoztatott eszköz saját eszköz iker |
| Közvetlen módszerek és felhőből az eszközre irányuló üzenetek | A felhő minden csatlakoztatott eszközt külön-külön kezelhet | A felhő csak az átjáróeszközt tudja megcímezni | A felhő minden csatlakoztatott eszközt külön-külön kezelhet |
| [Az IoT Hub szabályozása és kvótái](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Alkalmazás minden eszközre | Alkalmazás az átjáróeszközre | Alkalmazás minden eszközre |

Átlátszatlan átjáró (protokollfordítás) minta használata esetén az átjárón keresztül csatlakozó összes eszköz ugyanazt a felhőből az eszközre irányuló várólistát használja, amely legfeljebb 50 üzenetet tartalmazhat. Ebből következik, hogy az átlátszatlan átjárómintát csak akkor kell használni, ha kevés eszköz csatlakozik az egyes helyszíni átjárókon keresztül, és a felhőből az eszközre irányuló forgalom alacsony.

## <a name="next-steps"></a>További lépések

További információ az átlátszó átjáró beállításáról:

* [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
* [Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon](how-to-authenticate-downstream-device.md)
* [Lefelé irányuló eszköz csatlakoztatása Azure IoT Edge-átjáróhoz](how-to-connect-downstream-device.md)
