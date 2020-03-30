---
title: Nagy méretű adathalmazok kezelése
description: Ismerje meg, hogyan szerezheti le, formázhatja, lapozhatja és ugorhatja ki a nagy adatkészletekben lévő rekordokat az Azure Resource Graph használatával végzett munka során.
ms.date: 03/20/2020
ms.topic: conceptual
ms.openlocfilehash: be15a6234935627ca748276e6330c50c3ee5a775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064742"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Nagy Azure-erőforrás-adatkészletek bővítése

Az Azure Resource Graph az Azure-környezeterőforrásaival való használatra és az erőforrásokkal kapcsolatos információk beszerzésére szolgál. A Resource Graph gyorssá teszi az adatok lekérdezését, még akkor is, ha rekordok ezreit kérdezi le. Az Erőforrásgráf számos lehetőséget kínál a nagy adatkészletek közös működésére.

A nagy gyakoriságú lekérdezésekkel kapcsolatos útmutatásért olvassa [el a Szabályozott kérelmekhez](./guidance-for-throttled-requests.md)való útmutatást.

## <a name="data-set-result-size"></a>Adatkészlet eredménymérete

Alapértelmezés szerint a Resource Graph minden lekérdezést csak **100** rekord visszaadására korlátoz. Ez a vezérlő védi a felhasználót és a szolgáltatást a nem szándékos lekérdezések, amelyek nagy adatkészletek eredményezne. Ez az esemény leggyakrabban akkor történik, amikor az ügyfél kísérletezik lekérdezésekkel, hogy megtalálja és kiszűrje az erőforrásokat az adott igényeiknek megfelelő módon. Ez a vezérlő eltér a [felső](/azure/kusto/query/topoperator) vagy [korlátozza](/azure/kusto/query/limitoperator) az Azure Data Explorer nyelvi operátorok az eredmények korlátozása.

> [!NOTE]
> Az **Első**használatakor ajánlott az eredményeket legalább egy `asc` oszlopban megrendelni a vagy `desc`a alkalmazással. Rendezés nélkül a visszaadott eredmények véletlenszerűek és nem ismételhetők.

Az alapértelmezett korlát felülbírálható az Erőforrás-gráfnal való interakció minden módszerével. Az alábbi példák bemutatják, hogyan módosíthatja az adatkészlet méretkorlátját _200-ra:_

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

A [REST API-ban](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)a vezérlő **$top,** és a **QueryRequestOptions**része.

A _legkorlátozóbb_ vezérlő fog nyerni. Ha például a lekérdezés a **legfelső** vagy **korlátoperátort** használja, és az **Elsőnél**több rekordot eredményezne, a visszaadott rekordok maximális eredménye az **Első**lesz. Hasonlóképpen, ha **a felső** vagy **a korlát** kisebb, mint az **Első,** a visszaadott rekordhalmaz a **felső** vagy **korlát**szerint beállított kisebb érték lesz.

**Az első** maximális megengedett értéke _jelenleg 5000_.

## <a name="skipping-records"></a>Rekordok kihagyása

A következő lehetőség a nagy adatkészletek használata a **Kihagyás** vezérlő. Ez a vezérlő lehetővé teszi, hogy a lekérdezés átugorja vagy kihagyja a megadott rekordok számát az eredmények visszaadása előtt. **A Kihagyás** olyan lekérdezések nél hasznos, amelyek rendezése értelmes módon történik, ahol a szándék az, hogy az eredményhalmaz közepén lévő rekordokat kapja. Ha a szükséges eredmények a visszaadott adatkészlet végén vannak, hatékonyabb egy másik rendezési konfigurációhasználata, és az eredmények lekérése az adatkészlet tetejéről.

> [!NOTE]
> A **Kihagyás**használatakor ajánlott az eredményeket legalább `asc` egy `desc`oszlopban megrendelni a vagy a alkalmazással. Rendezés nélkül a visszaadott eredmények véletlenszerűek és nem ismételhetők.

