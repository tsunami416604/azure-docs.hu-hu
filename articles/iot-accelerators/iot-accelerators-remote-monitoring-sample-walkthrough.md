---
title: Távoli figyelés megoldásgyorsító áttekintése – Azure |} A Microsoft Docs
description: A távoli figyelési megoldásgyorsító áttekintése.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: dfe584532efeab1dbc0d2928b7afb0a6695a21ee
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39184945"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Távoli figyelés megoldásgyorsító áttekintése

A távoli figyelési [megoldásgyorsító](../iot-accelerators/about-iot-accelerators.md) valósít meg olyan teljes körű figyelési megoldást nyújt több gép távoli helyeken. A megoldás fontos Azure-szolgáltatások kombinációját kínálja az üzleti forgatókönyv általános megvalósítása érdekében. Használhatja a megoldás kiindulási pontként a saját implementációjához, és [testreszabása](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) azt a saját speciális üzleti igények kielégítése érdekében.

Ez a cikk végigvezeti néhány fontos elemét, hogy jobban megismerhesse, hogyan működik a távoli figyelési megoldás. Ezeknek az ismereteknek a birtokában:

* Elháríthatja a megoldásban felmerülő hibákat.
* Megtervezheti, hogy miképpen érdemes testre szabni a megoldást úgy, hogy az megfeleljen egyedi igényeinek.
* Kialakíthatja saját, Azure-szolgáltatásokat használó IoT-megoldását.

## <a name="logical-architecture"></a>Logikai architektúra

A következő diagram a távoli figyelési megoldásgyorsító átfedésben lévő logikai összetevőit vázolja a [IoT-architektúra](../iot-fundamentals/iot-introduction.md):

