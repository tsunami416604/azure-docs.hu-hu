---
title: Távfigyelési megoldásgyorsító áttekintése - Azure | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a távoli figyelési megoldás néhány kulcsfontosságú eleméről, amely lehetővé teszi annak megértését, hogyan működik.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: f501eb55f72811063ddf1d8e02a0ce2137d598f3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546312"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>A távoli monitorozási megoldásgyorsító áttekintése

A távoli figyelési [megoldásgyorsító](../iot-accelerators/about-iot-accelerators.md) egy végpontok között figyelő megoldást valósít meg több távoli helyen lévő gép számára. A megoldás fontos Azure-szolgáltatások kombinációját kínálja az üzleti forgatókönyv általános megvalósítása érdekében. A megoldást kiindulási pontként használhatja a saját implementációjához, és a saját üzleti igényeinek megfelelően [testreszabhatja](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) azt.

Ez a cikk végigvezeti a távoli figyelési megoldás néhány kulcsfontosságú elemén, hogy megismerhesse, hogyan működik. Ezeknek az ismereteknek a birtokában:

* Elháríthatja a megoldásban felmerülő hibákat.
* Megtervezheti, hogy miképpen érdemes testre szabni a megoldást úgy, hogy az megfeleljen egyedi igényeinek.
* Kialakíthatja saját, Azure-szolgáltatásokat használó IoT-megoldását.

A Távoli figyelési megoldásgyorsító kód ja:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Logikai architektúra

Az alábbi ábra az [IoT-architektúra](../iot-fundamentals/iot-introduction.md)távoli figyelési megoldásgyorsítójának logikai összetevőit ismerteti:

