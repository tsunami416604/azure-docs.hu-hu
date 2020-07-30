---
title: IoT Plug and Play kódtárak és SDK-k
description: Információk a IoT-Plug and Play engedélyezett megoldások fejlesztéséhez rendelkezésre álló eszköz-és szolgáltatási könyvtárakról.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 5638cd9973c6a4df809e0b200efe85b067aae026
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407795"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK-k a IoT Plug and Play

A IoT Plug and Play könyvtárak és SDK-k lehetővé teszik a fejlesztők számára, hogy több platformon különböző programozási nyelvek használatával IoT megoldásokat építsenek. A következő táblázat a kezdéshez segítséget nyújtó mintákra és gyors útmutatóra mutató hivatkozásokat tartalmaz:

## <a name="device-sdks-ga"></a>Eszköz SDK-k (GA)

| Nyelv | Csomag | Adattár | Példák | Gyorsútmutató | Referencia |
|---|---|---|---|---|---|
| C – eszköz | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [Példák](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device-c.md) | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET – eszköz | [NuGet 1.27.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Példák](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device-csharp.md) | [Referencia](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java – eszköz | [Maven 1.24.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Példák](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device-java.md) | [Referencia](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python – eszköz | [pip 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Példák](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device-python.md) | [Referencia](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Csomópont – eszköz | [NPM 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Példák](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device-node.md) | [Referencia](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

## <a name="device-sdks-preview"></a>Eszköz SDK-k (előzetes verzió)

| Nyelv | Adattár/minták |
|---|---|
|Beágyazott Azure SDK| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Azure RTOS IoT middleware| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Azure RTOS – első lépések útmutatók | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks-preview"></a>Szolgáltatási SDK-k (előzetes verzió)

| Nyelv | Csomag | Adattár | Példák | Gyorsútmutató | Referencia |
|---|---|---|---|---|---|
| .NET – IoT Hub szolgáltatás előzetes verzió | [NuGet 1.27.1 – előzetes verzió – 002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [Példák](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | N.A. | N.A. |
| Java – IoT Hub szolgáltatás előzetes verziója | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [Példák](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | N.A. | N.A. |
| A Node-IoT Hub szolgáltatás előzetes verziója | [NPM 1.12.4-PnP-frissítés. 4](https://www.npmjs.com/package/azure-iothub/v/1.12.4-pnp-refresh.4) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Példák](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/service/samples) | N.A. | N.A. |
| Python-IoT Hub/digitális Twins szolgáltatás előzetes verzió | [pip 2.2.1 RC1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [Példák](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [Interakció a IoT Hub digitális Twins API-val](quickstart-service-python.md) | N.A. |
| Csomópont – digitális ikrek szolgáltatás előzetes verzió | [NPM 1.0.0-PnP-frissítés. 3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Példák](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [Interakció a IoT Hub digitális Twins API-val](quickstart-service-node.md) | N.A. |

## <a name="next-steps"></a>Következő lépések

Az SDK-k és a kódtárak kipróbálásához tekintse meg a [fejlesztői útmutatót](concepts-developer-guide.md) , valamint az [eszköz](quickstart-connect-device-c.md) rövid útmutatóját és a [szolgáltatás](quickstart-service-node.md)rövid útmutatóját.
