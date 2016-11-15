---
title: "Azure IoT előre konfigurált megoldások | Microsoft Docs"
description: "Az Azure IoT előre konfigurált megoldások és az architektúrájuk leírása további erőforrások hivatkozásaival."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 597043b17993ebddc9cf730ddce849e1d6ff3bc9


---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Mik az Azure IoT előre konfigurált megoldások?
Az Azure IoT Suite előre konfigurált megoldások az általános IoT-megoldásminták megvalósításai, amelyeket az előfizetésével üzembe helyezhet az Azure-ban. A következőkre használhatja az előre konfigurált megoldásokat:

* A saját IoT-megoldásai kezdőpontjaként.
* Az IoT-megoldás kialakításakor és fejlesztésekor előforduló általános minták megismeréséhez.

Mindegyik előre konfigurált megoldás teljes körű megvalósítás, amelyben szimulált eszközök hoznak létre telemetriai adatokat.

A megoldások Azure-ban való üzembe helyezése és futtatása mellett letöltheti a teljes forráskódot, majd testreszabhatja és kibővítheti a megoldást, hogy megfeleljen saját IoT-követelményeinek.

> [!NOTE]
> Az egyik előre konfigurált megoldások üzembe helyezéséről lásd: [Microsoft Azure IoT Suite][lnk-azureiotsuite]. [Az IoT előre konfigurált megoldások bemutatása][lnk-getstarted-preconfigured] című cikk további információt tartalmaz a megoldások üzembe helyezéséről és futtatásáról.
> 
> 

A következő táblázatban láthatja, hogyan képezhetők le a megoldások adott IoT-funkciókra:

| Megoldás | Adatfeldolgozás | Eszközidentitás | Parancs és vezérlés | Szabályok és műveletek | Prediktív elemzés |
| --- | --- | --- | --- | --- | --- |
| [Távoli megfigyelés][lnk-getstarted-preconfigured] |Igen |Igen |Igen |Igen |- |
| [Prediktív karbantartás][lnk-predictive-maintenance] |Igen |Igen |Igen |Igen |Igen |

* *Adatfeldolgozás*: A felhőbe érkező nagy mennyiségű adatforgalom.
* *Eszközidentitás*: Minden csatlakoztatott eszköz egyedi identitásának kezelése.
* *Parancs és vezérlés*: Üzenetküldés egy eszközre a felhőből, hogy az eszköz végrehajtson egy műveletet.
* *Szabályok és műveletek*: A megoldás háttérrendszere felhasználói szabályokkal reagálnak adott, eszközről a felhőbe küldött adatokra.
* *Prediktív elemzés*: A megoldás háttérrendszere az eszközről a felhőbe küldött adatok elemzésével jelzi előre bizonyos műveletek időpontját. A repülőmotor telemetriájának elemzésével például meghatározható, mikor kell karbantartást végezni a motoron.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>A távoli figyelési előre konfigurált megoldás áttekintése
Ebben a cikkben a távoli figyelési előre konfigurált megoldást tárgyaljuk, mert ez sok általános, más megoldásokban is szereplő tervezési elemet bemutat.

A következő diagram a távoli figyelési megoldás fő elemeit ábrázolja. Az alábbi szakaszok további információt nyújtanak ezekről az elemekről.

![Távoli figyelési előre konfigurált megoldás architektúrája][img-remote-monitoring-arch]

## <a name="devices"></a>Eszközök
A távoli figyelési előre konfigurált megoldás üzembe helyezésekor négy szimulált eszköz van előre kiépítve a megoldásban, amelyek egy hűtőeszközt szimulálnak. Ezek a szimulált eszközök beépített hőmérséklet- és páratartalom-modellel rendelkeznek, amelyek telemetriai adatokat küldenek. Ezek a szimulált eszközök az adatok végpontok közötti folyamát mutatják be a megoldáson keresztül, valamint a telemetriák kényelmes forrását és a parancsok célját nyújtják a megoldást egyéni megvalósítás kezdőpontjaként használni kívánó háttérrendszer-fejlesztőknek.

Amikor egy eszköz először csatlakozik az IoT Hubhoz a távoli figyelési előre konfigurált megoldásban, az IoT Hubnak küldött eszközinformációs üzenet enumerálja azokat a parancsokat, amelyekre az eszköz válaszolhat. A távoli figyelési előre konfigurált megoldásban a következő parancsok használhatók: 

* *Eszköz pingelése*: Az eszköz nyugtázással válaszol erre a parancsra. Ez hasznos annak ellenőrzéséhez, hogy az eszköz továbbra is aktív és figyel-e.
* *Telemetria indítása*: A telemetria küldésének elkezdésére utasítja az eszközt.
* *Telemetria leállítása*: A telemetria küldésének leállítására utasítja az eszközt.
* *Megadott porthőmérséklet módosítása*: Az eszköz által küldött szimulált hőmérsékleti telemetriaértékeket vezérli. Ez a háttérlogika teszteléséhez hasznos.
* *Telemetria diagnosztikája*: Vezérli, hogy az eszköz telemetriaként küldje-e el a külső hőmérsékletet.
* *Eszközállapot módosítása*: Megadja az eszköz által jelentett eszközállapot metaadat-tulajdonságot. Ez a háttérlogika teszteléséhez hasznos.

