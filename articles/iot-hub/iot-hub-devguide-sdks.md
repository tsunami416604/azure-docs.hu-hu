---
title: Az Azure IoT SDK-k ismertetése |} Microsoft Docs
description: Fejlesztői útmutató - információk és hivatkozások a különböző Azure IoT eszköz és a szolgáltatás SDK-k, amelyek segítségével eszközön futó alkalmazások és a háttér-alkalmazásokat hozhat létre.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71d02e3f8bf0757d7ea0622e76b95acda1fefc95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Megismeréséhez és használatához Azure IoT Hub SDK-k

Az IoT-központ való munkához software development Kit (SDK) két kategóriába sorolhatók:

* **Eszközoldali SDK-k** lehetővé teszik az IoT-eszközök futó alkalmazások. Ezek az alkalmazások telemetriai adatokat küldeni az IoT hub, és opcionálisan üzenetek, feladat, módszer vagy kettős frissítések fogadása az IoT hub.

* **Szolgáltatás SDK-k** lehetővé teszik az IoT hub, kezelése és opcionálisan üzenetek küldéséhez, feladatok ütemezése, közvetlen metódusok vagy kívánt tulajdonság frissítéseket küld az IoT-eszközök.

További tudnivalók az Azure IoT SDK-k használatával történő fejlesztéséhez előnyei [Itt][lnk-benefits-blog].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Az Azure SDK-k IoT-eszközök

A Microsoft Azure IoT-eszközök SDK-k, amely elősegíti a épület eszközök és alkalmazások és szolgáltatások Azure IoT-központ által kezelt-kódot tartalmaz.

.NET-keretrendszerhez készült Azure IoT Hub eszköz SDK: 
* Telepítse a [Nuget][lnk-nuget-csharp-device]
* [Forráskód][lnk-dotnet-sdk]
* [API-referencia][lnk-dotnet-ref]

Az Azure IoT Hub eszköz SDK ANSI C (C99) írt adatok hordozhatóságára és széles körű platformkompatibilitásra C:
* Telepítse a [apt get, MBED, Arduino IDE vagy Nuget][lnk-c-package]
* [Forráskód][lnk-c-sdk]
* [API-referencia][lnk-c-ref]

Javához készült Azure IoT Hub eszköz SDK: 
* Adja hozzá [Maven] [ lnk-maven-device] projekt
* [Forráskód][lnk-java-sdk]
* [API-referencia][lnk-java-ref]

A Node.js az Azure IoT Hub eszköz SDK: 
* Telepítse a [npm][lnk-npm-device]
* [Forráskód][lnk-node-sdk]
* [API-referencia][lnk-node-ref]

Python-hez készült Azure IoT Hub eszköz SDK: 
* Telepítse a [pip][lnk-pip-device]
* [Forráskód][lnk-python-sdk]

> [!NOTE]
> Tekintse meg a GitHub-adattárak readme fájljaiban találhat használatáról nyelvet és a platform-specifikus csomag kezelők bináris fájljait és a függőségek telepítése a fejlesztési számítógépén.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Az operációs rendszer platform és hardver kompatibilitása

Bizonyos hardvereszközök kompatibilisek SDK kapcsolatos további információkért tekintse meg a [Azure IoT eszköz katalógus hitelesített] [ lnk-certified] vagy egyedi tárházba.

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDKs

Az Azure IoT szolgáltatás SDK-k lehetővé teszi az épület alkalmazásokat közvetlenül az IoT-központ kezelheti az eszközöket és a biztonsági kódot tartalmazhatnak.

.NET-keretrendszerhez készült Azure IoT-központ szolgáltatás SDK:
* Töltse le a [Nuget][lnk-nuget-csharp-service]
* [Forráskód][lnk-dotnet-sdk]
* [API-referencia][lnk-dotnet-service-ref]

Javához készült Azure IoT-központ szolgáltatás SDK: 
* Adja hozzá [Maven] [ lnk-maven-service] projekt
* [Forráskód][lnk-java-sdk]
* [API-referencia][lnk-java-service-ref]

A Node.js az Azure IoT-központ szolgáltatás SDK: 
* Töltse le a [npm][lnk-npm-service]
* [Forráskód][lnk-node-sdk]
* [API-referencia][lnk-node-service-ref]

Python-hez készült Azure IoT-központ szolgáltatás SDK: 
* Töltse le a [pip][lnk-pip-service]
* [Forráskód][lnk-python-sdk]

Az Azure IoT-központ szolgáltatás SDK C: 
* Töltse le a [apt get, MBED, Arduino IDE vagy Nuget][lnk-c-package]
* [Forráskód][lnk-c-sdk]

> [!NOTE]
> Tekintse meg a GitHub-adattárak readme fájljaiban találhat használatáról nyelvet és a platform-specifikus csomag kezelők bináris fájljait és a függőségek telepítése a fejlesztési számítógépén.


## <a name="next-steps"></a>További lépések

Az IoT Hub fejlesztői útmutató egyéb témaköröket tartalmazza:

* [IoT-központok végpontjai][lnk-devguide-endpoints]
* [Az IoT-központ lekérdezési nyelv eszköz twins, a feladatok és az üzenet-útválasztás][lnk-devguide-query]
* [Kvóták és sávszélesség-szabályozás][lnk-devguide-quotas]
* [Az IoT Hub MQTT támogatása][lnk-devguide-mqtt]
* [Az IoT Hub REST API-referencia][lnk-rest-ref]

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
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
