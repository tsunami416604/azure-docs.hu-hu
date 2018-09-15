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
ms.openlocfilehash: f059c57396610a10f9e35a6dad8408c6be1d89cb
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604306"
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

### <a name="physical-devices"></a>Fizikai eszközök

Fizikai eszközök csatlakoztatása a megoldáshoz. A szimulált eszközök Azure IoT eszközoldali SDK-k használatával viselkedését valósítható meg.

Telepíthet fizikai eszközökön a megoldásportál az irányítópultról.

### <a name="device-simulation-microservice"></a>Eszköz szimulálása mikroszolgáltatások

A megoldás magában foglalja a [eszköz szimulálása mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) , amely lehetővé teszi a végpontok közötti folyamat teszteléséhez a megoldásban a megoldásportál egy szimulált eszközök készletét kezelését. A szimulált eszközök:

* Hozzon létre az eszköz – felhő telemetriát.
* Az IoT hubról válaszolni a felhőből az eszközre metódust hívja.

A mikroszolgáltatások létrehozása, indítása és leállítása szimulációk egy RESTful végpontot biztosít. Minden szimulációs áll az virtuális eszközök különböző típusú, amely telemetriai adatokat küldhet, és az azokra való metódust hívja.

Szimulált eszközök a megoldásportálon az irányítópultról építhető ki.

### <a name="iot-hub"></a>IoT Hub

A [az IoT hub](../iot-hub/index.yml) feltölti a felhőbe a fizikai és szimulált eszközök által küldött telemetriát. Az IoT hub teszi az IoT-megoldás háttérszolgáltatás feldolgozásra a szolgáltatások elérhetővé a telemetriát.

A megoldásban az IoT Hub ezenkívül a következőket teszi:

* Fenntart egy identitásjegyzéket, amely tárolja a azonosítók és a hitelesítési kulcsokat, a portálhoz csatlakoztatható összes eszköz.
* Az eszközök metódusokat indít a megoldásgyorsító nevében.
* Az összes regisztrált eszközhöz biztosítja a megfelelő ikereszközt. Az ikereszközök tárolják az eszközök által jelentett tulajdonságértékeket. Az ikereszközök a megoldásportálon beállított kívánt tulajdonságokat is tárolják, amelyeket az eszköz a következő csatlakozáskor kérhet le.
* Feladatokat ütemez, hogy több eszközön is beállíthasson tulajdonságokat vagy meghívhasson metódusokat.

## <a name="data-processing-and-analytics"></a>Adatfeldolgozás és -elemzés

A megoldás az adatfeldolgozás és -elemzés a logikai architektúra egy része a következő összetevőket tartalmazza:

### <a name="iot-hub-manager-microservice"></a>Az IoT Hub manager mikroszolgáltatások

A megoldás magában foglalja a [az IoT Hub manager mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) például kezelni az interakciók az IoT hubbal:

* Létrehozása, és IoT-eszközök kezelése.
* Ikereszközök kezelése.
* Vyvolání módszerek az eszközökön.
* IoT-hitelesítő adatok kezelése.

Ez a szolgáltatás is fut, az IoT Hub lekérdezések felhasználói csoportokhoz tartozó eszközök lekéréséhez.

A mikroszolgáltatás egy RESTful végpontot kezelheti az eszközöket és ikereszközök, metódusokat hívhat meg, és az IoT Hub-lekérdezések futtatásához biztosít.

### <a name="device-telemetry-microservice"></a>Eszköz telemetriai mikroszolgáltatások

A [eszköz telemetriai mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) olvasási hozzáférésének eszköztelemetria tárolódik a Time Series Insights egy RESTful végpontot biztosít. A RESTful-végpont azt is lehetővé teszi, hogy a CRUD-műveletek szabályok és a storage-ból riasztás definíciók olvasási/írási hozzáférést.

### <a name="storage-adapter-microservice"></a>Tárolási adapter mikroszolgáltatások

A [tárolási adapter mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) kezeli a kulcs-érték párok, a storage szolgáltatás szemantikát paltformfüggetlen, és a egy egyszerű felületen bemutatása, a tetszőleges méretű, az Azure Cosmos DB adatait tárolja.

Értékek a gyűjteményekben vannak rendszerezve. Egyéni értékeket dolgozhat, vagy teljes gyűjtemények beolvasni. Összetett adatstruktúrákat szerializálni az ügyfelek és felügyelt egyszerű szöveges tartalom.

A szolgáltatás egy RESTful végpontot biztosít a kulcs-érték párok CRUD-műveletek. Értékek

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Megoldás gyorsító telepítések esetén használja [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) szabályok, riasztások, konfigurációs beállításokat és az összes többi ritka elérésű tárolási tárolásához.

