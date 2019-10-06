---
title: Nagy méretű adathalmazok kezelése
description: A nagyméretű adathalmazok beszerzésének és vezérlésének megismerése az Azure Resource Graph használata közben.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 4da890a5ef7acb44d0e8628dc4ec3904f6a065e4
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980322"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Nagyméretű Azure-beli erőforrás-adatkészletek használata

Az Azure Resource Graph az Azure-környezetben található erőforrásokkal való munkavégzésre és információk beszerzésére szolgál. Az erőforrás-gráf az adatok gyors beolvasását is lehetővé teszi, még akkor is, ha több ezer rekordot kérdez le. Az erőforrás-gráf számos lehetőséget kínál a nagyméretű adatkészletek használatához.

A lekérdezések magas gyakorisággal történő kezelésével kapcsolatos útmutatásért lásd: [útmutató a szabályozott kérelmekhez](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Az adatkészlet eredményének mérete

Alapértelmezés szerint az erőforrás-gráf korlátozza a lekérdezéseket, hogy csak **100** rekordokat adjanak vissza. Ez a vezérlő védi a felhasználót és a szolgáltatást olyan véletlen lekérdezésektől, amelyek nagy adatkészleteket eredményezhetnek. Ez az esemény leggyakrabban akkor fordul elő, amikor az ügyfél a lekérdezésekkel kísérletezik, és az adott igényeknek megfelelő módon keresi és szűri az erőforrásokat. Ez a vezérlő más, mint a [felső](/azure/kusto/query/topoperator) vagy [Az Azure adatkezelő](/azure/kusto/query/limitoperator) nyelvi operátorok használatának korlátozása az eredmények korlátozásához.

> [!NOTE]
> **Első**használata esetén ajánlott az eredményeket legalább egy olyan oszlop szerint rendelni, amelynek `asc` vagy `desc` értékűnek kell lennie. Rendezés nélkül a visszaadott eredmények véletlenszerűek, és nem ismételhetők.

Az alapértelmezett korlát felülbírálható az erőforrás-Gráftal való interakció minden módszerén keresztül. Az alábbi példák bemutatják, hogyan módosíthatja az adathalmaz méretének korlátját a _200_értékre:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

A [REST API](/rest/api/azureresourcegraph/resources/resources)a vezérlő **$Top** , és a **QueryRequestOptions**része.

A _legszigorúbb_ vezérlő fog nyerni. Ha például a lekérdezés a **felső** vagy a **korlátot** használja, és az **elsőnél**több rekordot fog eredményezni, akkor a visszaadott maximális rekordok megegyeznek az **elsővel**. Hasonlóképpen, ha a **felső** vagy a **korlát** kisebb, mint az **első**, a visszaadott rekord a **felső** vagy a **korlát**által konfigurált kisebb érték lesz.

Az **első** jelenleg a maximálisan engedélyezett _5000_-as értékkel rendelkezik.

## <a name="skipping-records"></a>Rekordok kihagyása

A nagyméretű adatkészletek használatának következő lehetősége a **kihagyás** vezérlőelem. Ez a vezérlő lehetővé teszi, hogy a lekérdezés átugorjon vagy kihagyja a megadott számú rekordot, mielőtt visszaadná az eredményeket. A **skip (kihagyás** ) olyan lekérdezések esetében hasznos, amelyek értelmes módon jelenítik meg az eredményeket. Ha a szükséges eredmények a visszaadott adathalmaz végén találhatók, akkor hatékonyabb, ha más rendezési konfigurációt használ, és az eredményeket az adathalmaz elejéről kéri le.

> [!NOTE]
> A **skip**használata esetén ajánlott az eredményeket legalább egy olyan oszlop szerint rendelni, amelynek `asc` vagy `desc` értékűnek kell lennie. Rendezés nélkül a visszaadott eredmények véletlenszerűek, és nem ismételhetők.

Az alábbi példák azt mutatják be, hogyan lehet kihagyni az első _10_ rekordot, amely a lekérdezés eredményét eredményezi, ehelyett a visszaadott eredményhalmaz 11. rekorddal való megadásával:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

A [REST API](/rest/api/azureresourcegraph/resources/resources)a vezérlő **$skip** , és a **QueryRequestOptions**része.

## <a name="paging-results"></a>Lapozás eredményei

Ha egy eredményhalmaz kisebb készletekre való bontására van szükség a feldolgozáshoz, vagy mert egy eredményhalmaz túllépi az engedélyezett maximális _1000_ -as értéket, használja a lapozást. A [REST API](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** a következő értékeket adja meg: **resultTruncated** és **$skipToken**.
a **resultTruncated** egy logikai érték, amely tájékoztatja a fogyasztót, ha a válaszban nem ad vissza további rekordokat. Ez az állapot akkor is azonosítható, ha a **Count** tulajdonság kisebb, mint a **totalRecords** tulajdonság. a **totalRecords** határozza meg, hogy hány rekord felel meg a lekérdezésnek.

Ha a **resultTruncated** értéke **igaz**, a válaszban a **$skipToken** tulajdonság van beállítva. Ez az érték ugyanazokkal a lekérdezési és előfizetési értékekkel együtt használható a lekérdezésnek megfelelő rekordok következő készletének beolvasásához.

Az alábbi példák bemutatják, hogyan **hagyhatja** ki az első 3000 rekordot, és az **első** 1000 rekordot visszaküldheti az Azure CLI-vel kihagyott és a Azure PowerShell:

```azurecli-interactive
az graph query -q "project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> A lekérdezésnek az **azonosító** mezőt kell megadnia ahhoz, **hogy a tördelés** működjön. Ha hiányzik a lekérdezésből, a válasz nem tartalmazza a **$skipToken**.

Példaként tekintse meg a [következő oldal lekérdezését](/rest/api/azureresourcegraph/resources/resources#next-page-query) a REST API docs webhelyen.

## <a name="next-steps"></a>További lépések

- Tekintse meg az [alapszintű lekérdezésekben](../samples/starter.md)használt nyelvet.
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](../samples/advanced.md).
- Ismerje meg az [erőforrások feltárását](explore-resources.md).