---
title: Távoli felügyeleti megoldás - Azure architektúra |} Microsoft Docs
description: A távoli felügyeleti megoldásgyorsító architektúrájának részletes útmutatás.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 3eaaa1ec09e9bd593a2d14e4a3bc751c431869d0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="remote-monitoring-solution-accelerator-architecture"></a>Távoli figyelés megoldás gyorsító architektúrája

A távoli megfigyelési [megoldásgyorsító](iot-suite-what-are-solution-accelerators.md) egy végpont figyelési megoldást igényelnek több valósítja meg a távoli helyeken. A megoldás fontos Azure-szolgáltatások kombinációját kínálja az üzleti forgatókönyv általános megvalósítása érdekében. Használhatja a megoldás kiindulási pontként a saját végrehajtásához és [testreszabása](iot-suite-remote-monitoring-customize.md) úgy, hogy a saját üzleti követelményeinek megfelelően.

Ebben a cikkben bemutatjuk a távoli figyelési megoldás néhány fontos elemét, hogy jobban megismerhesse a szolgáltatás működését. Ezeknek az ismereteknek a birtokában:

* Elháríthatja a megoldásban felmerülő hibákat.
* Megtervezheti, hogy miképpen érdemes testre szabni a megoldást úgy, hogy az megfeleljen egyedi igényeinek.
* Kialakíthatja saját, Azure-szolgáltatásokat használó IoT-megoldását.

## <a name="logical-architecture"></a>Logikai architektúra

A következő ábra bemutatja a távoli felügyeleti megoldásgyorsító az átfedett logikai összetevőinek a [IoT-architektúra](iot-suite-what-is-azure-iot.md):

