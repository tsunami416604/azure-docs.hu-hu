---
title: Ismerje meg az Azure IoT SDK-kat | Microsoft dokumentumok
description: Fejlesztői útmutató – információk és hivatkozások a különböző Azure IoT-eszközökés szolgáltatás SDK-k, amelyek segítségével eszközalkalmazások és háttéralkalmazások.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: be5fae45ee513dddf002995ce9c37c6b6565f50c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258388"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Az Azure IoT Hub SDK-k ismertetése és használata

Az IoT Hubbal való együttműködéshez a szoftverfejlesztői készletek (SDK-k) két kategóriába sorolhatók:

* **Az IoT Hub-eszköz SDK-k** lehetővé teszik, hogy olyan alkalmazásokat hozzon létre, amelyek az IoT-eszközökön futnak az eszközügyfél vagy a modulügyfél használatával. Ezek az alkalmazások telemetriát küldenek az IoT Hubnak, és opcionálisan üzeneteket, feladatokat, metódusokat vagy ikereszköz-frissítéseket fogadhatnak az IoT Hubból.  A modulügyfél segítségével [modulokat](../iot-edge/iot-edge-modules.md) is létrehozhat az [Azure IoT Edge futásidejű rendszeréhez.](../iot-edge/about-iot-edge.md)

* **Az IoT Hub-szolgáltatás SDK-i** lehetővé teszik, hogy háttéralkalmazásokat hozzon létre az IoT hub kezeléséhez, és szükség esetén üzeneteket küldjön, feladatokat ütemezzen, közvetlen metódusokat hívjana meg, vagy küldje el a kívánt tulajdonságfrissítéseket az IoT-eszközökre vagy -modulokra.

Emellett sdk-k készletét is biztosítjuk az [eszközkiépítési szolgáltatással](../iot-dps/about-iot-dps.md)való munkához.
* **Az eszköz SDK-k kiépítése** lehetővé teszi, hogy az IoT-eszközökön futó alkalmazásokat hozzon létre az eszközkiépítési szolgáltatással való kommunikációhoz.

* **A kiépítési szolgáltatás SDK-k** lehetővé teszik, hogy háttéralkalmazásokat hozzon létre az eszközkiépítési szolgáltatásban történő regisztrációk kezeléséhez.

