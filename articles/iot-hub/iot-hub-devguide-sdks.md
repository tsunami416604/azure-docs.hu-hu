---
title: "Az Azure IoT SDK-k ismertetése |} Microsoft Docs"
description: "Fejlesztői útmutató - információk és hivatkozások a különböző Azure IoT eszköz és a szolgáltatás SDK-k, amelyek segítségével eszközön futó alkalmazások és a háttér-alkalmazásokat hozhat létre."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 96dad8917f4483c3d7c74ea0462b96d89286f549
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="understand-and-use-azure-iot-sdks"></a>Megismeréséhez és használatához Azure IoT SDK-k

Az IoT-központ való munkához software development Kit (SDK) három kategóriába sorolhatók:

* **Eszközoldali SDK-k** lehetővé teszik az IoT-eszközök futó alkalmazások. Ezek az alkalmazások telemetriai adatokat küldeni az IoT hub, és opcionálisan az IoT hub-üzeneteket fogadjon.

* **Szolgáltatás SDK-k** lehetővé teszik az IoT hub kezelését, és opcionálisan az üzenetek küldése az IoT-eszközök.

* **Az Azure IoT peremhálózati** lehetővé teszi az eszközök, amelyek nem használják a támogatott protokollok átjárók összeállítását. Átjárók is a peremhálózaton lévő üzenetek feldolgozásához.

SDK-k több programozási nyelv támogatása biztosított.

## <a name="azure-iot-device-sdks"></a>Azure IoT device SDKs

A Microsoft Azure IoT-eszközök SDK-k, amely elősegíti a épület eszközök és alkalmazások és szolgáltatások Azure IoT-központ által kezelt-kódot tartalmaz.

A következő Azure IoT-eszközök SDK-k is letölthetők a Githubról:

* [Az Azure IoT-eszközök SDK a .NET-hez][lnk-dotnet-device-sdk]
* [Az Azure IoT-eszközök SDK Java][lnk-java-device-sdk]
* [Az Azure IoT-eszközök SDK for Node.js][lnk-node-device-sdk]
* [Az Azure IoT-eszközök SDK Python-hez][lnk-python-device-sdk]
* [Az Azure IoT-eszközök SDK c] [ lnk-c-device-sdk] írt adatok hordozhatóságára és széles körű platformkompatibilitásra ANSI C (C99). Nincsenek a két eszköz ügyféloldali kódtáraknál c, az alacsony szintű **iothub_client** és a **szerializáló**.

> [!NOTE]
> Tekintse meg a GitHub-adattárak readme fájljaiban találhat használatáról nyelvet és a platform-specifikus csomag kezelők bináris fájljait és a függőségek telepítése a fejlesztési számítógépén.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Az operációs rendszer platform és hardver kompatibilitása

Bizonyos hardvereszközök kompatibilisek SDK kapcsolatos további információkért tekintse meg a [Azure IoT eszköz katalógus hitelesített][lnk-certified].

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDKs

Az Azure IoT szolgáltatás SDK-k lehetővé teszi az épület alkalmazásokat közvetlenül az IoT-központ kezelheti az eszközöket és a biztonsági kódot tartalmazhatnak.

A következő Azure IoT-szolgáltatás SDK-k is letölthetők a Githubról:

* [Az Azure IoT szolgáltatás SDK a .NET-hez][lnk-dotnet-service-sdk]
* [Azure IoT-szolgáltatás SDK Java][lnk-java-service-sdk]
* [Az Azure IoT szolgáltatás SDK for Node.js][lnk-node-service-sdk]
* [Azure IoT-szolgáltatás SDK Python][lnk-python-service-sdk]
* [Az Azure IoT szolgáltatás SDK C-hez][lnk-c-service-sdk]

> [!NOTE]
> Tekintse meg a GitHub-adattárak readme fájljaiban találhat használatáról nyelvet és a platform-specifikus csomag kezelők bináris fájljait és a függőségek telepítése a fejlesztési számítógépén.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Azure IoT peremhálózati infrastruktúra és az IoT-átjáró megoldások létrehozásához modulok tartalmazza. Bővítheti IoT peremhálózati olyan végpont forgatókönyv igazított átjárók létrehozására.

Letöltheti a [Azure IoT peremhálózati] [ lnk-iot-edge] a Githubról.

## <a name="online-api-reference-documentation"></a>Online API referenciadokumentációt tartalmaz

Az alábbi lista tartalmazza az Azure IoT eszköz, a szolgáltatás és az átjáró szalagtárak online API referenciadokumentációt tartalmaz hivatkozásokat:

* [Az eszközök internetes hálózata (IoT) .NET][lnk-dotnet-ref]
* [Az Azure IoT-eszközök SDK Java][lnk-java-ref]
* [Azure IoT-szolgáltatás SDK Java][lnk-java-service-ref]
* [Az Azure IoT-eszközök SDK for Node.js][lnk-node-ref]
* [Az Azure IoT szolgáltatás SDK for Node.js][lnk-node-service-ref]
* [Az Azure IoT-eszközök SDK C-hez][lnk-c-ref]
* [Az IoT Hub REST][lnk-rest-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>További lépések

Az IoT Hub fejlesztői útmutató egyéb témaköröket tartalmazza:

* [IoT-központok végpontjai][lnk-devguide-endpoints]
* [Az IoT-központ lekérdezési nyelv eszköz twins, a feladatok és az üzenet-útválasztás][lnk-devguide-query]
* [Kvóták és sávszélesség-szabályozás][lnk-devguide-quotas]
* [Az IoT Hub MQTT támogatása][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
