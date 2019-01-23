---
title: Azure Event Grid SDKs
description: Ismerteti az SDK-k az Azure Event Gridhez. Ezek az SDK-k adja meg a felügyeleti, közzététel és használat.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 53a1f50647e05b71afe6accb85f8a90cd8b7e253
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470401"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Event Grid készült SDK-k kezelése és közzététele

Event Grid nyújt SDK-k, amelyek lehetővé teszik, hogy programozott módon kezelheti az erőforrásokat és események közzététele.

## <a name="management-sdks"></a>Management SDKs

A management SDK-k létrehozása, frissítése és törlése az event grid-témakörök és előfizetések lehetővé teszik. Jelenleg a következő SDK-k érhetők el:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Adatok adatsík SDK-k

Az adatsík SDK-k lehetővé teszik események témakörökre való közzétételéhez gondoskodva hitelesítéséhez, az esemény képező és aszinkron módon bejegyzés közzététele a megadott végponton. Ezen kívül lehetővé teszik, hogy először a gyártótól származó események felhasználásához. Jelenleg a következő SDK-k érhetők el:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>További lépések

* Például az alkalmazások létrehozásáról: [Event Grid-Kódminták](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure CLI Event Grid parancsokért lásd: [Azure CLI-vel](/cli/azure/eventgrid).
* A PowerShellben Event Grid parancsokért lásd: [PowerShell](/powershell/module/azurerm.eventgrid).
