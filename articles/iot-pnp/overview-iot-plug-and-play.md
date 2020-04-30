---
title: A IoT Plug and Play előzetes verziójának bemutatása | Microsoft Docs
description: További információ a IoT Plug and Play előzetes verzióról. A IoT Plug and Play egy nyílt modellezési nyelven alapul, amely lehetővé teszi a IoT-eszközök számára, hogy deklarálják képességeiket. A IoT-eszközök olyan felhőalapú megoldásokhoz csatlakoznak, mint például az Azure IoT Central vagy a partner alkalmazások. A felhőalapú megoldás ezután automatikusan megérti az eszközt, és megkezdheti a velük való interakciót anélkül, hogy kódot kellene írnia.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 12f3febac2c5c8ed01b9b156a64dc77f6ed0704f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770437"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Mi a IoT Plug and Play előzetes verzió?

A IoT Plug and Play Preview lehetővé teszi, hogy a megoldások fejlesztői bármilyen beágyazott kód írása nélkül integrálják az eszközöket a megoldásaikkal. A IoT Plug and Play a Device _képesség modell_ sémája, amely leírja az eszközök képességeit. Ez a séma egy olyan JSON-dokumentum, amely illesztőfelület-készletként van strukturálva, amely a következő definíciókat tartalmazza:

- Az eszköz vagy más entitás írásvédett és írási/olvasási állapotát jelképező _Tulajdonságok_ . Előfordulhat például, hogy egy eszköz sorozatszáma csak olvasható tulajdonság, a termosztát hőmérséklete pedig írási/olvasási tulajdonság lehet.
- Az eszköz által kibocsátott adatok _telemetria_ , hogy az adatok az érzékelők normál streamje, egy alkalmi hiba vagy egy tájékoztató üzenet.
- Egy eszközön elvégezhető függvényt vagy műveletet leíró _parancsok_ . Egy parancs például újraindíthatja az átjárót, vagy egy távoli kamera használatával is készíthet képet.

A felületeket újra felhasználhatja az eszköz képességeinek különböző modelljein, így egyszerűbbé és gyorsabbá teheti a fejlesztést.

Ahhoz, hogy a IoT Plug and Play zökkenőmentesen működjön az [Azure digitális Twins](../digital-twins/about-digital-twins.md)szolgáltatással, a IoT Plug and Play séma a [Digital Twin Definition Language (DTDL)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)használatával van definiálva. A IoT Plug and Play és a DTDL nyitva vannak a Közösség számára, és a Microsoft üdvözli az ügyfelekkel, partnerekkel és az iparággal való együttműködést. Mindkettő olyan nyílt W3C-szabványokon alapul, mint például a JSON-LD és az RDF, amelyek megkönnyítik a szolgáltatások és az eszközök egyszerűbb bevezetését. Emellett a IoT Plug and Play és a DTDL használata esetén nem kell külön fizetnie. Az Azure [IoT hub](../iot-hub/about-iot-hub.md), az [Azure IoT Central](../iot-central/core/overview-iot-central.md)és más Azure-szolgáltatások standard díjszabása változatlan marad.

A IoT Hubra vagy IoT Centralra épülő megoldások a IoT Plug and Play számára is hasznosak lehetnek.

Ez a cikk a következőket ismerteti:

- A IoT Plug and Play használó projekthez társított tipikus szerepkörök.
- A IoT Plug and Play-eszközök használata az alkalmazásban.
- A IoT Plug and Play támogató IoT-alkalmazás fejlesztése.
- IoT Plug and Play eszköz tanúsítása és közzététel a [Certified for IoT Device Catalogban](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Felhasználói szerepkörök

A IoT Plug and Play két típusú fejlesztő számára hasznos:

- A _megoldás fejlesztő_ feladata, hogy IoT-megoldást fejlesszen az Azure IoT és más Azure-erőforrásokkal, valamint a IoT-eszközök integrálásához.
- Az _eszközök fejlesztői_ létrehozzák a megoldáshoz csatlakoztatott eszközön futó kódot.

## <a name="use-iot-plug-and-play-devices"></a>IoT Plug and Play-eszközök használata

Megoldás fejlesztőként olyan felhőalapú IoT-megoldást fejleszthet, amely IoT Plug and Play eszközöket használ. A következő Azure-szolgáltatások egyikét használhatja:

- [IoT Central](../iot-central/core/overview-iot-central.md) – egy teljes körűen felügyelt IoT-szolgáltatás, amely megkönnyíti a fizikai és a digitális világok összekapcsolására szolgáló termékek létrehozását.
- [IoT hub](../iot-hub/about-iot-hub.md) – egy felügyelt felhőalapú szolgáltatás, amely a IoT-alkalmazás és az eszközök közötti biztonságos, kétirányú kommunikációra szolgál.

A IoT Plug and Play-eszközök az Azure Certified for IoT Device Catalog használatával találhatók meg. A rendszer ellenőrizte az összes IoT Plug and Play eszközt a katalógusban, és rendelkezik egy eszköz képesség modellel. Tekintse meg az eszköz képességeinek modelljét, hogy megismerje az eszköz funkcióit, vagy használja az eszköz Azure IoT Central-ban való szimulálása érdekében.

IoT Plug and Play eszköz csatlakoztatásakor megtekintheti az eszköz képességeinek modelljét, a modellben található interfészeket, valamint az ezekben a felületeken definiált telemetria, tulajdonságokat és parancsokat.

## <a name="develop-an-iot-device-application"></a>IoT-eszköz alkalmazásának fejlesztése

Eszköz-fejlesztőként kifejlesztheti a IoT Plug and Play támogató IoT hardveres terméket. A folyamat két fő lépésből áll:

1. Adja meg az eszköz képességének modelljét és felületeit. Az eszköz képességeit az [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)használatával DEKLARÁLó JSON-fájlok szerzője. Az eszköz-képesség modell egy teljes entitást, például egy fizikai terméket ír le, és meghatározza az adott entitás által megvalósított felületek készletét. A felületek olyan megosztott szerződések, amelyek egyedileg azonosítják az eszköz által támogatott telemetria, tulajdonságokat és parancsokat. A felületek újra felhasználhatók a különböző eszköz-képességi modellek között.

1. Az eszköz-képesség modellben és a felületeken deklarált képességeket megvalósító eszköz szoftverének vagy belső vezérlőprogram létrehozása. Az Azure IoT SDK API-kat tartalmaz az eszköz képességi modelljeinek megvalósításához.

A VS Code Extension Packhez készült [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) számos funkciót kínál a segítségére. Például az eszköz fejlesztője használhat egy bővítményt a csontváz C projekt létrehozásához egy képesség-modellből. Azonban bármilyen IDE-t használhat az eszköz képességi modelljeinek létrehozásához és megvalósításához.

## <a name="certify-an-iot-plug-and-play-device"></a>IoT Plug and Play eszköz tanúsítása

Eszköz-fejlesztőként IoT-termékeket küldhet a minősítéshez. Az IoT-eszközök katalógusában hitelesített eszközt is közzétehet. A tanúsítási folyamat lépései a következők:

- Csatlakoztassa a [Microsoft Partner Network](https://partner.microsoft.com).
- Az Azure IoT-portál minősítése.
- IoT Plug and Play eszköz-képesség modell és marketing információ beküldése új eszköz rekord létrehozásához.
- Az eszközre vonatkozó ellenőrzési tesztek automatizált készletének továbbítása.
- Tegye közzé a minősítést a IoT-eszköz katalógusában.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A nyilvános előzetes verzióban a IoT Plug and Play minden régióban elérhető.

## <a name="message-quotas-in-iot-hub"></a>Üzenetek kvótái IoT Hub
A nyilvános előzetes verzióban a IoT Plug and Play-eszközök külön üzeneteket küldenek egy felületen, ami növelheti az üzenetek számának az [üzenet-kvótába](../iot-hub/iot-hub-devguide-quotas-throttling.md)való számlálását.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a IoT Plug and Play, a javasolt következő lépés az egyik rövid útmutató kipróbálása:

- [Eszköz-képesség modell használata IoT Plug and Play eszköz létrehozásához](./quickstart-create-pnp-device-windows.md)
- [Eszköz csatlakoztatása az IoT Hubhoz](./quickstart-connect-pnp-device-c-windows.md)
- [Csatlakozás a megoldásban található eszközhöz](./quickstart-connect-pnp-device-solution-node.md)
