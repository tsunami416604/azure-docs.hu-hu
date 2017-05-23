---
title: "A Microsoft Azure IoT Suite áttekintése | Microsoft Docs"
description: "A cikk áttekinti, hogyan biztosít az Azure IoT Suite az eszközök internetes hálózatán alapuló, előre konfigurált megoldásokat adatok gyűjtéséhez, elemzéséhez és tárolásához, megjelenítések biztosításához, valamint a más rendszerekkel való integrációhoz."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c57c0ddbe8e33d77c6f07f58dfe3236733f6c62f
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="overview-of-azure-iot-suite"></a>Az Azure IoT Suite áttekintése

Az Azure eszközök internetes hálózata (IoT) szolgáltatások a képességek széles választékát nyújtják. Ezekkel a vállalati szintű szolgáltatásokkal a következőket teheti:

* Adatok gyűjtése eszközökről
* Streamek elemzése mozgás közben
* Nagy adatkészletek tárolása és lekérdezése
* Valós idejű és előzményadatok megjelenítése
* Integrálás irodai háttérrendszerekkel
* Eszközök kezelése

Ezen képességek biztosítása érdekében az Azure IoT Suite több Azure-szolgáltatást kínál egyéni bővítményekkel *előre konfigurált megoldásokként*. Ezek az előre konfigurált megoldások az általános IoT -megoldásminták alapszintű megvalósításai, amelyek segítenek csökkenteni az IoT-megoldások szolgáltatásához szükséges időt. Az [IoT szoftverfejlesztői készletekkel][lnk-sdks] testreszabhatja és kibővítheti ezeket a megoldásokat, hogy megfeleljenek a saját követelményeinek. Ezeket a megoldásokat példaként vagy sablonként is használhatja az új IoT-megoldások fejlesztésekor.

A következő videó az Azure IoT Suite bemutatását tartalmazza:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## <a name="azure-iot-services-in-azure-iot-suite"></a>Azure IoT-szolgáltatások az Azure IoT Suite-ban
Az előre konfigurált megoldások általában a következő szolgáltatásokat használják:

* Az Azure IoT Suite középpontjában az [Azure IoT Hub][lnk-iot-hub] szolgáltatás áll. Ez a szolgáltatás az eszközökről a felhőbe irányuló és a felhőből az eszközre irányuló üzenetküldési képességeket nyújt, és a felhő és a többi fontos IoT-szolgáltatás átjárójaként működik. A szolgáltatás lehetővé teszi, hogy nagy mennyiségű üzenetet fogadjon az eszközökről, és parancsokat küldjön az eszközökre. A szolgáltatással [felügyelheti is az eszközöket][lnk-device-management]. Konfigurálhat vagy újraindíthat például az IoT Hubhoz csatlakozó egy vagy több eszközt, illetve visszaállíthatja rajtuk a gyári beállításokat.
* Az [Azure Stream Analytics][lnk-asa] a mozgásban lévő adatok elemzését nyújtja. Az IoT Suite ezzel a szolgáltatással dolgozza fel a bejövő telemetriát, végez összesítést és észlel eseményeket. Az előre konfigurált megoldások is a Stream Analytics eszközzel dolgozzák fel azokat a tájékoztató üzeneteket, amelyek például metaadatokat vagy eszközök parancsválaszait tartalmazzák. A megoldások a Stream Analytics használatával dolgozzák fel az eszközökről kapott üzeneteket, és ezeket az üzeneteket más szolgáltatásokhoz küldik.
* Az [Azure Storage][lnk-azure-storage] és az [Azure Cosmos DB][lnk-document-db] biztosítja az adattárolási képességeket. Az előre konfigurált megoldások a Blob Storage-ban tárolják és teszik elérhetővé elemzésre a telemetriát. A megoldások a Cosmos DB-ben tárolják az eszközök metaadatait, és engedélyezik a megoldások eszközfelügyeleti képességeit.
* Az [Azure Web Apps][lnk-web-apps] és a [Microsoft Power BI][lnk-power-bi] biztosítja az adatmegjelenítési képességeket. A Power BI rugalmasságával gyorsan építheti fel a saját, IoT Suite-adatokat használó interaktív irányítópultjait.

A tipikus IoT-megoldások architektúrájának áttekintéséért lásd a [Microsoft Azure-t és az eszközök internetes hálózatát (IoT)][iot-suite-what-is-azure-iot] ismertető cikket.

## <a name="preconfigured-solutions"></a>Előre konfigurált megoldások

Az IoT Suite előre konfigurált megoldásokat tartalmaz, amelyekkel gyorsan teheti meg az első lépéseket és fedezheti fel az általános IoT-forgatókönyveket, például a *távoli figyelést*, a *prediktív karbantartást* és a *csatlakoztatott gyárat*. Ezeket a megoldásokat az Azure-előfizetésre telepítheti, majd egy teljes körű IoT-forgatókönyvet futtathat.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az IoT Suite képességeit és fő összetevőit, többet is megtudhat az IoT Suite előre konfigurált megoldásairól. További információkért lásd [az Azure IoT előre konfigurált megoldásait][lnk-what-are-preconfig] ismertető cikket.

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md

