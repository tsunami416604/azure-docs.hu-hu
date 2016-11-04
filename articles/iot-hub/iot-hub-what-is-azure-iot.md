---
title: Azure-megoldások az eszközök internetes hálózatához | Microsoft Docs
description: Az IoT áttekintése az Azure-ban, beleértve egy minta megoldásarchitektúrát és azt, hogy miként kapcsolódik az Azure IoT Hubhoz, az eszköz SDK-khoz és az előre konfigurált megoldásokhoz
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett

---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Következő lépések
Az Azure IoT Hub olyan Azure-szolgáltatás, amely biztonságos és megbízható kétirányú kommunikációt tesz lehetővé az alkalmazás háttérrendszere és több millió eszköz között. Az alkalmazás háttérrendszerét az alábbi funkciókkal látja el:

* Az eszközök telemetriai adatainak lekérése távolról.
* Az eszközök adatainak átirányítása egy stream-eseményfeldolgozóra.
* Fájlfeltöltés az eszközökről.
* Felhőből az eszközre irányuló parancsok küldése adott eszközökre.

Az IoT Hubbal saját megoldáshátteret valósíthat meg. Ezenkívül az IoT Hub eszközidentitás-jegyzéket tartalmaz, amellyel kiépíthetők az eszközök, a biztonsági hitelesítő adataik és a hubhoz való csatlakozással kapcsolatos jogosultságaik. További információk az IoT Hubról: [Mi az IoT Hub?][lnk-iot-hub].

További információk arról, hogyan felügyelheti, konfigurálhatja és frissítheti az eszközeit távolról, az Azure IoT Hub által biztosított szabványalapú IoT-eszközfelügyelet segítségével: [Az Azure IoT Hub-eszközfelügyelet áttekintése][lnk-device-management].

Ha az ügyfélalkalmazásokat az eszközök hardveres platformjainak és operációs rendszereinek széles választékára szeretné telepíteni, használhatja az IoT eszköz SDK-kat. Az IoT eszköz SDK-k olyan kódtárakat tartalmaznak, amelyek elősegítik a telemetria küldését az IoT Hubra, valamint a felhőből az eszközre irányuló parancsok fogadását. Amikor az SDK-kat használja, különféle hálózati protokollok közül választhat az IoT Hubbal való kommunikációhoz. További tudnivalókért lásd: [információ az eszköz SDK-król][lnk-device-sdks].

Bevetés a kódírásba és a példák futtatásába: [Bevezetés az IoT Hub használatába][lnk-getstarted] című oktatóanyag.

Az [Azure IoT Suite][lnk-iot-suite] is hasznos lehet, amely egy előre konfigurált megoldásokat tartalmazó gyűjtemény. Az IoT Suite lehetővé teszi, hogy gyorsan tegye meg az első lépéseket, és méretezze az IoT-projekteket a gyakori IoT-forgatókönyvek – például a távoli megfigyelés, az eszközkezelés és a prediktív karbantartás kezeléséhez.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md


<!--HONumber=Oct16_HO3-->


