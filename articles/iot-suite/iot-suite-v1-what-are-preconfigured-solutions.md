---
title: Azure IoT előre konfigurált megoldások | Microsoft Docs
description: Az Azure IoT előre konfigurált megoldások és az architektúrájuk leírása további erőforrások hivatkozásaival.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: d860c768a73737e6c8c52a8652d6b43434a3a07d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361988"
---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Mik az Azure IoT előre konfigurált megoldások?

Az Azure IoT Suite előre konfigurált megoldások az általános IoT-megoldásminták megvalósításai, amelyeket az előfizetésével üzembe helyezhet az Azure-ban. A következőkre használhatja az előre konfigurált megoldásokat:

* A saját IoT-megoldásai kezdőpontjaként.
* Az IoT-megoldás kialakításakor és fejlesztésekor előforduló általános minták megismeréséhez.

Mindegyik előre konfigurált megoldás teljes körű megvalósítás, amelyben szimulált eszközök hoznak létre telemetriai adatokat.

Letöltheti a teljes forráskódot, hogy saját IoT-követelményeinek megfelelően szabhassa testre és bővíthesse ki a megoldást.

> [!NOTE]
> Az egyik előre konfigurált megoldások üzembe helyezéséről lásd: [Microsoft Azure IoT Suite][lnk-azureiotsuite]. [Az IoT előre konfigurált megoldások bemutatása][lnk-getstarted-preconfigured] című cikk további információt tartalmaz a megoldások üzembe helyezéséről és futtatásáról.

A következő táblázatban láthatja, hogyan képezhetők le a megoldások adott IoT-funkciókra:

| Megoldás | Adatfeldolgozás | Eszközidentitás | Eszközfelügyelet | Parancs és vezérlés | Szabályok és műveletek | Prediktív elemzés |
| --- | --- | --- | --- | --- | --- | --- |
| [Távoli figyelés][lnk-getstarted-preconfigured] |Igen |Igen |Igen |Igen |Igen |- |
| [Prediktív karbantartás][lnk-predictive-maintenance] |Igen |Igen |- |Igen |Igen |Igen |
| [Csatlakoztatott gyár][lnk-getstarted-factory] |Igen |Igen |Igen |Igen |Igen |- |

* *Adatfeldolgozás*: A felhőbe érkező nagy mennyiségű adatforgalom.
* *Eszközidentitás*: Egyedi eszközidentitások kezelése és eszközhozzáférés biztosítása a megoldáshoz.
* *Eszközfelügyelet*: Eszköz metaadatainak kezelése és olyan műveletek elvégzése, mint az eszközök újraindítása és a belső vezérlőprogramok frissítése.
* *Parancs és vezérlés*: Üzenetküldés egy eszközre a felhőből, hogy az eszköz végrehajtson egy műveletet.
* *Szabályok és műveletek*: A megoldás háttérrendszere felhasználói szabályokkal reagál adott, eszközről a felhőbe küldött adatokra.
* *Prediktív elemzés*: A megoldás háttérrendszere az eszközről a felhőbe küldött adatok elemzésével jelzi előre bizonyos műveletek időpontját. A repülőmotor telemetriájának elemzésével például meghatározható, mikor kell karbantartást végezni a motoron.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>A távoli figyelési előre konfigurált megoldás áttekintése

Ebben a cikkben a távoli figyelési előre konfigurált megoldást tárgyaljuk, mert ez sok általános, más megoldásokban is szereplő tervezési elemet bemutat.

A következő diagram a távoli figyelési megoldás fő elemeit ábrázolja. A következő szakaszok további információt nyújtanak ezekről az elemekről.

![Távoli figyelési előre konfigurált megoldás architektúrája][img-remote-monitoring-arch]

## <a name="devices"></a>Eszközök

A távoli figyelési előre konfigurált megoldás üzembe helyezésekor négy szimulált eszköz van előre kiépítve a megoldásban, amelyek egy hűtőeszközt szimulálnak. Ezek a szimulált eszközök beépített hőmérséklet- és páratartalom-modellel rendelkeznek, amelyek telemetriai adatokat küldenek. Ezek a szimulált eszközök a következők érdekében vannak megadva:

- Bemutatják az adatok végpontok közötti folyamát a megoldáson keresztül.
- A telemetriák kényelmesen használható forrását nyújtják.
- A metódusok vagy parancsok célját biztosítják a megoldást egyéni implementálás kezdőpontjaként használni kívánó háttérrendszer-fejlesztőknek.

A megoldásban a szimulált eszközök a következő, az eszközök és a felhő közötti kommunikációra tudnak válaszolni:

- *Metódusok ([közvetlen metódusok][lnk-direct-methods])*: Kétirányú kommunikációs metódus, ahol egy csatlakoztatott eszköznek azonnal kell válaszolnia.
- *Parancsok (felhőből egy eszközre irányuló üzenetek)*: Egyirányú kommunikációs metódus, ahol egy eszköz tartós várólistáról kéri le a parancsot.