Ismerje meg az [Azure IoT SDK-k fejlesztésének előnyeit.](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Operációs rendszer platform- és hardverkompatibilitása

Az SDK-k támogatott platformjai az [Azure IoT SDK platformtámogatásban](iot-hub-device-sdk-platform-support.md)találhatók.

Az SDK adott hardvereszközökkel való kompatibilitásáról az [Azure Certified for IoT eszközkatalógusban](https://catalog.azureiotsolutions.com/) vagy az egyes tárházban talál további információt.

## <a name="azure-iot-hub-device-sdks"></a>Az Azure IoT Hub-eszköz SDK-k

A Microsoft Azure IoT-eszköz SDK-k olyan kódot tartalmaznak, amely megkönnyíti az Azure IoT Hub-szolgáltatásokhoz kapcsolódó és azok által felügyelt alkalmazások készítését.

Azure IoT Hub-eszköz SDK a .NET: 

* Letöltés [a NuGet .](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)  A névtér a Microsoft.Azure.Devices.Clients, amely IoT Hub-eszközügyfeleket (DeviceClient, ModuleClient) tartalmaz.
* [Forráskód](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-hivatkozás](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Modul hivatkozása](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Az Azure IoT Hub C-hez (ANSI C – C99) eszközSDK-je:

* Telepítés [apt-get, MBED, Arduino IDE vagy iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-c)
* [A C-eszköz SDK fordítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API-hivatkozás](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Modul hivatkozása](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [A C SDK portolása más platformokra](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Fejlesztői dokumentáció](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) a keresztfordításról, a különböző platformokon való indulásról stb.
* [Az Azure IoT Hub C SDK erőforrás-felhasználási adatai](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Azure IoT Hub-eszköz SDK Java-hoz: 

* Hozzáadás a [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projekthez
* [Forráskód](https://github.com/Azure/azure-iot-sdk-java)
* [API-hivatkozás](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Modul hivatkozása](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Azure IoT Hub-eszköz SDK node.js: 

* Telepítés [npm-től](https://www.npmjs.com/package/azure-iot-device)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-node)
* [API-hivatkozás](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Modul hivatkozása](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Azure IoT Hub-eszköz SDK Pythonhoz: 

* Telepítés [pip-ből](https://pypi.org/project/azure-iot-device/)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-python)
* [API-hivatkozás](https://docs.microsoft.com/python/api/azure-iot-device)

Az Azure IoT Hub-eszköz SDK-iOS-hez: 

* Telepítés a [CocoaPod-ról](https://cocoapods.org/pods/AzureIoTHubClient)
* [Példák](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API-hivatkozás: lásd: [C API-hivatkozás](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Az Azure IoT Hub szolgáltatás SDK-i

Az Azure IoT szolgáltatás SDK-k olyan kódot tartalmaznak, amely megkönnyíti az olyan alkalmazások készítését, amelyek közvetlenül együttműködnek az IoT Hubbal az eszközök és a biztonság kezeléséhez.

Az Azure IoT Hub sdk szolgáltatása a .NET-hez:

* Letöltés [a NuGet .](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  A névtér a Microsoft.Azure.Devices, amely az IoT Hub szolgáltatásügyfeleit (RegistryManager, ServiceClients) tartalmazza.
* [Forráskód](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-hivatkozás](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Az Azure IoT Hub szolgáltatás SDK Java-hoz: 

* Hozzáadás a [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projekthez
* [Forráskód](https://github.com/Azure/azure-iot-sdk-java)
* [API-hivatkozás](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Az Azure IoT Hub szolgáltatás SDK-ja a Node.js-hez: 

* Letöltés [az npm-től](https://www.npmjs.com/package/azure-iothub)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-node)
* [API-hivatkozás](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Az Azure IoT Hub szolgáltatás SDK pythonhoz: 

* Letöltés [pip](https://pypi.python.org/pypi/azure-iot-hub/)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-python/tree/master)

Az Azure IoT Hub szolgáltatás SDK C-hez: 

* Letöltés [az apt-get, MBED, Arduino IDE vagy NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-c)

Az Azure IoT Hub szolgáltatás SDK iOS-hez: 

* Telepítés a [CocoaPod-ról](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Példák](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Tekintse meg a readme fájlokat a GitHub-adattárak ban a nyelvi és platformspecifikus csomagkezelők használatával kapcsolatos információkért a bináris fájlok és a fejlesztői gép függőségei telepítéséhez.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure kiépítési SDK-k

A **Microsoft Azure-kiépítési SDK-k** lehetővé teszik, hogy eszközöket létesítsen az IoT Hubba az [eszközkiépítési szolgáltatás](../iot-dps/about-iot-dps.md)használatával.

Azure kiépítési eszköz és szolgáltatás SDK-k a C#:

* Töltse le az [Eszköz SDK-ból](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) és [a Service SDK-ból](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) a NuGet-től.
* [Forráskód](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API-hivatkozás](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Azure Provisioning device and service SDKs for C:

* Telepítés [apt-get, MBED, Arduino IDE vagy iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API-hivatkozás](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Azure Provisioning device and service SDKs for Java:

* Hozzáadás a [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projekthez
* [Forráskód](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API-hivatkozás](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Azure kiépítési eszköz és szolgáltatás SDK-k node.js:

* [Forráskód](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API-hivatkozás](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* [Eszköz SDK](https://badge.fury.io/js/azure-iot-provisioning-device) és [Service SDK](https://badge.fury.io/js/azure-iot-provisioning-service) letöltése npm-től

Azure kiépítési eszköz és szolgáltatás SDK-k python:

* [Forráskód](https://github.com/Azure/azure-iot-sdk-python)
* [Eszköz SDK](https://pypi.org/project/azure-iot-device/) és [Service SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) letöltése pip-ből

## <a name="next-steps"></a>További lépések

Az Azure IoT SDK-k a fejlesztéshez szükséges eszközöket is biztosítják:
* [iothub-diagnosztika:](https://github.com/Azure/iothub-diagnostics)egy platformfüggetlen parancssori eszköz, amely segít az IoT Hubbal való kapcsolattal kapcsolatos problémák diagnosztizálására.
* [azure-iot-explorer:](https://github.com/Azure/azure-iot-explorer)egy platformfüggetlen asztali alkalmazás, amely csatlakozik az IoT Hubhoz, és hozzáadó/kezel/kommunikál az IoT-eszközökkel.

Az Azure IoT SDK-k használatával történő fejlesztéssel kapcsolatos releváns dokumentumok:
* Ismerje [meg, hogyan kezelheti a kapcsolatot és a megbízható üzenetküldést](iot-hub-reliability-features-in-sdks.md) az IoT Hub SDK-k használatával.
* További információ [arról, hogyan fejleszthet mobilplatformokra,](iot-hub-how-to-develop-for-mobile-devices.md) például iOS-re és Androidra.
* [Az Azure IoT SDK platformtámogatása](iot-hub-device-sdk-platform-support.md)


Az IoT Hub fejlesztői útmutatójának további referenciatémakörei a következők:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md)
* [Az IoT Hub lekérdezési nyelve az ikereszközök, feladatok és az üzenetek útválasztása](iot-hub-devguide-query-language.md)
* [Kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md)
* [Az IoT Hub MQTT támogatása](iot-hub-mqtt-support.md)
* [IoT Hub REST API-referencia](/rest/api/iothub/)