![Logikai architektúra](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Miért érdemes mikroszolgáltatásokat?

Felhőarchitektúra alakult óta a Microsoft, amely az első megoldásgyorsítók. [Mikroszolgáltatások](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) rendelkezik kiderült bevált gyakorlat szerint a skálázhatóságot és rugalmasságot érhet el a fejlesztés sebessége feláldozása nélkül. Számos Microsoft-szolgáltatások használata architekturális mintáról belsőleg nagy megbízhatóság és méretezhetőség eredményeket. A frissített megoldásgyorsítók helyezi át a gyakorlatba ezek tapasztalatainkat, így a is élvezheti ezeket.

> [!TIP]
> További információk a mikroszolgáltatás-architektúrákról: [.NET-alkalmazás architektúrája](https://www.microsoft.com/net/learn/architecture) és [Mikroszolgáltatások: egy felhőben zajló alkalmazásforradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Eszközkapcsolatok

A megoldás az eszköz kapcsolat a logikai architektúra egy része a következő összetevőket tartalmazza:

### <a name="simulated-devices"></a>Szimulált eszközök

A megoldás, amely lehetővé teszi a végpontok közötti folyamat teszteléséhez a megoldásban a szimulált eszközök készletét kezelését mikroszolgáltatások tartalmaz. A szimulált eszközök:

* Hozzon létre az eszköz – felhő telemetriát.
* Az IoT hubról válaszolni a felhőből az eszközre metódust hívja.

A mikroszolgáltatások létrehozása, indítása és leállítása szimulációk egy RESTful végpontot biztosít. Minden szimulációs áll az virtuális eszközök különböző típusú, amely telemetriai adatokat küldhet, és az azokra való metódust hívja.

Szimulált eszközök a megoldásportálon az irányítópultról építhető ki.

### <a name="physical-devices"></a>Fizikai eszközök

Fizikai eszközök csatlakoztatása a megoldáshoz. A szimulált eszközök Azure IoT eszközoldali SDK-k használatával viselkedését valósítható meg.

Telepíthet fizikai eszközökön a megoldásportál az irányítópultról.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>Az IoT Hub és az IoT-kezelő mikroszolgáltatások

A [az IoT hub](../iot-hub/index.yml) feltölti a felhőbe az eszközök által küldött adatokat, és lehetővé teszi az a `telemetry-agent` mikroszolgáltatás.

A megoldásban az IoT Hub ezenkívül a következőket teszi:

* Fenntart egy identitásjegyzéket, amely tárolja a azonosítók és a hitelesítési kulcsokat, a portálhoz csatlakoztatható összes eszköz. Az identitásjegyzéken keresztül a felhasználó engedélyezheti és letilthatja az eszközöket.
* Metódusokat indít az eszközökön a megoldásportál nevében.
* Az összes regisztrált eszközhöz biztosítja a megfelelő ikereszközt. Az ikereszközök tárolják az eszközök által jelentett tulajdonságértékeket. Az ikereszközök a megoldásportálon beállított kívánt tulajdonságokat is tárolják, amelyeket az eszköz a következő csatlakozáskor kérhet le.
* Feladatokat ütemez, hogy több eszközön is beállíthasson tulajdonságokat vagy meghívhasson metódusokat.

A megoldás magában foglalja a `iot-manager` mikroszolgáltatás például kezelni az interakciók az IoT hubbal:

* Létrehozása, és IoT-eszközök kezelése.
* Ikereszközök kezelése.
* Vyvolání módszerek az eszközökön.
* IoT-hitelesítő adatok kezelése.

Ez a szolgáltatás is fut, az IoT Hub lekérdezések felhasználói csoportokhoz tartozó eszközök lekéréséhez.

A mikroszolgáltatás egy RESTful végpontot kezelheti az eszközöket és ikereszközök, metódusokat hívhat meg, és az IoT Hub-lekérdezések futtatásához biztosít.

## <a name="data-processing-and-analytics"></a>Adatfeldolgozás és -elemzés

A megoldás az adatfeldolgozás és -elemzés a logikai architektúra egy része a következő összetevőket tartalmazza:

### <a name="device-telemetry"></a>Eszköztelemetria

A megoldás tartalmaz két mikroszolgáltatás-alapú eszköz telemetriai adatok kezeléséhez.

A [telemetriai ügynök](https://github.com/Azure/telemetry-agent-dotnet) mikroszolgáltatás:

* Az Azure Cosmos DB tárolja a telemetriai adatokat.
* Az eszközök telemetriastreamjét elemzi.
* Meghatározott szabályok szerint riasztást generál.

A riasztások az Azure Cosmos DB tárolja.

A [telemetriai ügynök](https://github.com/Azure/telemetry-agent-dotnet) mikroszolgáltatás lehetővé teszi, hogy a megoldás portálján, olvassa el az eszközök által küldött telemetriát. A megoldás portálja is használja ezt a szolgáltatást:

* Például a küszöbértékeket, riasztást kiváltó figyelési szabályok definiálása
* Elmúlt riasztások listájának beolvasása.

A mikroszolgáltatások által biztosított RESTful-végpont használatával telemetriai adatokat, a szabályok és a riasztások kezelése.

### <a name="storage"></a>Storage

A [tárolóadaptert](https://github.com/Azure/pcs-storage-adapter-dotnet) a mikroszolgáltatások általában az adapter beállításai a megoldásgyorsító használt fő storage szolgáltatás elé. Egyszerű adatgyűjtési és -kulcs-érték tároló biztosít.

A normál a megoldásgyorsító központi Azure Cosmos DB használja, mint a fő storage szolgáltatás.

Az Azure Cosmos DB-adatbázisban tárol adatokat a megoldásgyorsító. A **tárolóadaptert** mikroszolgáltatás a többi mikroszolgáltatások hozzáférés tárolási szolgáltatásokhoz a megoldásban az adapter funkcionál.

## <a name="presentation"></a>Bemutató

A megoldás a logikai architektúrát bemutató részében a következő összetevőket tartalmazza:

A [webes felhasználói felülete egy React Javascript-alkalmazás, amely](https://github.com/Azure/pcs-remote-monitoring-webui). Az alkalmazás:

* Javascript React csak használ, és teljes mértékben a böngészőben futtatja.
* A kódstílusként CSS használatával.
* Nyilvános irányuló mikroszolgáltatások AJAX-hívások keresztül kommunikál.

A felhasználói felület a megoldás gyorsító funkciókat mutat be, és más szolgáltatásokkal együttműködő például:

* A [hitelesítési](https://github.com/Azure/pcs-auth-dotnet) mikroszolgáltatás felhasználói adatok védelme érdekében.
* A [iothub-kezelő](https://github.com/Azure/iothub-manager-dotnet) mikroszolgáltatás listázása és az IoT-eszközök felügyeletére.

A [ui-config](https://github.com/Azure/pcs-config-dotnet) mikroszolgáltatás lehetővé teszi, hogy a felhasználói felület, tárolására és beolvasására a konfigurációs beállításokat.

## <a name="next-steps"></a>További lépések

Ha szeretné a forrás-kód és a fejlesztői dokumentáció, a kezdéshez válasszon egyet a két fő GitHub-adattárak:

* [A távoli megfigyelés az Azure IoT (.NET) megoldásgyorsító](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/).
* [Az Azure IoT (Java) a távoli figyelési megoldásgyorsító](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Architektúra-diagramok megoldás részletes:
* [Architektúra távoli figyelési megoldásgyorsító](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [testre szabhatja a megoldásgyorsító](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
