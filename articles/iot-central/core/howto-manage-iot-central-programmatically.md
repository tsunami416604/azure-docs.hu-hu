---
title: IoT Central programozott kezelése | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet IoT Central programozott módon. Az alkalmazást több nyelvi SDK-val is megtekintheti, módosíthatja és távolíthatja el, például JavaScript, Python, C#, Ruby és go használatával.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/19/2020
ms.topic: how-to
ms.openlocfilehash: 773793c7681890098fea1a37cc5b9912c0ecb75c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122994"
---
# <a name="manage-iot-central-programmatically"></a>IoT Central programozott kezelése

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

IoT Central alkalmazások az [azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén való létrehozása és kezelése helyett az Azure SDK-k használatával programozott módon kezelheti alkalmazásait. A támogatott nyelvek közé tartoznak a JavaScript, a Python, a C#, a Ruby és a go.

## <a name="install-the-sdk"></a>Az SDK telepítése

A következő táblázat felsorolja az SDK-Tárházak és a csomag telepítési parancsait:

| SDK-tárház | Csomag telepítése |
| -------------- | ------------ |
| [A JavaScripthez készült Azure IotCentralClient SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [A Pythonhoz készült Microsoft Azure SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [Azure SDK for .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [Microsoft Azure SDK for Ruby-Resource Management (előzetes verzió)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [Javához készült Azure SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Maven-csomag](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [Csomagok kiadásai](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>Példák

Az [azure IOT Central ARM SDK Samples](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) adattárában több programozási nyelv is szerepel, amelyek bemutatják az Azure IoT Central-alkalmazások létrehozását, frissítését, listázását és törlését.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan felügyelheti az Azure IoT Central-alkalmazásokat programozott módon, egy javasolt következő lépés az [Azure Resource Manager](../../azure-resource-manager/management/overview.md) szolgáltatás további megismerése.