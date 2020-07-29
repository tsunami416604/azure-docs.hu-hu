---
title: A kérelem fejlécének hibakeresése túl nagy vagy 400 hibás kérelem Azure Cosmos DB
description: A kérelem fejlécének diagnosztizálása és kijavítása túl nagy kivétel
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294427"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Azure Cosmos DB kérelem fejlécének diagnosztizálása és megoldása túl nagy üzenet
A kérelem fejléce túl nagy üzenetet tartalmaz a 400-es HTTP-hibakód alapján. Ez a hiba akkor fordul elő, ha a kérelem fejlécének mérete túl nagy, és meghaladja a maximálisan megengedett méretet. Javasoljuk, hogy az SDK legújabb verzióját használja. Ügyeljen arra, hogy legalább a 3. x vagy 2. x verziót használja, mert ezek a verziók a fejléc méretének nyomon követését adja hozzá a kivételt jelző üzenethez.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
A kérelem fejléce túl nagy üzenet, ha a munkamenet vagy a folytatási jogkivonat túl nagy. A következő szakaszok ismertetik a probléma okát és megoldását az egyes kategóriákban.

### <a name="1-session-token-too-large"></a>1. a munkamenet-token túl nagy

#### <a name="cause"></a>Ok:
400 helytelen kérést a munkamenet tokenje nagy valószínűséggel okoz. Ha a következő utasítások igazak, akkor megerősíti, hogy a munkamenet-jogkivonat túl nagy.

* A hiba a pontokon történik, például Létrehozás, olvasás, frissítés és stb., ahol nincs folytatólagos jogkivonat.
* A kivétel az alkalmazás módosítása nélkül indult el. A munkamenet-jogkivonat nő, mint a partíciók számának növekedése a tárolóban. A partíciók számának növekedése az adatmennyiség növekedésével vagy az átviteli sebesség növelésével.

#### <a name="temporary-mitigation"></a>Ideiglenes csökkentés: 
Indítsa újra az ügyfélalkalmazás alkalmazást az összes munkamenet-token alaphelyzetbe állításához. A munkamenet tokenje végül a problémát okozó korábbi méretre fog növekedni. Ezért a következő szakaszban található megoldás használatával elkerülhető a probléma teljesen.

#### <a name="solution"></a>Megoldás:
1. Kövesse a [.net v3](performance-tips-dotnet-sdk-v3-sql.md) vagy [.NET v2](performance-tips.md) teljesítményű tippek című cikk útmutatását, és alakítsa át az alkalmazást közvetlen kapcsolati mód használatára TCP protokollal. A TCP protokollt használó közvetlen mód nem rendelkezik a fejléc méretére vonatkozó korlátozással, például a HTTP protokollal, így elkerülhető a probléma. Ügyeljen arra, hogy az SDK legújabb verzióját használja, amely a lekérdezési műveletekhez javítással rendelkezik, ha a szolgáltatás-együttműködés nem érhető el.
2. Ha a közvetlen kapcsolati mód TCP protokollal nem választható a számítási feladatokhoz, akkor az [ügyfél konzisztencia-szintjének](how-to-manage-consistency.md)módosításával enyhítheti azt. A munkamenet-jogkivonat csak a munkamenet konzisztenciájához használható, amely a Azure Cosmos DB alapértelmezett konzisztencia-szintje. Más konzisztencia-szintek nem használják a munkamenet tokenjét.

### <a name="2-continuation-token-too-large"></a>2. a folytatási token túl nagy

#### <a name="cause"></a>Ok:
Az 400-es hibás kérelem olyan lekérdezési műveleteken történik, ahol a folytatási token használatban van. Ha a folytatási jogkivonat túl nagyra nőtt, vagy ha a különböző lekérdezések eltérő folytatási jogkivonat-mérettel rendelkeznek.
    
#### <a name="solution"></a>Megoldás:
1. Kövesse a [.net v3](performance-tips-dotnet-sdk-v3-sql.md) vagy [.NET v2](performance-tips.md) teljesítményű tippek című cikk útmutatását, és alakítsa át az alkalmazást közvetlen kapcsolati mód használatára TCP protokollal. A TCP protokollt használó közvetlen mód nem rendelkezik a fejléc méretére vonatkozó korlátozással, például a HTTP protokollal, így elkerülhető a probléma. 
3. Ha a közvetlen kapcsolati mód TCP protokollal nem választható a munkaterhelés számára, akkor próbálja meg beállítani a beállítást `ResponseContinuationTokenLimitInKb` . Ezt a lehetőséget a `FeedOptions` for v2 vagy a V3 esetében találja `QueryRequestOptions` .

## <a name="next-steps"></a>További lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md)
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md) teljesítményével kapcsolatos irányelvek ismertetése
