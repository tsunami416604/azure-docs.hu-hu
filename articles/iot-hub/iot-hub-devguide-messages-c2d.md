---
title: Megismerheti az Azure IoT Hub felhőből az eszközre irányuló üzenetküldési |} A Microsoft Docs
description: A fejlesztői útmutató bemutatja, miként használhatja az IoT hub üzenetküldés felhőből az eszközre. Az üzenet életciklus és konfigurációs lehetőségek vonatkozó információkat tartalmaz.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 0fc1b65a4ba1c8a3d76b48206d6a4703035e05bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055318"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Felhőből az eszközre irányuló üzenetek küldése az IoT hubról

Egyirányú értesítések küldéséhez egy eszközalkalmazás, a megoldás háttérrendszerének, felhőből az eszközre irányuló üzeneteket küldhet a az IoT hub az eszközre. Az Azure IoT Hub által támogatott lehetőségekért felhőből az eszközre, lásd: [felhőből az eszközre irányuló kommunikáció útmutatóját](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ön üzenetküldés a felhőből az eszközre keresztül a szolgáltatás felé néző végpont */üzenetek/devicebound*. Egy eszköz majd fogadja az üzeneteket egy eszközspecifikus végponton keresztül */devices/ {deviceId} / üzenetek/devicebound*.

Célozza meg minden felhőből az eszközre irányuló üzenet egyetlen eszközön, az IoT hub beállítja a **való** tulajdonságot */devices/ {deviceId} / üzenetek/devicebound*.

Minden egyes eszköz üzenetsorhoz tárolja, legfeljebb 50 felhőből az eszközre irányuló üzenetek. Szeretné kipróbálni a további üzeneteket küldeni az azonos eszköz eredmények hibát.

## <a name="the-cloud-to-device-message-life-cycle"></a>A felhőből az eszközre irányuló üzenetek életciklusának

Üzenet: legalább egyszeri kézbesítési biztosításához az IoT hub továbbra is fennáll eszközönkénti üzenetsorok üzenetek felhőből az eszközre. Az IoT hub, az üzenetek eltávolításához az üzenetsorból, az eszközök kifejezetten elismeri kell *befejezési*. Ez a megközelítés kapcsolat és eszközhibák szembeni ellenálló-képesség biztosítja.

Az életciklus-állapot graph a következő ábra jelenik meg:

![Felhőből az eszközre irányuló üzenetek életciklusának](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Amikor az IoT hub szolgáltatás egy üzenetet küld egy eszköz, a szolgáltatás üzenet állapotba állítása *várólistán lévő*. Ha egy eszköz szeretne *kap* egy üzenetet, az IoT hub *zárolások* az üzenetet úgy, hogy az állapot *láthatatlan*. Ebben az állapotban lehetővé teszi, hogy más szálak elindításához a többi üzenet fogadása az eszközön. Ha egy eszköz hozzászólásláncot egy üzenet feldolgozása befejeződik, értesíti a felhasználót az IoT hub által *befejezése* az üzenetet. Az IoT hub majd állapotba állítása *befejezve*.

Egy eszköz is:

* *Elutasítás* az üzenet, amely azt eredményezi, állítsa be az IoT hub a *kapcsolat megszakadásának lettered* állapota. A Message Queuing Telemetry Transport (MQTT) protokollon keresztül csatlakozó eszközök nem utasíthat el a felhőből az eszközre.

* *Abandon* az üzenet, amely az IoT hubot, hogy az üzenet vissza a várólistába helyezni, az állapot értéke a *várólistán lévő*. Eszközöket, amelyek az MQTT protokoll használatával kapcsolódnak a felhőből az eszközre irányuló üzenetek nem abandon.

A szál sikertelen lehet feldolgozni egy üzenetet az IoT hub értesítése nélkül. Ebben az esetben üzenetek automatikus áttérés a a *láthatatlan* állapot vissza a *várólistán lévő* állapot után egy *látható-e* időkorlátja (vagy *Zárolás* időkorlátja). Az időkorlát alapértelmezett értéke egy perc.

A **kézbesítések száma legfeljebb** az IoT hub a tulajdonság határozza meg, egy üzenet közötti váltáshoz a maximális száma a *várólistán lévő* és *láthatatlan* állapotok. Adott számú átmenetek után az IoT hub üzenet állapotúra állítja *kapcsolat megszakadásának lettered*. Hasonlóképpen, az IoT hub üzenet állapotúra állítja *kapcsolat megszakadásának lettered* a lejárati időpont után. További információkért lásd: [élettartam](#message-expiration-time-to-live).

A [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-csharp-csharp-c2d.md) a cikk bemutatja, hogyan lehet a felhőből az eszközre irányuló üzenetküldés a felhőből, és fogadhatók az eszközön.

Egy eszköz parancsmagokéval ér véget a felhőből az eszközre irányuló üzenet, amikor elvesztését, az üzenet nem befolyásolja az alkalmazás logikáját. Például az lehet, amikor az eszköz rendelkezik megőrzött helyileg a tartalmukat, vagy rendelkezik sikeresen végrehajtott egy műveletet. Sikerült az üzenet is biztosítunk az átmeneti információkat, amelyek adatvesztés nem lenne hatással az alkalmazás működése. Egyes esetekben a hosszan futó feladatokat, a következőket teheti:

* Végezze el a felhőből az eszközre irányuló üzenet, miután az eszköz rendelkezik megőrzi a helyi tárban a feladat leírása.

* A megoldás háttérrendszerének integrációját egy vagy több eszköz – felhő üzeneteket értesíti a feladat előrehaladtát különböző szakaszaiban.

## <a name="message-expiration-time-to-live"></a>Üzenetek lejárata (élettartama)

Minden felhőből az eszközre irányuló üzenetek lejárati időt tartalmaz. Ez idő értéke szerint a következő lehetőségek közül:

* A **ExpiryTimeUtc** tulajdonság a szolgáltatásban
* Az IoT hub és az alapértelmezett használatával *élettartam* , amely az IoT hub tulajdonság van megadva

Lásd: [felhőből az eszközre konfigurációs beállítások](#cloud-to-device-configuration-options).

Egy üzenet lejárati előnyeit, így elkerülheti a üzeneteket küld a leválasztott eszközöket gyakori módja az, hogy beállítása rövid *élettartam* értékeket. Ez a megközelítés az eszköz kapcsolati állapotát karbantartása ugyanazt az eredményt éri el, de hatékonyabb. Amikor üzenet nyugták igényel, az IoT hub értesítést küld, amely az eszközök:

* Képes üzeneteket fogadni.
* Nem érhetők el, vagy nem sikerült.

## <a name="message-feedback"></a>Üzenet visszajelzés

Felhőből az eszközre irányuló üzenet küldése, amikor a szolgáltatás kérhetnek üzenetenkénti visszajelzést a végső állapotát az üzenet kézbesítése. Ezt úgy teszi a **iothub-ack** application tulajdonságot a felhőből az eszközre irányuló üzenetet küld a rendszer a következő négy értékek egyikére:

| Nyugtázási tulajdonság értéke | Viselkedés |
| ------------ | -------- |
| Egyik sem     | Az IoT hub létrehozása nem egy visszajelzés üzenet (alapértelmezés). |
| pozitív | Ha eléri a felhőből az eszközre irányuló üzenet a *befejezve* állapotba, az IoT hub egy visszajelzés-üzenetet hoz létre. |
| negatív | Ha eléri a felhőből az eszközre irányuló üzenet a *kapcsolat megszakadásának lettered* állapotba, az IoT hub egy visszajelzés-üzenetet hoz létre. |
| korlátlan     | Az IoT hub egy visszajelzés üzenetet mindkét esetben hoz létre. |

Ha a **Ack** érték *teljes*, és nem kapja meg egy visszajelzés üzenetet, azt jelenti, hogy a visszajelzés üzenet lejárt. A szolgáltatás nem tudja, mi történt az eredeti üzenet. A gyakorlatban egy szolgáltatás biztosítania kell, hogy a visszajelzés lejárata előtt fel tud dolgozni. A maximális lejárati ideje két nap, így a szolgáltatás elemszámú fut újra hiba történik.

A [végpontok](iot-hub-devguide-endpoints.md), az IoT hub biztosít visszajelzést keresztül a szolgáltatás felé néző végpont */messages/servicebound/feedback*, üzenetekként. A visszajelzést kapó szemantikát megegyeznek a felhőből az eszközre. Amikor csak lehetséges, üzenet visszajelzés van kötegelni egyetlen üzenetben, a következő formátumban:

| Tulajdonság     | Leírás |
| ------------ | ----------- |
| EnqueuedTime | Időbélyeg, amely azt jelzi, ha a visszajelzés üzenetet kapott a hub |
| Felhasználói azonosító       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

A szervezetnek egy JSON-szerializált rekordokból álló tömböt, minden, az alábbi tulajdonságokkal:

| Tulajdonság           | Leírás |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Időbélyeg, amely azt jelzi, ha az üzenet eredményét történt (például a hub visszajelzés hibaüzenetet kapta, vagy az eredeti üzenet lejárt) |
| OriginalMessageId  | A *üzenetazonosító* a felhőből az eszközre irányuló üzenet, amely a visszajelzési információk vonatkozik |
| StatusCode         | Egy szükséges karakterláncot, a visszajelzési üzeneteket az IoT hub által előállított használt: <br/> *Sikeres* <br/> *Expired* <br/> *DeliveryCountExceeded* <br/> *Elutasított* <br/> *Purged* |
| Leírás        | Karakterlánc-értékek *StatusCode* |
| DeviceId           | A *DeviceId* a céleszköz a felhőből az eszközre irányuló üzenet visszajelzés ezen részének tárgyát képező |
| DeviceGenerationId | A *DeviceGenerationId* a céleszköz a felhőből az eszközre irányuló üzenet visszajelzés ezen részének tárgyát képező |

A felhőből az eszközre irányuló üzenet a visszajelzés korrelációját, ha az eredeti üzenettel, meg kell adnia a szolgáltatás egy *üzenetazonosító*.

Egy visszajelzés üzenet törzsét az alábbi kódban látható:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Felhő-eszköz konfigurációs beállításai

Minden IoT-központ a felhőből az eszközre irányuló üzenetküldés az alábbi konfigurációs lehetőségeket tesz elérhetővé:

| Tulajdonság                  | Leírás | Tartomány és az alapértelmezett |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Felhőből az eszközre irányuló üzenetek alapértelmezett élettartam | ISO_8601 időköz legfeljebb két napig (legalább 1 perc); alapértelmezett érték: 1 óra |
| maxDeliveryCount          | A felhőből az eszközre az eszközönkénti várólisták kézbesítések maximális száma | 1 és 100; alapértelmezett érték: 10 |
| feedback.ttlAsIso8601     | Szolgáltatás kötött visszajelzési üzenetek megőrzési ideje | ISO_8601 időköz legfeljebb két napig (legalább 1 perc); alapértelmezett érték: 1 óra |
| feedback.maxDeliveryCount | A visszajelzési üzenetsort kézbesítések maximális száma | 1 és 100; alapértelmezett érték: 100 |

A konfigurációs beállításokról beállításával kapcsolatos további információkért lásd: [hozzon létre IoT-központok](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>További lépések

Az SDK-kat a felhőből az eszközre irányuló üzenetek fogadása használatával kapcsolatos információkért lásd: [Azure IoT SDK-k](iot-hub-devguide-sdks.md).

Próbálja ki a felhőből az eszközre irányuló üzenetek fogadása, tekintse meg a [küldése a felhőből az eszközre](iot-hub-csharp-csharp-c2d.md) oktatóanyag.