![Logikai architektúra](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Miért mikroszolgáltatások?

A felhőarchitektúra azóta fejlődött, hogy a Microsoft kiadta az első megoldásgyorsítókat. [A mikroszolgáltatások](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) bevált gyakorlatként jelentek meg a méretezés és a rugalmasság eléréséhez a fejlesztési sebesség feláldozása nélkül. Számos Microsoft-szolgáltatás használja ezt az architekturális mintát belsőleg, nagy megbízhatósággal és méretezhetőségi eredménnyel. A frissített megoldásgyorsítók ezeket a tanulságokat átülik a gyakorlatba, így ön is élvezheti azok előnyeit.

> [!TIP]
> További információk a mikroszolgáltatás-architektúrákról: [.NET-alkalmazás architektúrája](https://www.microsoft.com/net/learn/architecture) és [Mikroszolgáltatások: egy felhőben zajló alkalmazásforradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Eszközkapcsolatok

A megoldás a logikai architektúra eszközkapcsolati részében a következő összetevőket tartalmazza:

### <a name="real-devices"></a>Valós eszközök

Valódi eszközöket csatlakoztathat a megoldáshoz. A szimulált eszközök viselkedését az Azure IoT-eszköz SDK-k használatával valósíthatja meg.

Valós eszközöket hozhat létre az irányítópultról a megoldási portálon.

### <a name="device-simulation-microservice"></a>Eszközszimulációs mikroszolgáltatás

A megoldás tartalmazza az [eszközszimulációs mikroszolgáltatást,](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) amely lehetővé teszi a szimulált eszközök készletének kezelését a megoldásportálról a megoldás végpontok közötti folyamatának teszteléséhez. A szimulált eszközök:

* Eszközről a felhőbe irányuló telemetriai adatok létrehozása.
* Válaszoljon az IoT Hubról a felhőből az eszközre irányuló metódushívásokra.

A mikroszolgáltatás egy RESTful végpontot biztosít a szimulációk létrehozásához, indításához és leállításához. Minden szimuláció különböző típusú virtuális eszközök készletéből áll, amelyek telemetriai adatokat küldenek, és válaszolnak a metódushívásokra.

A megoldási portál irányítópultjáról kiépítheti a szimulált eszközöket.

### <a name="iot-hub"></a>IoT Hub

Az [IoT hub](../iot-hub/index.yml) betöltése telemetriai küldött mind a valós és szimulált eszközök a felhőbe. Az IoT hub elérhetővé teszi a telemetriai adatokat az IoT-megoldás háttér-a feldolgozáshoz.

A megoldásban az IoT Hub ezenkívül a következőket teszi:

* Identitásjegyzéket tart fenn, amely tárolja a portálhoz való csatlakozásra jogosult összes eszköz azonosítóit és hitelesítési kulcsait.
* Metódusok meghívása az eszközökön a megoldásgyorsító nevében.
* Az összes regisztrált eszközhöz biztosítja a megfelelő ikereszközt. Az ikereszközök tárolják az eszközök által jelentett tulajdonságértékeket. Az ikereszközök a megoldásportálon beállított kívánt tulajdonságokat is tárolják, amelyeket az eszköz a következő csatlakozáskor kérhet le.
* Feladatokat ütemez, hogy több eszközön is beállíthasson tulajdonságokat vagy meghívhasson metódusokat.

## <a name="data-processing-and-analytics"></a>Adatfeldolgozás és -elemzés

A megoldás a logikai architektúra adatfeldolgozási és elemzési részében a következő összetevőket tartalmazza:

### <a name="iot-hub-manager-microservice"></a>IoT Hub-kezelő mikroszolgáltatás

A megoldás tartalmazza az [IoT Hub-kezelő mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) az IoT hubbal való interakciók kezelésére, például:

* IoT-eszközök létrehozása és kezelése.
* Eszközikrek kezelése.
* Az eszközökre vonatkozó módszerek meghívása.
* IoT-hitelesítő adatok kezelése.

Ez a szolgáltatás is fut IoT Hub-lekérdezések a felhasználó által definiált csoportokhoz tartozó eszközök lekérése.

A mikroszolgáltatás egy RESTful végpontot biztosít az eszközök és az ikereszközök kezeléséhez, metódusok meghívásához és Az IoT Hub-lekérdezések futtatásához.

### <a name="device-telemetry-microservice"></a>Eszköztelemetriai mikroszolgáltatás

Az [eszköz telemetriai mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) egy RESTful végpont olvasási hozzáférést biztosít a Time Series Insights ban tárolt eszköz telemetriai adatok hoz. A RESTful végpont is lehetővé teszi crud műveletek szabályok és olvasási/írási hozzáférést riasztásdefiníciók a tárolóból.

### <a name="storage-adapter-microservice"></a>Tárolóadapter mikroszolgáltatása

A [tárolóadapter mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) kezeli a kulcs-érték párok, absztrakt a tárolási szolgáltatás szemantikája, és bemutatja egy egyszerű felület adatok tárolására bármilyen formátumban az Azure Cosmos DB használatával.

Az értékek gyűjteményekbe vannak rendezve. Egyéni értékeken dolgozhat, vagy teljes gyűjteményeket lehívhat. Az összetett adatstruktúrákat az ügyfelek szerializálják, és egyszerű szöveges hasznos adatként kezelik.

A szolgáltatás egy RESTful végpontot biztosít a kulcs-érték párok CRUD-műveletekhez. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

A megoldásgyorsító kreténtelepítései [az Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) használatával tárolják a szabályokat, riasztásokat, konfigurációs beállításokat és minden más hűtőterületet.

### <a name="azure-stream-analytics-manager-microservice"></a>Az Azure Stream Analytics-kezelő mikroszolgáltatása

Az [Azure Stream Analytics-kezelő mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) kezeli az Azure Stream Analytics (ASA) feladatokat, beleértve a konfigurációbeállításukat, indításukat és leállítását, valamint állapotuk figyelését.

Az ASA-feladatot két referenciaadatkészlet támogatja. Egy adatkészlet határozza meg a szabályokat, és egy eszközcsoportokat. A szabályok referenciaadatok az eszköz telemetriai mikroszolgáltatás által kezelt információkból jönlétre. Az Azure Stream Analytics-kezelő mikroszolgáltatás a telemetriai szabályokat adatfolyam-feldolgozási logikává alakítja.

Az eszközcsoportok referenciaadatai segítségével azonosítják, hogy a bejövő telemetriai üzenetekre melyik szabálycsoportot kell alkalmazni. Az eszközcsoportokat a konfigurációs mikroszolgáltatás kezeli, és az Azure IoT Hub-eszköz ikerlekérdezéseit használja.

Az ASA-feladatok biztosítják a telemetriai adatokat a csatlakoztatott eszközökről a Time Series Insights ba tárolásra és elemzésre.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Az Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) egy eseményfeldolgozó motor, amely lehetővé teszi, hogy megvizsgálja a nagy mennyiségű adat streamelése az eszközökről.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Az Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) tárolja a telemetriai adatokat a megoldásgyorsítóhoz csatlakoztatott eszközökről. Azt is lehetővé teszi, hogy vizualizálja és lekérdezi az eszköz telemetriai adatokat a megoldás webes felhasználói felületén.

