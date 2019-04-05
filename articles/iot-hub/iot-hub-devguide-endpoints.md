---
title: Megismerheti az Azure IoT Hub-végpontok |} A Microsoft Docs
description: Fejlesztői útmutató – az IoT Hub információk rendelkező eszköz és szolgáltatás felé néző végpontok.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: 5015068f9b165190bef3b0cb97ddb194e173303e
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045917"
---
# <a name="reference---iot-hub-endpoints"></a>Referencia – IoT Hub-végpontok

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub names

Annak az állomásneve, amelyen a végpontok a hubhoz a portálon az IoT hub a **áttekintése** lapot. Alapértelmezés szerint a DNS-neve az IoT hub néz ki: `{your iot hub name}.azure-devices.net`.

## <a name="list-of-built-in-iot-hub-endpoints"></a>Beépített IoT Hub-végpontok listája

Az Azure IoT Hub egy több-bérlős szolgáltatás, amely a funkció különböző actors. Az alábbi ábrán látható, hogy az IoT Hub tesz a különböző végpontok.

![IoT Hub-végpontok](./media/iot-hub-devguide-endpoints/endpoints.png)

Az alábbi lista ismerteti a végpontok:

* **Erőforrás-szolgáltató**. Az IoT Hub erőforrás-szolgáltató tesz közzé egy [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) felületet. Ez az interfész lehetővé teszi, hogy az Azure-előfizetéssel rendelkező létrehozására és törlésére az IoT hub és az IoT hub tulajdonságainak frissítése. Az IoT Hub tulajdonságai szabályozzák [eseményközpont-szintű biztonsági házirendek](iot-hub-devguide-security.md#access-control-and-permissions), ellentétben az eszközszintű hozzáférés-vezérlés és funkcionális beállításainak üzenetküldés felhőből az eszközre és eszközről a felhőbe. Az IoT Hub erőforrás-szolgáltató lehetővé teszi, hogy [eszközidentitások exportálása](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Eszköz Identitáskezelés**. Minden IoT-központ megjelenít egy HTTPS-REST-végpontok eszközidentitások kezelése (létrehozása, beolvasása, frissítése és törlése). [Eszközidentitások](iot-hub-devguide-identity-registry.md) eszköz-hitelesítés és hozzáférés-vezérlés szolgálnak.

* **Ikereszköz kezelés**. Minden IoT-központ megjelenít egy lekérdezést és a frissítési szolgáltatás által használt HTTPS REST-végpont [ikereszközök](iot-hub-devguide-device-twins.md) (címkék és tulajdonságok frissítése).

* **Felügyeleti feladatok**. Minden IoT-központ megjelenít egy szolgáltatás által használt HTTPS REST-végpont lekérdezését és kezelését [feladatok](iot-hub-devguide-jobs.md).

* **Eszköz végpontok**. Az eszközidentitás-jegyzékben lévő minden egyes eszközhöz az IoT Hub végpontok tünteti fel:

  * *Eszköz – felhő üzeneteket küldhet*. Az eszköz használja ennek a végpontnak a [eszközt a felhőbe irányuló üzenetek küldése](iot-hub-devguide-messages-d2c.md).

  * *Felhőből az eszközre irányuló üzenetek fogadása*. Az eszköz használja ezt a végpontot fogadásához célzott [felhőből az eszközre irányuló üzenetek](iot-hub-devguide-messages-c2d.md).

  * *Fájlfeltöltés kezdeményezése*. Az eszköz használja ezt a végpontot egy Azure Storage SAS URI-t fogadni az IoT Hub segítségével [töltsön fel egy fájlt](iot-hub-devguide-file-upload.md).

  * *A lekérésére és frissítésére, eszköz-ikertulajdonságok*. Egy eszköz elérésére használja ezt a végpontot a [ikereszköz](iot-hub-devguide-device-twins.md)a tulajdonságai.

  * *Közvetlen metódus kérések fogadására*. Eszköz ezt a végpontot használja, figyeljen a [közvetlen metódus](iot-hub-devguide-direct-methods.md)a kérelmeket.

    Ezeket a végpontokat használatával érhetők el [MQTT v3.1.1](https://mqtt.org/), HTTPS 1.1-es és [AMQP 1.0](https://www.amqp.org/) protokollokat. Az AMQP is keresztül érhető el [WebSockets](https://tools.ietf.org/html/rfc6455) 443-as porton.

* **Szolgáltatásvégpontokat**. Minden IoT-központ megjelenít egy végpontokat a megoldás háttérrendszerének, és az eszközök közötti kommunikációt. Egy kivétellel ezeket a végpontokat csak akkor érhetők el használatával a [AMQP](https://www.amqp.org/) protokollt. A metódus meghívása végpont a HTTPS protokoll használatával van közzétéve.
  
  * *Eszköz – felhő üzeneteket fogadni*. Ez a végpont kompatibilis [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). A háttérszolgáltatás segítségével, olvassa el a [eszköz – felhő üzeneteket](iot-hub-devguide-messages-d2c.md) az eszközök által küldött. Az egyedi végpontok is létrehozhat az IoT hub mellett a beépített végpont.
  
  * *Felhőből az eszközre irányuló üzenetek küldése és fogadása kézbesítési visszaigazolások*. A végpontok engedélyezése a megoldás háttérrendszerén küldése megbízható [felhőből az eszközre irányuló üzenetek](iot-hub-devguide-messages-c2d.md), és a megfelelő kézbesítési vagy lejárati nyugták fogadásához.
  
  * *Fájl értesítések fogadása*. Ezen a végponton üzenetküldési lehetővé teszi az értesítéseket kaphat, amikor az eszközök a fájl feltöltése sikeresen megtörtént. 
  
  * *A közvetlen metódus meghívásának*. Ez a végpont lehetővé teszi, hogy a háttérszolgáltatás meghívni egy [közvetlen metódus](iot-hub-devguide-direct-methods.md) az eszközön.
  
  * *Műveletek figyelése események fogadásához*. Ez a végpont lehetővé teszi, hogy megkaphatja a műveleti események figyelését, ha konfigurálva van az IoT hub kibocsátható őket. További információkért lásd: [IoT Hub-műveletek monitorozása](iot-hub-operations-monitoring.md).

A [Azure IoT SDK-k](iot-hub-devguide-sdks.md) cikk elérni ezeket a végpontokat különböző módjait ismerteti.

Minden IoT Hub-végpontok használata a [TLS](https://tools.ietf.org/html/rfc5246) protokoll és a nem figyelt olyan végpont minden eddiginél elérhetővé titkosítatlan/titkosítatlan csatornán.

## <a name="custom-endpoints"></a>Egyéni végpontok

Meglévő Azure-szolgáltatások az előfizetésben az IoT hubhoz való működésre üzenet-útválasztása végpontjaiként kapcsolat. Ezeket a végpontokat Szolgáltatásvégpontok-kiszolgálóként, amely fogadóként üzenet útvonalak. Eszközök közvetlenül a további végpontok nem lehet írni. Tudjon meg többet [üzenet-útválasztása](../iot-hub/iot-hub-devguide-messages-d2c.md).

Az IoT Hub a következő Azure-szolgáltatások jelenleg további végpontokat támogatja:

* Azure Storage-tárolók
* Event Hubs
* Service Bus által kezelt üzenetsorok
* Service Bus-üzenettémák

Hozzáadhat végpontok számának korlátozásairól lásd: [kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md).

A REST API-hoz [végpont állapotának lekérése](https://docs.microsoft.com/de-de/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) beolvasni a végpontok állapotát. Javasoljuk, hogy használja a [az IoT Hub-metrikák](iot-hub-metrics.md) útválasztási üzenet késése alapján azonosíthatja és elháríthatja a hibákat, amikor végpontonkénti állapotot kézbesíthetetlen vagy nem megfelelő állapotú kapcsolódó.

|Állapotadatok|Leírás|
|---|---|
|kifogástalan|A végpont már fogad üzeneteket, várt módon.|
|Nem megfelelő állapotú|A végpont nem fogad üzeneteket várt módon, és az IoT Hub újból megkísérli a adatokat lehet küldeni ennek a végpontnak. Nem megfelelő állapotú végpont állapota kifogástalanra frissül, az IoT Hub állapotának egy végül konzisztens állapotba létrejöttekor.|
|ismeretlen|Az IoT Hub nem létrehozott egy kapcsolatot a végponthoz. Nincsenek üzenetek érkeznek, vagy elutasították erről a végpontról.|
|kézbesítetlen levelek|A végpont nem fogad üzeneteket, az IoT Hub újrapróbált üzenetek küldése az retrial időszak után.|

## <a name="field-gateways"></a>Helyszíni átjárók

IoT-megoldások egy *helyszíni átjáró* az eszközök és az IoT Hub-végpontok között helyezkedik el. Általában megtalálható, az eszközök közelében. Az eszközök közvetlenül a helyszíni átjáró használatával kommunikálnak az eszközök által támogatott protokoll. A helyszíni átjáró csatlakozik az IoT Hub-végponton egy IoT Hub által támogatott protokoll használatával. Helyszíni átjáró egy dedikált hardvereszköz, vagy az alacsony egyéni gateway szoftvert futtató számítógép lehet.

Használhat [Azure IoT Edge](/azure/iot-edge/) helyszíni átjáró megvalósításához. IoT Edge funkciók, például több eszközről az IoT Hub ugyanazt a kapcsolatot az alakzatot multiplex kommunikációt biztosít.

## <a name="next-steps"></a>További lépések

Ez az IoT Hub fejlesztői útmutató más referencia témaköröket tartalmazza:

* [Az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása](iot-hub-devguide-query-language.md)
* [Kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md)
