---
title: "Az Azure IoT Suite csatlakoztatott gyár megoldásának bemutatója | Microsoft Docs"
description: "Az Azure IoT előre konfigurált csatlakoztatott gyár megoldásának és architektúrájának leírása."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 3011fd608ba83561c319e57c8a7b5a4f3c4c2284
ms.contentlocale: hu-hu
ms.lasthandoff: 05/26/2017


---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>Előre konfigurált csatlakoztatott gyár megoldás – bemutató

Az IoT Suite [előre konfigurált][lnk-preconfigured-solutions] csatlakoztatott gyár megoldással egy teljes körű ipari megoldást implementálhat, amely:

* Szimulált gyártósorokon OPC UA-kiszolgálókat futtató szimulált ipari eszközökhöz és valódi OPC UA-kiszolgálóeszközökhöz is csatlakozik. Az OPC UA architektúráról további információt talál a [gyakran ismételt kérdések][lnk-faq] között.
* Megjeleníti ezen eszközök és gyártósorok működési KPI- és OEE-értékeit.
* Bemutatja, hogyan lehet kommunikálni az OPC UA-kiszolgálórendszerekkel felhőalapú alkalmazások használatával.
* Lehetővé teszi, hogy összekapcsolja saját OPC UA-kiszolgálóeszközeit.
* Lehetővé teszi az OPC UA-kiszolgálóadatok tallózását és módosítását.
* Integrálható az Azure Time Series Insights (TSI) szolgáltatással, amely testreszabott nézeteket biztosít az OPC UA-kiszolgálók adatairól.

A megoldást kiindulási pontként használhatja a saját implementációjához, és a saját üzleti igényeinek megfelelően [testreszabhatja][lnk-customize] azt.

Ebben a cikkben bemutatjuk a csatlakoztatott gyár megoldás néhány fontos elemét, hogy jobban megismerhesse a szolgáltatás működését. Ezeknek az ismereteknek a birtokában:

* Elháríthatja a megoldásban felmerülő hibákat.
* Megtervezheti, hogy miképpen érdemes testre szabni a megoldást úgy, hogy az megfeleljen egyedi igényeinek.
* Kialakíthatja saját, Azure-szolgáltatásokat használó IoT-megoldását.

## <a name="logical-architecture"></a>Logikai architektúra

A következő diagram az előre konfigurált megoldás logikai összetevőit vázolja fel:

![Csatlakoztatott gyár logikai architektúrája][connected-factory-logical]

## <a name="simulation"></a>Szimuláció

A gyártósorok szimulált állomásokból és szimulált gyártási végrehajtó rendszerekből (MES) állnak. A szimulált eszközök és az OPC kiadói modul az OPC Foundation által kiadott [OPC UA .NET standardon][lnk-OPC-UA-NET-Standard] alapul.

Az OPC Proxy és az OPC Publisher modulként van megvalósítva az [Azure IoT Edge][lnk-Azure-IoT-Gateway] alapján. Mindegyik szimulált gyártósorhoz kijelölt átjáró van csatlakoztatva.

Minden szimulációs összetevő Azure Linux virtuális gépen futtatott Docker-tárolókban fut. A szimuláció úgy van konfigurálva, hogy alapértelmezés szerint nyolc szimulált gyártósort futtasson.

## <a name="simulated-production-line"></a>Szimulált gyártósor

A gyártósorok alkatrészeket gyártanak. Különböző állomásokból állnak: összeszerelő állomásból, tesztelő állomásból és csomagoló állomásból.

A szimuláció futtatja és frissíti az OPC UA-csomópontokon keresztül közzétett adatokat. Minden szimulált gyártósori állomást a MES vezényel az OPC UA-n keresztül.

## <a name="simulated-manufacturing-execution-system"></a>Szimulált gyártási végrehajtó rendszer

A MES az OPC UA-n keresztül figyeli a gyártósor egyes állomásait azok állapotváltozásainak észlelése érdekében. OPC UA-metódusokat hív meg az állomások vezérlése érdekében, és egyik állomásról a következőre továbbítja a termékeket a befejezésükig.

## <a name="gateway-opc-publisher-module"></a>Átjáró OPC kiadói modul

