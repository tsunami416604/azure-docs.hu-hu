---
title: Nagy méretű adathalmazok kezelése
description: Megtudhatja, hogyan, és szabályozhatja a nagy méretű adatkészleteket az Azure-erőforrás Graph használata során.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 40aa8ca0ebfcc8eb5b686143960af1441768622a
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058391"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Azure-erőforrások nagy adatkészletek használata

Az Azure erőforrás-Graph úgy lett kialakítva, használata és erőforrásaival kapcsolatos információk lekérése az Azure-környezetben. Erőforrás-grafikon teszi, hogy ezek az adatok gyors, első akár több ezer olyan rekordok lekérdezésekor. Erőforrás-grafikon rendelkezik-e nagy adatkészletek használata számos lehetőség közül választhat.

## <a name="data-set-result-size"></a>Adatkészlet eredményének mérete

Alapértelmezés szerint erőforrás Graph korlátozza az adatszolgáltató csak minden lekérdezés **100** rögzíti. Ez a vezérlő megvédi a felhasználói és a szolgáltatás a véletlen lekérdezések, amelyek nagy méretű adatkészleteket eredményezne. Ez az esemény leggyakrabban akkor fordul elő, egy ügyfél keresése és erőforrások adott igényeiknek leginkább megfelelő módon szűrése lekérdezésekkel van kísérletezgetést. Ez a vezérlő eltér attól a [felső](/azure/kusto/query/topoperator) vagy [korlát](/azure/kusto/query/limitoperator) Azure adatkezelő nyelvi operátorokat korlátozza az eredményeket.

> [!NOTE]
> Használata esetén **első**, azt javasoljuk, hogy legalább egy oszlopot az eredmények rendezés `asc` vagy `desc`. Rendezés, nélkül adatsorban visszaadott a véletlenszerű, és nem ismételhető.

Az alapértelmezett korlát felülbírálható az Erőforrás-grafikon folytatott összes módszer használatával. Az alábbi példák bemutatják az adatkészlet méretének korlátját történő megváltoztatása _200_:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

Az a [REST API-val](/rest/api/azureresourcegraph/resources/resources), a vezérlő **$top** és része **QueryRequestOptions**.

A vezérlő, amely _leginkább korlátozó_ legyőzi. Például, ha a lekérdezés használ a **felső** vagy **korlát** operátorok és -nál több rekordot eredményezne **első**, a rekordok maximális visszaadott egyenlőlenne**Első**. Hasonlóképpen ha **felső** vagy **korlát** kisebb, mint **első**, a rekordhalmaz visszaadott értékének által konfigurált lenne **felső** vagy **korlát**.

**Első** jelenleg rendelkezik a megengedett maximális értékét _5000_.

## <a name="skipping-records"></a>Átugorja a rekordokat

A következő lehetőség a nagy adatkészletekkel dolgozik, a **kihagyása** vezérlő. Ez a vezérlő lehetővé teszi, hogy a jump keresztül, vagy hagyja ki a megadott számú rekord előtt az eredményt adnak vissza a lekérdezést. **Kihagyás** akkor hasznos, ha lekérdezéseket, ahol a célja a rekordok valahol az eredményhalmaz közepén található első értelmezhető módon rendezheti az eredményeket. Ha az eredmények szükséges a végén a visszaadott adatkészlet, sokkal hatékonyabban eltérő rendezési konfigurációt használja, és helyette az adatkészlet elejétől kérje le az eredményeket.

> [!NOTE]
> Használata esetén **kihagyása**, azt javasoljuk, hogy legalább egy oszlopot az eredmények rendezés `asc` vagy `desc`. Rendezés, nélkül adatsorban visszaadott a véletlenszerű, és nem ismételhető.

Az alábbi példák bemutatják az első kihagyása _10_ rekordok egy lekérdezést eredményez, és a 11 rekordot tartalmazó állítsa helyette indítása a visszaadott eredmény:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

Az a [REST API-val](/rest/api/azureresourcegraph/resources/resources), a vezérlő **$skip** és része **QueryRequestOptions**.

## <a name="paging-results"></a>Lapozás eredményei

Szükséges egy eredményhalmazt bemásolja a feldolgozáshoz kisebb rekordhalmazok esetén, vagy mert az eredményhalmaz túllépné a megengedett maximális értékét _1000_ lapozással adja vissza a rekordokat. A [REST API-val](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** értékek jelzésére egy készlet rendelkezik lett osztva az eredmények itt: **resultTruncated** és **$skipToken** .
**resultTruncated** egy logikai érték, amely tájékoztatja a fogyasztó további rekordok léteznek. Ha nem a válaszban visszaadott. Ez az állapot akkor is azonosítható, ha a **száma** tulajdonság értéke kisebb, mint a **totalRecords** tulajdonság. **totalRecords** határozza meg, hogy hány rögzíti a lekérdezésnek megfelelő.

Amikor **resultTruncated** van **igaz**, a **$skipToken** tulajdonság értéke a válaszban. Ez az érték szolgál ugyanazon lekérdezés és az előfizetés értékekkel lekérése a következő rekordkészletet, amely megfelel a lekérdezést.

> [!IMPORTANT]
> A lekérdezés kell **projekt** a **azonosító** ahhoz, hogy működjön tördelés mezőt. Hiányzik a lekérdezésből, ha a REST API-válasz nem tartalmazza a **$skipToken**.

Egy vonatkozó példáért lásd: [következő lap lekérdezés](/rest/api/azureresourcegraph/resources/resources#next_page_query) a REST API-dokumentumokhoz.

## <a name="next-steps"></a>További lépések

- Tekintse meg a használt nyelv [alapszintű lekérdezések](../samples/starter.md)
- Tekintse meg a speciális használ [összetettebb lekérdezésekhez](../samples/advanced.md)
- Információ az [erőforrások felfedezéséről](explore-resources.md)