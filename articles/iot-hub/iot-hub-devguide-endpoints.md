---
title: Az Azure IoT Hub végpontjainak megismerése | Microsoft dokumentumok
description: Fejlesztői útmutató – az IoT Hub-eszköz- és szolgáltatásnéző végpontokra vonatkozó referenciainformációk.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: da6d17e42407048b7ecbcacade67ef48046d7fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284641"
---
# <a name="reference---iot-hub-endpoints"></a>Referencia – IoT Hub-végpontok

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub-nevek

A végpontokat fogadó IoT hub állomásnevét megtalálhatja a portálon a hub **áttekintése** lapon. Alapértelmezés szerint az IoT hub DNS-neve `{your iot hub name}.azure-devices.net`így néz ki: .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Beépített IoT Hub-végpontok listája

Az Azure IoT Hub egy több-bérlős szolgáltatás, amely különböző szereplők számára elérhetővé teszi a funkciókat. Az alábbi ábrán az IoT Hub által elérhetővé tett különböző végpontok láthatók.

![IoT Hub-végpontok](./media/iot-hub-devguide-endpoints/endpoints.png)

Az alábbi lista a végpontokat ismerteti:

* **Erőforrás-szolgáltató**. Az IoT Hub erőforrás-szolgáltató elérhetővé teszi az [Azure Resource Manager](../azure-resource-manager/management/overview.md) felületét. Ez a felület lehetővé teszi az Azure-előfizetés-tulajdonosok számára, hogy iot-központokat hozzanak létre és töröljenek, és frissítsék az IoT hub tulajdonságait. Az IoT Hub tulajdonságai szabályozzák [a központi szintű biztonsági házirendeket,](iot-hub-devguide-security.md#access-control-and-permissions)szemben az eszközszintű hozzáférés-vezérléssel, valamint a felhőből az eszközre és az eszközről a felhőbe irányuló üzenetküldés funkcionális beállításaira. Az IoT Hub-erőforrás-szolgáltató lehetővé teszi [az eszközidentitások exportálását](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)is.

* **Eszközidentitás-kezelés**. Minden IoT hub https REST-végpontok készletét teszi elérhetővé az eszközidentitások (létrehozása, lekérése, frissítése és törlése) kezeléséhez. [Az eszközidentitások](iot-hub-devguide-identity-registry.md) eszközhitelesítéshez és hozzáférés-vezérléshez használatosak.

* **Az ikereszköz kezelése**. Minden IOt-központ elérhetővé teszi a szolgáltatással kapcsolatos HTTPS REST-végpontegy sorát [az eszköztwins](iot-hub-devguide-device-twins.md) lekérdezéséhez és frissítéséhez (címkék és tulajdonságok frissítése).

* **Munkahelyek kezelése**. Minden IoT-központ elérhetővé teszi a szolgáltatással kapcsolatos HTTPS REST-végpontkészletet [a feladatok](iot-hub-devguide-jobs.md)lekérdezéséhez és kezeléséhez.

* **Az eszköz végpontjai**. Az IoT Hub az identitásjegyzékben szereplő valamennyi eszközhöz elérhetővé tesz néhány végpontot:

  * *Eszközről felhőbe irányuló üzenetek küldése*. Az eszköz ezt a végpontot használja [az eszközről a felhőbe irányuló üzenetek küldésére.](iot-hub-devguide-messages-d2c.md)

  * *Felhőből az eszközre*irányuló üzenetek fogadása . Az eszköz ezt a végpontot használja [célzott, felhőből az eszközre irányuló üzenetek fogadására.](iot-hub-devguide-messages-c2d.md)

  * *Fájlfeltöltések kezdeményezése.* Egy eszköz ezt a végpontot használja egy Azure Storage SAS URI-nak az IoT Hubtól [egy fájl feltöltéséhez.](iot-hub-devguide-file-upload.md)

  * *Az ikereszköz tulajdonságainak beolvasása és frissítése*. Az eszköz ezt a végpontot használja az [ikereszköz](iot-hub-devguide-device-twins.md)tulajdonságainak eléréséhez.

  * *Közvetlen metóduskérések fogadása*. Egy eszköz ezt a végpontot használja a [közvetlen metódus](iot-hub-devguide-direct-methods.md)kéréseifigyeléséhez.

    Ezek a végpontok [mqtt v3.1.1,](https://mqtt.org/)HTTPS 1.1 és [AMQP 1.0](https://www.amqp.org/) protokollok használatával vannak elérhetővé téve. Az AMQP a [443-as](https://tools.ietf.org/html/rfc6455) porton a WebSockets-en keresztül is elérhető.

* **Szolgáltatásvégpontok**. Minden IOt hub elérhetővé teszi a végpontok a megoldás háttér-az eszközökkel való kommunikációhoz. Egy kivétellel ezek a végpontok csak az [AMQP](https://www.amqp.org/) protokoll használatával vannak elérhetővé téve. A metódus meghívási végpontja https protokollon keresztül érhető el.
  
  * *Eszközről a felhőbe irányuló üzenetek fogadása*. Ez a végpont kompatibilis az [Azure Event Hubs szolgáltatással.](https://azure.microsoft.com/documentation/services/event-hubs/) A háttérszolgáltatás segítségével olvashatja az eszközök által küldött [eszközök közötti üzeneteket.](iot-hub-devguide-messages-d2c.md) Ehhez a beépített végponthoz is létrehozhat egyéni végpontokat az IoT-központon.
  
  * *Felhőből az eszközre irányuló üzenetek küldése és kézbesítési nyugták fogadása.* Ezek a végpontok lehetővé teszik, hogy a megoldás háttérrendszeréből megbízható [felhőből az eszközre](iot-hub-devguide-messages-c2d.md)irányuló üzeneteket küldjön, és megkapja a megfelelő kézbesítési vagy lejárati nyugtákat.
  
  * *Fájlértesítések fogadása*. Ez az üzenetkezelési végpont lehetővé teszi, hogy értesítéseket kapjon abról, ha az eszközök sikeresen feltöltik a fájlt. 
  
  * *Közvetlen metódus-meghívás*. Ez a végpont lehetővé teszi, hogy egy háttérszolgáltatás [közvetlen metódust](iot-hub-devguide-direct-methods.md) hívjon meg egy eszközön.
  
  * *Műveletek figyelési eseményeinek fogadása*. Ez a végpont lehetővé teszi, hogy a műveletek figyelése eseményeket, ha az IoT hub van konfigurálva, hogy kifejezze őket. További információ: [IoT Hub-műveletek figyelése.](iot-hub-operations-monitoring.md)

Az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) cikk ismerteti a végpontok elérésének különböző módjait.

Minden IoT Hub-végpont a [TLS](https://tools.ietf.org/html/rfc5246) protokollt használja, és egyetlen végpont sem érhető el titkosítatlan/nem biztonságos csatornákon.

## <a name="custom-endpoints"></a>Egyéni végpontok

Az előfizetésben lévő meglévő Azure-szolgáltatások összekapcsolhatók az IoT-központtal, hogy az üzenet-útválasztás végpontjaiként működjenek. Ezek a végpontok szolgáltatásvégpontként működnek, és az üzenetútvonalak fogadóiként használatosak. Az eszközök nem írhatnak közvetlenül a további végpontokra. További információ az [üzenetek útválasztásáról.](../iot-hub/iot-hub-devguide-messages-d2c.md)

Az IoT Hub jelenleg a következő Azure-szolgáltatásokat támogatja további végpontként:

* Azure Storage-tárolók
* Event Hubs
* Service Bus által kezelt üzenetsorok
* Service Bus-üzenettémák

A hozzáadható végpontok számának korlátait a [Kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md)című témakörben tésiterületen.

A REST API [Végpontok begetése állapot](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) a végpontok állapotának lehívásához használhatja. Azt javasoljuk, hogy az [IoT Hub metrikák](iot-hub-metrics.md) az útválasztási üzenet késése az útválasztási üzenet késése azonosításához és hibakereséshibák azonosításához és hibakereséséhez, ha a végpont állapota halott vagy nem kifogástalan, mivel azt várjuk, hogy a késés magasabb legyen, ha a végpont az egyik ilyen állapotban van.

|Állapotadatok|Leírás|
|---|---|
|Egészséges|A végpont a várt módon fogadja az üzeneteket.|
|Egészségtelen|A végpont nem fogadja el az üzeneteket a várt módon, és az IoT Hub újra megpróbálja elküldeni az adatokat erre a végpontra. Egy nem megfelelő állapotú végpont állapota kifogástalan állapotú lesz, ha az IoT Hub létrehozott egy végül konzisztens állapot.|
|Ismeretlen|Az IoT Hub nem hozott létre kapcsolatot a végpont. Erről a végpontról nem érkezett üzenet, illetve nem lett elutasítva.|
|Halott|A végpont nem fogad üzeneteket, miután az IoT Hub újra próbálkozott az újratárgyalási időszakra vonatkozó üzenetek küldésével.|

## <a name="field-gateways"></a>Helyszíni átjárók

Egy IoT-megoldásban egy *helyszíni átjáró* az eszközök és az IoT Hub-végpontok között helyezkedik el. Általában az eszközök közelében található. Az eszközök közvetlenül kommunikálnak a helyszíni átjáróval az eszközök által támogatott protokoll használatával. A helyszíni átjáró egy IoT Hub-végponthoz csatlakozik az IoT Hub által támogatott protokoll használatával. A helyszíni átjáró lehet dedikált hardvereszköz vagy egyéni átjárószoftvert futtató alacsony fogyasztású számítógép.

[Az Azure IoT Edge](/azure/iot-edge/) segítségével egy helyszíni átjáró implementálható. Az IoT Edge olyan funkciókat kínál, mint például a több eszközről érkező kommunikáció multiplexelése ugyanarra az IoT Hub-kapcsolatra.

## <a name="next-steps"></a>További lépések

Az IoT Hub fejlesztői útmutatójának további referenciatémakörei a következők:

* [Az IoT Hub lekérdezési nyelve az ikereszközök, feladatok és az üzenetek útválasztása](iot-hub-devguide-query-language.md)
* [Kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md)
* [Az IoT Hub MQTT támogatása](iot-hub-mqtt-support.md)
* [Az IoT hub IP-címének megismerése](iot-hub-understand-ip-address.md)