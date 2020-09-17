---
title: A többrégiós környezetekben elérhető Azure Cosmos SDK-k rendelkezésre állásának diagnosztizálása és megoldása
description: Ismerje meg az Azure Cosmos SDK rendelkezésre állási viselkedését többrégiós környezetben való működés esetén.
author: ealsur
ms.service: cosmos-db
ms.date: 09/16/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0c717aca88095df05fc7927f3c3d6e2d481925d2
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708917"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>A többrégiós környezetekben elérhető Azure Cosmos SDK-k rendelkezésre állásának diagnosztizálása és megoldása

Ez a cikk ismerteti az Azure Cosmos SDK-k legújabb verziójának viselkedését, ha egy adott régióhoz vagy egy régió feladatátvételéhez kapcsolódási probléma jelenik meg.

Az összes Azure Cosmos SDK lehetőséget biztosít a területi beállítások testreszabására. A különböző SDK-k a következő tulajdonságokat használják:

* A [ConnectionPolicy. PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) tulajdonság a .NET v2 SDK-ban.
* A [CosmosClientOptions. ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) vagy a [CosmosClientOptions. ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) tulajdonság a .net v3 SDK-ban.
* A [CosmosClientBuilder. preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) metódus a Java v4 SDK-ban.
* A [CosmosClient. preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) paraméter a Python SDK-ban.

Az egyszeri írási régió fiókjai esetében az összes írási művelet mindig az írási régióba kerül, így az előnyben részesített régiók lista csak olvasási műveletekre érvényes. Több írási régióhoz tartozó fiókok esetében a preferencia-lista hatással van az olvasási és írási műveletekre.

Ha nem állít be előnyben részesített régiót, a területi beállítások sorrendjét a [Azure Cosmos db régió lista sorrendjében](distribute-data-globally.md)határozhatja meg.

Ha az alábbi esetek bármelyike előfordul, az Azure Cosmos SDK-t használó ügyfél megjeleníti a naplókat, és az újrapróbálkozási információkat is tartalmazza a **művelet diagnosztikai információinak**részeként.

## <a name="removing-a-region-from-the-account"></a><a id="remove region"></a>Régió eltávolítása a fiókból

Ha eltávolít egy régiót egy Azure Cosmos-fiókból, a fiókot aktívan használó SDK-ügyfelek a háttérbeli válasz kódján keresztül fogják észlelni a régió eltávolítását. Az ügyfél ezután nem elérhetőként jelöli meg a regionális végpontot. Az ügyfél újrapróbálkozik az aktuális művelettel, és az összes jövőbeli művelet véglegesen át lesz irányítva a következő régióba, a preferencia sorrendben.

## <a name="adding-a-region-to-an-account"></a>Régió hozzáadása egy fiókhoz

Az Azure Cosmos SDK-ügyfél 5 percenként beolvassa a fiók konfigurációját, és frissíti azokat a régiókat, amelyekről tisztában van.

Ha eltávolít egy régiót, majd később visszaadja azt a fiókhoz, ha a hozzáadott régió magasabb prioritással rendelkezik, akkor az SDK visszavált a régió használatára véglegesen. A hozzáadott régió észlelése után a rendszer az összes jövőbeli kérelmet átirányítja.

Ha úgy konfigurálja az ügyfelet, hogy egy olyan régióhoz kapcsolódjon, amelyhez az Azure Cosmos-fiók nem rendelkezik, a rendszer figyelmen kívül hagyja az előnyben részesített régiót. Ha később hozzáadja ezt a régiót, akkor az ügyfél észleli azt, és véglegesen átvált az adott régióra.

## <a name="failover-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Az írási régió feladatátvétele egyetlen írási régióbeli fiókban

Ha az aktuális írási régió feladatátvételét kezdeményezi, a következő írási kérelem sikertelen lesz, amely egy ismert háttérbeli választ ad. Ha a rendszer ezt a választ észleli, az ügyfél lekérdezi a fiókot az új írási régió megismeréséhez, és folytatja az aktuális művelet ismételt megadását és az összes jövőbeli írási művelet végleges átirányítását az új régióba.

## <a name="regional-outage"></a>Regionális leállás

Ha a fiók egyetlen írási régió, és a regionális leállás egy írási művelet során következik be, a viselkedés hasonló a [manuális feladatátvételhez](#manual-failover-single-region). Olvasási kérelmek vagy több írási régió fiókjai esetén a viselkedés hasonló a [régió eltávolításához](#remove region).

## <a name="session-consistency-guarantees"></a>Munkamenet konzisztenciájának garanciái

A [munkamenet konzisztenciájának](consistency-levels.md#guarantees-associated-with-consistency-levels)használatakor az ügyfélnek biztosítania kell, hogy a saját írásait tudja olvasni. Az olyan egyszeri írási régió fiókjaiban, amelyekben az olvasási régió beállításai eltérnek az írási régiótól, előfordulhat, hogy a felhasználó írást okoz, és a helyi régióból való olvasáskor a helyi régió még nem kapta meg az adatreplikálást (a könnyű korlátozás sebessége). Ilyen esetekben az SDK észleli az olvasási művelet adott hibáját, és újrapróbálkozik a központi régió olvasásával a munkamenet konzisztenciájának biztosítása érdekében.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Átmeneti kapcsolódási problémák a TCP protokollon

Olyan esetekben, amikor az Azure Cosmos SDK-ügyfél a TCP protokoll használatára van konfigurálva, egy adott kérelem esetében előfordulhatnak olyan helyzetek, amikor a hálózati feltételek átmenetileg érintik egy adott végponttal folytatott kommunikációt. Ezek az ideiglenes hálózati feltételek TCP-időtúllépésként használhatók. Az ügyfél néhány másodpercen belül újra megkísérli a kérést helyileg ugyanazon a végponton.

Ha a felhasználó több régióval konfigurált egy előnyben részesített régiót, és az Azure Cosmos-fiók több írási régió vagy egyetlen írási régió, és a művelet egy olvasási kérelem, az ügyfél újra megpróbálja ezt az egyetlen műveletet a következő régióban a preferencia listából.

## <a name="next-steps"></a>Következő lépések

* A legújabb [.net SDK](sql-api-sdk-dotnet-standard.md) használata
* A legújabb [Java SDK](sql-api-sdk-java-v4.md) használata
* A legújabb [PYTHON SDK](sql-api-sdk-python.md) használata
* A legújabb [Node SDK](sql-api-sdk-node.md) használata