A következő példák bemutatják, hogyan lehet kihagyni az első _10_ rekordot, amelyet egy lekérdezés eredményezne, ahelyett, hogy a visszaadott eredményhalmazt a 11.

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

A [REST API-ban](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)a vezérlő **$skip,** és a **QueryRequestOptions**része.

## <a name="paging-results"></a>Lapozás eredményei

Ha az eredményhalmazt feldolgozásra szolgáló rekordok kisebb halmazára kell bontani, vagy mert az eredményhalmaz túllépné az _1000_ visszaadott rekord maximális engedélyezett értékét, használja a lapozást. A [REST API QueryResponse](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **QueryResponse** értékeket ad meg, amelyek jelzik, hogy az eredmény megszakadt: **az eredményCsonkolásés** **$skipToken**.
**resultTruncated** egy logikai érték, amely tájékoztatja a fogyasztót, ha vannak további rekordok nem ad vissza a válaszban. Ez a feltétel akkor is azonosítható, ha a **count** tulajdonság kisebb, mint a **totalRecords** tulajdonság. **Az totalRecords** azt határozza meg, hogy hány rekord felel meg a lekérdezésnek.

 **resultTruncated** **akkor igaz,** ha a lapozás le `id` van tiltva, vagy nem lehetséges, mert nincs oszlop, vagy ha kevesebb erőforrás áll rendelkezésre, mint a lekérdezés kér. Ha **az eredmény: A deszkafuttatás** **igaz,** a **$skipToken** tulajdonság nincs beállítva.

A következő példák bemutatják, hogyan **hagyhatja ki** az első 3000 rekordot, és hogyan adja vissza az **első** 1000 rekordot, miután ezek a rekordok kihagyták az Azure CLI és az Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> A lekérdezésnek ki kell **vetítenie** az **azonosítómezőt** ahhoz, hogy a tördelés működjön. Ha hiányzik a lekérdezésből, a válasz nem tartalmazza a **$skipToken.**

Például lásd: [Következő lap lekérdezés](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query) a REST API-dokumentumokban.

## <a name="formatting-results"></a>Formázási eredmények

Az Erőforrásgráf lekérdezés eredményei két formátumban, a _Table_ és az _ObjectArray_formátumban találhatók. A formátum a **resultFormat** paraméterrel van konfigurálva a kérési beállítások részeként. A _Táblázat_ formátum a **resultFormat**alapértelmezett értéke.

Az Azure CLI eredményei alapértelmezés szerint a JSON-ban találhatók. Az Azure PowerShellben az eredmények alapértelmezés szerint **PSCustomObject objektumok,** de `ConvertTo-Json` gyorsan átalakíthatók JSON-ra a parancsmag használatával. Más SDK-k esetében a lekérdezés eredményei beállíthatók az _ObjectArray_ formátum kimenetére.

### <a name="format---table"></a>Formátum - Táblázat

Az alapértelmezett formátum , _tábla_, json formátumot ad vissza, amely kiemeli a lekérdezés által visszaadott tulajdonságok oszloptervét és sorértékeit. Ez a formátum nagyon hasonlít a strukturált táblázatban vagy számolótáblában meghatározott adatokra, ahol először az azonosított oszlopok, majd minden sor az oszlopokhoz igazított adatokat jelöli.

Íme egy példa a _tábla_ formázásával rendelkező lekérdezéseredményre:

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Formátum - ObjectArray

Az _ObjectArray_ formátum JSON formátumú eredményeket is ad vissza. Ez a kialakítás azonban a JSON-ban közös kulcs-érték pár kapcsolathoz igazodik, ahol az oszlop és a soradatok tömbcsoportokban egyeztetve vannak.

Íme egy példa az _ObjectArray_ formázással rendelkező lekérdezéseredményre:

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Íme néhány példa az **resultFormat** _objectarray_ formátum használatára történő beállítására:

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>További lépések

- Tekintse meg a használt nyelvet az [Indítólekérdezésekben.](../samples/starter.md)
- Lásd: Speciális használat a [Speciális lekérdezésekben.](../samples/advanced.md)
- További információ az [erőforrások felfedezéséről.](explore-resources.md)