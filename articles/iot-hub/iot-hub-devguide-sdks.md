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
ms.openlocfilehash: 1eeb0afdd5ffcbe00357914d6a98c8d0b3d452ec
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017959"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Elsajátítása és használata az Azure IoT Hub SDK-k

Szoftverfejlesztői készletek (SDK-k) használatához az IoT Hub három kategóriába sorolhatók:

* **Eszköz SDK-k** lehetővé teszi olyan alkalmazások fordítása, futtassa az ügyfél vagy a modul ügyfél IoT-eszközökön. Ezek az alkalmazások telemetriát küldjön az IoT hubhoz, és igény szerint üzeneteket, a feladat, a metódus vagy az ikereszköz-frissítések fogadása az IoT hub.  Hozzon létre is használhatja a modul ügyfél [modulok](../iot-edge/iot-edge-modules.md) a [Azure IoT Edge-futtatókörnyezet](../iot-edge/about-iot-edge.md).

* **Szolgáltatási SDK-k** lehetővé teszi az IoT hub kezelése és igény szerint üzenetküldés, feladatok ütemezése, közvetlen metódusok meghívása vagy kívánt tulajdonság frissítéseket küld az IoT-eszközök vagy modulokat.

* **Eszköz kiépítése SDK-k** lehetővé teszi az IoT hubon az eszközön a [Device Provisioning Service](../iot-dps/about-iot-dps.md).

További információ a [előnyeit az Azure IoT SDK-k használatával történő fejlesztéséhez,](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Az Azure IoT eszközoldali SDK-k

A Microsoft Azure IoT eszközoldali SDK-k, amely elősegíti a épület eszközök és alkalmazások csatlakozik, és az Azure IoT Hub-szolgáltatások által kezelt-kódot tartalmaz.

.NET-hez készült Azure IoT Hub eszközoldali SDK: 

* Telepítse a [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-leírások](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Modul-hivatkozás](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

A C, a hordozhatóság és a széles körű platform kompatibilitását (C99) ANSI C nyelven írt Azure IoT Hub eszközoldali SDK:

* Telepítse a [apt-get paranccsal végzi, az MBED, az Arduino IDE vagy a Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-c)
* [API-leírások](https://azure.github.io/azure-iot-sdk-c/index.html)
* [Modul-hivatkozás](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

A Javához készült Azure IoT Hub eszközoldali SDK: 

* Adja hozzá a [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projekt
* [Forráskód](https://github.com/Azure/azure-iot-sdk-java)
* [API-leírások](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Modul-hivatkozás](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)

NODE.js-hez készült Azure IoT Hub eszközoldali SDK: 

* Telepítse a [npm](https://www.npmjs.com/package/azure-iot-device)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-node)
* [API-leírások](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Modul-hivatkozás](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Pythonhoz készült Azure IoT Hub eszközoldali SDK: 

* Telepítse a [pip](https://pypi.python.org/pypi/azure-iothub-device-client/)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-python)
* API-referencia: lásd: [C API-referencia](https://azure.github.io/azure-iot-sdk-c/index.html)

IOS-hez készült Azure IoT Hub eszközoldali SDK: 

* Telepítse a [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Minták](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API-referencia: lásd: [C API-referencia](https://azure.github.io/azure-iot-sdk-c/index.html)

> [!NOTE]
> Tekintse meg a GitHub-adattárában információk nyelv és platform-specifikus csomagkezelők bináris fájljait és függőségeinek telepítéséhez a fejlesztői gépen információs fájljaiban.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Operációsrendszer-platform és a hardver-kompatibilitás

Az SDK-k által támogatott platformok található [Azure IoT SDK-k Eszközplatform-támogatás](iot-hub-device-sdk-platform-support.md).

Bizonyos hardvereszközök az SDK-kompatibilitással kapcsolatos további információkért lásd: a [Azure Certified for IoT eszközkatalógus](https://catalog.azureiotsuite.com/) vagy egyes adattárban.

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDKs

Az Azure IoT service SDK-k megkönnyítik az alkalmazások létrehozása, amely interakciót közvetlenül az IoT hubbal, kezelheti az eszközöket és a biztonsági kódot tartalmaznak.

.NET-hez készült Azure IoT Hub Szolgáltatásoldali SDK-val:

* Töltse le a [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-leírások](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

A Javához készült Azure IoT Hub Szolgáltatásoldali SDK-val: 

* Adja hozzá a [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
) projekt
* [Forráskód](https://github.com/Azure/azure-iot-sdk-java)
* [API-leírások](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

NODE.js-hez készült Azure IoT Hub Szolgáltatásoldali SDK-val: 

* Töltse le a [npm](https://www.npmjs.com/package/azure-iothub)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-node)
* [API-leírások](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Pythonhoz készült Azure IoT Hub Szolgáltatásoldali SDK-val: 

* Töltse le a [pip](https://pypi.python.org/pypi/azure-iothub-service-client/)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-python)

Az Azure IoT Hub szolgáltatási SDK-val a C: 

* Töltse le a [apt-get paranccsal végzi, az MBED, az Arduino IDE vagy a Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-c)

IOS-hez készült Azure IoT Hub Szolgáltatásoldali SDK-val: 

* Telepítse a [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Minták](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Tekintse meg a GitHub-adattárában információk nyelv és platform-specifikus csomagkezelők bináris fájljait és függőségeinek telepítéséhez a fejlesztői gépen információs fájljaiban.

## <a name="device-provisioning-sdks"></a>Eszközkiépítési SDK-k

A **a Microsoft Azure-kiépítés SDK-k** lehetővé teszi az IoT hubon az eszközön a [Device Provisioning Service](../iot-dps/about-iot-dps.md).

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k használata a C#:

* [Eszköz ügyfél-SDK kiépítése](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [Eszközkiépítési szolgáltatás ügyfél-SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k a Javához készült:

* [Eszköz ügyfél-SDK kiépítése](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-device-client)
* [Eszközkiépítési szolgáltatás ügyfél-SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k Node.js-hez:

* [Eszköz ügyfél-SDK kiépítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [Eszközkiépítési szolgáltatás ügyfél-SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k a Python:

* [Eszköz ügyfél-SDK kiépítése](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [Eszközkiépítési szolgáltatás ügyfél-SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

Az Azure kiépítési eszköz- és szolgáltatásspecifikus SDK-k a C:

* [Eszköz ügyfél-SDK kiépítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Eszközkiépítési szolgáltatás ügyfél-SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_service_client)

## <a name="next-steps"></a>További lépések

Az Azure IoT SDK-k is biztosítanak a fejlesztési eszközöket:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): az IoT Hub kapcsolati kapcsolódó a többplatformos parancssori eszköz segítségével diagnosztizálhatja a problémákat.
* [eszköz-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): csatlakozni az IoT Hub Windows asztali alkalmazások.

Ez az IoT Hub fejlesztői útmutató más referencia témaköröket tartalmazza:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md)
* [Az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása](iot-hub-devguide-query-language.md)
* [Kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md)
* [IoT Hub – REST API-referencia](/rest/api/iothub/)
* [Eszközplatform-támogatás bevezetésének IoT SDK-val](iot-hub-device-sdk-platform-support.md)