Az OPC kiadói modul az állomás OPC UA-kiszolgálóihoz csatlakozik és feliratkozik a kiadandó OPC-csomópontokra. A modul a csomópontadatokat JSON-formátumba alakítja, titkosítja, majd az IoT Hubra küldi OPC UA Pub/Sub üzenetekként.

Az OPC kiadói modulnak csak kimenő https-portra (443) van szüksége, és képes együttműködni a meglévő vállalati infrastruktúrával.

## <a name="gateway-opc-proxy-module"></a>Átjáró OPC-proxymodul

Az átjáró OPC UA-proxymodul bináris OPC UA-parancs- és vezérlési üzeneteket továbbít, és csak kimenő https-portot (443) igényel. Képes a meglévő vállalati infrastruktúrát használni, beleértve a webes proxykat is.

IoT Hub-eszközmetódusokkal viszi át a csomagolt TCP/IP-adatokat az alkalmazásrétegen, így az SSL/TLS segítségével biztosítja a végpontok megbízhatóságát, az adatok titkosítását és az integritást.

A magán a proxyn keresztül továbbított OPC UA bináris protokoll UA-hitelesítést és -titkosítást használ.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

Az átjáró OPC kiadói modul feliratkozik az OPC UA-kiszolgálócsomópontokra az adatértékek változásainak észlelése érdekében. Ha a rendszer adatmódosítást észlel valamelyik csomóponton, ez a modul üzeneteket küld az Azure IoT Hubnak.

Az IoT Hub eseményforrást biztosít az Azure TSI-nek. A TSI 30 napig tárol adatokat az üzenetekhez csatolt időbélyegek alapján. A adatok tartalma:

* OPC UA ApplicationUri
* OPC UA NodeId
* A csomópont értéke
* Forrás időbélyege
* OPC UA DisplayName

A TSI jelenleg nem engedélyezi, hogy az ügyfelek testreszabják, mennyi ideig szeretnék megőrizni az adatokat.

A TSI a csomópontadatok lekérdezését egy SearchSpan (Time.From, Time.To), az összesítést pedig az OPC UA ApplicationUri, az OPC UA NodeId vagy az OPC UA DisplayName attribútum alapján végzi.

Az OEE- és a KPI-mérőműszer adatainak és az idősorozat-diagramok lekérdezése érdekében a rendszer az események száma, valamint a Sum, az Avg, a Min és a Max értékek alapján összesíti az adatokat.

Az idősorozatok más folyamattal vannak felépítve. Az OEE és a KPI-k állomások alapadataiból vannak kiszámítva és buborékba vannak rendezve a topológiához (gyártósorok, gyárak, vállalat) az alkalmazásban.

Ezenkívül az OEE- és a KPI-topológia idősorozatai az alkalmazásban lesznek kiszámítva, amikor egy megjelenített időtartomány készen áll. A nap nézet például minden teljes órában frissül.

A csomópontadatok idősorozat nézete közvetlenül a TSI-ból származik az időtartomány összegzése segítségével.

## <a name="iot-hub"></a>IoT Hub
Az [IoT hub][lnk-IoT Hub] fogadja az OPC kiadói modulból a felhőbe küldött adatokat, és elérhetővé teszi azokat az Azure TSI szolgáltatás számára. 

A megoldásban az IoT Hub ezenkívül a következőket teszi:
- Fenntart egy identitásjegyzéket, amely tárolja az összes OPC kiadói modul és az összes OPC-proxymodul azonosítóját.
- Átviteli csatornaként szolgál az OPC-proxymodul kétirányú kommunikációjához.

## <a name="azure-storage"></a>Azure Storage
A megoldás Azure Blob Storage tárolót használ a virtuális gép lemezes tárolójaként, valamint az üzembehelyezési adatok tárolásához.

## <a name="web-app"></a>Webalkalmazás
Az előre konfigurált megoldás részeként üzembe helyezett webalkalmazás integrált OPC UA-ügyfélből, riasztások feldolgozásából és a telemetria megjelenítéséből áll.

## <a name="next-steps"></a>Következő lépések

Folytassa az IoT Suite megismerését az alábbi cikkek elolvasásával:

* [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions]

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
