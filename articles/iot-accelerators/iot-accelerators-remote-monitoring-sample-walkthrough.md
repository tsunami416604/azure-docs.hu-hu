---
title: A távoli figyelési megoldás gyorsítása – áttekintés – Azure | Microsoft Docs
description: Ez a cikk áttekintést nyújt a távoli figyelési megoldás egyes kulcsfontosságú elemeiről, hogy megtudja, hogyan működik.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: f501eb55f72811063ddf1d8e02a0ce2137d598f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80546312"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>A távoli monitorozási megoldásgyorsító áttekintése

A távoli figyelési [megoldás gyorsítása](../iot-accelerators/about-iot-accelerators.md) teljes körű figyelési megoldást valósít meg több, távoli helyen lévő gép számára. A megoldás fontos Azure-szolgáltatások kombinációját kínálja az üzleti forgatókönyv általános megvalósítása érdekében. A megoldást kiindulási pontként használhatja a saját implementációjához, és a saját üzleti igényeinek megfelelően [testreszabhatja](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) azt.

Ez a cikk végigvezeti a távoli figyelési megoldás néhány kulcsfontosságú elemén, így megismerheti, hogyan működik. Ezeknek az ismereteknek a birtokában:

* Elháríthatja a megoldásban felmerülő hibákat.
* Megtervezheti, hogy miképpen érdemes testre szabni a megoldást úgy, hogy az megfeleljen egyedi igényeinek.
* Kialakíthatja saját, Azure-szolgáltatásokat használó IoT-megoldását.

A távoli figyelési megoldás gyorsító kódja a GitHubon érhető el:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Logikai architektúra

Az alábbi ábra a távoli figyelési megoldás gyorsított részének logikai összetevőit vázolja fel a [IoT architektúrán](../iot-fundamentals/iot-introduction.md):

