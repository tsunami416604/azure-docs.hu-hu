---
title: "Az Azure Resource Manager kérelmekre vonatkozó korlátokat |} Microsoft Docs"
description: "Ismerteti, hogyan használható az Azure Resource Manager által szabályozás, amikor a rendszer elérte az előfizetési korlátozásait."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: tomfitz
ms.openlocfilehash: 6d7eeaf460674c3ab98425a5412ffa465b9ffd1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="throttling-resource-manager-requests"></a>Erőforrás-kezelő szabályozás
Minden egyes előfizetésekhez és bérlői erőforrás-kezelő korlátok óránként 15 000 kérelmek és olvasási kérések 1200 óránként. Ezek a korlátozások vonatkoznak az Azure Resource Manager feltünteti; több példánya van minden Azure-régiót, és az Azure Resource Manager a rendszer minden Azure-régió.  Így a, a gyakorlatban korlátok hatékonyan sokkal nagyobb, mint a fent felsorolt, felhasználóként kérelmek általában által kiszolgált számos különböző példányai.

Ha az alkalmazás vagy a parancsfájl eléri a működés felső korlátjának, amelyekkel korlátozhatja a kérelmeket szüksége. Ez a témakör bemutatja, hogyan határozza meg a fennmaradó kérelmek korlát elérése előtt, és hogyan válaszol, amikor elérte a maximális.

Amikor eléri a határértéket, kapja-e a HTTP-állapotkód: **429-es jelű túl sok kérelem**.

A kérelmek száma az előfizetés vagy a bérlő hatókörét. Ha több, egyidejű alkalmazások kérelmet benyújtó az előfizetésében szereplő azon alkalmazások kérelmeinek felvétele fennmaradó kérések száma meghatározása érdekében.

Az előfizetés hatókörrel rendelkező kérelmek azok a átadja az előfizetés-azonosítóval, például az erőforrás beolvasása involve csoportosítja az előfizetésben. Bérlői hatókörű kérelmek nem tartalmaznak az előfizetés-azonosítóval, mint érvényes Azure helyek lekérése.

## <a name="remaining-requests"></a>Fennmaradó kérelmek
Válaszfejlécek megvizsgálásával azt is meghatározhatja a fennmaradó kérelmek száma. Minden egyes kérelem fennmaradó olvasási és írási kérések száma értékeket tartalmaz. A következő táblázat ismerteti a válaszfejlécek ezeket az értékeket a ellenőrizheti:

| Válaszfejléc | Leírás |
| --- | --- |
| x-MS-ratelimit-remaining-Subscription-reads |Előfizetés hatókörű beolvassa a fennmaradó |
| x-MS-ratelimit-remaining-Subscription-writes |Előfizetés hatókörű ír fennmaradó |
| x-MS-ratelimit-remaining-tenant-reads |Bérlői hatókörű beolvassa a fennmaradó |
| x-MS-ratelimit-remaining-tenant-writes |Bérlői hatókörű ír fennmaradó |
| x-MS-ratelimit-remaining-Subscription-Resource-Requests |Előfizetés fennmaradó erőforrás típusú kérések hatókörét.<br /><br />Ezt a fejlécértéket csak akkor ad vissza, ha a szolgáltatás felülírta az alapértelmezett határérték. Erőforrás-kezelő hozzáadása az előfizetés olvasások és írások helyett ezt az értéket. |
| x-MS-ratelimit-remaining-Subscription-Resource-entities-Read |Előfizetés az erőforrás típusa adatgyűjtési kérelmek fennmaradó hatókörét.<br /><br />Ezt a fejlécértéket csak akkor ad vissza, ha a szolgáltatás felülírta az alapértelmezett határérték. Ez az érték a fennmaradó adatgyűjtési kérelmek (lista erőforrások) számát jeleníti meg. |
| x-MS-ratelimit-remaining-tenant-Resource-Requests |Bérlői erőforrás típusú kérések fennmaradó hatókörét.<br /><br />A bérlői szintű kérelmek csak hozzáadja az ezt a fejlécet, és csak akkor, ha a szolgáltatás felülírta az alapértelmezett határérték. Erőforrás-kezelő felveszi ezt az értéket a bérlő olvasások és írások helyett. |
| x-MS-ratelimit-remaining-tenant-Resource-entities-Read |Bérlői erőforrás típusa adatgyűjtési kérelmek fennmaradó hatókörét.<br /><br />A bérlői szintű kérelmek csak hozzáadja az ezt a fejlécet, és csak akkor, ha a szolgáltatás felülírta az alapértelmezett határérték. |

## <a name="retrieving-the-header-values"></a>A fejléc értékei beolvasása
A fejléc értékei a kód vagy parancsfájl beolvasása ugyanolyan helyzetet teremt, mint bármely fejléc értékének beolvasása. 

Például a **C#**, visszaállíthatja a Fejlécérték egy **HttpWebResponse** nevű objektum **válasz** az alábbi kódra:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

A **PowerShell**, a fejléc értékének lekérése az Invoke-WebRequest műveletet.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Vagy, ha szeretne látni a fennmaradó kérelmek hibakeresési, megadhatja a **-Debug** paraméter a **PowerShell** parancsmag.

```powershell
Get-AzureRmResourceGroup -Debug
```

Több értékhez, többek között a következő válasz értéket visszaadó:

```powershell
...
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 14999
...
```

A **Azure CLI**, a részletesebb beállítás használatával beolvashatja a fejléc értéke.

```azurecli
azure group list -vv --json
```

Visszaadó sok értéket, beleértve a következő objektumot:

```azurecli
...
silly: returnObject
{
  "statusCode": 200,
  "header": {
    "cache-control": "no-cache",
    "pragma": "no-cache",
    "content-type": "application/json; charset=utf-8",
    "expires": "-1",
    "x-ms-ratelimit-remaining-subscription-reads": "14998",
    ...
```

## <a name="waiting-before-sending-next-request"></a>Várakozás a következő kérelem elküldése előtt
Ha eléri a kérelmi korlátjának, erőforrás-kezelő adja vissza a **429** HTTP-állapotkód és egy **újrapróbálkozási után** a fejléc értéke. A **újrapróbálkozási után** érték másodperc megvárja-e az alkalmazás (vagy alvó) számát adja meg a következő kérelem elküldése előtt. Az újrapróbálási értéket letelte előtt kérelmet küld, ha a kérelem nincs feldolgozva, és próbálkozzon újra új értéket ad vissza.

## <a name="next-steps"></a>Következő lépések

* Korlátozásai és a kvóták kapcsolatos további információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md).
* Aszinkron REST-kérések kezelésével kapcsolatos információkért lásd: [nyomon követheti a aszinkron Azure műveleteket](resource-manager-async-operations.md).