![Logikai architektúra](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Miért mikroszolgáltatások létrehozására?

Mivel a Microsoft, amely az első megoldás gyorsítók architektúra változásokon ment keresztül. [Mikroszolgáltatások](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) rendelkezik kiderült, bevált gyakorlat méretezés és rugalmasság eléréséhez fejlesztési sebesség feláldozása nélkül. Több Microsoft-szolgáltatások ebben a mintában architekturális belsőleg használja a nagy megbízhatósági és méretezhetőségi eredményeit. A frissített megoldás gyorsítók ezek learnings gyakorlati helyezze el, révén is kihasználhatja a őket.

> [!TIP]
> További információk a mikroszolgáltatás-architektúrákról: [.NET-alkalmazás architektúrája](https://www.microsoft.com/net/learn/architecture) és [Mikroszolgáltatások: egy felhőben zajló alkalmazásforradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Eszközkapcsolatok

A megoldás az eszköz kapcsolat logikai architektúrájának a része a következő összetevőket tartalmazza:

### <a name="simulated-devices"></a>Szimulált eszközök

A megoldás tartalmaz egy mikroszolgáltatási, amely lehetővé teszi a megoldásban a végpont folyamat tesztelése szimulált eszközök készlete kezelését. A szimulált eszköz:

* Eszköz-felhő telemetriai készítése.
* Az IoT-központ felhő eszközre metódushívások válaszolni.

A mikroszolgáltatási hozhat létre, indítása és leállítása szimulációja egy RESTful végpontja biztosítja. Minden egyes szimulálása az, hogy telemetriát küldhessenek, valamint válaszolni metódushívások különböző típusú virtuális eszközök áll.

A megoldás portálon az irányítópultról szimulált eszköz építhető ki.

### <a name="physical-devices"></a>Fizikai eszköz

A megoldás a fizikai eszközök csatlakozhat. A szimulált eszköz az Azure IoT-eszközök SDK-k viselkedését is létrehozható.

A megoldás portálon az irányítópultról fizikai eszközök építhető ki.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>Az IoT-központ és az IoT-kezelő mikroszolgáltatási

A [IoT-központ](../iot-hub/index.yml) ingests eszközről a felhőbe küldött adatok, és lehetővé teszi a `telemetry-agent` mikroszolgáltatási.

A megoldásban az IoT Hub ezenkívül a következőket teszi:

* Kezeli az identitásjegyzékhez, amely tárolja az azonosítók és a hitelesítési kulcsokat, a portálhoz való csatlakozáshoz engedélyezett összes eszközt. Az identitásjegyzéken keresztül a felhasználó engedélyezheti és letilthatja az eszközöket.
* Metódusokat indít az eszközökön a megoldásportál nevében.
* Az összes regisztrált eszközhöz biztosítja a megfelelő ikereszközt. Az ikereszközök tárolják az eszközök által jelentett tulajdonságértékeket. Az ikereszközök a megoldásportálon beállított kívánt tulajdonságokat is tárolják, amelyeket az eszköz a következő csatlakozáskor kérhet le.
* Feladatokat ütemez, hogy több eszközön is beállíthasson tulajdonságokat vagy meghívhasson metódusokat.

A megoldás tartalmaz a `iot-manager` kezelni, mint az IoT hub interakció mikroszolgáltatási:

* Létrehozása, és az IoT-eszközök kezelése.
* Eszköz twins kezelése.
* Módszerek az eszközökön hívásakor.
* Az IoT-hitelesítő adatok kezelése.

Ez a szolgáltatás is fut, az IoT-központ lekérdezések felhasználói csoportokhoz tartozó eszközök beolvasása.

A mikroszolgáltatási biztosít egy RESTful végpontot, kezelheti az eszközöket és az eszköz twins, lehetőség módszerek meghívására, és az IoT-központ lekérdezéseket futtathat.

## <a name="data-processing-and-analytics"></a>Adatfeldolgozás és -elemzés

A megoldás az adatok feldolgozása és elemzés logikai architektúrájának a része a következő összetevőket tartalmazza:

### <a name="device-telemetry"></a>Telemetriát

A megoldás két mikroszolgáltatások telemetriát kezelésére tartalmaz.

A [telemetria-ügynök](https://github.com/Azure/telemetry-agent-dotnet) mikroszolgáltatási:

* A Cosmos DB telemetriai adatokat tárolja.
* Elemzi a telemetriai adatok adatfolyam eszközökről.
* Meghatározott szabályok szerint riasztást generál.

A riasztások Cosmos DB vannak tárolva.

A `telemetry-agent` mikroszolgáltatási lehetővé teszi, hogy a megoldás portal eszközről küldött telemetriai adatok olvasásához. A megoldás portal is használja a szolgáltatást:

* Például a küszöbértékeket, riasztást kiváltó figyelési szabályok definiálása
* Elmúlt riasztások listájának beolvasása.

A RESTful végpont a mikroszolgáltatási által biztosított segítségével telemetriai adatokat, a szabályok és a riasztások kezelése.

### <a name="storage"></a>Storage

A [tárolóadapter](https://github.com/Azure/pcs-storage-adapter-dotnet) mikroszolgáltatási egy adapter elé a megoldásgyorsító használt fő tároló szolgáltatás. Egyszerű gyűjtemény és a kulcs-érték tároló biztosít.

A normál a megoldásgyorsító központi Cosmos DB használja, mint a fő tároló szolgáltatás.

A Cosmos DB adatbázisban tárol adatokat a megoldásgyorsító. A **tárolóadapter** mikroszolgáltatási úgy működik, mint az adaptert a megoldásban való hozzáférés tárolási szolgáltatások más mikroszolgáltatások létrehozására.

## <a name="presentation"></a>Bemutató

A megoldás a logikai architektúrát bemutató részében a következő összetevőket tartalmazza:

A [webes felhasználói felülete egy reagálni Javascript-alkalmazás, amely](https://github.com/Azure/pcs-remote-monitoring-webui). Az alkalmazás:

* Javascript reagálni csak használ, és teljesen a böngészőben futtatja.
* CSS való stílussal van.
* Nyilvános irányuló mikroszolgáltatások AJAX-hívások keresztül kommunikál.

A felhasználói felület a megoldás gyorsító funkcióit mutatja be, és kommunikációja más szolgáltatásokkal, mint:

* A [hitelesítési](https://github.com/Azure/pcs-auth-dotnet) mikroszolgáltatási felhasználói adatok védelmét.
* A [IOT hubbal-kezelő](https://github.com/Azure/iothub-manager-dotnet) mikroszolgáltatási listában, és az IoT-eszközök kezeléséhez.

A [ui-config](https://github.com/Azure/pcs-config-dotnet) mikroszolgáltatási lehetővé teszi, hogy a felhasználói felület tárolásához és lekéréséhez a konfigurációs beállításokat.

## <a name="next-steps"></a>További lépések

Ha szeretné használni a kódot és fejlesztői dokumentációját, indítsa el az egyik a két fő GitHub-adattárak:

* [A távoli figyelése az Azure IoT (.NET) megoldásgyorsító](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/).
* [A távoli megfigyelés az Azure IoT (Java) megoldásgyorsító](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).
* [Megoldásgyorsító távoli figyelési architektúra)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

További elméleti kapcsolatos további információkért a távoli felügyeleti megoldásgyorsító: [testre szabhatja a megoldásgyorsító](iot-suite-remote-monitoring-customize.md).
