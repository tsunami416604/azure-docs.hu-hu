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
ms.openlocfilehash: 1b534c6ccb3bfca6c7dddaec82d8794deae72ff2
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651309"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK-k a IoT Plug and Play

A IoT Plug and Play könyvtárak és SDK-k lehetővé teszik a fejlesztők számára, hogy több platformon különböző programozási nyelvek használatával IoT megoldásokat építsenek. A következő táblázat a kezdéshez segítséget nyújtó mintákra és gyors útmutatóra mutató hivatkozásokat tartalmaz:

## <a name="device-sdks"></a>Eszköz SDK-k

| Nyelv | Csomag | Adattár | Példák | Gyorsútmutató | Hivatkozás |
|---|---|---|---|---|---|
| C – eszköz | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Példák](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device-c.md) | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET – eszköz | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Példák](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device-csharp.md) | [Referencia](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet&preserve-view=true) |
| Java – eszköz | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Példák](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device-java.md) | [Referencia](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable&preserve-view=true) |
| Python – eszköz | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Példák](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device-python.md) | [Referencia](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python&preserve-view=true) |
| Csomópont – eszköz | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Példák](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device-node.md) | [Referencia](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest&preserve-view=true) |
| Beágyazott C-eszköz | N/A | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Példák](howto-use-embedded-c.md#samples) | [A beágyazott C használata](howto-use-embedded-c.md) | N/A

## <a name="service-sdks"></a>Szolgáltatási SDK-k

| Platform  | Csomag | Adattár | Példák | Gyorsútmutató | Hivatkozás |
|---|---|---|---|---|---|
| .NET – IoT Hub szolgáltatás | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Példák](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N/A | [Referencia](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true) |
| Java – IoT Hub szolgáltatás | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Példák](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N/A | [Referencia](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable&preserve-view=true) |
| Csomópont-IoT Hub szolgáltatás | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Példák](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N/A | [Referencia](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest&preserve-view=true) |
| Python – digitális Twins szolgáltatás | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Példák](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interakció a IoT Hub digitális Twins API-val](quickstart-service-python.md) | N/A |
| Csomópont – digitális Twins szolgáltatás | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Példák](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interakció a IoT Hub digitális Twins API-val](quickstart-service-node.md) | N/A |

## <a name="next-steps"></a>Következő lépések

Az SDK-k és a kódtárak kipróbálásához tekintse meg a  [fejlesztői útmutatót](concepts-developer-guide-device-csharp.md) , valamint az [eszköz](quickstart-connect-device-c.md) rövid útmutatóját és a [szolgáltatás](quickstart-service-node.md)rövid útmutatóját.
