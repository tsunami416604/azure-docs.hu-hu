---
title: Azure Event Grid SDKs
description: A témakör ismerteti az SDK-k Azure esemény rács. Ezeket az SDK-k adja meg a felügyeleti, a közzététel és -fogyasztás.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: d9bb4b3b161060f20fca34760872a24cbfcabf30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Esemény rács készült SDK-k kezelése és közzététele

Esemény rács SDK-kat, amelyek lehetővé teszik programokon keresztül kezelheti az erőforrásokat, és események közzétételéhez biztosít.

## <a name="management-sdks"></a>Felügyeleti SDK-k

A felügyeleti SDK-k lehetővé teszik a létrehozása, frissítése és az esemény rács témakörök és előfizetések törlése. Jelenleg a következő SDK-k érhetők el:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure.eventgrid-2018-01-01/azure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Adatok vezérlősík SDK-k

Az adatok vezérlősík SDK-k lehetővé teszik események témakörök által gondoskodnak a hitelesítéshez, az esemény képező és aszinkron módon közzététel a megadott végpontot a post. Lehetővé teszik az első fél események felhasználásához. Jelenleg a következő SDK-k érhetők el:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>További lépések

* Esemény rácshoz ismertetőért lásd: [Mi az az esemény rács?](overview.md)
* Az Azure parancssori felület parancsai esemény rács, lásd: [Azure CLI](/cli/azure/eventgrid).
* PowerShell esemény rács parancsai, lásd: [PowerShell](/powershell/module/azurerm.eventgrid).