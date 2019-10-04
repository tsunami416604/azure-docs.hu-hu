---
title: Útmutató a szabályozott kérelmeinek száma
description: Ismerje meg, hatékonyabb lekérdezések elkerülése érdekében kérelmek szabályozva az Azure erőforrás-diagramhoz.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276898"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Útmutató a szabályozott kérelmeinek száma az Azure Erőforrás-grafikon

Programozott és gyakori használatát az Azure-erőforrás gráfadatok létrehozásakor figyelembe kell hozni hogyan szabályozási hatással van a lekérdezések eredményeit. Az adatok megjelenítésének, a rendszer lekéri segítséget, valamint a szervezet elkerülése érdekében a szabályozás alatt áll, és az Azure-erőforrások időben adatok fenntartása érdekében.

Ez a cikk ismerteti a négy területek és a lekérdezések az Azure Erőforrás-grafikon létrehozása kapcsolatos minták:

- Sávszélesség-szabályozási fejlécek ismertetése
- Lekérdezések kötegelés
- Elképesztő lekérdezések
- Tördelés hatása

## <a name="understand-throttling-headers"></a>Sávszélesség-szabályozási fejlécek ismertetése

Az Azure Erőforrás-grafikon kvóta száma minden felhasználó egy olyan időkeretet alapján foglalja le. Például egy felhasználó is küldhetnek legfeljebb 15 lekérdezést minden 5 másodperces időtartamon belül nem szabályozás alatt áll. A kvótaérték számos tényező határozza meg, és változhatnak.

Minden lekérdezés válaszban az Azure-erőforrás Graph két szabályozási fejléceket ad hozzá:

- `x-ms-user-quota-remaining` (int): A felhasználó többi erőforrás kvótáját. Ez az érték lekérdezés száma képezi le.
- `x-ms-user-quota-resets-after` (ÓÓ:) Az időtartam, amíg a felhasználó kvóta fogyasztás alaphelyzetbe áll.

Megmutatják, hogyan működnek a fejlécek, nézzük meg, amelynek a fejlécet és értékeit lekérdezési válasz `x-ms-user-quota-remaining: 10` és `x-ms-user-quota-resets-after: 00:00:03`.

- A következő 3 másodpercen belül legfeljebb 10 lekérdezések szabályozva nélkül lehet benyújtani.
- 3 másodperc, értékeit `x-ms-user-quota-remaining` és `x-ms-user-quota-resets-after` visszaáll `15` és `00:00:05` jelölik.

A fejlécek történő használatának példájáért tekintse meg a _leállítási_ a lekérdezésekre vonatkozó kérelmek, tekintse meg a mintát, a [párhuzamos lekérdezés](#query-in-parallel).

## <a name="batching-queries"></a>Lekérdezések kötegelés

Az előfizetés, erőforráscsoport vagy egyéni erőforrás lekérdezések kötegelés hatékonyabb, mint a párhuzamosan futtatni a lekérdezést. A nagyobb lekérdezés kvóta költsége gyakran kisebb, mint a sok kis méretű, célzott lekérdezések kvóta költségét. Ajánlott a Köteg mérete lehet kisebb, mint _300_.

- Egy rosszul optimalizált megközelítésre példa

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Optimalizált kötegelés megközelítés #1. példa

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Optimalizált kötegelés megközelítés #2. példa

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Elképesztő lekérdezések

Módja miatt szabályozás van érvényben, javasoljuk, hogy a lekérdezések során. Vagyis helyett 60 lekérdezéseket küld egyszerre, szinkronizálások eltolása lekérdezések négy 5 másodperces Windows:

- A nem egyenletesen elosztani lekérdezés ütemezése

  | Lekérdezés száma         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Időköz (másodperc) | 0-5 | 5-10 | 10-15 | 15-20 |

- Egyenletesen elosztani a lekérdezés ütemezése

  | Lekérdezés száma         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Időköz (másodperc) | 0-5 | 5-10 | 10-15 | 15-20 |

Az alábbi, az Azure Erőforrás-grafikon lekérdezésekor tiszteletben tartja a sávszélesség-szabályozási fejlécek példát:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Párhuzamos lekérdezés

Annak ellenére, hogy a kötegelés keresztül ezerszer használata javasolt, vannak olyan helyzetek, ahol lekérdezéseket nem lehet könnyen kötegelni. Ezekben az esetekben érdemes az Azure-erőforrás gráf lekérdezése több lekérdezés párhuzamos módon küldésével. Alul látható egy példa bemutatja, hogyan _leállítási_ szabályozás ilyen forgatókönyvek például a fejlécek alapján:

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Tördelés

Mivel az Azure-erőforrás Graph legfeljebb 1000 bejegyzés egyetlen lekérdezés választ ad vissza, szükség lehet [böngész](./work-with-data.md#paging-results) a lekérdezéseket a keresett teljes adatkészleten. Azonban egyes Azure-erőforrás Graph-ügyfelek kezeléséhez tördelés eltérően, mint mások.

- C# SDK

  ResourceGraph SDK használatakor adja át az előző lekérdezésekre adott válaszok a következő többoldalas lekérdezésre a visszaadott kihagyandó lexikális tördelés kezelni kell. Ez a kialakítás azt jelenti, hogy kell gyűjteni az eredmények összes többoldalas hívás és összevonni őket a végén. Ebben az esetben minden egyes többoldalas lekérdezést küld egy lekérdezés kvóta hajtja végre:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Az Azure CLI- / Azure PowerShell

  Azure CLI-vel vagy az Azure PowerShell használatakor az Azure-erőforrás Graph-lekérdezések vannak automatikusan többoldalas legfeljebb 5000 bejegyzés beolvasni. A lekérdezési eredmények visszaadása minden többoldalas hívások tételek kombinált listája. Ebben az esetben a lekérdezés eredménye a bejegyzések számától függően egyetlen többoldalas lekérdezés felhasználható egynél több lekérdezés kvótát. Például az alábbi példában a lekérdezés egyetlen futtatással felhasználható legfeljebb öt lekérdezés kvóta:

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>Továbbra is leszabályozza?

Ha, még a fenti javaslatok gyakorlása után első szabályozva, lépjen kapcsolatba a csapatának [ resourcegraphsupport@microsoft.com ](mailto:resourcegraphsupport@microsoft.com).

Adja meg az alábbi adatokat:

- Az adott használati esetekre és üzleti illesztőprogram szabályozási nagyobb Korlátértékre van szüksége.
- Hány erőforrások rendelkezik hozzáféréssel? A rendszer hány egy lekérdezés által visszaadott egyetlen?
- Milyen típusú erőforrások szeretne?
- Mi az a lekérdezés minta? Lekérdezések Y másodpercenként stb.

## <a name="next-steps"></a>További lépések

- Tekintse meg a használt nyelv [alapszintű lekérdezéseket](../samples/starter.md).
- Tekintse meg a speciális használ [összetettebb lekérdezésekhez](../samples/advanced.md).
- Ismerje meg, hogyan [források](explore-resources.md).