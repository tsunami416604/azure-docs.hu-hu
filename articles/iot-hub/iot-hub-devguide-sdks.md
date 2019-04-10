---
title: Az Azure IoT SDK-k ismertetése |} A Microsoft Docs
description: Fejlesztői útmutató – információ és a különböző Azure IoT eszköz- és szolgáltatásspecifikus SDK-k használatával hozhat létre az eszköz és az alkalmazások háttér-mutató hivatkozásokat.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: e51313bbed21459de9f717edd123887caed18f4b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279352"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Elsajátítása és használata az Azure IoT Hub SDK-k

Szoftverfejlesztői készletek (SDK-k) az IoT Hub használata a két kategóriába sorolhatók:

* **IoT Hub eszközoldali SDK-k** lehetővé teszi olyan alkalmazások fordítása, futtassa az ügyfél vagy a modul ügyfél IoT-eszközökön. Ezek az alkalmazások telemetriát küldjön az IoT hubhoz, és igény szerint üzeneteket, a feladat, a metódus vagy az ikereszköz-frissítések fogadása az IoT hub.  Hozzon létre is használhatja a modul ügyfél [modulok](../iot-edge/iot-edge-modules.md) a [Azure IoT Edge-futtatókörnyezet](../iot-edge/about-iot-edge.md).

* **IoT Hub szolgáltatási SDK-k** hozhat létre az IoT hub kezelése, és igény szerint üzenetküldés, háttéralkalmazásokhoz feladatok ütemezése, közvetlen metódusok meghívása vagy kívánt tulajdonság frissítéseket küld az IoT-eszközök vagy a modulok engedélyezése.

Emellett is biztosítunk SDK-k készlete való munkához a [Device Provisioning Service](../iot-dps/about-iot-dps.md).
* **Kiépítés eszközoldali SDK-k** lehetővé teszi olyan alkalmazások fordítása, futtassa a Device Provisioning Service szolgáltatással való kommunikációhoz IoT-eszközein.

* **Szolgáltatási SDK-k kiépítés** lehetővé teszik a Device Provisioning Service-ben a regisztrációk kezelése háttérmodul-alkalmazásokkal.

