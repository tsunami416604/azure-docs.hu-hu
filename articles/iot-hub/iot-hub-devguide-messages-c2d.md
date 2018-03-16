---
title: "Azure IoT Hub eszközre cloud messaging megértése |} Microsoft Docs"
description: "Fejlesztői útmutató - felhő eszközre üzenetküldés az IoT-központ használatával. Az üzenet életciklusát, és a konfigurációs beállítások kapcsolatos adatokat tartalmaz."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2018
ms.author: dobett
ms.openlocfilehash: d265d35c7d5a394afa0e59f40ff1a5741e0ec35c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Felhő-eszközre küldött üzenetek küldése az IoT-központ

Az eszköz alkalmazásának a megoldás háttérből egyirányú értesítések küldésére, felhő-eszközök üzenetek küldése az IoT hub az eszközét. Más felhőalapú-eszközök beállításai, IoT-központ által támogatott tárgyalását lásd: [felhő eszközre kommunikációs útmutatást][lnk-c2d-guidance].

A szolgáltatás felé néző végpont keresztül felhő eszközre üzeneteket küld (**/üzenetek/devicebound**). Egy eszköz majd fogadja az üzeneteket egy eszközspecifikus végpontot keresztül (**/devices/ {deviceId} / üzenetek/devicebound**).

Amelyekre az egyes felhőalapú eszközre üzeneteinek egyetlen eszközt, az IoT-központ beállítja a **való** tulajdonságot **/devices/ {deviceId} / üzenetek/devicebound**.

Minden eszköz várólista legfeljebb 50 felhő eszközre üzeneteket tárolja. A el ugyanazon eszköz hiba további üzenetek küldésére tett kísérlet.

## <a name="the-cloud-to-device-message-lifecycle"></a>A felhő-eszközre küldött üzenetek életciklusa

A legalább egyszeri üzenet kézbesítési biztosításához IoT-központ továbbra is fennáll eszköz felhő eszközre üzeneteket. Eszközök kell kifejezetten elismeri *befejezési* IoT hub azok eltávolítása a sorból. Ez a megközelítés biztosítja, hogy kapcsolatot és meghibásodása esetén szembeni hibatűrést.

A következő diagramon láthatók a felhőből eszközre üzenet életciklus állapotát ábrázoló az IoT-központ.

![Felhő-eszközre küldött üzenetek életciklusa][img-lifecycle]

Az IoT-központ szolgáltatás üzenetet küld egy eszközt, ha a szolgáltatás üzenet állapotba állítása **a várólistában levő**. Ha egy eszköz kísérel *kap* üzenetet az IoT-központ *zárolások* az üzenet (által a állapotra **láthatatlan**), amely lehetővé teszi, hogy más szálak megkezdése az eszközön más üzeneteket. Egy eszköz szál befejezése után egy üzenet feldolgozása értesíti a felhasználót az IoT-központ által *befejezése* az üzenetet. Az IoT-központ majd állapotba állítása **befejezve**.

Egy eszköz is megteheti, hogy:

* *Elutasítása* az üzenet, amely azt eredményezi, állítsa be az IoT-központ a **Dead lettered** állapotát. A MQTT protokollon keresztül csatlakozó eszközökön nem utasíthat el a felhő-eszközre küldött üzenetek.
* *Abandon* az üzenet, amely azt eredményezi, az üzenet vissza a várólistába helyezni, az állapot beállítása az IoT-központ **a várólistában levő**. A MQTT protokollon keresztül csatlakozó eszközök felhő-eszközre küldött üzenetek nem abandon.

A szál nem sikerül felvenni feldolgozni egy üzenetet az IoT-központ értesítése nélkül. Ebben az esetben üzenetek automatikusan állnak a **láthatatlan** állapot vissza a **a várólistában levő** állapot után egy *látható (vagy a zárolás) időtúllépési*. Ez az időkorlát alapértelmezett értéke egy perc.

A **kézbesítési száma legfeljebb** IoT hub tulajdonság határozza meg, egy üzenet is átmenet között a maximálisan megengedett számú a **a várólistában levő** és **láthatatlan** állapotok. Követően, hogy áttérések számával, IoT-központ állapotát állítja, az üzenet **Dead lettered**. Ehhez hasonlóan az IoT-központ üzenet állapotúra állítja **Dead lettered** a lejárati idő után (lásd: [élettartama][lnk-ttl]).

A [IoT hubbal felhő eszközre üzenetek küldése] [ lnk-c2d-tutorial] bemutatja, hogyan felhő-eszközre küldött üzenetek küldéséhez a felhőből, és fogadásukra az eszközön.

Általában egy eszköz befejezése felhő eszközre üzenet, amikor elvész az az üzenet nem befolyásolja az alkalmazás logikáját. Például ha az eszköz rendelkezik helyileg tárolt tartalom üzenet vagy a sikeresen végrehajtott egy művelet. Az üzenetet is sikerült továbbítani az átmeneti információkat, amelyek adatvesztés nem befolyásolná az alkalmazás funkcióit. Egyes esetekben a hosszan futó feladatokat, a következőket teheti:

* Végezze el a felhőből eszközre üzenet után megőrzése a helyi tároló feladat leírását.
* Értesítse a megoldás háttérrendszerének integrációját egy vagy több eszköz-a-felhőbe küldött üzeneteket különböző szakaszaiban a feladat előrehaladását.

## <a name="message-expiration-time-to-live"></a>Üzenet lejárati (élő idő)

Minden felhő eszközre üzenetnek lejárati időt. Az időtartamot egyikével:

* A **ExpiryTimeUtc** tulajdonság a szolgáltatásban.
* Az alapértelmezett IoT-központ *élettartama* egy IoT-központ tulajdonság szerepel.

