---
title: SDK-k Azure Event Grid
description: A Azure Event Grid SDK-kat ismerteti. Ezek az SDK-k felügyeletet, közzétételt és felhasználást biztosítanak.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "60822847"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDK-k Event Grid a felügyelethez és a közzétételhez

A Event Grid olyan SDK-kat biztosít, amelyek lehetővé teszik az erőforrások programozott felügyeletét és az események közzétételét.

## <a name="management-sdks"></a>Felügyeleti SDK-k

A felügyeleti SDK-k lehetővé teszik az Event Grid-témakörök és-előfizetések létrehozását, frissítését és törlését. Jelenleg a következő SDK-k érhetők el:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Ugrás](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Adatsík SDK-k

Az adatközponti SDK-k lehetővé teszik események küldését a témakörökbe a hitelesítés, az esemény kialakítása és a megadott végpontra történő aszinkron közzététel megtételével. Azt is lehetővé teszik, hogy az első féltől származó eseményeket használja. Jelenleg a következő SDK-k érhetők el:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Ugrás](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>További lépések

* Példa az alkalmazásokra: [Event Grid a kód mintáit](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* A Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Az Azure CLI Event Grid parancsaival kapcsolatban lásd az [Azure CLI](/cli/azure/eventgrid)-t.
* Event Grid parancsok a PowerShellben: [PowerShell](/powershell/module/az.eventgrid).
