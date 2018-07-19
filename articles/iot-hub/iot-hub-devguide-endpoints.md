---
title: Megismerheti az Azure IoT Hub-végpontok |} A Microsoft Docs
description: Fejlesztői útmutató – az IoT Hub információk rendelkező eszköz és szolgáltatás felé néző végpontok.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: bf23046b8a80b02bc1667f647cb1d475503a8feb
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125776"
---
# <a name="reference---iot-hub-endpoints"></a>Referencia – IoT Hub-végpontok

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Az IoT Hub nevét

Annak az állomásneve, amelyen a végpontok a hubhoz a portálon az IoT hub a **áttekintése** lapot. Alapértelmezés szerint a DNS-neve az IoT hub néz ki: `{your iot hub name}.azure-devices.net`.

Az Azure DNS használatával hozzon létre egy egyéni DNS-nevet az IoT hub. További információt az [egyéni tartománybeállítások egy Azure-szolgáltatáshoz az Azure DNS használatával történő megadását](../dns/dns-custom-domain.md) ismertető cikkben talál.

## <a name="list-of-built-in-iot-hub-endpoints"></a>Beépített IoT Hub-végpontok listája

Az Azure IoT Hub egy több-bérlős szolgáltatás, amely a funkció különböző actors. Az alábbi ábrán látható, hogy az IoT Hub tesz a különböző végpontok.

![IoT Hub-végpontok][img-endpoints]

Az alábbi lista ismerteti a végpontok:

* **Erőforrás-szolgáltató**. Az IoT Hub erőforrás-szolgáltató tesz közzé egy [Azure Resource Manager] [ lnk-arm] felületet. Ez az interfész lehetővé teszi, hogy az Azure-előfizetéssel rendelkező létrehozására és törlésére az IoT hub és az IoT hub tulajdonságainak frissítése. Az IoT Hub tulajdonságai szabályozzák [eseményközpont-szintű biztonsági házirendek][lnk-accesscontrol], ellentétben az eszközszintű hozzáférés-vezérlés és funkcionális beállításainak üzenetküldés felhőből az eszközre és eszközről a felhőbe. Az IoT Hub erőforrás-szolgáltató lehetővé teszi, hogy [eszközidentitások exportálása][lnk-importexport].
* **Eszköz Identitáskezelés**. Minden IoT-központ megjelenít egy HTTPS-REST-végpontok eszközidentitások kezelése (létrehozása, beolvasása, frissítése és törlése). [Eszközidentitások] [ lnk-device-identities] eszköz-hitelesítés és hozzáférés-vezérlés szolgálnak.
* **Ikereszköz kezelés**. Minden IoT-központ megjelenít egy lekérdezést és a frissítési szolgáltatás által használt HTTPS REST-végpont [ikereszközök] [ lnk-twins] (címkék és tulajdonságok frissítése).
* **Felügyeleti feladatok**. Minden IoT-központ megjelenít egy szolgáltatás által használt HTTPS REST-végpont lekérdezését és kezelését [feladatok][lnk-jobs].
* **Eszköz végpontok**. Az eszközidentitás-jegyzékben lévő minden egyes eszközhöz az IoT Hub végpontok tünteti fel:

  * *Eszköz – felhő üzeneteket küldhet*. Az eszköz használja ennek a végpontnak a [eszközt a felhőbe irányuló üzenetek küldése][lnk-d2c].
  * *Felhőből az eszközre irányuló üzenetek fogadása*. Az eszköz használja ezt a végpontot fogadásához célzott [felhőből az eszközre irányuló üzenetek][lnk-c2d].
  * *Fájlfeltöltés kezdeményezése*. Az eszköz használja ezt a végpontot egy Azure Storage SAS URI-t fogadni az IoT Hub segítségével [töltsön fel egy fájlt][lnk-upload].
  * *A lekérésére és frissítésére, eszköz-ikertulajdonságok*. Egy eszköz elérésére használja ezt a végpontot a [ikereszköz][lnk-twins]a tulajdonságai.
  * *Közvetlen metódus kérések fogadására*. Eszköz ezt a végpontot használja, figyeljen a [közvetlen metódus][lnk-methods]a kérelmeket.

    Ezeket a végpontokat használatával érhetők el [MQTT v3.1.1][lnk-mqtt], HTTPS 1.1-es és [AMQP 1.0] [ lnk-amqp] protokollokat. Az AMQP is keresztül érhető el [WebSockets] [ lnk-websockets] 443-as porton.

* **Szolgáltatásvégpontokat**. Minden IoT-központ megjelenít egy végpontokat a megoldás háttérrendszerének, és az eszközök közötti kommunikációt. Egy kivétellel ezeket a végpontokat csak akkor érhetők el használatával a [AMQP] [ lnk-amqp] protokollt. A metódus meghívása végpont a HTTPS protokoll használatával van közzétéve.
  
  * *Eszköz – felhő üzeneteket fogadni*. Ez a végpont kompatibilis [Azure Event Hubs][lnk-event-hubs]. A háttérszolgáltatás segítségével, olvassa el a [eszköz – felhő üzeneteket] [ lnk-d2c] az eszközök által küldött. Az egyedi végpontok is létrehozhat az IoT hub mellett a beépített végpont.
  * *Felhőből az eszközre irányuló üzenetek küldése és fogadása kézbesítési visszaigazolások*. A végpontok engedélyezése a megoldás háttérrendszerén küldése megbízható [felhőből az eszközre irányuló üzenetek][lnk-c2d], és a megfelelő kézbesítési vagy lejárati nyugták fogadásához.
  * *Fájl értesítések fogadása*. Ezen a végponton üzenetküldési lehetővé teszi az értesítéseket kaphat, amikor az eszközök a fájl feltöltése sikeresen megtörtént. 
  * *A közvetlen metódus meghívásának*. Ez a végpont lehetővé teszi, hogy a háttérszolgáltatás meghívni egy [közvetlen metódus] [ lnk-methods] az eszközön.
  * *Műveletek figyelése események fogadásához*. Ez a végpont lehetővé teszi, hogy megkaphatja a műveleti események figyelését, ha konfigurálva van az IoT hub kibocsátható őket. További információkért lásd: [IoT Hub-műveletek monitorozása][lnk-operations-mon].

