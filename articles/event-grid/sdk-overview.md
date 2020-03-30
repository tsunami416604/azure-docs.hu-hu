---
title: Azure Event Grid SDK-k
description: Az Azure Event Grid SDK-k ismertetése. Ezek az SDK-k biztosítják a kezelést, a közzétételt és a felhasználást.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60822847"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Eseményrács sdk-k kezelése és közzététele

Az Event Grid sdk-ket biztosít, amelyek lehetővé teszik az erőforrások programozott kezelését és az események közzétételét.

## <a name="management-sdks"></a>Felügyeleti SDK-k

A felügyeleti SDK-k lehetővé teszik eseményrács-témakörök és előfizetések létrehozását, frissítését és törlését. Jelenleg a következő SDK-k érhetők el:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Ugrás](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Adatsík SDK-k

Az sdk-k adatsík lehetővé teszi, hogy eseményeket tegyen közzé a témakörökben azáltal, hogy gondoskodik az esemény hitelesítéséről, létrehozásáról és aszinkron módon a megadott végpontra való feladási ról. Azt is lehetővé teszik, hogy a felhasználó által iszdik eseményeket. Jelenleg a következő SDK-k érhetők el:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Ugrás](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>További lépések

* Például az alkalmazások, lásd: [Event Grid kódminták](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Az Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Az Azure CLI Event Grid parancsairól az Azure CLI.For Event Grid commands in Azure CLI, see [Azure CLI.](/cli/azure/eventgrid)
* A PowerShell Event Grid-parancsairól a [PowerShell ben](/powershell/module/az.eventgrid)található.