### <a name="configuration-microservice"></a>Konfigurációs mikroszolgáltatás

A [konfigurációs mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) egy RESTful végpontot biztosít a CRUD-műveletekhez az eszközcsoportokon, a megoldásbeállításokon és a felhasználó beállításokon a megoldásgyorsítóban. Együttműködik a tárolóadapter mikroszolgáltatással a konfigurációs adatok megőrzéséhez.

### <a name="authentication-and-authorization-microservice"></a>Hitelesítési és engedélyezési mikroszolgáltatás

A [hitelesítési és engedélyezési mikroszolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) kezeli a megoldásgyorsító elérésére jogosult felhasználókat. A felhasználókezelés bármely olyan identitásszolgáltató használatával elvégezhető, amely támogatja az [OpenId Connect szolgáltatást.](https://openid.net/connect/)

### <a name="azure-active-directory"></a>Azure Active Directory

A megoldásgyorsító-alkalmazások az [Azure Active Directoryt](https://docs.microsoft.com/azure/active-directory/) OpenID Connect-szolgáltatóként használják. Az Azure Active Directory tárolja a felhasználói adatokat, és tanúsítványokat biztosít a JWT tokenaláírások érvényesítéséhez.

## <a name="presentation"></a>Megjelenítés

A megoldás a logikai architektúra bemutató részében a következő összetevőket tartalmazza:

A [webes felhasználói felület egy React Javascript alkalmazás](https://github.com/Azure/pcs-remote-monitoring-webui). Az alkalmazás:

* Csak Javascript React-et használ, és teljes egészében a böngészőben fut.
* Stílusú CSS.
* Az AJAX-hívásokon keresztül kommunikál a nyilvános mikroszolgáltatásokkal.

A felhasználói felület bemutatja az összes megoldásgyorsító funkciót, és más mikroszolgáltatásokkal is együttműködik, például:

* A felhasználói adatok védelmét szolgáló hitelesítési és engedélyezési mikroszolgáltatás.
* Az IoT Hub-kezelő mikroszolgáltatás az IoT-eszközök listázásához és kezeléséhez.

A felhasználói felület integrálja az Azure Time Series Insights explorer lehetővé teszi az eszköz telemetriai lekérdezésének és elemzésének engedélyezéséhez.

A konfigurációs mikroszolgáltatás lehetővé teszi a felhasználói felület számára a konfigurációs beállítások tárolását és beolvasását.

## <a name="next-steps"></a>További lépések

Ha meg szeretné vizsgálni a forráskódot és a fejlesztői dokumentációt, kezdje a két GitHub-adattárak egyikével:

* [Megoldásgyorsító az Azure IoT (.NET) távoli figyeléséhez.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Megoldásgyorsító az Azure IoT (Java) használatával történő távoli figyeléshez.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Részletes megoldásarchitektúra-diagramok:
* [Megoldásgyorsító a távoli figyelési architektúrához](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

A távfigyelési megoldásgyorsítóról a [Megoldásgyorsító testreszabása című](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)témakörben talál további általános tudnivalókat.