A [Azure IoT SDK-k] [ lnk-sdks] cikk elérni ezeket a végpontokat különböző módjait ismerteti.

Minden IoT Hub-végpontok használata a [TLS] [ lnk-tls] protokoll és a nem figyelt olyan végpont minden eddiginél elérhetővé titkosítatlan/titkosítatlan csatornán.

## <a name="custom-endpoints"></a>Egyéni végpontok

Meglévő Azure-szolgáltatások az előfizetésben az IoT hubhoz való működésre üzenet-útválasztása végpontjaiként kapcsolat. Ezeket a végpontokat Szolgáltatásvégpontok-kiszolgálóként, amely fogadóként üzenet útvonalak. Eszközök közvetlenül a további végpontok nem lehet írni. Üzenet útvonalakkal kapcsolatos további tudnivalókért tekintse meg a fejlesztői útmutató a [az IoT hub-üzenetek küldése és fogadása][lnk-devguide-messaging].

Az IoT Hub a következő Azure-szolgáltatások jelenleg további végpontokat támogatja:

* Az Azure Storage-tárolók
* Event Hubs
* Service Bus által kezelt üzenetsorok
* Service Bus-üzenettémák

Az IoT Hub szolgáltatás a végpontokkal való írási hozzáférés van szüksége üzenet-útválasztása működjön. Ha a konfigurálja a végpontokat az Azure Portalon keresztül, a szükséges engedélyekkel meg lesz hozzáadva. Ellenőrizze, hogy konfigurálja a szolgáltatások, a várt teljesítményről támogatásához. Amikor először konfigurálja az IoT-megoldás, szükség lehet a további végpontok monitorozása és a tényleges betöltést a szükséges módosításokat.

Ha egy üzenet egyezést több irányítja, hogy az összes mutasson egyazon végpont, az IoT Hub továbbítja az üzenet, hogy a végpont csak egyszer. Így nem kell a deduplikáció konfigurálása a Service Bus-üzenetsor vagy témakör. A particionált üzenetsorok a partíció affinitás üzenetrendezés garantálja.

Hozzáadhat végpontok számának korlátozásairól lásd: [kvóták és szabályozás][lnk-devguide-quotas].

### <a name="when-using-azure-storage-containers"></a>Az Azure Storage-tárolók használata esetén

IoT Hub által támogatott csak, írja az adatokat az Azure Storage-tárolók, blobok a [Apache Avro](http://avro.apache.org/) formátumban. Az IoT Hub kötegeli az üzeneteket, és abba adatokat író egy blobot, ha:

* A köteg bizonyos méretet elér.
* Vagy egy bizonyos mennyi idő telt el.

Az IoT Hub üres blob fog írni, ha ott nem szerepel megjeleníthető adat írni.

Az IoT Hub az alapértelmezett fájl alábbi elnevezési szabályt követik:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Használhat bármilyen fájlelnevezési, szeretné, azonban kell használnia minden felsorolt jogkivonatokat.

### <a name="when-using-service-bus-queues-and-topics"></a>Service Bus-üzenetsorok és témakörök használata esetén

Service Bus-üzenetsorok és témakörök használt IoT Hub-végpontok nesmí mít **munkamenetek** vagy **duplikáltelem-észlelési** engedélyezve van. Ha ezek a lehetőségek valamelyikét engedélyezve vannak, a végpont megjelenik **nem elérhető** az Azure Portalon.

## <a name="field-gateways"></a>Helyszíni átjárók

IoT-megoldások egy *helyszíni átjáró* az eszközök és az IoT Hub-végpontok között helyezkedik el. Általában megtalálható, az eszközök közelében. Az eszközök közvetlenül a helyszíni átjáró használatával kommunikálnak az eszközök által támogatott protokoll. A helyszíni átjáró csatlakozik az IoT Hub-végponton egy IoT Hub által támogatott protokoll használatával. Helyszíni átjáró egy dedikált hardvereszköz, vagy az alacsony egyéni gateway szoftvert futtató számítógép lehet.

Használhat [Azure IoT Edge] [ lnk-iot-edge] helyszíni átjáró megvalósításához. IoT Edge funkciók, például több eszközről az IoT Hub ugyanazt a kapcsolatot az alakzatot multiplex kommunikációt biztosít.

## <a name="next-steps"></a>További lépések

Ez az IoT Hub fejlesztői útmutató más referencia témaköröket tartalmazza:

* [Az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása][lnk-devguide-query]
* [Kvóták és szabályozás][lnk-devguide-quotas]
* [IoT Hub MQTT-támogatás][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md