Lásd: [felhő eszközre konfigurációs beállítások][lnk-c2d-configuration].

Egy közös üzenet lejárati előnyeit, és elkerülheti a leválasztott eszközök üzenetküldésre módja értékek élő rövid idő beállítása. Ez a megközelítés fenntartása az eszköz kapcsolati állapotát, miközben sokkal hatékonyabb ugyanazt az eredményt éri el. Amikor üzenet nyugták kér le, az IoT-központ értesíti a felhasználót, amely az eszközök:

* Képes üzeneteket fogadni.
* Nincs online állapotban, vagy nem sikerült.

## <a name="message-feedback"></a>Üzenet visszajelzés

A felhőből eszközre üzenetet küld, ha a szolgáltatás kérhetnek az üzenet visszajelzés kapcsolatban a végső állapot üzenet kézbesítését.

| Nyugtázási tulajdonság | Viselkedés |
| ------------ | -------- |
| **positive** | Ha a felhő eszközre üzenet eléri a **befejezve** állapotba kerül, az IoT-központ visszajelzés üzenetet hoz létre. |
| **negative** | Ha a felhő eszközre üzenet eléri a **Dead lettered** állapotba kerül, az IoT-központ visszajelzés üzenetet hoz létre. |
| **full**     | Az IoT-központ visszajelzés üzenet mindkét esetben állít elő. |

Ha **nyugtázási** van **teljes**, és nem kap egy üzenetet, visszajelzést, az azt jelenti, hogy a visszajelzés üzenet lejárt. A szolgáltatás nem tudja az eredeti üzenet Mi történt. A gyakorlatban egy szolgáltatás győződjön meg arról, hogy a visszajelzés lejárata előtt is feldolgozza. A lejárati idő két nap, így a szolgáltatás elemszámú fut újra hiba esetén.

A [végpontok][lnk-endpoints], IoT-központ biztosítja a szolgáltatás felé néző végpont visszajelzései (**/messages/servicebound/feedback**) üzeneteihez. Visszajelzés fogadott szemantikáját megegyeznek a felhő-eszközre küldött üzenetek. Amikor csak lehetséges, a program egy üzenetben, a következő formátumú kötegelni üzenet visszajelzés:

| Tulajdonság     | Leírás |
| ------------ | ----------- |
| EnqueuedTime | Jelzi, ha a visszajelzés üzenetet kapott a központ időbélyegző. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

A szervezet rekord, egy JSON-szerializált tömbje, minden, a következő tulajdonságokkal:

| Tulajdonság           | Leírás |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Mikor történt, az üzenet eredményeit jelző időbélyegző. Például a központ visszajelzés hibaüzenetet kapta, vagy az eredeti üzenet érvényessége lejárt. |
| OriginalMessageId  | **MessageId** a felhőből eszközre üzenet, amely a visszajelzési információk vonatkozik. |
| statusCode         | Szükséges karakterlánc. Az IoT-központ által generált visszajelzés üzenetekben használatos. <br/> 'Success' <br/> 'Expired' <br/> 'DeliveryCountExceeded' <br/> "Visszautasított" <br/> "Kiürítve" |
| Leírás        | Karakterlánc-értékek **StatusCode**. |
| DeviceId           | **DeviceId** a célként megadott eszköz a felhőből eszközre üzenet, amely a visszajelzésekben vonatkozik. |
| DeviceGenerationId | **DeviceGenerationId** a célként megadott eszköz a felhőből eszközre üzenet, amely a visszajelzésekben vonatkozik. |

A szolgáltatás meg kell adnia egy **MessageId** a felhőből eszközre üzenet tudjanak a visszajelzés a kivizsgált az eredeti üzenet.

A következő példa bemutatja a visszajelzés üzenet törzsét.

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

## <a name="cloud-to-device-configuration-options"></a>Felhő-eszköz konfigurációs beállítások

Minden egyes IoT-központ mutatja meg a következő konfigurációs beállításokat a felhőből eszközre üzenetek:

| Tulajdonság                  | Leírás | Tartomány- és alapértelmezett |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Felhő-eszközre küldött üzenetek alapértelmezett élettartam. | ISO_8601 időköz legfeljebb 2D (legalább 1 perc). Alapértelmezett: 1 óra. |
| maxDeliveryCount          | Felhő eszközre eszközönkénti sorok maximális száma. | 1 és 100 közötti. Alapértelmezett: 10. |
| feedback.ttlAsIso8601     | Szolgáltatás adathoz kötött visszajelzés üzenetek megőrzési. | ISO_8601 időköz legfeljebb 2D (legalább 1 perc). Alapértelmezett: 1 óra. |
| feedback.maxDeliveryCount |A visszajelzési üzenetsor maximális száma. | 1 és 100 közötti. Alapértelmezett: 100. |

A konfigurációs beállításokról telepítésével kapcsolatos további információkért lásd: [létrehozása IoT-központok][lnk-portal].

## <a name="next-steps"></a>További lépések

További információ az SDK-k segítségével felhő eszközre üzeneteket fogadni,: [Azure IoT SDK-k][lnk-sdks].

Próbálja ki felhő-eszközre küldött üzenetek fogadását, tekintse meg a [küldése eszközre felhő] [ lnk-c2d-tutorial] oktatóanyag.

[img-lifecycle]: ./media/iot-hub-devguide-messages-c2d/lifecycle.png

[lnk-portal]: iot-hub-create-through-portal.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-ttl]: #message-expiration-time-to-live
[lnk-c2d-configuration]: #cloud-to-device-configuration-options
[lnk-lifecycle]: #message-lifecycle
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
