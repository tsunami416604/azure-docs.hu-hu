---
title: A Microsoft Azure IoT Suite áttekintése | Microsoft Docs
description: A cikk áttekinti, hogyan biztosít az Azure IoT Suite az eszközök internetes hálózatán alapuló, előre konfigurált megoldásokat adatok gyűjtéséhez, elemzéséhez és tárolásához, megjelenítések biztosításához, valamint a más rendszerekkel való integrációhoz.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2016
ms.author: dobett

---
# Mi az Azure IoT Suite?
Az Azure eszközök internetes hálózata (IoT) szolgáltatások a képességek széles választékát nyújtják. Ezekkel a vállalati szintű szolgáltatásokkal a következőket teheti:

* Adatok gyűjtése eszközökről
* Streamek elemzése mozgás közben
* Nagy adatkészletek tárolása és lekérdezése
* Valós idejű és előzményadatok megjelenítése
* Integrálás irodai háttérrendszerekkel

Ezen képességek biztosítása érdekében az Azure IoT Suite több Azure-szolgáltatást kínál egyéni bővítményekkel *előre konfigurált megoldásokként*. Ezek az előre konfigurált megoldások az általános IoT -megoldásminták alapszintű megvalósításai, amelyek segítenek csökkenteni az IoT-megoldások szolgáltatásához szükséges időt. Az [IoT szoftverfejlesztői készletekkel][lnk-sdks] testreszabhatja és kibővítheti ezeket a megoldásokat, hogy megfeleljenek a saját követelményeinek. Ezeket a megoldásokat példaként vagy sablonként is használhatja az új IoT-megoldások fejlesztésekor.

A következő videó az Azure IoT Suite bemutatását tartalmazza:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## Azure IoT-szolgáltatások az Azure IoT Suite-ban
Az előre konfigurált megoldások általában a következő szolgáltatásokat használják:

* Az Azure IoT Suite középpontjában az [Azure IoT Hub][lnk-iot-hub] szolgáltatás áll. Ez a szolgáltatás az eszközökről a felhőbe irányuló és a felhőből az eszközre irányuló üzenetküldési képességeket nyújt, és a felhő és a többi fontos IoT-szolgáltatás átjárójaként működik. A szolgáltatás lehetővé teszi, hogy nagy mennyiségű üzenetet fogadjon az eszközökről, és parancsokat küldjön az eszközökre.
* Az [Azure Stream Analytics][lnk-asa] a mozgásban lévő adatok elemzését nyújtja. Az IoT Suite ezzel a szolgáltatással dolgozza fel a bejövő telemetriát, végez összesítést és észlel eseményeket. Az előre konfigurált megoldások is a Stream Analytics eszközzel dolgozzák fel azokat a tájékoztató üzeneteket, amelyek például metaadatokat vagy eszközök parancsválaszait tartalmazzák. A megoldások a Stream Analytics használatával dolgozzák fel az eszközökről kapott üzeneteket, és ezeket az üzeneteket más szolgáltatásokhoz küldik.
* Az [Azure Storage][lnk-azure-storage] és az [Azure DocumentDB][lnk-document-db] biztosítja az adattárolási képességeket. Az előre konfigurált megoldások a Blob Storage-ban tárolják és teszik elérhetővé elemzésre a telemetriát. A megoldások a DocumentDB-ben tárolják az eszközök metaadatait, és engedélyezik a megoldások eszközfelügyeleti képességeit.
* Az [Azure Web Apps][lnk-web-apps] és a [Microsoft Power BI][lnk-power-bi] biztosítja az adatmegjelenítési képességeket. A Power BI rugalmasságával gyorsan építheti fel a saját, IoT Suite-adatokat használó interaktív irányítópultjait.

A tipikus IoT-megoldások architektúrájának áttekintéséért lásd: [Microsoft Azure és az eszközök internetes hálózata (IoT)][iot-suite-what-is-azure-iot].

## Előre konfigurált megoldások
Az IoT Suite előre konfigurált megoldásokat tartalmaz, amelyekkel gyorsan teheti meg az első lépéseket és fedezheti fel az Azure IoT Suite által biztosított általános IoT-forgatókönyveket, például a *távoli megfigyelést* és a *prediktív karbantartást*. Ezeket a megoldásokat az Azure-előfizetésre telepítheti, majd egy teljes körű IoT-forgatókönyvet futtathat.

## Következő lépések
Most, hogy áttekintette az IoT Suite képességeit és fő összetevőit, többet is megtudhat az IoT Suite előre konfigurált megoldásairól a következő cikkből: [Mik az Azure IoT előre konfigurált megoldásai?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md



<!--HONumber=Sep16_HO4-->


