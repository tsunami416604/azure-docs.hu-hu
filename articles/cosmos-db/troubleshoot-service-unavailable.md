---
title: Azure Cosmos DB szolgáltatás nem elérhető kivételének hibáinak megoldása
description: Cosmos DB szolgáltatás nem elérhető kivételének diagnosztizálása és javítása
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294266"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Nem érhető el a Azure Cosmos DB szolgáltatás diagnosztizálása és megoldása
Az SDK nem tudott kapcsolódni a Azure Cosmos DB szolgáltatáshoz.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
A következő lista a szolgáltatás nem elérhető kivételeinek ismert okait és megoldásait tartalmazza.

### <a name="1-the-required-ports-are-not-enabled"></a>1. a szükséges portok nincsenek engedélyezve.
Ellenőrizze, hogy az összes [szükséges port](performance-tips-dotnet-sdk-v3-sql.md#networking) engedélyezve van-e.

## <a name="if-an-existing-application-or-service-started-getting-503"></a>Ha egy meglévő alkalmazás vagy szolgáltatás megkezdte a 503

### <a name="1-there-is-an-outage"></a>1. leállás van
Tekintse meg az [Azure állapotát](https://status.azure.com/status) , és ellenőrizze, hogy van-e folyamatban probléma.

### <a name="2-snat-port-exhaustion"></a>2. SNAT-port kimerülése
Kövesse a [SNAT-porthoz tartozó kimerültség útmutatóját](troubleshoot-dot-net-sdk.md#snat).

### <a name="3-the-required-ports-are-being-blocked"></a>3. a szükséges portok blokkolása folyamatban van
Ellenőrizze, hogy az összes [szükséges port](performance-tips-dotnet-sdk-v3-sql.md#networking) engedélyezve van-e.

## <a name="next-steps"></a>További lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md)
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md) teljesítményével kapcsolatos irányelvek ismertetése