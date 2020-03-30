---
title: Bevezetés az IoT Plug and Play előzetesverzióba | Microsoft dokumentumok
description: További információ az IoT Plug and Play előzetes verzióról. Az IoT Plug and Play egy nyílt modellezési nyelven alapul, amely lehetővé teszi az IoT-eszközök számára képességeik deklarálását. Az IoT-eszközök bemutatják ezt a deklarációt, az úgynevezett eszközképességi modellt, amikor felhőalapú megoldásokhoz, például az Azure IoT Centralhoz vagy a partneralkalmazásokhoz csatlakoznak. A felhőalapú megoldás ezután automatikusan megérti az eszközt, és elkezdi a vele való interakciót – mindezt kód írása nélkül.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 0399e1659fb7cc6a650c6b3c1d0189c8802d4904
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064310"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Mi az IoT Plug and Play előzetes verzió?

Az IoT Plug and Play Preview lehetővé teszi a megoldásfejlesztők számára, hogy beágyazott kód írása nélkül integrálják az eszközöket a megoldásaikba. Az IoT Plug and Play lényege egy _eszközképességi_ modellséma, amely leírja az eszköz képességeit. Ez a séma egy JSON-dokumentum, amely olyan felületek halmazaként épül fel, amelyek a következők definícióit tartalmazzák:

- _Olyan tulajdonságok,_ amelyek egy eszköz vagy más entitás írásvédett és olvasható/olvasható állapotát jelölik. Az eszköz sorozatszáma lehet például írásvédett tulajdonság, a termosztát célhőmérséklete pedig olvasási/írási tulajdonság lehet.
- _Telemetriai adatok,_ amely az eszköz által kibocsátott adatok, függetlenül attól, hogy az adatok egy rendszeres adatfolyam az érzékelő olvasmányok, alkalmi hiba, vagy információs üzenet.
- Az eszközön elvégezhető függvényt vagy műveletet leíró _parancsok._ Egy parancs például újraindíthat egy átjárót, vagy távoli kamerával fényképezhet.

Az eszközképesség-modellek közötti felületeket újra felhasználhatja az együttműködés megkönnyítése és a fejlesztés felgyorsítása érdekében.

Annak érdekében, hogy az IoT Plug and Play zökkenőmentesen működjön az [Azure Digital Twins-szel,](../digital-twins/about-digital-twins.md)az IoT Plug and Play séma a Digital Twin Definition Language [(DTDL)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)használatával van definiálva. Az IoT Plug and Play és a DTDL nyitott a közösség számára, és a Microsoft üdvözli az ügyfelekkel, partnerekkel és az iparággal való együttműködést. Mindkettő olyan nyílt W3C szabványokon alapul, mint a JSON-LD és az RDF, amelyek lehetővé teszik a szolgáltatások és az eszközök közötti könnyebb bevezetést. Emellett az IoT Plug and Play és a DTDL használatának nem jár külön költsége. Az [Azure IoT Hub,](../iot-hub/about-iot-hub.md)az [Azure IoT Central](../iot-central/core/overview-iot-central.md)és más Azure-szolgáltatások standard díjai változatlanok maradnak.

Az IoT Hubra vagy az IoT Centralra épülő megoldások élvezhetik az IoT Plug and Play előnyeit.

Ez a cikk a következőket ismerteti:

