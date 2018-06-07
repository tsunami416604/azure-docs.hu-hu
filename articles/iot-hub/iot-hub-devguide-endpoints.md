---
title: Azure IoT-központok végpontjai megértése |} Microsoft Docs
description: Fejlesztői útmutató - információk az IoT-központ eszköz számára is elérhető, és a szolgáltatás felé néző végpontok.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 37e1bd0b479e27f0d871495c0725fc865cbb8572
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632577"
---
# <a name="reference---iot-hub-endpoints"></a>Referencia - IoT-központok végpontjai

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Az IoT-központ nevét

A neve, amelyen a végpontokat a portálon az IoT-központ megtalálhatja a **áttekintése** panelen. Alapértelmezés szerint a DNS-neve az IoT-központ néz: `{your iot hub name}.azure-devices.net`.

Azure DNS használatával hozzon létre egy egyéni DNS-nevet az IoT hub. További információkért lásd: [használata Azure DNS számára adja meg az egyéni tartomány beállításait az Azure-szolgáltatások](../dns/dns-custom-domain.md).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Beépített IoT-központok végpontjai listája

Az Azure IoT Hub egy több-bérlős szolgáltatást, amely elérhetővé teszi a funkció különböző szereplője. Az alábbi ábrán látható, hogy az IoT-központ elérhetővé teszi a különböző végpontok.

![IoT Hub-végpontok][img-endpoints]

Az alábbi lista ismerteti a végpontok:

* **Erőforrás-szolgáltató**. Az IoT-központ erőforrás-szolgáltató közzétesz egy [Azure Resource Manager] [ lnk-arm] felületet. Ez az interfész lehetővé teszi, hogy az Azure-előfizetések tulajdonosai létrehozására és törlésére az IoT-központok, és az IoT hub tulajdonságainak módosítása. Az IoT Hub tulajdonságainak szabályozására [hub szintű biztonsági házirendek][lnk-accesscontrol], eszközszintű hozzáférés-vezérlés, és működési beállításainak felhő eszköz és eszköz-felhő üzenetküldési szemben. Az IoT-központ erőforrás-szolgáltató azt is lehetővé teszi, hogy [eszköz identitások exportálása][lnk-importexport].
* **Eszköz Identitáskezelés**. Minden egyes IoT-központ tesz elérhetővé a HTTPS REST-végpontok eszköz identitások kezeléséhez (létrehozása, beolvasása, frissítése és törlése). [Eszköz identitások] [ lnk-device-identities] eszköz hitelesítési és hozzáférés-vezérlés használhatók.
* **A két kezelés**. Minden egyes IoT-központ tesz elérhetővé, lekérdezés és a frissítési szolgáltatás irányuló HTTPS REST-végpont [eszköz twins] [ lnk-twins] (címkék és a tulajdonságok frissítése).
* **Felügyeleti feladatok**. Minden egyes IoT-központ tesz elérhetővé, lekérdezését és kezelését szolgáltatás irányuló HTTPS REST-végpont [feladatok][lnk-jobs].
* **Eszköz végpontok**. Az egyes eszközök a identitás beállításjegyzék az IoT-központ tesz elérhetővé, végpontok:

  * *Eszköz-felhő üzenetek küldéséhez*. Egy eszköz használja ezt a végpontot a [eszközről a felhőbe üzenetek küldéséhez][lnk-d2c].
  * *Felhő-eszközre küldött üzenetek fogadására*. Egy eszköz használja ehhez a végponthoz fogadásához célzott [felhő-eszközre küldött üzenetek][lnk-c2d].
  * *Fájlfeltöltési kezdeményezése*. Egy eszköz használja ezt a végpontot egy Azure Storage SAS URI-t fogadni az IoT-központ [-fájl feltöltése][lnk-upload].
  * *A lekérésére és frissítésére iker eszköztulajdonságok*. Egy eszköz elérésére használja ezt a végpontot a [eszköz iker][lnk-twins]tartozó tulajdonságok.
  * *Közvetlen módszer kérések fogadásához*. Egy eszköz használja ehhez a végponthoz tartozó [közvetlen módszer][lnk-methods]a kérelmeket.

    Ezeket a végpontokat feltárt használatával [MQTT v3.1.1][lnk-mqtt], HTTPS 1.1-es és [AMQP 1.0] [ lnk-amqp] protokollokat. AMQP is rendelkezésre áll, keresztül [websocket elemek] [ lnk-websockets] a 443-as porton.

* **Szolgáltatás végpontjait**. Minden egyes IoT-központ a megoldás háttérrendszeréhez kommunikálnak az eszközök végpontjainak tesz elérhetővé. Egy kivétellel ezeket a végpontokat csak akkor jelennek meg használatával a [AMQP] [ lnk-amqp] protokoll. A metódus meghívása végpont a HTTPS protokollon keresztül kommunikál.
  
  * *Eszköz-felhő üzeneteket fogadni*. Ez a végpont összeegyeztethető [Azure Event Hubs][lnk-event-hubs]. A háttér-szolgáltatás segítségével, olvassa el a [eszköz a felhőbe küldött üzeneteket] [ lnk-d2c] az eszközök által küldött. Egyéni végpontokat létrehozhat az IoT hub mellett a beépített végpont.
  * *Felhő-eszközre küldött üzenetek küldésére és fogadására a kézbesítési visszaigazolások*. Ezeket a végpontokat engedélyezése a megoldás háttérrendszeréhez küldése megbízható [felhő-eszközre küldött üzenetek][lnk-c2d], és a megfelelő kézbesítési vagy lejárati nyugták fogadásához.
  * *Fájl értesítéseket*. Az üzenetkezelési végpont értesítést kapjon, ha az eszközök a fájl feltöltése sikeresen teszi lehetővé. 
  * *A metódushívás közvetlen*. Ehhez a végponthoz, lehetővé teszi egy háttér-szolgáltatás meghívni egy [közvetlen módszer] [ lnk-methods] az eszközön.
  * *Fogadási műveletek események figyelése*. Ehhez a végponthoz lehetővé teszi a fogadási műveletek események figyelése, ha konfigurálva van az IoT hub hozható létre őket. További információkért lásd: [IoT-központ műveletek figyelési][lnk-operations-mon].

