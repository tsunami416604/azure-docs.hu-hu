---
title: Azure Cosmos DB kérelmek sebességének és a nagy kivételnek a megoldása
description: Kérelmek arányának diagnosztizálása és kijavítása nagy kivétel esetén
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294267"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>Cosmos DB túl sok kérés diagnosztizálása és megoldása
"A kérelmek aránya túl nagy" vagy a 429-es hibakód azt jelzi, hogy a kérések szabályozása folyamatban van.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Az alábbi lista túl sok kérelemhez tartalmaz ismert okokat és megoldásokat.

### <a name="1-check-the-metrics"></a>1. a metrikák keresése
Az ügyfélnek ellenőriznie kell a [Azure Cosmos db figyelését](monitor-cosmos-db.md) , hogy ellenőrizze, hogy a 429-es számú kivétel szerepel-e.

## <a name="cause"></a>Ok:
A felhasznált átviteli sebesség (RU/s) túllépte a [kiosztott átviteli sebességet](set-throughput.md). Az SDK automatikusan újrapróbálkozik a kérelmekkel a megadott újrapróbálkozási házirend alapján. Ha ez a hiba gyakran előfordul, érdemes lehet növelni az átviteli sebességet a gyűjteményen. Tekintse meg a portál metrikáit, és ellenőrizze, hogy a 429-es hibát észlel-e. Tekintse át a partíciós kulcsot, és győződjön meg arról, hogy a [tárolók és a kérelmek mennyiségének egyenletes eloszlását](partition-data.md)eredményezi.

## <a name="solution"></a>Megoldás:
1. A kiépített átviteli sebesség növeléséhez használja a [portált vagy az SDK](set-throughput.md) -t.
2. Az adatbázis vagy a tároló automatikus [skálázásra](provision-throughput-autoscale.md)váltása.

## <a name="next-steps"></a>További lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md)
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md) teljesítményével kapcsolatos irányelvek ismertetése