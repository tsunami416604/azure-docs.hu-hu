---
title: IoT Plug and Play kódtárak és SDK-k
description: Információk a IoT-Plug and Play engedélyezett megoldások fejlesztéséhez rendelkezésre álló eszköz-és szolgáltatási könyvtárakról.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b328d7fe4cf3a3487614ed93dcf130aa7a233efd
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830570"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT Plug and Play kódtárak és SDK-k

A IoT Plug and Play könyvtárak és SDK-k lehetővé teszik a fejlesztők számára, hogy több platformon különböző programozási nyelvek használatával IoT megoldásokat építsenek. A következő táblázat a kezdéshez segítséget nyújtó mintákra és gyors útmutatóra mutató hivatkozásokat tartalmaz:

## <a name="microsoft-supported-libraries-and-sdks"></a>Microsoft által támogatott kódtárak és SDK-k

| Platform | Könyvtár/csomag | Forráskód | Minta | Gyors útmutató | Leírások |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Eszköz SDK az apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHubon](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitális Twin Client-minták](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Kapcsolódás IoT Hubhoz](./quickstart-connect-pnp-device-c-linux.md) | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Eszköz SDK a Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHubon](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitális Twin Client-minták](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Kapcsolódás IoT Hubhoz](./quickstart-connect-pnp-device-c-windows.md) | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [Az eszköz SDK BEÁGYAZva](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHubon](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitális Twin Client-minták](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Device SDK az Arduino IDE-ben](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHubon](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitális Twin Client-minták](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Eszköz SDK a CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHubon](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitális Twin Client-minták](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHubon](https://github.com/Azure/azure-iot-sdk-csharp) | [Digitális kettős minták](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Kapcsolódás IoT Hubhoz](./quickstart-connect-pnp-device-csharp.md) | [Referencia](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven 3](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHubon](https://github.com/Azure/azure-iot-sdk-java) | [Digitális kettős minták](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Kapcsolódás IoT Hubhoz](./quickstart-connect-pnp-device-java.md) | [Referencia](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHubon](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Digitális kettős minták](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Kapcsolódás IoT Hubhoz](./quickstart-connect-pnp-device-node.md) | [Referencia](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |

## <a name="iot-hub-support"></a>IoT Hub támogatás

A IoT Plug and Play eszköz képességeit csak az [ingyenes és a standard szintű IoT hubok](../iot-hub/iot-hub-scaling.md)támogatják.

## <a name="next-steps"></a>Következő lépések

Az eszköz SDK-jai és kódtárai mellett a REST API-kkal is használhatja a modell-adattárakat.