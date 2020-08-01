---
title: IoT Plug and Play architektúra | Microsoft Docs
description: Megoldás-szerkesztőként megismerheti a IoT Plug and Play legfontosabb építészeti elemeit.
author: ridomin
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f656de0bb2e5244e137ae21a6d7af88f3430b12c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475685"
---
# <a name="iot-plug-and-play-preview-architecture"></a>Az IoT Plug and Play előzetes verziójának architektúrája

A IoT Plug and Play előzetes verziója lehetővé teszi a megoldás-építők számára, hogy manuális konfiguráció nélkül integrálják az intelligens eszközöket a megoldásaikkal. A IoT Plug and Play egy olyan eszköz _modellje_ , amely leírja az eszköz képességeit egy IoT Plug and Play-kompatibilis alkalmazás számára. Ez a modell illesztőfelület-készletként van strukturálva, amely az alábbiakat határozza meg:

- Egy eszköz vagy más entitás írásvédett vagy írható állapotát jelképező _Tulajdonságok_ . Előfordulhat például, hogy egy eszköz sorozatszáma csak olvasható tulajdonság, és a termosztát hőmérséklete írható tulajdonság lehet.
- _Telemetria_ az eszköz által kibocsátott adatok, függetlenül attól, hogy az adatok az érzékelők normál streamje, egy alkalmi hiba vagy egy tájékoztató üzenet.
- Egy eszközön elvégezhető függvényt vagy műveletet leíró _parancsok_ . Egy parancs például újraindíthatja az átjárót, vagy egy távoli kamera használatával is készíthet képet.

Minden modell és felület egyedi AZONOSÍTÓval rendelkezik.

Az alábbi ábrán egy IoT Plug and Play megoldás fő elemei láthatók:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT Plug and Play architektúra":::

## <a name="model-repository"></a>Modelladattár

A [Model adattár](./concepts-model-repository.md) a modell-és illesztőfelület-definíciók tárolója. A modelleket és a csatolókat a [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)használatával határozhatja meg.

A webes felhasználói felület lehetővé teszi a modellek és felületek kezelését.

A Model repository a RBAC használatával korlátozza az illesztőfelület-definíciók elérését.

## <a name="devices"></a>Eszközök

Egy IoT intelligens eszközön a kód egy [Azure IoT-eszköz SDK](./libraries-sdks.md)-k használatával történő futtatását valósítja meg. Az eszköz SDK-k segítik az Eszközkezelőt a következőknek:

- Biztonságos kapcsolódás egy IoT hubhoz.
- Regisztrálja az eszközt az IoT hub-ban, és jelentse be az eszköz által megvalósított felületek gyűjteményét azonosító modell AZONOSÍTÓját.
- Frissítse az eszköz által megvalósított DTDL-illesztőfelületekben definiált tulajdonságokat. Ezek a tulajdonságok olyan digitális ikrek használatával valósulnak meg, amelyek kezelik a szinkronizálást az IoT hubhoz.
- Az eszköz által megvalósított DTDL-adapterekben definiált parancsokhoz tartozó parancs-kezelők hozzáadása.
- Telemetria küldése az IoT hub-nak.

## <a name="iot-hub"></a>IoT Hub

[IoT hub](../iot-hub/about-iot-hub.md) egy felhőalapú szolgáltatás, amely központi üzenetsorként működik a IoT-megoldás és az általa felügyelt eszközök közötti kétirányú kommunikációhoz.

Egy IoT hub:

- Egy eszköz által megvalósított modell AZONOSÍTÓját a háttérrendszer számára elérhetővé teszi.
- Karbantartja a központhoz csatlakoztatott Plug and Play-eszközökhöz társított digitális IKeret.
- Továbbítja a telemetria streameket más szolgáltatásoknak feldolgozásra vagy tárolásra.
- Átirányítja a digitális kettős változási eseményeket más szolgáltatásoknak az eszközök figyelésének engedélyezéséhez.

## <a name="backend-solution"></a>Háttérrendszer-megoldás

A háttérrendszer-megoldás a csatlakoztatott eszközöket figyeli és szabályozza a IoT hub digitális ikrekkel való interakciója révén. Használja az egyik Service SDK-t a háttérbeli megoldás megvalósításához. A csatlakoztatott eszközök képességeinek megismeréséhez a megoldás háttere:

1. Az IoT hub-ban regisztrált eszköz AZONOSÍTÓjának beolvasása.
1. A modell AZONOSÍTÓját használja az illesztőfelület-definíciók beolvasásához bármely modell adattárból.
1. A Model Parser használatával kinyeri az adatokat az illesztőfelület-definíciók közül.

A háttérrendszer-megoldás az illesztőfelület-definíciók adatait a következőre használhatja:

- Az eszközök által jelentett tulajdonságértékek olvasása.
- Eszközön lévő írható tulajdonságok frissítése.
- Egy eszköz által megvalósított parancsok hívása.
- Az eszköz által eljuttatott telemetria formátumának megismerése.

## <a name="next-steps"></a>További lépések

Most, hogy áttekinti a IoT Plug and Play megoldás architektúráját, a következő lépésekkel többet is megtudhat a következőkről:

- [A modell tárháza](./concepts-model-repository.md)
- [Digitális kettős modell integrációja](./concepts-model-discovery.md)
- [Fejlesztés a IoT Plug and Play](./concepts-developer-guide.md)