A [Azure IoT SDK-k] [ lnk-sdks] a cikk ismerteti a különböző módszereket ezeket a végpontokat eléréséhez.

Minden IoT-központok végpontjai használja a [TLS] [ lnk-tls] protokoll, és nincs végpont legalább egyszer fel van fedve titkosítatlanul/titkosítatlan csatornán.

## <a name="custom-endpoints"></a>Egyéni végpontok

Az előfizetésében szereplő meglévő Azure-szolgáltatások társíthatja az IoT hub üzenet irányításához végpontok nevében járhasson el. Ezeket a végpontokat Szolgáltatásvégpontok összekötőként és üzenet útvonalak mosdók használják. Eszközök közvetlenül a további végpontok nem írható. Üzenet útvonalakkal kapcsolatban további tudnivalókért tekintse meg a fejlesztői útmutató bejegyzés [IoT hubbal üzenetek küldése és fogadása][lnk-devguide-messaging].

Az IoT-központ jelenleg a további végpontként támogatja az Azure-szolgáltatásokat:

* Az Azure Storage tárolók
* Event Hubs
* Service Bus által kezelt üzenetsorok
* Service Bus-üzenettémák

Az IoT-központ üzenet irányításához működéséhez ezeket Szolgáltatásvégpontok írási engedéllyel kell rendelkeznie. Konfigurálja a végpontokat az Azure portálon keresztül, ha a szükséges engedélyek hozzáadásakor meg. Ügyeljen arra, hogy a szolgáltatásokat, hogy a várt átviteli támogatja-e. Amikor először konfigurálja az IoT-megoldásból, szükség lehet a figyelheti a további végpontokat, majd végezze el a szükséges módosításokat a tényleges betöltést az.

Ha egy üzenet megfelel több irányítja, hogy az összes mutasson a azonos végpont, IoT-központ kézbesíti üzenetet az adott végpontra csak egyszer. Emiatt nem kell a deduplikáció konfigurálása a Service Bus-üzenetsorba vagy témakör. A particionált várólisták partíció affinitás biztosítja, hogy az üzenet rendezés.

Hozzáadhat végpontok számának használati korlátait, lásd: [kvóták és sávszélesség-szabályozási][lnk-devguide-quotas].

### <a name="when-using-azure-storage-containers"></a>Azure Storage tárolók használata

Az IoT-központ csak támogatja az írás a Azure-tárolókban lévő blobokat, a [Apache Avro](http://avro.apache.org/) formátumban. Az IoT-központ kötegek üzeneteket, és írja az adatokat a blob amikor:

* A kötegelt elér egy adott méretet.
* Vagy egy bizonyos idő eltelte.

Az IoT-központ üres blob fognak írni, ha nincsenek adatok írása.

Az IoT-központ alapértelmezett értéke a következő fájl elnevezési konvenció:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Használhat bármilyen fájlelnevezési, kívánja, azonban kell használnia minden felsorolt jogkivonatokat.

### <a name="when-using-service-bus-queues-and-topics"></a>Service Bus-üzenetsorok és témakörök használata esetén

Service Bus-üzenetsorok és témakörök használatos az IoT-központok végpontjai nem lehet **munkamenetek** vagy **ismétlődő észlelési** engedélyezve van. Ha ezek a lehetőségek valamelyikét engedélyezve vannak, a végpont megjelenik **Unreachable** az Azure portálon.

## <a name="field-gateways"></a>A mező átjárók

Az IoT-megoldás egy *mező átjáró* között az eszközök és az IoT-központok végpontjai helyezkedik el. Általában megtalálható, az eszközök közelében. Az eszközök közvetlenül a mező átjáró használatával kommunikálnak az eszközök által támogatott protokoll. A mező átjáró csatlakozik az IoT-központ végpontjának IoT-központ által támogatott protokoll használatát. Egy mező átjáró egy dedikált hardvereszköz, vagy az alacsony futtató egyéni átjáró szoftverének lehet.

Használhat [Azure IoT peremhálózati] [ lnk-iot-edge] mező átjáró végrehajtásához. IoT biztonsági funkciók, például multiplex folytatott kommunikáció több eszközön, ugyanazt az IoT-központ kapcsolatot biztosít.

## <a name="next-steps"></a>További lépések

Az IoT Hub fejlesztői útmutató egyéb témaköröket tartalmazza:

* [Az IoT-központ lekérdezési nyelv eszköz twins, a feladatok és az üzenet-útválasztás][lnk-devguide-query]
* [Kvóták és sávszélesség-szabályozás][lnk-devguide-quotas]
* [Az IoT Hub MQTT támogatása][lnk-devguide-mqtt]

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
