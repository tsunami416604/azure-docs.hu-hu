---
title: "Azure-megoldások az eszközök internetes hálózatához | Microsoft Docs"
description: "Egy példa IoT-megoldásarchitektúra áttekintése, és hogy miként kapcsolódik eszközökhöz, az Azure IoT Hub szolgáltatáshoz, az Azure IoT eszközoldali SDK-hoz, az Azure IoT szolgálatásoldali SDK-khoz és egyéb Azure-szolgáltatásokhoz."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: e223d0613cd48994315451da87e6b7066585bdb6
ms.openlocfilehash: 36e4afb54485c12b39349124e241a2d74617702c


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Következő lépések
Az Azure IoT Hub olyan Azure-szolgáltatás, amely biztonságos és megbízható kétirányú kommunikációt tesz lehetővé a megoldás háttérrendszere és több millió eszköz között. A megoldás háttérrendszerét az alábbi funkciókkal látja el:

* Az eszközök telemetriai adatainak lekérése távolról.
* Az eszközök adatainak átirányítása egy stream-eseményfeldolgozóra.
* Fájlfeltöltés az eszközökről.
* Felhőből az eszközre irányuló üzenetek küldése adott eszközökre.

Az IoT Hubbal saját megoldáshátteret valósíthat meg. Ezenkívül az IoT Hub tartalmaz egy identitásjegyzéket, amellyel kiépíthetők az eszközök, a biztonsági hitelesítő adataik és a hubhoz való csatlakozással kapcsolatos jogosultságaik. További információk az IoT Hubról: [Mi az IoT Hub?][lnk-iot-hub].

További információk arról, hogyan felügyelheti, konfigurálhatja és frissítheti az eszközeit távolról, az Azure IoT Hub által biztosított szabványalapú eszközfelügyelet segítségével: [Az IoT Hub eszközkezelésének áttekintése][lnk-device-management].

Ha az ügyfélalkalmazásokat az eszközök hardveres platformjainak és operációs rendszereinek széles választékára szeretné telepíteni, használhatja az Azure IoT eszközoldali SDK-kat. Az eszközoldali SDK-k olyan kódtárakat tartalmaznak, amelyek elősegítik a telemetria küldését az IoT Hubra, valamint a felhőből az eszközre irányuló üzenetek fogadását. Amikor az eszköz SDK-kat használja, számos hálózati protokoll közül választhat az IoT Hubbal való kommunikációhoz. További tudnivalókért tekintse meg [az eszközoldali SDK-k ismertető oldalát][lnk-device-sdks].

Bevezetés a kódírásba és a példák futtatásába: [Bevezetés az IoT Hub használatába][lnk-getstarted] című oktatóanyag.

Az [Azure IoT Suite][lnk-iot-suitelnk-iot-suite] is hasznos lehet, amely egy előre konfigurált megoldásokat tartalmazó gyűjtemény. Az IoT Suite lehetővé teszi, hogy gyorsan tegye meg az első lépéseket, és méretezze az IoT-projekteket a gyakori IoT-forgatókönyvek – például a távoli megfigyelés, az eszközkezelés és a prediktív karbantartás kezeléséhez.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md



<!--HONumber=Dec16_HO1-->


