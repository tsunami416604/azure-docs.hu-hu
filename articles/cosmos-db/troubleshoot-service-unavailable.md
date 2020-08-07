---
title: Azure Cosmos DB szolgáltatás nem elérhető kivételének hibáinak megoldása
description: Cosmos DB szolgáltatás nem elérhető kivételének diagnosztizálása és javítása
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987375"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Nem érhető el a Azure Cosmos DB szolgáltatás diagnosztizálása és megoldása
Az SDK nem tudott kapcsolódni a Azure Cosmos DB szolgáltatáshoz.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
A következő lista a szolgáltatás nem elérhető kivételeinek ismert okait és megoldásait tartalmazza.

### <a name="1-the-required-ports-are-being-blocked"></a>1. a szükséges portok blokkolása folyamatban van
Ellenőrizze, hogy az összes [szükséges port](performance-tips-dotnet-sdk-v3-sql.md#networking) engedélyezve van-e.

### <a name="2-client-side-transient-connectivity-issues"></a>2. ügyféloldali átmeneti kapcsolódási problémák
A szolgáltatás nem érhető el, mert az időtúllépést okozó átmeneti kapcsolódási problémák miatt nem használhatók a felületek. Általában az ehhez a forgatókönyvhöz kapcsolódó verem-nyomkövetés tartalmazni fog egy `TransportException` , például:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

A probléma megoldásához kövesse a [kérés időtúllépésének elhárítása című témakört](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) .

### <a name="3-service-outage"></a>3. a szolgáltatás kimaradása
Tekintse meg az [Azure állapotát](https://status.azure.com/status) , és ellenőrizze, hogy van-e folyamatban probléma.


## <a name="next-steps"></a>További lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md)
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md) teljesítményével kapcsolatos irányelvek ismertetése