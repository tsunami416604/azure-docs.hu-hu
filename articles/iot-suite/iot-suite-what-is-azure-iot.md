---
title: "Azure-megoldások az eszközök internetes hálózatához | Microsoft Docs"
description: "Az IoT áttekintése az Azure-ban, beleértve egy minta megoldásarchitektúrát és azt, hogy miként kapcsolódik az Azure IoT Suite-hoz és az előre konfigurált megoldásokhoz"
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
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1a6dd35278f0a4a4f972642c40a0976986dd79ae
ms.openlocfilehash: bd2b41944e3a37fde1cecd3d0ff299f949cd4eb4


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="azure-iot-suite"></a>Azure IoT Suite
A Microsoft Azure IoT Suite egy vállalati szintű megoldás, amellyel gyorsan teheti meg az első lépéseket az előre konfigurált megoldások bővíthető készletével. Ezek a megoldások olyan általános IoT-forgatókönyveket érintenek, mint például a [távoli megfigyelés][lnk-preconfigured-solutions] és a [prediktív karbantartás][lnk-predictive-maintenance]. Ezek a megoldások az ebben a cikkben leírt IoT-megoldásarchitektúra megvalósításai.

Az előre konfigurált megoldások teljes körű, működőképes és átfogó megoldások, amelyek a következőket tartalmazzák:

- Szimulált eszközök az elkezdéséhez.
- Olyan előre konfigurált Azure-szolgáltatások, mint például az [Azure IoT Hub][Azure IoT Hub], [Azure Event Hubs][Azure Event Hubs], [Azure Stream Analytics][Azure Stream Analytics], [Azure Machine Learning][Azure Machine Learning] és [Azure Storage tárterület][Azure Storage tárterület].
- Megoldásspecifikus felügyeleti konzolok.

Az előre konfigurált megoldások bevált, éles környezetekben használható kódot tartalmaznak, amelyek testreszabásával és bővítésével megvalósíthatja saját specifikus IoT-megoldásait.

A sok előre konfigurált megoldás által használt [Azure IoT Hub][Azure IoT Hub] szolgáltatás is érdekelheti. Az [Azure IoT Hub][Azure IoT Hub] biztonságos és megbízható, az előre konfigurált megoldásarchitektúrában használt kétirányú kommunikációt nyújt az eszközök és a felhő között.

## <a name="next-steps"></a>Következő lépések
Ezekben a forrásanyagokban további információt talál az IoT Suite-ról és az előre konfigurált megoldásokról:

* [Mi az Azure IoT Suite?][lnk-whatissuite]
* [Mik az Azure IoT előre konfigurált megoldások?][lnk-whatarepreconfigured]

[lnk-whatissuite]: iot-suite-overview.md
[lnk-whatarepreconfigured]: iot-suite-what-are-preconfigured-solutions.md

[lnk-preconfigured-solutions]: iot-suite-getstarted-preconfigured-solutions.md
[Azure IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[Azure Machine Learning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure Storage tárterület]: https://azure.microsoft.com/documentation/services/storage/
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md



<!--HONumber=Nov16_HO3-->


