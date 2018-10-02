---
title: Megismerheti az Azure IoT Hub felhőből az eszközre irányuló üzenetküldési |} A Microsoft Docs
description: Fejlesztői útmutató – hogyan használhatja az IoT Hub üzenetküldés felhőből az eszközre. Az üzenet életciklusát, és a konfigurációs beállításokat kapcsolatos információkat tartalmaz.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: dobett
ms.openlocfilehash: 3f137ea80dc67bb075f34846e5563fb72c72b69a
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585645"
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>A felhőből az eszközökre irányuló üzenetek küldése az IoT Hubról

Az eszközalkalmazás egyirányú értesítések küldéséhez, a megoldás háttérrendszerének, felhő-eszközök üzeneteket küldhet a az IoT hub az eszközre. IoT Hub által támogatott más felhőalapú-eszközök közül, lásd: [felhőből az eszközre irányuló kommunikáció útmutatóját](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ön üzenetküldés a felhőből az eszközre keresztül a szolgáltatás felé néző végpont (**/üzenetek/devicebound**). Egy eszköz majd fogadja az üzeneteket egy eszközspecifikus végponton keresztül (**/devices/ {deviceId} / üzenetek/devicebound**).

Célozza meg minden felhőből az eszközre irányuló üzenet egyetlen eszközön, az IoT Hub beállítja a **való** tulajdonságot **/devices/ {deviceId} / üzenetek/devicebound**.

Minden egyes eszköz várólista legfeljebb 50 felhőből az eszközre irányuló üzenetek tárolja. Kísérlet a további üzeneteket küldeni az azonos eszköz eredmények hibát.

## <a name="the-cloud-to-device-message-lifecycle"></a>A felhőből az eszközre irányuló üzenetek életciklusának

Üzenet: legalább egyszeri kézbesítési biztosításához az IoT Hub továbbra is fennáll felhőből az eszközre irányuló üzenetek eszközönkénti várólistára. Eszközök explicit módon kell fogadnia *befejezési* az IoT Hub segítségével távolítsa el őket az üzenetsorból. Ez a megközelítés kapcsolat és eszközhibák szembeni ellenálló-képesség biztosítja.

Az alábbi ábrán látható a életciklus állapot grafikon a felhőből az eszközre irányuló üzenetek IoT hubban.

![Felhőből az eszközre irányuló üzenetek életciklusának](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Amikor az IoT Hub szolgáltatás egy üzenetet küld egy eszköz, a szolgáltatás üzenet állapotba állítása **várólistán lévő**. Amikor egy eszköz szeretne *kap* üzenetet az IoT Hub *zárolások* az üzenet (úgy, hogy az állapot **láthatatlan**), amely lehetővé teszi, hogy más szálak fogad el az eszközön más üzeneteket. Ha egy eszköz hozzászólásláncot egy üzenet feldolgozása befejeződik, értesíti a felhasználót az IoT Hub által *befejezése* az üzenetet. Az IoT Hub majd állapotba állítása **befejezve**.

Egy eszköz is választhatja, hogy:

* *Elutasítás* az üzenet, amely hatására az IoT Hub segítségével állítsa be a **kapcsolat megszakadásának lettered** állapota. Eszközöket, amelyek az MQTT protokoll használatával kapcsolódnak a felhőből az eszközre irányuló üzenetek nem utasíthat el.

* *Abandon* az üzenet, amely IoT hubot, hogy az üzenet vissza a várólistába helyezni, az állapot értéke a **várólistán lévő**. Eszközöket, amelyek az MQTT protokoll használatával kapcsolódnak a felhőből az eszközre irányuló üzenetek nem abandon.

A szál sikertelen lehet feldolgozni egy üzenetet az IoT Hub értesítése nélkül. Ebben az esetben üzenetek automatikusan át a **láthatatlan** állapot vissza a **várólistán lévő** állapot után egy *látható-e (vagy a zárolás) időtúllépési*. Ez az időkorlát alapértelmezett értéke egy perc.

A **kézbesítések száma legfeljebb** az IoT hub tulajdonság határozza meg, egy üzenet közötti váltáshoz a maximális száma a **várólistán lévő** és **láthatatlan** állapotok. Adott számú átmenetek után az IoT Hub üzenet állapotúra állítja **kapcsolat megszakadásának lettered**. Hasonlóképpen, az IoT Hub üzenet állapotúra állítja **kapcsolat megszakadásának lettered** a lejárati idő után (lásd: [élettartam](#message-expiration-time-to-live)).

A [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-csharp-csharp-c2d.md) bemutatja, hogyan felhőből az eszközre irányuló üzenetküldés a felhőből, és fogadhatók az eszközön.

Általában az eszköz a felhőből az eszközre irányuló üzenet befejeződik, amikor elvesztését, az üzenet nem befolyásolja az alkalmazás logikáját. Például, ha az eszköz rendelkezik helyi megőrzi a tartalom üzenet vagy sikeresen végrehajtott egy műveletet. Az üzenet sikerült is biztosítunk átmeneti információkat, amelyek adatveszteség nem hatnak ki az alkalmazás működése. Egyes esetekben a hosszan futó feladatokat, a következőket teheti:

* A felhőből az eszközre irányuló üzenet befejezése után a feladat leírása a helyi tároló megőrzése.

* A megoldás háttérrendszerének integrációját egy vagy több eszköz – felhő üzeneteket értesíti a feladat előrehaladtát különböző szakaszaiban.

## <a name="message-expiration-time-to-live"></a>Üzenetek lejárata (élettartama)

Minden felhőből az eszközre irányuló üzenetek lejárati időt tartalmaz. Jelenleg egy másik van beállítva:

* A **ExpiryTimeUtc** tulajdonság a szolgáltatásban.
* Az IoT Hub, az alapértelmezett *élettartam* egy IoT Hub tulajdonsággal megadott.

Lásd: [felhőből az eszközre konfigurációs beállítások](#cloud-to-device-configuration-options).

Egy közös üzenetek lejáratkor előnyeit, és elkerülheti az üzeneteket küld a leválasztott eszközöket módja élettartam-értékek rövid idő beállítása. Ez a megközelítés karbantartása az eszköz kapcsolati állapotát, ugyanakkor hatékonyabban ugyanazt az eredményt éri el. Amikor üzenet nyugtázás igényel, az IoT Hub értesítést küld, amely az eszközök:

* Képes üzeneteket fogadni.
* Nem érhetők el, vagy nem sikerült.

## <a name="message-feedback"></a>Üzenet visszajelzés

Felhőből az eszközre irányuló üzenet küldése, amikor a szolgáltatás kérhetnek üzenetenkénti visszajelzéseit a végső állapotát az üzenet kézbesítése.

| Nyugtázási tulajdonság | Viselkedés |
| ------------ | -------- |
| **pozitív** | Ha eléri a felhőből az eszközre irányuló üzenet a **befejezve** állapotba, az IoT Hub egy visszajelzés-üzenetet hoz létre. |
| **negative** | Ha eléri a felhőből az eszközre irányuló üzenet a **kapcsolat megszakadásának lettered** állapotba, az IoT Hub egy visszajelzés-üzenetet hoz létre. |
| **full**     | Az IoT Hub egy visszajelzés üzenetet mindkét esetben hoz létre. |

Ha **Ack** van **teljes**, és nem kapja meg egy visszajelzés üzenetet, azt jelenti, hogy a visszajelzés üzenet lejárt. A szolgáltatás nem tudja, mi történt az eredeti üzenet. A gyakorlatban egy szolgáltatás biztosítania kell, hogy a visszajelzés lejárata előtt fel tud dolgozni. A maximális lejárati ideje két nap, így a szolgáltatás elemszámú fut újra hiba történik.

A [végpontok](iot-hub-devguide-endpoints.md), az IoT Hub szolgáltatás által használt végponton keresztül visszajelzéseket biztosít (**/messages/servicebound/feedback**) üzenetekként. A visszajelzést kapó szemantikát megegyeznek a felhőből az eszközre. Amikor csak lehetséges, üzenet visszajelzés van kötegelni egyetlen üzenetben, a következő formátumban:

| Tulajdonság     | Leírás |
| ------------ | ----------- |
| EnqueuedTime | Időbélyeg, amely azt jelzi, ha a visszajelzés üzenetet kapott a hub. |
| Felhasználói azonosító       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

A szervezetnek egy JSON-szerializált rekordokból álló tömböt, minden, az alábbi tulajdonságokkal:

| Tulajdonság           | Leírás |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Mikor történt a az üzenet eredményét jelző időbélyeg. Ha például a hub visszajelzés hibaüzenetet kapta, vagy az eredeti üzenet lejárt. |
| OriginalMessageId  | **Üzenetazonosító** a felhőből az eszközre irányuló üzenet, amely a visszajelzési információk vonatkozik. |
| Állapotkód         | Szükséges karakterlánc. Visszajelzési üzenetek az IoT Hub által generált használni. <br/> "Sikeres" <br/> 'Expired' <br/> 'DeliveryCountExceeded' <br/> Elutasítva: <br/> "Törölve" |
| Leírás        | Karakterlánc-értékeket **StatusCode**. |
| Eszközazonosító           | **DeviceId** a céleszköz a felhőből az eszközre irányuló üzenet visszajelzés ezen részének tárgyát képező. |
| DeviceGenerationId | **DeviceGenerationId** a céleszköz a felhőből az eszközre irányuló üzenet visszajelzés ezen részének tárgyát képező. |

A szolgáltatás meg kell adnia egy **üzenetazonosító** a felhőből az eszközre irányuló üzenet tudnia kell korrelálni a visszajelzéseket az eredeti üzenettel.

Az alábbi példa bemutatja egy visszajelzés üzenet törzsében.

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
| defaultTtlAsIso8601       | Alapértelmezett TTL a felhőből az eszközre. | ISO_8601 időköz legfeljebb 2D (legalább 1 perc). Alapértelmezett érték: 1 óra. |
| maxDeliveryCount          | Kézbesítések maximális száma a felhőből az eszközre az eszközönkénti sorra. | 1 és 100 között. Alapértelmezett: 10. |
| feedback.ttlAsIso8601     | Szolgáltatás kötött visszajelzési üzenetek megőrzési ideje. | ISO_8601 időköz legfeljebb 2D (legalább 1 perc). Alapértelmezett érték: 1 óra. |
| feedback.maxDeliveryCount |Kézbesítések maximális száma a visszajelzési üzenetsort. | 1 és 100 között. Alapértelmezett: 100. |

A konfigurációs beállításokról beállításával kapcsolatos további információkért lásd: [hozzon létre IoT-központok](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>További lépések

Használhatja a felhőből az eszközre irányuló üzenetek fogadása az SDK-kkal kapcsolatos információk: [Azure IoT SDK-k](iot-hub-devguide-sdks.md).

Próbálja ki a felhőből az eszközre irányuló üzenetek fogadása, tekintse meg a [küldése a felhőből az eszközre](iot-hub-csharp-csharp-c2d.md) oktatóanyag.