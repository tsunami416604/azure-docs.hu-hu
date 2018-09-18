---
title: Az Azure IoT SDK-k ismertetése |} A Microsoft Docs
description: Fejlesztői útmutató – információ és a különböző Azure IoT eszköz- és szolgáltatásspecifikus SDK-k használatával hozhat létre az eszköz és az alkalmazások háttér-mutató hivatkozásokat.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: ba06617762650afc8cd3eecb2fcddda6d24f4228
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734990"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Elsajátítása és használata az Azure IoT Hub SDK-k

Szoftverfejlesztői készletek (SDK-k) használatához az IoT Hub három kategóriába sorolhatók:

* **Eszköz SDK-k** lehetővé teszi olyan alkalmazások fordítása, futtassa az ügyfél vagy a modul ügyfél IoT-eszközökön. Ezek az alkalmazások telemetriát küldjön az IoT hubhoz, és igény szerint üzeneteket, a feladat, a metódus vagy az ikereszköz-frissítések fogadása az IoT hub.  Hozzon létre is használhatja a modul ügyfél [modulok](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) a [Azure IoT Edge-futtatókörnyezet](https://docs.microsoft.com/en-us/azure/iot-edge/about-iot-edge).

* **Szolgáltatási SDK-k** lehetővé teszi az IoT hub kezelése és igény szerint üzenetküldés, feladatok ütemezése, közvetlen metódusok meghívása vagy kívánt tulajdonság frissítéseket küld az IoT-eszközök vagy modulokat.

* **Eszköz kiépítése SDK-k** lehetővé teszi az IoT hubon az eszközön a [Device Provisioning Service](../iot-dps/about-iot-dps.md).

Megismerheti az előnyeit az Azure IoT SDK-k használatával történő fejlesztéséhez [Itt][lnk-benefits-blog].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Az Azure IoT eszközoldali SDK-k

A Microsoft Azure IoT eszközoldali SDK-k, amely elősegíti a épület eszközök és alkalmazások csatlakozik, és az Azure IoT Hub-szolgáltatások által kezelt-kódot tartalmaz.

.NET-hez készült Azure IoT Hub eszközoldali SDK: 
* Telepítse a [Nuget][lnk-nuget-csharp-device]
* [Forráskód][lnk-dotnet-sdk]
* [API-referencia][lnk-dotnet-ref]
* [Modul-hivatkozás](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Az Azure IoT Hub eszközoldali SDK-t a hordozhatóság és a széles körű platform kompatibilitását (C99) ANSI C nyelven írt: az
* Telepítse a [apt-get paranccsal végzi, az MBED, az Arduino IDE vagy a Nuget][lnk-c-package]
* [Forráskód][lnk-c-sdk]
* [API-referencia][lnk-c-ref]
* [Modul-hivatkozás](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

A Javához készült Azure IoT Hub eszközoldali SDK: 
* Adja hozzá a [Maven] [ lnk-maven-device] projekt
* [Forráskód][lnk-java-sdk]
* [API-referencia][lnk-java-ref]
* [Modul-hivatkozás](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)

NODE.js-hez készült Azure IoT Hub eszközoldali SDK: 
* Telepítse a [npm][lnk-npm-device]
* [Forráskód][lnk-node-sdk]
* [API-referencia][lnk-node-ref]
* [Modul-hivatkozás](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Pythonhoz készült Azure IoT Hub eszközoldali SDK: 
* Telepítse a [pip][lnk-pip-device]
* [Forráskód][lnk-python-sdk]
* API-referencia: lásd: [C API-referencia][lnk-c-ref]

IOS-hez készült Azure IoT Hub eszközoldali SDK: 
* Telepítse a [CocoaPod][lnk-cocoa-device]
* [A minták][lnk-ios-sample]
* API-referencia: lásd: [C API-referencia][lnk-c-ref]

> [!NOTE]
> Tekintse meg a GitHub-adattárában információk nyelv és platform-specifikus csomagkezelők bináris fájljait és függőségeinek telepítéséhez a fejlesztői gépen információs fájljaiban.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Operációsrendszer-platform és a hardver-kompatibilitás

Ez az SDK-k által támogatott platformok található [dokumentum](iot-hub-device-sdk-platform-support.md).
Bizonyos hardvereszközök az SDK-kompatibilitással kapcsolatos további információkért lásd: a [Azure Certified for IoT eszközkatalógus] [ lnk-certified] vagy egyes adattárban.

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDKs

Az Azure IoT service SDK-k megkönnyítik az alkalmazások létrehozása, amely interakciót közvetlenül az IoT hubbal, kezelheti az eszközöket és a biztonsági kódot tartalmaznak.

.NET-hez készült Azure IoT Hub Szolgáltatásoldali SDK-val:
* Töltse le a [Nuget][lnk-nuget-csharp-service]
* [Forráskód][lnk-dotnet-sdk]
* [API-referencia][lnk-dotnet-service-ref]

A Javához készült Azure IoT Hub Szolgáltatásoldali SDK-val: 
* Adja hozzá a [Maven] [ lnk-maven-service] projekt
* [Forráskód][lnk-java-sdk]
* [API-referencia][lnk-java-service-ref]

NODE.js-hez készült Azure IoT Hub Szolgáltatásoldali SDK-val: 
* Töltse le a [npm][lnk-npm-service]
* [Forráskód][lnk-node-sdk]
* [API-referencia][lnk-node-service-ref]

Pythonhoz készült Azure IoT Hub Szolgáltatásoldali SDK-val: 
* Töltse le a [pip][lnk-pip-service]
* [Forráskód][lnk-python-sdk]

Az Azure IoT Hub szolgáltatási SDK-val a C: 
* Töltse le a [apt-get paranccsal végzi, az MBED, az Arduino IDE vagy a Nuget][lnk-c-package]
* [Forráskód][lnk-c-sdk]

IOS-hez készült Azure IoT Hub Szolgáltatásoldali SDK-val: 
* Telepítse a [CocoaPod][lnk-cocoa-service]
* [A minták][lnk-ios-sample]

> [!NOTE]
> Tekintse meg a GitHub-adattárában információk nyelv és platform-specifikus csomagkezelők bináris fájljait és függőségeinek telepítéséhez a fejlesztői gépen információs fájljaiban.

## <a name="device-provisioning-sdks"></a>Eszközkiépítési SDK-k

A **a Microsoft Azure-kiépítés SDK-k** lehetővé teszi az IoT hubon az eszközön a [Device Provisioning Service](../iot-dps/about-iot-dps.md).

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k használata a C#:
* [Eszköz ügyfél-SDK kiépítése](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [Eszközkiépítési szolgáltatás ügyfél-SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k a Javához készült:
* [Eszköz ügyfél-SDK kiépítése](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning-device-client)
* [Eszközkiépítési szolgáltatás ügyfél-SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k Node.js-hez:
* [Eszköz ügyfél-SDK kiépítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [Eszközkiépítési szolgáltatás ügyfél-SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k a Python:
* [Eszköz ügyfél-SDK kiépítése](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [Eszközkiépítési szolgáltatás ügyfél-SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k a C:
* [Eszköz ügyfél-SDK kiépítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Eszközkiépítési szolgáltatás ügyfél-SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning/service)

## <a name="next-steps"></a>További lépések

Az Azure IoT SDK-k is biztosítanak a fejlesztési eszközöket:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): az IoT Hub kapcsolati kapcsolódó a többplatformos parancssori eszköz segítségével diagnosztizálhatja a problémákat.
* [eszköz-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): csatlakozni az IoT Hub Windows asztali alkalmazások.

Ez az IoT Hub fejlesztői útmutató más referencia témaköröket tartalmazza:

* [IoT Hub-végpontok][lnk-devguide-endpoints]
* [Az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása][lnk-devguide-query]
* [Kvóták és szabályozás][lnk-devguide-quotas]
* [IoT Hub MQTT-támogatás][lnk-devguide-mqtt]
* [IoT Hub – REST API-referencia][lnk-rest-ref]
* [Eszközplatform-támogatás bevezetésének IoT SDK-val](iot-hub-device-sdk-platform-support.md)

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
[lnk-cocoa-device]: https://cocoapods.org/pods/AzureIoTHubClient
[lnk-ios-sample]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-cocoa-service]: https://cocoapods.org/pods/AzureIoTHubServiceClient