További szimulált eszközöket adhat a megoldáshoz, amelyek ugyanazon telemetriát küldik, és ugyanazon parancsokra válaszolnak. 

## <a name="iot-hub"></a>IoT Hub
Ebben az előre konfigurált megoldásban az IoT Hub-példány egy jellemző [IoT-megoldásarchitektúra][lnk-what-is-azure-iot] *felhőátjárójának* felel meg.

Az IoT Hubok egyetlen végpontban fogadnak telemetriát az eszközökről. Az IoT Hubok az eszközspecifikus végpontokat is fenntartják, ahol az egyes eszközök lekérhetik a nekik küldött parancsokat.

Az IoT Hub a szolgáltatásoldali telemetriaolvasási végponton keresztül teszi elérhetővé a fogadott telemetriát.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Az előre konfigurált megoldás három [Azure Stream Analytics-][lnk-asa] (ASA-) feladattal szűri az eszközök telemetriastreamjét:

* *DeviceInfo munka* – adatokat küld egy eseményközpontba, amely az eszközregisztrációval kapcsolatos üzeneteket irányítja (amikor az eszközök először csatlakoznak vagy az **Eszközállapot módosítása** parancsra válaszul) a megoldás eszközjegyzékébe (DocumentDB-adatbázisba). 
* *Telemetriafeladat* – csak nyers telemetriát küld az Azure Blob Storage-ba offline tárolásra, és kiszámítja a megoldás irányítópultján megjelenő telemetriaösszesítéseket.
* *Szabályfeladat* – olyan értékeket szűr a telemetriastreamből, amelyek meghaladják valamelyik szabály küszöbértékét, és az adatokat egy eseményközpontba küldi. Amikor egy szabály riasztást vált ki, a megoldásportál irányítópult-nézete új sorban jeleníti meg az eseményt a riasztáselőzmények táblájában, és kivált egy műveletet a megoldásportál Szabályok és Művelet nézeteiben megadott beállítások alapján.

Ebben az előre konfigurált megoldásban az ASA-feladatok az **IoT-megoldás hátterének** részei a tipikus [IoT-megoldásarchitektúrában][lnk-what-is-azure-iot].

## <a name="event-processor"></a>Eseményfeldolgozó
Ebben az előre konfigurált megoldásban az eseményfeldolgozó az **IoT-megoldás hátterének** része a tipikus [IoT-megoldásarchitektúrában][lnk-what-is-azure-iot].

A **DeviceInfo** és **Rules** ASA-feladatok a kimenetüket egy eseményközpontokba küldik, amely más háttérszolgáltatásoknak továbbítja őket. A megoldás [WebJob][lnk-web-job] feladatban futó [EventPocessorHost][lnk-event-processor] példányt használ az eseményközpontokból érkező üzenetek olvasásához. Az **EventProcessorHost** a **DeviceInfo** adatokkal frissíti az eszközadatokat a DocumentDB-adatbázisban, és a **Rules** adatokkal indítja el a logikai alkalmazást, és frissíti a megoldásportálon megjelenő riasztásokat.

## <a name="device-identity-registry-and-documentdb"></a>Eszközidentitás-jegyzék és DocumentDB
Minden IoT Hub tartalmaz eszközkulcsokat tároló [eszközidentitás-jegyzéket][lnk-identity-registry]. Az IoT Hub ezekkel az információkkal hitelesíti az eszközöket – az eszközöknek regisztrálva kell lenniük és érvényes kulccsal kell rendelkezniük, hogy csatlakozhassanak a hubhoz.

Ez a megoldás további információt tárol az eszközökről, például az állapotukat, az általuk támogatott parancsokat és más metaadatokat. A megoldás DocumentDB-adatbázisban tárolja ezeket a megoldásspecifikus eszközadatokat, és a megoldásportál adatokat kér le ebből a DocumentDB-adatbázisból megjelenítés és szerkesztés céljából.

A megoldásnak az eszközidentitás-jegyzékben lévő információkat szinkronban is kell tartania a DocumentDB-adatbázis tartalmával. Az **EventProcessorHost** a **DeviceInfo** streamelemzési feladattal kezeli a szinkronizációt.

## <a name="solution-portal"></a>Megoldásportál
![A megoldás irányítópultja][img-dashboard]

A megoldásportál egy webalapú felhasználói felület, amely az előre konfigurált megoldás részeként van üzembe helyezve a felhőben. Ezzel a következőket teheti:

* Telemetria és riasztások előzményeinek megtekintése egy irányítópulton.
* Új eszközök kiépítése.
* Eszközök kezelése és figyelése.
* Parancsok küldése adott eszközökre.
* Szabályok és műveletek kezelése.

Ebben az előre konfigurált megoldásban a megoldásportál az **IoT-megoldás hátterének** és a **feldolgozási és üzleti kapcsolatoknak** a része a tipikus [IoT megoldásarchitektúrákban][lnk-what-is-azure-iot].

## <a name="next-steps"></a>Következő lépések
További információ az IoT-megoldások architektúrájáról: [Microsoft Azure IoT-szolgáltatások: referenciaarchitektúra][lnk-refarch].

Most, hogy már tudja, mik azok az előre konfigurált megoldások, üzembe helyezheti az előre konfigurált *távoli megfigyelési* megoldást: [Az előre konfigurált megoldások bemutatása][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md



<!--HONumber=Nov16_HO2-->


