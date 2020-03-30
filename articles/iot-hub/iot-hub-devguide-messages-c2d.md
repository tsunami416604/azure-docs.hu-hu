---
title: Az Azure IoT Hub felhőből az eszközökre történő üzenetküldése | Microsoft dokumentumok
description: Ez a fejlesztői útmutató ismerteti, hogyan használhatja a felhőből az eszközökre irányuló üzenetküldést az IoT hubbal. Információkat tartalmaz az üzenet életciklusáról és a konfigurációs beállításokról.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3a7254cc9de89a297811792b4dd64b4b669ba8e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271238"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Felhőből eszközre irányuló üzenetek küldése IoT-központból

Ha egyirányú értesítéseket szeretne küldeni egy eszközalkalmazásba a megoldás háttértartalékából, küldjön felhőből az eszközre irányuló üzeneteket az IoT hubról az eszközre. Az Azure IoT Hub által támogatott egyéb felhőből az eszközre irányuló beállításokról a [Felhőből az eszközre irányuló kommunikációra vonatkozó útmutatást című témakörben talál.](iot-hub-devguide-c2d-guidance.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

A felhőből az eszközre irányuló üzeneteket egy szolgáltatásfelé néző végponton, *a /messages/devicebound kapcsolón keresztül küldheti el.* Az eszköz ezután egy eszközspecifikus végponton, *a /devicesId}/messages/devicebound*kapcsolón keresztül fogadja az üzeneteket.

Ha minden felhőből az eszközre irányuló üzenetet egyetlen eszközön szeretne megcélozni, az IoT hub a **tulajdonságot** a */devices/{deviceId}/messages/devicebound értékre állítja.*

Minden eszközsor legbőleg50 felhőből az eszközre irányuló üzenetet tárol. Ha több üzenetet próbál küldeni ugyanarra az eszközre, az hibát eredményez.

## <a name="the-cloud-to-device-message-life-cycle"></a>A felhőből az eszközre irányuló üzenetek életciklusa

Az üzenetek legalább egyszeri kézbesítésének biztosítása érdekében az IoT hub megőrzi a felhőből az eszközre irányuló üzeneteket az eszközönkénti várólistákban. Ahhoz, hogy az IoT hub távolítsa el az üzeneteket a várólistából, az eszközöknek explicit módon el kell ismerniük a *befejezést.* Ez a megközelítés garantálja a kapcsolódási és eszközhibák elleni rugalmasságot.

Az életciklus-állapot grafikon ja:

![Felhőből az eszközre irányuló üzenetek életciklusa](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Amikor az IoT hub szolgáltatás üzenetet küld egy eszköznek, a szolgáltatás az üzenet állapotát *Enqueued (Enqueued) állapotra állítja.* Ha egy eszköz üzenetet szeretne *kapni,* az IoT hub *zárolja* az üzenetet azáltal, hogy az *állapotot Láthatatlan állapotra állítja.* Ez az állapot lehetővé teszi, hogy az eszközön lévő más szálak más üzenetek fogadását is megkezdjék. Amikor egy eszközszál befejezi az üzenet feldolgozását, az üzenet *végrehajtásával* értesíti az IoT-központot. Az IoT hub ezután beállítja az *állapotot Befejezett*.

Az eszköz a következőket is megteheti:

* *Utasítsa el* az üzenetet, amelynek hatására az IoT hub állítsa be a *Dead levelű* állapotban. A Message Queuing telemetriai átviteli (MQTT) protokollon keresztül csatlakozó eszközök nem utasíthatják el a felhőből az eszközre irányuló üzeneteket.

* Az üzenet *elhagyása,* amelynek hatására az IoT hub visszahelyezi az üzenetet a várólistába, és az állapot *beállítása Enqueued*. Az MQTT protokollon keresztül csatlakozó eszközök nem hagyhatják el a felhőből az eszközre irányuló üzeneteket.

Egy szál nem tudta feldolgozni az üzenetet az IoT hub értesítése nélkül. Ebben az esetben az üzenetek automatikusan áttérnek a *Láthatatlan* állapotról a *várólistán lévő* állapotra egy *láthatósági* időtúltöltés (vagy *zárolási* időtúlzáró) után. Ennek az időtúlhirdetésnek az értéke egy perc, és nem módosítható.

Az IoT hub **maximális kézbesítési száma** tulajdonság határozza meg, hogy egy üzenet legfeljebb hányszor válthat a *várólistán lévő* és a *láthatatlan* állapotok között. Ennyi átmenet után az IoT hub az üzenet állapotát *halott betűvel beállítja.* Hasonlóképpen az IoT hub beállítja az üzenet állapotát a lejárati idő után *leírandó kézbesítési* állapotra. További információ: [Time to live](#message-expiration-time-to-live).

A [Felhőből az eszközökre irányuló üzenetek küldése az IoT Hub segítségével](iot-hub-csharp-csharp-c2d.md) cikk bemutatja, hogyan küldhet felhőből az eszközre irányuló üzeneteket a felhőből, és hogyan fogadhatja őket egy eszközön.

Az eszköz általában befejezi a felhőből az eszközre üzenetet, ha az üzenet elvesztése nincs hatással az alkalmazás logikájára. Erre példa lehet, ha az eszköz helyileg megőrizte az üzenet tartalmát, vagy sikeresen végrehajtott egy műveletet. Az üzenet átmeneti információkat is hordozhat, amelyek elvesztése nem befolyásolja az alkalmazás működését. A hosszú ideig futó feladatok esetében néha a következőkre van lehetőség:

* Töltse ki a felhőből az eszközre üzenetet, miután az eszköz megőrizte a feladat leírását a helyi tárolóban.

* Értesítse a megoldás háttér-végén egy vagy több eszköz-felhő üzenetek különböző szakaszaiban a feladat előrehaladását.

## <a name="message-expiration-time-to-live"></a>Az üzenetek lejárata (az élet ideje)

Minden felhőből az eszközre irányuló üzenet nek van lejárati ideje. Ezt az időt a következők határozzák meg:

* A szolgáltatás Ban található **ExpiryTimeUtc** tulajdonság
* Az IoT hub, az IoT hub-tulajdonságként megadott alapértelmezett *életidő* használatával

Lásd: [Felhőből az eszközre irányuló konfigurációs beállítások.](#cloud-to-device-configuration-options)

Az üzenetek lejárati előnyeinek kihasználásával és a leválasztott eszközökre küldött üzenetek elkerülésével rövid időt állíthat be *az élő értékekre.* Ez a megközelítés ugyanazt az eredményt éri el, mint az eszköz kapcsolati állapotának fenntartása, de hatékonyabb. Amikor üzenetnyugtákat kér, az IoT hub értesíti, hogy mely eszközök:

* Képes üzeneteket fogadni.
* Nincsenek online állapotban, vagy nem sikerült.

## <a name="message-feedback"></a>Üzenetvisszajelzés

Amikor felhőből az eszközre irányuló üzenetet küld, a szolgáltatás kérheti az üzenet végső állapotáról szóló üzenetenkénti visszajelzést. Ezt úgy teheti meg, hogy az **iothub-ack** alkalmazástulajdonságot a felhőből az eszközre irányuló üzenetben állítja be, amelyet a rendszer az alábbi négy érték egyikére küld:

| Ack tulajdonság értéke | Viselkedés |
| ------------ | -------- |
| Nincs     | Az IoT hub nem hoz létre visszajelzési üzenetet (alapértelmezett viselkedés). |
| pozitív | Ha a felhőből az eszközre irányuló üzenet eléri a *Befejezett* állapotot, az IoT hub visszajelzési üzenetet hoz létre. |
| negatív | Ha a felhőből az eszközre irányuló üzenet eléri a *kézbesítésben lévő levelű* állapotot, az IoT hub visszajelzési üzenetet hoz létre. |
| Teljes     | Az IoT hub mindkét esetben visszajelzési üzenetet hoz létre. |

Ha az **Ack-érték** *megtelt,* és nem kap visszajelzési üzenetet, az azt jelenti, hogy a visszajelzési üzenet lejárt. A szolgáltatás nem tudhatja, mi történt az eredeti üzenettel. A gyakorlatban a szolgáltatásnak biztosítania kell, hogy a visszajelzések lejárata előtt feldolgozhassák. A maximális lejárati idő két nap, amely időt hagy a szolgáltatás újra futtatásához, ha hiba történik.

[Avégpontok](iot-hub-devguide-endpoints.md)magyarázata szerint az IoT hub visszajelzést küld egy szolgáltatás felé néző végponton keresztül, */messages/servicebound/feedback*, üzenetként. A visszajelzések fogadásának szemantikája megegyezik a felhőből az eszközre irányuló üzenetek szemantikája. Amikor csak lehetséges, az üzenetek visszajelzése egyetlen üzenetben lesz kötegelve, a következő formátummal:

| Tulajdonság     | Leírás |
| ------------ | ----------- |
| EnqueuedTime | Időbélyeg, amely jelzi, hogy a központ mikor kapta meg a visszajelzési üzenetet |
| UserId (Felhasználóazonosító)       | `{iot hub name}` |
| ContentType (Tartalomtípus)  | `application/vnd.microsoft.iothub.feedback.json` |

A törzs egy JSON-szerializált rekordtömb, amelyek mindegyike a következő tulajdonságokkal rendelkezik:

| Tulajdonság           | Leírás |
| ------------------ | ----------- |
| VárólistánTimeUtc    | Időbélyeg, amely jelzi, hogy mikor történt az üzenet eredménye (például a központ megkapta a visszajelzési üzenetet vagy az eredeti üzenet lejárt) |
| Eredeti üzenetazonosító  | Annak a felhőből az eszköznek a *MessageId azonosítója,* amelyre ez a visszajelzési információ vonatkozik |
| StatusCode (Állapotkód)         | Az IoT hub által létrehozott visszajelzési üzenetekben használt kötelező karakterlánc: <br/> *Siker* <br/> *Lejárt* <br/> *DeliveryCountExceeded* <br/> *Elutasítva* <br/> *Kiürítve* |
| Leírás        | A *StatusCode* karakterlánc-értékei |
| DeviceId           | Annak a felhőből az eszköznek az eszközére vonatkozó eszköz *eszközazonosítója,* amelyre ez a visszajelzés vonatkozik |
| DeviceGenerationId | Annak a felhőből az eszköznek a *céleszközének A DeviceGenerationId azonosítója,* amelyhez ez a visszajelzés vonatkozik |

Ahhoz, hogy a felhőből az eszközre irányuló üzenet korrelálja a visszajelzést az eredeti üzenettel, a szolgáltatásnak *messageid azonosítót kell megadnia.*

A visszacsatolási üzenet törzse a következő kódban látható:

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

**Függőben lévő visszajelzés a törölt eszközökhöz**

Az eszköz törlésekor a függőben lévő visszajelzések is törlődnek. Az eszközvisszajelzést kötegekben küldi el a rendszer. Ha egy eszköz törlődik a keskeny ablakban (gyakran kevesebb, mint 1 másodperc) az üzenet átvételének és a következő visszajelzési köteg elkészítésének ellenőrzése között, a visszajelzés nem fog bekövetkezni.

Ezt a viselkedést úgy oldhatja meg, hogy az eszköz törlése előtt megvár egy ideig, amíg a függőben lévő visszajelzés megérkezik. A kapcsolódó üzenetek visszajelzését az eszköz törlése után elveszettnek kell tekinteni.

## <a name="cloud-to-device-configuration-options"></a>Felhőből az eszközre beállítási lehetőségek

Minden IOt-központ a következő konfigurációs lehetőségeket teszi elérhetővé a felhőből az eszközre irányuló üzenetküldéshez:

| Tulajdonság                  | Leírás | Tartomány és alapértelmezett |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | A felhőből az eszközre irányuló üzenetek alapértelmezett ttl-je | ISO_8601 időközleglegig 2 napig (legalább 1 perc); alapértelmezett: 1 óra |
| maxDeliveryCount (maxDeliveryCount)          | A felhőből az eszközre irányuló, eszközről az eszközre irányuló várólisták maximális kézbesítési száma | 1-100; alapértelmezett: 10 |
| feedback.ttlAsIso8601     | Szolgáltatáshoz kötött visszajelzési üzenetek megőrzése | ISO_8601 időközleglegig 2 napig (legalább 1 perc); alapértelmezett: 1 óra |
| feedback.maxDeliveryCount | A visszajelzési várólista maximális kézbesítési száma | 1-100; alapértelmezett: 10 |
| feedback.lockDurationAsIso8601 | A visszajelzési várólista maximális kézbesítési száma | ISO_8601 5 és 300 másodperc közötti időközt (legalább 5 másodpercig); alapértelmezett: 60 másodperc. |

A konfigurációs beállításokat az alábbi módokon állíthatja be:

* **Azure Portal:** Az IoT-központ **beállításai** csoportban válassza **a Beépített végpontok** lehetőséget, és bontsa ki **a Felhőt az eszközüzenet-küldésre.** (A **feedback.maxDeliveryCount** és **feedback.lockDurationAsIso8601** tulajdonságok beállítása jelenleg nem támogatott az Azure Portalon.)

    ![A felhőből az eszközre irányuló üzenetküldés konfigurációs beállításainak megadása a portálon](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: Használja az [az iot hub update](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update) parancsot:

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>További lépések

A felhőből az eszközre irányuló üzenetek fogadásához használható SDK-król az [Azure IoT SDK-k](iot-hub-devguide-sdks.md)című témakörben talál további információt.

A felhőből az eszközre irányuló üzenetek fogadásához tekintse meg a [Felhő-eszközbe](iot-hub-csharp-csharp-c2d.md) című oktatóanyagot.
