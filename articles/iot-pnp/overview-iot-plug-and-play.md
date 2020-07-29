---
title: A IoT Plug and Play előzetes verziójának bemutatása | Microsoft Docs
description: További információ a IoT Plug and Play előzetes verzióról. A IoT Plug and Play egy nyílt modellezési nyelven alapul, amely lehetővé teszi, hogy az intelligens IoT-eszközök bejelentsenek képességeiket. A IoT-eszközök a deklarációt, az eszköz modelljét használják, amikor felhőalapú megoldásokhoz csatlakoznak. A felhőalapú megoldás ezután automatikusan megérti az eszközt, és megkezdheti a velük való interakciót, anélkül, hogy kódot kellene írnia.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 7f889d6730012a11ebf82a78583ef420b7621075
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337653"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Mi a IoT Plug and Play előzetes verzió?

A IoT Plug and Play előzetes verziója lehetővé teszi a megoldás-építők számára, hogy manuális konfiguráció nélkül integrálják az intelligens eszközöket a megoldásaikkal. A IoT-Plug and Play középpontjában a egy eszköz _modellje_ , amelyet az eszköz használ a képességeinek egy IoT Plug and Play-kompatibilis alkalmazásba való reklámozásához. Ez a modell olyan elemek halmaza, amelyek a következőket határozzák meg:

- Egy eszköz vagy más entitás írásvédett vagy írható állapotát jelképező _Tulajdonságok_ . Előfordulhat például, hogy egy eszköz sorozatszáma csak olvasható tulajdonság, és a termosztát hőmérséklete írható tulajdonság lehet.
- _Telemetria_ az eszköz által kibocsátott adatok, függetlenül attól, hogy az adatok az érzékelők normál streamje, egy alkalmi hiba vagy egy tájékoztató üzenet.
- Egy eszközön elvégezhető függvényt vagy műveletet leíró _parancsok_ . Egy parancs például újraindíthatja az átjárót, vagy egy távoli kamera használatával is készíthet képet.

Ezeket az elemeket az interfészek között csoportosítva újra felhasználhatja a modelleket, így egyszerűbbé és gyorsabbá teheti a fejlesztést.

Ahhoz, hogy a IoT-Plug and Play működjön az [Azure Digital Twins](../digital-twins/about-digital-twins.md)szolgáltatással, a modelleket és a felületeket a [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)használatával határozhatja meg. A IoT Plug and Play és a DTDL nyitva vannak a Közösség számára, és a Microsoft üdvözli az ügyfelekkel, partnerekkel és az iparággal való együttműködést. Mindkettő olyan nyílt W3C-szabványokon alapul, mint például a JSON-LD és az RDF, amely megkönnyíti a szolgáltatások és az eszközök használatát.

A IoT Plug and Play és a DTDL használata nem igényel további költségeket. Az [azure IoT hub](../iot-hub/about-iot-hub.md) és más Azure-szolgáltatások standard díjszabása változatlan marad.

Ez a cikk a következőket ismerteti:

- A IoT Plug and Play használó projekthez társított tipikus szerepkörök.
- A IoT Plug and Play-eszközök használata az alkalmazásban.
- A IoT Plug and Play támogató IoT-alkalmazás fejlesztése.

## <a name="user-roles"></a>Felhasználói szerepkörök

A IoT Plug and Play két típusú fejlesztő számára hasznos:

- A _megoldás-szerkesztő_ feladata, hogy IoT-megoldást fejlesszen az Azure IoT hub és más Azure-erőforrások használatával, valamint a IoT-eszközök integrálásához.
- Egy _eszköz-szerkesztő_ létrehozza a megoldáshoz csatlakoztatott eszközön futó kódot.

## <a name="use-iot-plug-and-play-devices"></a>IoT Plug and Play-eszközök használata

Megoldás-szerkesztőként olyan felhőalapú IoT-megoldást fejleszthet, amely IoT Plug and Play eszközöket használ. A IoT-alkalmazás és az eszközök közötti biztonságos, kétirányú kommunikációhoz a [IoT hub](../iot-hub/about-iot-hub.md) által felügyelt felhőalapú szolgáltatást használhatja.

Amikor egy IoT Plug and Play eszközt csatlakoztat egy IoT hubhoz, az [Azure IoT Explorer](./howto-use-iot-explorer.md) eszköz használatával megtekintheti a modellt alkotó felületeken definiált telemetria, tulajdonságokat és parancsokat.

## <a name="develop-an-iot-device-application"></a>IoT-eszköz alkalmazásának fejlesztése

Eszköz-szerkesztőként olyan IoT-terméket fejleszthet, amely támogatja a IoT Plug and Play. A folyamat három fő lépést tartalmaz:

1. Adja meg az eszköz modelljét. A [DTDL](https://github.com/Azure/opendigitaltwins-dtdl)használatával olyan JSON-fájlokat hozhat létre, amelyek meghatározzák az eszköz képességeit. A modell egy teljes entitást, például egy fizikai terméket ír le, és meghatározza az adott entitás által megvalósított felületek készletét. A felületek olyan megosztott szerződések, amelyek egyedileg azonosítják az eszköz által támogatott telemetria, tulajdonságokat és parancsokat. A felületek újra felhasználhatók különböző modellekben.

1. Az eszköz szoftverét vagy a belső vezérlőprogramot úgy hozhatja létre, hogy a telemetria, tulajdonságaik és parancsaik kövessék a IoT Plug and Play konvenciókat.

1. Az eszköz bejelenti a modell AZONOSÍTÓját a MQTT-kapcsolat részeként. Az Azure IoT SDK új szerkezeteket tartalmaz, amelyek a modell AZONOSÍTÓjának megadására szolgálnak a kapcsolódás időpontjában.

> [!Important]
> A IoT Plug and Play-eszközöknek MQTT vagy MQTT-t kell használniuk a WebSockets-en keresztül. Más protokollok, például a AMQP vagy a HTTP nem érvényesek a IoT Plug and Play-eszközök megvalósítására.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Ez a IoT Plug and Play előzetes verzió frissítését az USA középső régiójában, Észak-Európában és Kelet-japán régióban létrehozott IoT-hubokon érhető el.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a IoT Plug and Play, a javasolt következő lépés az egyik rövid útmutató kipróbálása:

- [Eszköz csatlakoztatása IoT Hubhoz (C)](./quickstart-connect-device-c.md)
- [Eszköz használata a megoldásban (Node.js)](./quickstart-service-node.md)

