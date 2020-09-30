---
title: IoT Plug and Play jelenlegi kiadás | Microsoft Docs
description: Megtudhatja, mit tartalmaz a jelenlegi IoT Plug and Play kiadás.
author: dominicbetts
ms.author: dobett
ms.date: 09/19/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4cfc9ca14a4b344423a27d9683114de5275fb20b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583557"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>Mi a jelenlegi IoT Plug and Play kiadás

Ez a cikk az aktuális IoT Plug and Play kiadását támogató eszközöket, SDK-kat és API-kat foglalja össze. A megjelenített verziószámok azt jelzik, hogy az időponthoz tartozó IoT Plug and Play általánosan elérhetővé vált. A verziók száma a kiadás után megnövelhető.

## <a name="modeling-language"></a>Modellezési nyelv

[Digitális Twins Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Ha többet szeretne megtudni arról, hogy a IoT Plug and Play-eszközök hogyan működnek a DTDL-mel, tekintse meg a [IoT Plug and Play konvenciókat](concepts-convention.md).

## <a name="tools-and-utilities"></a>Eszközök és segédprogramok

- Az Azure IoT Explorer 0.12.0.

    További információt az [Azure IoT Explorer telepítése és használata](howto-use-iot-explorer.md)című témakörben talál.

- VS Code Extension 1.0.0.

    További információ: [install and use The DTDL authoring Tools](howto-use-dtdl-authoring-tools.md).

- Visual Studio 2019 kiterjesztés 1.0.0.

    További információ: [install and use The DTDL authoring Tools](howto-use-dtdl-authoring-tools.md).

- Azure CLI-IoT bővítmény 0.10.0.

    További információ: az Azure [IoT bővítmény telepítése és használata az Azure CLI-hez](howto-use-iot-pnp-cli.md).

    > [!TIP]
    > Az Azure IoT bővítmény olyan parancsokat tartalmaz, amelyek segítségével hitelesítheti az eszközöket. Lásd: `az iot product -h`.



## <a name="libraries-and-sdks"></a>Kódtárak és SDK-k

További információ a kódtárak és SDK-k használatáról: [Microsoft SDK-k a IoT Plug and Play](libraries-sdks.md).

- C Device SDK [VcPkg 2020-07-19](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)
- .NET-eszköz SDK [NuGet Microsoft. Azure. Devices. Client 1.30.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
- Java-eszköz SDK [Maven IOT-eszköz-ügyfél 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Python-eszköz SDK [pip Azure-IOT-Device v 2.2.0](https://pypi.org/project/azure-iot-device/)
- Node.js Device SDK [NPM Azure-IOT-Device 1.17.1](https://www.npmjs.com/package/azure-iot-device)
- .NET – IoT Hub szolgáltatás [NuGet Microsoft. Azure. Devices 1.22.0](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
- Java – IoT Hub Service [Maven IoT – szolgáltatás – ügyfél-1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-service-client)
- Node.js-IoT Hub Service [NPM Azure-iothub 1.12.5](https://www.npmjs.com/package/azure-iothub)
- Python-IoT Hub/digitális Twins szolgáltatás [pip Azure-IoT-hub 2.2.2](https://pypi.org/project/azure-iot-hub/)
- DTDL Model Parser [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

### <a name="preview"></a>Előnézet

- Azure SDK for Embedded [1.0.0 – előzetes verzió. 5](https://github.com/Azure/azure-sdk-for-c/releases/tag/1.0.0-preview.5)
- Azure RTOS IoT middleware [v6.0_beta3](https://github.com/azure-rtos/azure-iot-preview/releases/tag/v6.0_beta3)

## <a name="rest-apis"></a>REST API-k

REST API [2020-09-30](https://docs.microsoft.com/rest/api/iothub).

További információ: [IoT Plug and Play fejlesztői útmutató](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>IoT Hub

A IoT Plug and Play az összes régióban IoT Hub támogatja. A IoT Plug and Play csak a standard vagy az ingyenes szint IoT-hubok támogatják.

## <a name="announcements"></a>Közlemények

Az aktuális és az előző IoT Plug and Play hirdetményekben tekintse meg a következő blogbejegyzéseket:

- [Nyilvános előzetes verzió frissítése (kifüggesztett augusztus 29., 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Készítse elő és tanúsítsa az eszközeit a IoT Plug and Play (közzétették: augusztus 26., 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [A IoT Plug and Play már előzetes verzióban érhető el (közzétették: augusztus 22., 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Build az Azure IoT Central és a IoT Plug and Play (2019. május 7.)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