További információ a [előnyeit az Azure IoT SDK-k használatával történő fejlesztéséhez,](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Operációsrendszer-platform és a hardver-kompatibilitás

Az SDK-k által támogatott platformok található [Azure IoT SDK-k Eszközplatform-támogatás](iot-hub-device-sdk-platform-support.md).

Bizonyos hardvereszközök az SDK-kompatibilitással kapcsolatos további információkért lásd: a [Azure Certified for IoT eszközkatalógus](https://catalog.azureiotsolutions.com/) vagy egyes adattárban.

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT Hub Device SDKs

A Microsoft Azure IoT eszközoldali SDK-k kódot tartalmaznak, amely lehetővé teszi, hogy csatlakozik, és az Azure IoT Hub-szolgáltatások által kezelt alkalmazások létrehozása.

.NET-hez készült Azure IoT Hub eszközoldali SDK: 

* Töltse le a [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  A névtér a Microsoft.Azure.Devices.Clients, amely tartalmazza az IoT Hub Device-ügyfelek (DeviceClient, ModuleClient).
* [Forráskód](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-referencia](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Segédanyagok a modulokhoz](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

C (ANSI C - C99) az Azure IoT Hub eszközoldali SDK:

* Telepítse a [apt-get paranccsal végzi, MBED, Arduino IDE vagy iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-c)
* [Fordítsa le a C eszközoldali SDK-t](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API-referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Segédanyagok a modulokhoz](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Más platformokon az C SDK-val portolása](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Fejlesztői dokumentáció](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) információkat cross-fordítása, első lépések a különböző platformokon, stb.
* [Az Azure IoT Hub C SDK erőforrás-felhasználási adatokat](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

A Javához készült Azure IoT Hub eszközoldali SDK: 

* Adja hozzá a [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projekt
* [Forráskód](https://github.com/Azure/azure-iot-sdk-java)
* [API-referencia](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Segédanyagok a modulokhoz](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

NODE.js-hez készült Azure IoT Hub eszközoldali SDK: 

* Telepítse a [npm](https://www.npmjs.com/package/azure-iot-device)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-node)
* [API-referencia](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Segédanyagok a modulokhoz](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Pythonhoz készült Azure IoT Hub eszközoldali SDK: 

* Telepítse a [pip](https://pypi.python.org/pypi/azure-iothub-device-client/)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-python)
* API-referencia: lásd: [C API-referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

IOS-hez készült Azure IoT Hub eszközoldali SDK: 

* Telepítse a [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Példák](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API-referencia: lásd: [C API-referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT Hub Service SDKs

Az Azure IoT service SDK-k megkönnyítik az alkalmazások létrehozása, amely interakciót közvetlenül az IoT hubbal, kezelheti az eszközöket és a biztonsági kódot tartalmaznak.

.NET-hez készült Azure IoT Hub Szolgáltatásoldali SDK-val:

* Töltse le a [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  A névtér a Microsoft.Azure.Devices, amely tartalmazza az IoT Hub szolgáltatás ügyfelek (RegistryManager, ServiceClients).
* [Forráskód](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-referencia](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

A Javához készült Azure IoT Hub Szolgáltatásoldali SDK-val: 

* Adja hozzá a [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projekt
* [Forráskód](https://github.com/Azure/azure-iot-sdk-java)
* [API-referencia](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

NODE.js-hez készült Azure IoT Hub Szolgáltatásoldali SDK-val: 

* Töltse le a [npm](https://www.npmjs.com/package/azure-iothub)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-node)
* [API-referencia](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Pythonhoz készült Azure IoT Hub Szolgáltatásoldali SDK-val: 

* Töltse le a [pip](https://pypi.python.org/pypi/azure-iothub-service-client/)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-python)

Az Azure IoT Hub szolgáltatási SDK-val a C: 

* Töltse le a [apt-get paranccsal végzi, az MBED, az Arduino IDE vagy a Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-c)

IOS-hez készült Azure IoT Hub Szolgáltatásoldali SDK-val: 

* Telepítse a [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Példák](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Tekintse meg a GitHub-adattárában információk nyelv és platform-specifikus csomagkezelők bináris fájljait és függőségeinek telepítéséhez a fejlesztői gépen információs fájljaiban.

## <a name="microsoft-azure-provisioning-sdks"></a>A Microsoft Azure-kiépítés SDK-k

A **a Microsoft Azure-kiépítés SDK-k** lehetővé teszi az IoT hubon az eszközön a [Device Provisioning Service](../iot-dps/about-iot-dps.md).

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k használata a C#:

* Töltse le a [eszközoldali SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) és [Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) nugetről.
* [Forráskód](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API-referencia](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k a C:

* Telepítse a [apt-get paranccsal végzi, MBED, Arduino IDE vagy iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API-referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k a Javához készült:

* Adja hozzá a [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projekt
* [Forráskód](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API-referencia](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k Node.js-hez:

* [Forráskód](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API-referencia](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Töltse le [eszközoldali SDK](https://badge.fury.io/js/azure-iot-provisioning-device) és [Service SDK](https://badge.fury.io/js/azure-iot-provisioning-service) az npm-ből

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k a Python:

* [Forráskód](https://github.com/Azure/azure-iot-sdk-python)
* Töltse le [eszközoldali SDK](https://pypi.org/project/azure-iot-provisioning-device-client/) és [Service SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) pip-címről

## <a name="next-steps"></a>További lépések

Az Azure IoT SDK-k is biztosítanak a fejlesztési eszközöket:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): az IoT Hub kapcsolati kapcsolódó a többplatformos parancssori eszköz segítségével diagnosztizálhatja a problémákat.
* [eszköz-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): csatlakozni az IoT Hub Windows asztali alkalmazások.

Fejlesztés az Azure IoT SDK-k használatával kapcsolatos fontos docs:
* Ismerje meg [kezelése, kapcsolatok és megbízható üzenetküldést](iot-hub-reliability-features-in-sdks.md) az IoT Hub SDK-k használatával.
* Hogyan [mobilplatformokra fejlesztése](iot-hub-how-to-develop-for-mobile-devices.md) például az iOS és Android.
* [Az Azure IoT SDK-val eszközplatform-támogatás](iot-hub-device-sdk-platform-support.md)


Ez az IoT Hub fejlesztői útmutató más referencia témaköröket tartalmazza:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md)
* [Az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása](iot-hub-devguide-query-language.md)
* [Kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md)
* [IoT Hub – REST API-referencia](/rest/api/iothub/)
