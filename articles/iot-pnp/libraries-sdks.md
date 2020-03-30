---
title: IoT Plug and Play könyvtárak és SDK-k
description: Információk az IoT Plug and Play-kompatibilis megoldások fejlesztéséhez elérhető eszköz- és szolgáltatástárakról.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064332"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT Plug and Play könyvtárak és SDK-k

Az IoT Plug and Play-kódtárak és Az SDK-k lehetővé teszik a fejlesztők számára, hogy különböző programozási nyelvek használatával több platformon is többféle programozási nyelvet használjanak. Az alábbi táblázat a mintákra mutató hivatkozásokat és rövid útmutatókat tartalmaz az első lépések hez:

## <a name="microsoft-supported-libraries-and-sdks"></a>Microsoft által támogatott tárak és SDK-k

| Platform | Könyvtár/csomag | Forráskód | Sample | Első lépések | Referencia |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Eszköz SDK az apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [Github](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitális Twin ügyfélminták](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Csatlakozás az IoT Hubhoz](./quickstart-connect-pnp-device-c-linux.md) | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Eszköz SDK vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [Github](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitális Twin ügyfélminták](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Csatlakozás az IoT Hubhoz](./quickstart-connect-pnp-device-c-windows.md) | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [Eszköz SDK-ja az EMBED-en](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [Github](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitális Twin ügyfélminták](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Eszköz SDK az Arduino IDE-ben](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [Github](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitális Twin ügyfélminták](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Eszköz SDK a CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [Github](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitális Twin ügyfélminták](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [Github](https://github.com/Azure/azure-iot-sdk-csharp) | [Digitális Twin minták](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Csatlakozás az IoT Hubhoz](./quickstart-connect-pnp-device-csharp.md) | [Referencia](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [Github](https://github.com/Azure/azure-iot-sdk-java) | [Digitális Twin minták](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Csatlakozás az IoT Hubhoz](./quickstart-connect-pnp-device-java.md) | [Referencia](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [Github](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Digitális Twin minták](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Csatlakozás az IoT Hubhoz](./quickstart-connect-pnp-device-node.md) | [Referencia](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Az IoT Hub támogatása

Az IoT Plug and Play eszközök képességeit csak [az ingyenes és szabványos szintű IoT-központok támogatják.](../iot-hub/iot-hub-scaling.md)

## <a name="next-steps"></a>További lépések

Az eszköz SDK-k és kódtárak mellett rest API-k használatával is kommunikálhat a modelltártárakkal.