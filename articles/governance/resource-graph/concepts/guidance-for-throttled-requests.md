---
title: Útmutatás szabályozott kérésekhez
description: Ismerje meg, hogy a csoportosítás, a tántolás, a paginate és a lekérdezés párhuzamosan, hogy elkerüljék a kérelmek et az Azure Resource Graph.
ms.date: 12/02/2019
ms.topic: conceptual
ms.openlocfilehash: fbd4bec715b187bcc643fe32b8452b0e062e7713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259850"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Szabályozással kapcsolatos kérelmekhez vezető útmutató az Azure Resource Graph ban

Az Azure Resource Graph-adatok programozott és gyakori használata kor, figyelembe kell venni, hogy a szabályozás hogyan befolyásolja a lekérdezések eredményeit. Az adatok kérésének módosítása segíthet Önnek és a szervezetnek elkerülni a szabályozást, és az Azure-erőforrásokra vonatkozó időben iratot.

Ez a cikk négy területet és mintákat tartalmaz az Azure Resource Graph lekérdezések létrehozásához kapcsolódóan:

- Szabályozási fejlécek ismertetése
- Lekérdezések csoportosítása
- Lekérdezések eltolása
- A tördelés hatása

## <a name="understand-throttling-headers"></a>Szabályozási fejlécek ismertetése

Az Azure Resource Graph kvótaszámot rendel le minden felhasználó számára egy időablak alapján. Például egy felhasználó küldhet legbőlegben 15 lekérdezések belül 5 másodperces ablakban anélkül, hogy szabályozott. A kvótaértéket számos tényező határozza meg, és változhat.

Az Azure Resource Graph minden lekérdezési válaszban két sávszélesség-szabályozási fejlécet ad hozzá:

- `x-ms-user-quota-remaining`(int): A felhasználó fennmaradó erőforráskvótája. Ez az érték leképezi a lekérdezések számát.
- `x-ms-user-quota-resets-after`(óó:pp:ss): A felhasználó kvótafelhasználásának alaphelyzetbe állításáig tartó időtartam.

A fejlécek működésének szemléltetéséhez nézzünk meg egy lekérdezési választ, `x-ms-user-quota-resets-after: 00:00:03`amelynek fejléce és értékei és a és a értékei `x-ms-user-quota-remaining: 10` vannak.

- A következő 3 másodpercen belül legbelül legbőlegelhető 10 lekérdezések nélkül lehet benyújtani.
- 3 másodperc múlva `x-ms-user-quota-remaining` az `x-ms-user-quota-resets-after` értékek, `15` és `00:00:05` vissza áll, és volt.

Ha példát szeretne látni arra, hogy a fejlécek használatával _kihátrálanak_ a lekérdezési kérelmekhez, tekintse meg a [Lekérdezés párhuzamos című](#query-in-parallel)témakörben található mintát.

## <a name="grouping-queries"></a>Lekérdezések csoportosítása

A lekérdezések csoportosítása az előfizetés, erőforráscsoport vagy az egyes erőforrások szerint hatékonyabb, mint a lekérdezések párhuzamosítása. A nagyobb lekérdezések kvótaköltsége gyakran kisebb, mint sok kis és célzott lekérdezés kvótaköltsége. A csoport mérete ajánlott kevesebb, mint _300_.

- Példa a rosszul optimalizált megközelítésre

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Példa #1 optimalizált csoportosítási megközelítésre

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Példa #2 egy optimalizált csoportosítási megközelítésre, amely több erőforrás beszerzésére tesz lehetővé egy lekérdezésben

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Lekérdezések eltolása

A szabályozás kényszerítésének módja miatt javasoljuk, hogy a lekérdezések lépcsőzetesen legyenek. Ez azt, hogy ahelyett, hogy egyszerre 60 lekérdezést küldene, a lekérdezéseket négy 5 másodperces ablakba tántorogja:

- Nem lépcsőzetes lekérdezésütemezés

  | Lekérdezések száma         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Időköz (mp) | 0-5 | 5-10 | 10-15 | 15-20 |

- Lépcsőzetes lekérdezésütemezés

  | Lekérdezések száma         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Időköz (mp) | 0-5 | 5-10 | 10-15 | 15-20 |

Az alábbiakban egy példa látható a szabályszabályozás-fejlécek tiszteletben tartásáról az Azure Resource Graph lekérdezésekéneksorán:

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

### <a name="query-in-parallel"></a>Lekérdezés párhuzamosan

Annak ellenére, hogy a csoportosítás párhuzamosításon keresztül ajánlott, vannak esetek, amikor a lekérdezések nem könnyen csoportosíthatók. Ezekben az esetekben előfordulhat, hogy szeretné lekérdezni az Azure Resource Graph több lekérdezések párhuzamos módon küldött. Az alábbi példa bemutatja, hogyan _lehet_ az ilyen esetekben a szabályosító fejlécek alapján visszalépni:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

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

Mivel az Azure Resource Graph legbőrészt 1000 bejegyzést ad vissza egyetlen lekérdezési válaszban, előfordulhat, hogy [a](./work-with-data.md#paging-results) teljes adatkészlet lekéri a keresett adatkészletet. Egyes Azure Resource Graph-ügyfelek azonban másként kezelik a tördelést, mint mások.

- C# SDK

  A ResourceGraph SDK használatakor a tördelést úgy kell kezelnie, hogy átadja az előző lekérdezésválaszból visszaadott átugrást a következő lapszámozott lekérdezésnek. Ez a kialakítás azt jelenti, hogy össze kell gyűjteni az eredményeket az összes lapszámozott hívások és összekapcsolják őket együtt a végén. Ebben az esetben minden elküldött lapszámozott lekérdezés egy lekérdezési kvótát vesz igénybe:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
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

- Azure CLI / Azure PowerShell

  Az Azure CLI vagy az Azure PowerShell használata esetén az Azure Resource Graph lekérdezései automatikusan lapszámozásra kerülnek, hogy legtöbb mint 5000 bejegyzéssel érkezz. A lekérdezés eredményei az összes lapszámozott hívás ból származó bejegyzések összesített listáját adják vissza. Ebben az esetben a lekérdezés eredményében szereplő bejegyzések számától függően egyetlen lapszámozott lekérdezés egynél több lekérdezési kvótát is igénybe vehet. Az alábbi példában például a lekérdezés egyetlen futtatása legfeljebb öt lekérdezési kvótát vehet fel:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Még mindig fojtogatnak?

Ha a fenti ajánlások gyakorlása után fojtogatják, lépjen kapcsolatba [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)a csapattal a .

Adja meg a következő adatokat:

- A konkrét használati eset és az üzleti illesztőprogram nagyobb sávszélesség-szabályozási korlátot igényel.
- Hány erőforráshoz fér hozzá? Hány a dedáka egyetlen lekérdezés?
- Milyen típusú források érdeklik?
- Mi a lekérdezési minta? X lekérdezések /Y másodperc stb.

## <a name="next-steps"></a>További lépések

- Tekintse meg a használt nyelvet az [Indítólekérdezésekben.](../samples/starter.md)
- Lásd: Speciális használat a [Speciális lekérdezésekben.](../samples/advanced.md)
- További információ az [erőforrások felfedezéséről.](explore-resources.md)