- Az IoT Plug and Play programot használó projekthez társított tipikus szerepkörök.
- Az IoT Plug and Play eszközök használata az alkalmazásban.
- IoT-eszközalkalmazás fejlesztése, amely támogatja az IoT Plug and Play alkalmazást.
- IoT Plug and Play eszköz hitelesítése és közzététele a [Certified for IoT eszközkatalógusban.](https://catalog.azureiotsolutions.com/)

## <a name="user-roles"></a>Felhasználói szerepkörök

Az IoT Plug and Play kétféle fejlesztő számára hasznos:

- A _megoldásfejlesztő_ felelős az Azure IoT és más Azure-erőforrásokat használó IoT-megoldások fejlesztéséért és az integrálandó IoT-eszközök azonosításáért.
- Az _eszközfejlesztő_ létrehozza a megoldáshoz csatlakoztatott eszközön futó kódot.

## <a name="use-iot-plug-and-play-devices"></a>IoT Plug and Play eszközök használata

Megoldásfejlesztőként olyan felhőalapú IoT-megoldást fejleszthet, amely IoT Plug and Play eszközöket használ. Az alábbi Azure-szolgáltatások egyikét használhatja:

- [IoT Central](../iot-central/core/overview-iot-central.md) – egy teljes körűen felügyelt IoT-szoftver-szolgáltatásként megoldás, amely megkönnyíti a fizikai és digitális világokat összekötő termékek létrehozását.
- [IoT Hub](../iot-hub/about-iot-hub.md) – egy felügyelt felhőszolgáltatás, amely az IoT-alkalmazás és az eszközök közötti biztonságos, kétirányú kommunikáció üzenetközpontjaként működik.

Az IoT Plug and Play eszközök az Azure Certified for IoT eszközkatalógusban találhatók. A katalógusban lévő minden IoT Plug and Play eszköz érvényesítve lett, és rendelkezik egy eszközképességi modellel. Tekintse meg az eszköz képességmodelljét az eszköz funkcióinak megismeréséhez, vagy használja azt az eszköz szimulálására az Azure IoT Centralban.

IoT Plug and Play eszköz csatlakoztatásakor megtekintheti az eszköz képességmodelljét, a modellben található felületeket, valamint az ezekben az összeköttetésekben definiált telemetriai adatokat, tulajdonságokat és parancsokat.

## <a name="develop-an-iot-device-application"></a>IoT-eszközalkalmazás fejlesztése

Eszközfejlesztőként olyan IoT-hardverterméket fejleszthet, amely támogatja az IoT Plug and Play-t. A folyamat két kulcsfontosságú lépést foglal magában:

1. Adja meg az eszközképességi modellt és az interfészeket. Olyan JSON-fájlokat hoz létre, amelyek deklarálják az eszköz képességeit a [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)segítségével. Az eszközképesség-modell egy teljes entitást, például egy fizikai terméket ír le, és meghatározza az adott entitás által megvalósított felületek készletét. A felületek olyan megosztott szerződések, amelyek egyedileg azonosítják az eszköz által támogatott telemetriai adatokat, tulajdonságokat és parancsokat. Az interfészek újra felhasználhatók a különböző eszközképességi modellek között.

1. Az eszköz képességi modelljében és felületein deklarált képességeket megvalósító eszközszoftver vagy belső vezérlőprogram szerzője. Az Azure IoT SDK api-kat tartalmaz az eszközképességi modellek megvalósításához.

Az [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bővítménycsomag számos olyan funkciót kínál, amelyek segítséget nyújtanak. Eszközfejlesztőként például egy bővítmény segítségével létrehozhat egy csontváz C-projektet egy képességmodellből. Azonban bármilyen IDE segítségével eszközképességi modelleket hozhat meg és valósíthat meg.

## <a name="certify-an-iot-plug-and-play-device"></a>IoT Plug and Play eszköz hitelesítése

Eszközfejlesztőként IoT-hardvertermékeket küldhet be minősítésre. Közzétehet egy hitelesített eszközt az IoT-tanúsítvány eszközkatalógusban. A tanúsítási folyamat lépései a következők:

- Csatlakozzon a [Microsoft Partner Network hálózathoz](https://partner.microsoft.com).
- Az Azure IoT-portál certified for- portálra való alaplapra.
- Küldjön be egy IoT Plug and Play eszközképességi modellt és marketingadatokat egy új eszközrekord létrehozásához.
- Adja át az eszköz automatikus ellenőrzési tesztjeit.
- Közzététel az IoT-eszközök hitelesített eszközkatalógusában.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A nyilvános előzetes verzió során az IoT Plug and Play minden régióban elérhető.

## <a name="message-quotas-in-iot-hub"></a>Üzenetkvóták az IoT Hubban
A nyilvános előzetes verzió során az IoT Plug and Play eszközök felületenként külön üzeneteket küldenek, ami növelheti az [üzenetkvótába](../iot-hub/iot-hub-devguide-quotas-throttling.md)beszámított üzenetek számát.

## <a name="next-steps"></a>További lépések

Most, hogy már áttekintheti az IoT Plug and Play játékot, a javasolt következő lépés az egyik rövid útmutató kipróbálása:

- [IoT Plug and Play eszköz létrehozása eszközképességi modell használatával](./quickstart-create-pnp-device-windows.md)
- [Eszköz csatlakoztatása az IoT Hubhoz](./quickstart-connect-pnp-device-c-windows.md)
- [Csatlakozás a megoldásban található eszközhöz](./quickstart-connect-pnp-device-solution-node.md)
