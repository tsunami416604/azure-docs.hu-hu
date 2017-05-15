---
title: "Azure-megoldások az eszközök internetes hálózatához (IoT Suite) | Microsoft Docs"
description: "Az IoT áttekintése az Azure-ban, beleértve egy minta megoldásarchitektúrát és azt, hogy miként kapcsolódik az Azure IoT Suite-hoz és az előre konfigurált megoldásokhoz."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 437d2655-896f-4a9e-a4a8-b864790d3ef8
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 1c8703d64ff45e589a7ce93f1f2176681e1bf331
ms.contentlocale: hu-hu
ms.lasthandoff: 04/25/2017


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="azure-iot-suite"></a>Azure IoT Suite
A Microsoft Azure IoT Suite egy vállalati szintű megoldás, amellyel gyorsan teheti meg az első lépéseket az előre konfigurált megoldások bővíthető készletével. Ezek a megoldások olyan általános IoT-forgatókönyveket érintenek, mint a [távoli megfigyelés][lnk-preconfigured-solutions], a [prediktív karbantartás][lnk-predictive-maintenance] és a [csatlakoztatott gyár][lnk-connected-factory]. Ezek a megoldások az ebben a cikkben leírt IoT-megoldásarchitektúra megvalósításai.

Az előre konfigurált megoldások teljes körű, működőképes és átfogó megoldások, amelyek a következőket tartalmazzák:

- Szimulált eszközök az elkezdéséhez.
- Olyan előre konfigurált Azure-szolgáltatások, mint az [Azure IoT Hub][Azure IoT Hub], [Azure Event Hubs][Azure Event Hubs], [Azure Stream Analytics][Azure Stream Analytics], [Azure Machine Learning][Azure Machine Learning] és [Azure Storage][Azure storage].
- Megoldásspecifikus felügyeleti konzolok.

Az előre konfigurált megoldások bevált, éles környezetekben használható kódot tartalmaznak, amelyek testreszabásával és bővítésével megvalósíthatja saját specifikus IoT-megoldásait.

A számos előre konfigurált megoldás által használt [Azure IoT Hub][Azure IoT Hub] szolgáltatás is érdekelheti. Az [Azure IoT Hub][Azure IoT Hub] biztonságos és megbízható, az előre konfigurált megoldásarchitektúrában használt kétirányú kommunikációt nyújt az eszközök és a felhő között.

## <a name="next-steps"></a>Következő lépések
Ezekben a forrásanyagokban további információt talál az IoT Suite-ról és az előre konfigurált megoldásokról:

* [Mi az az Azure IoT Suite?][lnk-whatissuite]
* [Mik az Azure IoT Suite előre konfigurált megoldásai?][lnk-whatarepreconfigured]

[lnk-whatissuite]: iot-suite-overview.md
[lnk-whatarepreconfigured]: iot-suite-what-are-preconfigured-solutions.md

[lnk-preconfigured-solutions]: iot-suite-getstarted-preconfigured-solutions.md
[Azure IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[Azure Machine Learning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-connected-factory]: iot-suite-connected-factory-overview.md