Ezen különböző megközelítések összehasonlításáért lásd a [felhőből egy eszközre irányuló kommunikáció útmutatóját][lnk-c2d-guidance].

Amikor egy eszköz először csatlakozik az IoT Hubhoz az előre konfigurált megoldásban, eszközinformációs üzenetet küld az IoT Hubnak. Ez az üzenet számba veszi azokat a metódusokat, amelyekre az eszköz válaszolhat. Az előre konfigurált távoli figyelési megoldásban a szimulált eszközök ezeket a metódusokat támogatják:

* *Belső vezérlőprogram frissítésének indítása*: ez a metódus egy aszinkron feladatot indít az eszközön a belső vezérlőprogram frissítésének elvégzése érdekében. Az aszinkron feladat jelentett tulajdonságokkal küld állapotfrissítéseket a megoldás irányítópultjának.
* *Újraindítás*: ez a metódus a szimulált eszköz újraindítását okozza.
* *FactoryReset*: ez a metódus a gyári beállítások visszaállítását okozza a szimulált eszközön.

Amikor egy eszköz először csatlakozik az IoT Hubhoz az előre konfigurált megoldásban, eszközinformációs üzenetet küld az IoT Hubnak. Ez az üzenet számba veszi azokat a parancsokat, amelyekre az eszköz válaszolhat. Az előre konfigurált távoli figyelési megoldásban a szimulált eszközök ezeket a parancsokat támogatják:

* *Eszköz pingelése*: Az eszköz nyugtázással válaszol erre a parancsra. Ez a parancs hasznos annak ellenőrzéséhez, hogy az eszköz továbbra is aktív és figyel-e.
* *Telemetria indítása*: A telemetria küldésének elkezdésére utasítja az eszközt.
* *Telemetria leállítása*: A telemetria küldésének leállítására utasítja az eszközt.
* *Megadott porthőmérséklet módosítása*: Az eszköz által küldött szimulált hőmérsékleti telemetriaértékeket vezérli. Ez a parancs a háttérlogika teszteléséhez hasznos.
* *Telemetria diagnosztikája*: Vezérli, hogy az eszköz telemetriaként küldje-e el a külső hőmérsékletet.
* *Eszközállapot módosítása*: Megadja az eszköz által jelentett eszközállapot metaadat-tulajdonságot. Ez a parancs a háttérlogika teszteléséhez hasznos.

További szimulált eszközöket adhat a megoldáshoz, amelyek ugyanazon telemetriát küldik, és ugyanazon metódusokra és parancsokra válaszolnak.

A parancsokra és metódusokra adott válaszon kívül a megoldás [ikereszközöket][lnk-device-twin] használ. Az eszközök ikereszközökkel jelentenek tulajdonságértékeket a metódus háttérrendszerének. A megoldás irányítópultja ikereszközökkel állítja be az új kívánt tulajdonságértékeket az eszközökön. Egy belső vezérlőprogram frissítési folyamata során például a szimulált eszköz a jelentett tulajdonságokkal jelenti a frissítés állapotát.

## <a name="iot-hub"></a>IoT Hub

Ebben az előre konfigurált megoldásban az IoT Hub-példány egy jellemző [IoT-megoldásarchitektúra][lnk-what-is-azure-iot] *felhőátjárójának* felel meg.

Az IoT Hubok egyetlen végpontban fogadnak telemetriát az eszközökről. Az IoT Hubok az eszközspecifikus végpontokat is fenntartják, ahol az egyes eszközök lekérhetik a nekik küldött parancsokat.

Az IoT Hub a szolgáltatásoldali telemetriaolvasási végponton keresztül teszi elérhetővé a fogadott telemetriát.

Az IoT Hub eszközfelügyeleti lehetőségeivel felügyelheti az eszköz tulajdonságait a megoldásportálról, valamint beütemezhet a következőkhöz hasonló műveleteket végző feladatokat:

- Eszközök újraindítása
- Eszközállapotok módosítása
- Belső vezérlőprogram frissítése

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Az előre konfigurált megoldás három [Azure Stream Analytics-][lnk-asa] (ASA-) feladattal szűri az eszközök telemetriastreamjét:

* *DeviceInfo feladat* – adatokat küld egy eseményközpontba, amely az eszközregisztrációval kapcsolatos üzeneteket irányítja a megoldás eszközjegyzékébe. Ez az eszközjegyzék egy Azure Cosmos DB-adatbázis. Ezeket az üzeneteket akkor küldi a rendszer, amikor az eszközök először csatlakoznak, vagy az **Eszközállapot módosítása** parancsra válaszul.
* *Telemetriafeladat* – csak nyers telemetriát küld az Azure Blob Storage-ba offline tárolásra, és kiszámítja a megoldás irányítópultján megjelenő telemetriaösszesítéseket.
* *Szabályfeladat* – olyan értékeket szűr a telemetriastreamből, amelyek meghaladják valamelyik szabály küszöbértékét, és az adatokat egy eseményközpontba küldi. Amikor egy szabály riasztást vált ki, a megoldásportál irányítópult-nézete egy új sorban jeleníti meg az eseményt a riasztáselőzmények táblázatában. Ezek a szabályok kiváltanak egy műveletet is a megoldásportál **Szabályok** és **Művelet** nézetében megadott beállítások alapján.

Ebben az előre konfigurált megoldásban az ASA-feladatok az **IoT-megoldás hátterének** részei a tipikus [IoT-megoldásarchitektúrában][lnk-what-is-azure-iot].

## <a name="event-processor"></a>Eseményfeldolgozó

Ebben az előre konfigurált megoldásban az eseményfeldolgozó az **IoT-megoldás hátterének** része a tipikus [IoT-megoldásarchitektúrában][lnk-what-is-azure-iot].

A **DeviceInfo** és **Rules** ASA-feladatok a kimenetüket egy eseményközpontokba küldik, amely más háttérszolgáltatásoknak továbbítja őket. A megoldás [WebJob][lnk-web-job] feladatban futó [EventProcessorHost][lnk-event-processor] példányt használ az eseményközpontokból érkező üzenetek olvasásához. Az **EventProcessorHost** a következőket használja:
- A **DeviceInfo**-adatokkal frissíti az eszközadatokat a Cosmos DB-adatbázisban.
- A **Rules**-adatokkal indítja el a logikai alkalmazást, és frissíti a megoldásportálon megjelenő riasztásokat.

## <a name="device-identity-registry-device-twin-and-cosmos-db"></a>Eszközidentitás-jegyzék, ikereszköz és Cosmos DB

Minden IoT Hub tartalmaz eszközkulcsokat tároló [eszközidentitás-jegyzéket][lnk-identity-registry]. Az IoT Hub ezekkel az információkkal hitelesíti az eszközöket – az eszközöknek regisztrálva kell lenniük és érvényes kulccsal kell rendelkezniük, hogy csatlakozhassanak a hubhoz.

Az [ikereszközök][lnk-device-twin] az IoT Hub által felügyelt JSON-dokumentumok. Az eszközök ikereszköze a következőket tartalmazza:

- Az eszköz által az IoT Hubra küldött jelentett tulajdonságok. Ezeket a tulajdonságokat a megoldásportálon tekintheti meg.
- Az eszközre küldendő kívánt tulajdonságok. Ezeket a tulajdonságokat a megoldásportálon állíthatja be.
- Csak az ikereszközön (nem az eszközön) létező címkék. Ezekkel a címkékkel szűrheti a megoldásportálon az eszközök listáját.

Ez a megoldás ikereszközökkel kezeli az eszköz metaadatait. A megoldás Cosmos DB-adatbázist is használ a megoldással kapcsolatos további eszközadatok, például az egyes eszközök által támogatott parancsok és a parancselőzmények tárolásához.

A megoldásnak az eszközidentitás-jegyzékben lévő információkat szinkronban is kell tartania a Cosmos DB-adatbázis tartalmával. Az **EventProcessorHost** a **DeviceInfo** streamelemzési feladattal kezeli a szinkronizációt.

## <a name="solution-portal"></a>Megoldásportál

![megoldásportál][img-dashboard]

A megoldásportál egy webalapú felhasználói felület, amely az előre konfigurált megoldás részeként van üzembe helyezve a felhőben. Ezzel a következőket teheti:

* Telemetria és riasztások előzményeinek megtekintése egy irányítópulton.
* Új eszközök kiépítése.
* Eszközök kezelése és figyelése.
* Parancsok küldése adott eszközökre.
* Metódusok meghívása adott eszközökön.
* Szabályok és műveletek kezelése.
* Feladatok ütemezése az egy vagy több eszközön történő futtatáshoz.

Ebben az előre konfigurált megoldásban a megoldásportál az **IoT-megoldás hátterének** és a **feldolgozási és üzleti kapcsolatoknak** a része a tipikus [IoT-megoldásarchitektúrákban][lnk-what-is-azure-iot].

## <a name="next-steps"></a>További lépések

További információ az IoT-megoldásarchitektúrákról: [Microsoft Azure IoT-szolgáltatások: referenciaarchitektúra][lnk-refarch].

Most, hogy már tudja, mik azok az előre konfigurált megoldások, üzembe helyezheti az előre konfigurált *távoli megfigyelési* megoldást: [Az előre konfigurált megoldások bemutatása][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-v1-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-v1-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-getstarted-factory]:../iot-accelerators/iot-accelerators-connected-factory-overview.md