![Logikai architektúra](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Miért a Services?

A Felhőbeli architektúra fejlődött, mivel a Microsoft kiadta az első megoldás-gyorsító. A [szolgáltatás](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) bevált gyakorlatnak bizonyult a méretezés és a rugalmasság elérése érdekében a fejlesztési sebesség feláldozása nélkül. Számos Microsoft-szolgáltatás alkalmazza ezt az építészeti mintát belsőleg a megbízhatósági és méretezhetőségi eredményekkel. A frissített megoldás-gyorsítók ezeket a betanulást a gyakorlatba helyezik, így Ön is kihasználhatja őket.

> [!TIP]
> További információk a mikroszolgáltatás-architektúrákról: [.NET-alkalmazás architektúrája](https://www.microsoft.com/net/learn/architecture) és [Mikroszolgáltatások: egy felhőben zajló alkalmazásforradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Eszközkapcsolatok

A megoldás a következő összetevőket tartalmazza a logikai architektúra eszköz kapcsolati részében:

### <a name="real-devices"></a>Valós eszközök

A valódi eszközöket összekapcsolhatjuk a megoldással. A szimulált eszközök viselkedését az Azure IoT Device SDK-k használatával valósíthatja meg.

Az irányítópultról kiépítheti a valódi eszközöket a megoldás-portálon.

### <a name="device-simulation-microservice"></a>Eszköz-szimulációs szolgáltatás

A megoldás magában foglalja a [Device szimulációs szolgáltatást](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) , amely lehetővé teszi a szimulált eszközök készletének kezelését a megoldás-portálról a megoldás végpontok közötti folyamatának teszteléséhez. A szimulált eszközök:

* Eszközről a felhőbe irányuló telemetria előállítása.
* Válaszoljon a felhőből az eszközre irányuló metódusokra IoT Hub.

A Service egy REST-végpontot biztosít a szimulációk létrehozására, indítására és leállítására. Minden szimuláció különböző típusú virtuális eszközökből áll, amelyek telemetria küldenek, és válaszolnak a metódus hívására.

Szimulált eszközöket az irányítópultról is kiépítheti a megoldás-portálon.

### <a name="iot-hub"></a>IoT Hub

Az [IoT hub](../iot-hub/index.yml) a valós és szimulált eszközökről a felhőbe érkező telemetria is betölti. Az IoT hub lehetővé teszi a telemetria számára a IoT-megoldási háttérbeli szolgáltatások számára a feldolgozást.

A megoldásban az IoT Hub ezenkívül a következőket teszi:

* Egy olyan identitás-nyilvántartót tart fenn, amely a portálhoz való kapcsolódásra engedélyezett összes eszköz azonosítóit és hitelesítési kulcsait tárolja.
* Metódusokat hív meg az eszközökön a megoldás-gyorsító nevében.
* Az összes regisztrált eszközhöz biztosítja a megfelelő ikereszközt. Az ikereszközök tárolják az eszközök által jelentett tulajdonságértékeket. Az ikereszközök a megoldásportálon beállított kívánt tulajdonságokat is tárolják, amelyeket az eszköz a következő csatlakozáskor kérhet le.
* Feladatokat ütemez, hogy több eszközön is beállíthasson tulajdonságokat vagy meghívhasson metódusokat.

## <a name="data-processing-and-analytics"></a>Adatfeldolgozás és -elemzés

A megoldás a következő összetevőket tartalmazza a logikai architektúra adatfeldolgozási és elemzési részében:

### <a name="iot-hub-manager-microservice"></a>IoT Hub Manager-szolgáltatás

A megoldás tartalmazza a [IoT hub Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) -alapú szolgáltatást, amely az IoT-hubhoz való interakciókat kezeli, például:

* IoT-eszközök létrehozása és kezelése.
* Az eszközök az ikrek kezelése.
* Metódusok meghívása az eszközökön.
* IoT hitelesítő adatainak kezelése.

A szolgáltatás IoT Hub lekérdezéseket is futtat a felhasználó által definiált csoportokhoz tartozó eszközök lekéréséhez.

A szolgáltatás egy REST-végpontot biztosít az eszközök és eszközök ikrek kezelésére, metódusok meghívására és IoT Hub lekérdezések futtatására.

### <a name="device-telemetry-microservice"></a>Device telemetria Service

A [Device telemetria Service](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) egy Rest-végpontot biztosít a Time Series Insightsban tárolt eszköz-telemetria olvasási hozzáféréséhez. A REST-végpont lehetővé teszi a szabályok és az írási/olvasási hozzáférés használatát a tárolóból.

### <a name="storage-adapter-microservice"></a>Storage-adapter-szolgáltatás

A [Storage-adapter-szolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) kezeli a kulcs-érték párokat, a tárolási szolgáltatás szemantikai összevonását, és egy egyszerű felületet mutat be, amely bármilyen formátumú adattárolást tartalmaz Azure Cosmos db használatával.

Az értékek gyűjteményekbe vannak rendezve. Az egyes értékeken dolgozhat, vagy teljes gyűjteményeket is beolvashat. Az összetett adatstruktúrákat az ügyfelek szerializálják, és egyszerű szöveges adattartalomként kezelhetők.

A szolgáltatás egy REST-végpontot biztosít a szifilisz-műveletekhez a kulcs-érték párokon. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

A megoldás-gyorsító környezetekben a [Azure Cosmos db](https://docs.microsoft.com/azure/cosmos-db/) a szabályok, a riasztások, a konfigurációs beállítások és az összes többi hideg tárterület tárolására használható.

### <a name="azure-stream-analytics-manager-microservice"></a>Azure Stream Analytics Manager-szolgáltatás

A [Azure stream Analytics Manager szolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) kezeli a Azure stream Analytics (ASA) feladatokat, beleértve a konfiguráció beállítását, a megkezdését és leállítását, valamint az állapotuk figyelését.

Az ASA-feladatot két hivatkozási adatkészlet támogatja. Egy adatkészletben a szabályok és az egyik definiált eszközosztály szerepel. A szabályok hivatkozási adatai a Device telemetria Service által kezelt információk alapján jönnek létre. A Azure Stream Analytics Manager szolgáltatás a telemetria-szabályokat adatfolyam-feldolgozási logikára alakítja át.

Az eszközcsoport-referenciák segítségével azonosíthatók a bejövő telemetria-üzenetekre alkalmazandó szabályok. Az eszközök csoportjait a Configuration Service felügyeli, és az Azure IoT Hub Device Twin lekérdezéseket használja.

Az ASA-feladatok biztosítják a telemetria a csatlakoztatott eszközökről Time Series Insights a tároláshoz és az elemzéshez.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) egy eseményvezérelt motor, amely lehetővé teszi az eszközökről érkező nagy mennyiségű adatfolyam vizsgálatát.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) a telemetria a megoldás-gyorssegédhez csatlakoztatott eszközökről tárolja. Emellett lehetővé teszi az eszközök telemetria megjelenítését és lekérdezését a megoldás webes felhasználói felületén.

### <a name="configuration-microservice"></a>Konfigurációs szolgáltatás

A [konfigurációs szolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) egy Rest-végpontot biztosít a szifiliszi műveletekhez az erőforráscsoportok, a megoldás beállításai és a felhasználói beállítások között a megoldás-gyorsító eszközben. Együttműködik a Storage-adapter-szolgáltatással, hogy megmaradjon a konfigurációs adatgyűjtés.

### <a name="authentication-and-authorization-microservice"></a>Hitelesítési és engedélyezési szolgáltatás

A [hitelesítési és engedélyezési szolgáltatás](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) kezeli azokat a felhasználókat, akik jogosultak a megoldás-gyorsító elérésére. A felhasználói felügyelet bármely, az [OpenId connectet](https://openid.net/connect/)támogató Identitáskezelő-szolgáltató használatával végezhető el.

### <a name="azure-active-directory"></a>Azure Active Directory

A megoldás-gyorsító telepítések OpenID Connect-szolgáltatóként [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) használnak. Azure Active Directory tárolja a felhasználói adatokat, és tanúsítványokat biztosít a JWT-jogkivonat aláírásának ellenőrzéséhez.

## <a name="presentation"></a>Megjelenítés

A megoldás a következő összetevőket tartalmazza a logikai architektúra bemutató részében:

A [webes felhasználói felület egy reagálásra szolgáló JavaScript-alkalmazás](https://github.com/Azure/pcs-remote-monitoring-webui). Az alkalmazás:

* Kizárólag a JavaScriptet használja, és kizárólag a böngészőben fut.
* A CSS stílusú.
* Az AJAX-hívásokon keresztül kommunikál a nyilvános szolgáltatásokkal.

A felhasználói felület a megoldás-gyorsító összes funkcióját mutatja be, és együttműködik más szolgáltatásokkal, például a következőkkel:

* A hitelesítési és engedélyezési szolgáltatás a felhasználói adatainak védelme érdekében.
* A IoT Hub Manager-szolgáltatás a IoT-eszközök listázásához és kezeléséhez.

A felhasználói felület integrálja a Azure Time Series Insights Explorert, hogy lehetővé tegye az eszközök telemetria lekérdezését és elemzését.

A konfigurálási szolgáltatás lehetővé teszi a felhasználói felület számára a konfigurációs beállítások tárolását és beolvasását.

## <a name="next-steps"></a>További lépések

Ha meg szeretné vizsgálni a forráskódot és a fejlesztői dokumentációt, kezdje a következő két GitHub-tárház egyikével:

* [Megoldás-gyorsító távoli figyeléshez az Azure IoT (.net)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)használatával.
* [Megoldás-gyorsító távoli figyeléshez az Azure IoT (Java) szolgáltatással](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Részletes megoldási architektúrák diagramjai:
* [Megoldás-gyorsító a távoli figyelési architektúrához](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

A távoli figyelési megoldás-gyorsító részletes ismertetését lásd: [a megoldás-gyorsító testreszabása](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