### <a name="azure-stream-analytics-manager-microservice"></a>Az Azure Stream Analytics manager mikroszolgáltatások

A [Azure Stream Analytics manager mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) kezeli az Azure Stream Analytics (ASA) feladatokat, beleértve a beállítás a konfigurációt, indítása és leállítása őket, és azok állapotának figyelése.

Az ASA-feladat két referencia-adathalmaz támogatja. Egy adatkészlet meghatározza a szabályok, és a egy meghatározza, hogy az eszközcsoportok. A szabályok referenciaadatok jön létre az eszköz telemetriai mikroszolgáltatás által kezelt adatok alapján. Az Azure Stream Analytics manager mikroszolgáltatás streamfeldolgozó logikákat telemetriai szabályok alakítja át.

Az eszköz csoportok referenciaadatok melyik csoporthoz a alkalmazni a bejövő telemetriát üzenet szabályok azonosítására szolgál. Az eszközcsoportok a konfigurációs mikroszolgáltatás által felügyelt, és az Azure IoT Hub ikereszköz-lekérdezések használata.

Az ASA-feladatok továbbítására a telemetriát a csatlakoztatott eszközökből Time Series Insights tárolására és elemzésére.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Az Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) egy eseményfeldolgozó motor, amely lehetővé teszi, hogy az adatokat az eszközökről streamelt nagy mennyiségű van.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Az Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) szolgáltatásokkal a megoldásgyorsító csatlakoztatott a telemetriát az eszközökről. Azt is lehetővé teszi a jelenítenek meg, és eszközök telemetriai megoldás webes felhasználói felület lekérdezéséhez.

> [!NOTE]
> A Time Series Insights jelenleg nem áll rendelkezésre az Azure China felhőben. Új távoli figyelési megoldás gyorsító üzemelő példányok az Azure China felhőben Cosmos DB összes tárolót használja.

### <a name="configuration-microservice"></a>Konfigurációs mikroszolgáltatások

A [konfigurációs mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) RESTful végpontot biztosít az eszközcsoportok, a megoldásbeállítások és a felhasználói-beállítások a megoldásgyorsító CRUD-műveletek. A tárolási adapter mikroszolgáltatás megőrizheti a konfigurációs adatokat is működik.

### <a name="authentication-and-authorization-microservice"></a>Hitelesítés és engedélyezés mikroszolgáltatások

A [hitelesítési és engedélyezési mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) jogosult hozzáférni a megoldásgyorsító a felhasználókat kezeli. Felhasználókezelés végezhető identitásszolgáltatóval minden olyan szolgáltatás, amely támogatja a [OpenId Connect](http://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Megoldás gyorsító telepítések esetén használja [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) , az OpenID Connect-szolgáltató. Az Azure Active Directory tárolja a felhasználói adatok és tanúsítványok ellenőrzése a JWT jogkivonat-aláírás biztosítja.

## <a name="presentation"></a>Bemutató

A megoldás a logikai architektúrát bemutató részében a következő összetevőket tartalmazza:

A [webes felhasználói felülete egy React Javascript-alkalmazás, amely](https://github.com/Azure/pcs-remote-monitoring-webui). Az alkalmazás:

* Javascript React csak használ, és teljes mértékben a böngészőben futtatja.
* A kódstílusként CSS használatával.
* Nyilvános irányuló mikroszolgáltatások AJAX-hívások keresztül kommunikál.

A felhasználói felület a megoldás gyorsító funkciókat mutat be, és kommunikál a többi mikroszolgáltatások például:

* A hitelesítési és engedélyezési mikroszolgáltatás felhasználói adatok védelme érdekében.
* Az IoT Hub manager mikroszolgáltatás listázása és az IoT-eszközök felügyeletére.

A felhasználói felület integrálható az Azure Time Series Insights explorer lekérdezését és elemzését eszköztelemetria engedélyezéséhez.

A konfigurációs mikroszolgáltatás lehetővé teszi, hogy a felhasználói felület, tárolására és beolvasására a konfigurációs beállításokat.

## <a name="next-steps"></a>További lépések

Ha szeretné a forrás-kód és a fejlesztői dokumentáció, a kezdéshez válasszon egyet a két GitHub-adattárak:

* [A távoli megfigyelés az Azure IoT (.NET) megoldásgyorsító](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Az Azure IoT (Java) a távoli figyelési megoldásgyorsító](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Architektúra-diagramok megoldás részletes:
* [Architektúra távoli figyelési megoldásgyorsító](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [testre szabhatja a megoldásgyorsító](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
