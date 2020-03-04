---
title: Kérelemkorlátozások és -szabályozás
description: Ismerteti, hogyan használható az Azure Resource Manager által szabályozás előfizetési korlátok elérésekor.
ms.topic: conceptual
ms.date: 10/26/2019
ms.custom: seodec18
ms.openlocfilehash: 43ccf4f2e8098f6577f18943c4ab4132884b66f2
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251343"
---
# <a name="throttling-resource-manager-requests"></a>Resource Manager-kérelmek szabályozása

Ez a cikk azt ismerteti, hogyan Azure Resource Manager szabályozza a kérelmeket. Bemutatja, hogyan követheti nyomon a korlátot megelőzően megmaradó kérelmek számát, és hogyan válaszolhat a korlát elérésekor.

A szabályozás két szinten történik. Azure Resource Manager szabályozza az előfizetés és a bérlő kérelmeit. Ha a kérelem az előfizetés és a bérlő szabályozási korlátja alá esik, a Resource Manager átirányítja a kérést az erőforrás-szolgáltatónak. Az erőforrás-szolgáltató a műveletekhez igazított szabályozási korlátozásokat alkalmaz. Az alábbi képen látható, hogyan történik a szabályozás alkalmazása a felhasználótól a Azure Resource Manager és az erőforrás-szolgáltatótól.

![Kérelmek szabályozása](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Előfizetés és bérlő korlátai

Minden előfizetési szintű és bérlői szintű művelethez szabályozási korlátok vonatkoznak. Az előfizetési kérelmek olyanok, amelyek az előfizetés-azonosító átadását foglalják magukban, például az erőforráscsoportok beolvasása az előfizetésben. Bérlő kérelmek nem tartalmaznak, például lekér érvényes Azure-helyen az előfizetés-Azonosítóját.

Az alapértelmezett szabályozási korlát/óra az alábbi táblázatban látható.

| Hatókör | Műveletek | Korlát |
| ----- | ---------- | ------- |
| Előfizetést | Olvasás | 12000 |
| Előfizetést | törli | 15 000 |
| Előfizetést | Írja | 1200 |
| Bérlő | Olvasás | 12000 |
| Bérlő | Írja | 1200 |

Ezeknek a korlátoknak a hatóköre a kéréseket intéző rendszerbiztonsági tag (felhasználó vagy alkalmazás) és az előfizetés azonosítója vagy a bérlő azonosítója. Ha a kérések több rendszerbiztonsági tagból érkeznek, akkor az előfizetésben vagy a bérlőben a korlát nagyobb, mint óránként 12 000 olvasás és 1200 írás.

Ezek a korlátok vonatkoznak minden Azure Resource Manager-példány. Több példány minden Azure-régióban, és Azure Resource Manager az összes Azure-régióban üzemel.  Így a gyakorlatban a határértékek magasabbak, mint a határértékek. A felhasználóktól érkező kéréseket általában a Azure Resource Manager különböző példányai kezelik.

## <a name="resource-provider-limits"></a>Erőforrás-szolgáltatói korlátok

Az erőforrás-szolgáltatók saját szabályozási korlátokat alkalmaznak. Mivel az erőforrás-kezelő az elsődleges azonosító és a Resource Manager-példány alapján szabályozza a szabályozást, előfordulhat, hogy az erőforrás-szolgáltató több kérést kap, mint az előző szakasz alapértelmezett korlátai.

Ez a szakasz a széles körben használt erőforrás-szolgáltatók szabályozási korlátait tárgyalja.

### <a name="storage-throttling"></a>Tárolás szabályozása

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Hálózati sávszélesség-szabályozás

A Microsoft. Network erőforrás-szolgáltató a következő szabályozási korlátokat alkalmazza:

| Művelet | Korlát |
| --------- | ----- |
| írás/törlés (PUT) | 1000/5 perc |
| olvasás (GET) | 10000/5 perc |

### <a name="compute-throttling"></a>Számítási szabályozás

További információ a számítási műveletek korlátozásáról: [API-szabályozási hibák elhárítása – számítás](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md).

Virtuálisgép-méretezési csoporton belüli virtuálisgép-példányok ellenőrzéséhez használja a [Virtual Machine Scale sets műveleteket](/rest/api/compute/virtualmachinescalesetvms). Használja például a virtuálisgép [-méretezési csoport virtuális gépek-lista](/rest/api/compute/virtualmachinescalesetvms/list) paramétereit a virtuálisgép-példányok energiagazdálkodási állapotának vizsgálatához. Ez az API csökkenti a kérelmek számát.

### <a name="azure-resource-graph-throttling"></a>Az Azure Resource Graph szabályozása

Az [Azure Resource Graph](../../governance/resource-graph/overview.md) korlátozza a műveleteire irányuló kérések számát. A cikkben ismertetett lépések alapján megállapíthatja a fennmaradó kérelmeket, és hogyan reagálhat a korlátra, ha az erőforrás-gráfra is érvényes. Az erőforrás-gráf azonban beállítja a saját korlátját és az alaphelyzetbe állítási arányt. További információ: az [erőforrás-gráf szabályozására szolgáló fejlécek](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

## <a name="request-increase"></a>Kérések növekedése

Időnként a szabályozás korlátai is megnövelhető. Ha szeretné megtekinteni, hogy a forgatókönyv szabályozási korlátai megnövelhető-e, hozzon létre egy támogatási kérést. A rendszer kiértékeli a hívási minta részleteit.

## <a name="error-code"></a>Hibakód

Ha eléri a korlátot, a 429-as HTTP-állapotkód **túl sok kérést**kap. A válasz tartalmazza az **újrapróbálkozások** utáni értéket, amely meghatározza, hogy az alkalmazás hány másodpercig várjon (vagy alvó állapotba), mielőtt elküldené a következő kérést. Az újrapróbálkozások letelte előtt egy kérelmet küld, ha a kérelem feldolgozása nem, és a egy új újrapróbálkozási értéket adja vissza.

A megadott időpontra való várakozás után lezárhatja és újra megnyithatja az Azure-hoz való kapcsolódást. A kapcsolat alaphelyzetbe állításával csatlakozhat a Azure Resource Manager egy másik példányához.

Ha Azure SDK-t használ, lehet, hogy az SDK automatikus újrapróbálkozási konfigurációval rendelkezik. További információ: [újrapróbálkozási útmutató az Azure-szolgáltatásokhoz](/azure/architecture/best-practices/retry-service-specific).

Egyes erőforrás-szolgáltatók a 429-et egy ideiglenes probléma jelentésére adják vissza. A probléma olyan túlterhelési feltétel lehet, amely közvetlenül nem a kérelem által okozott. Vagy ideiglenes hibát jelenthet a célként megadott erőforrás vagy a függő erőforrás állapotáról. A hálózati erőforrás-szolgáltató például a 429 értéket adja vissza a **RetryableErrorDueToAnotherOperation** hibakódtal, ha a célként megadott erőforrás zárolva van egy másik művelettel. Annak megállapításához, hogy a hiba a szabályozásból vagy egy ideiglenes állapotból származik-e, tekintse meg a válaszban a hiba részleteit.

## <a name="remaining-requests"></a>Fennmaradó kérelmek

Megadhatja, hogy a fennmaradó kérések száma válaszfejlécek megvizsgálásával. Az olvasási kérelmek egy értéket adnak vissza a fejlécben a hátralévő olvasási kérelmek számának megfelelően. Az írási kérelmek tartalmazzák a fennmaradó írási kérések számát. A következő táblázat ismerteti a válaszfejlécek ezekhez az értékekhez ellenőrizheti:

| Válaszfejléc | Leírás |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Hatókörrel rendelkező előfizetés beolvasása van hátra. Ezt az értéket adja vissza az olvasási műveletekre. |
| x-ms-ratelimit-remaining-subscription-writes |Hatókörrel rendelkező előfizetés ír van hátra. Ez az érték az írási művelet adja vissza. |
| x-ms-ratelimit-remaining-tenant-reads |Hatókörrel rendelkező bérlő beolvasása van hátra |
| x-ms-ratelimit-remaining-tenant-writes |Hatókörrel rendelkező bérlő ír van hátra |
| x-ms-ratelimit-remaining-subscription-resource-requests |Előfizetés a fennmaradó erőforrást típusú kérések hatókörét.<br /><br />A fejléc értéke csak akkor ad vissza, ha egy szolgáltatás rendelkezik felül az alapértelmezett korlát. Erőforrás-kezelő hozzáadja ezt az értéket az előfizetés olvasási vagy írási műveletek helyett. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Előfizetés erőforrás típusa adatgyűjtési kérelmek fennmaradó hatókörét.<br /><br />A fejléc értéke csak akkor ad vissza, ha egy szolgáltatás rendelkezik felül az alapértelmezett korlát. Ezt az értéket megadja a fennmaradó adatgyűjtési kérelmek (lista erőforrások). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Bérlői erőforrás-típusú kérések fennmaradó hatókörét.<br /><br />Ez a fejléc csak bérlői szintű kérelmek ad hozzá, és csak akkor, ha egy szolgáltatás rendelkezik felül az alapértelmezett korlát. Erőforrás-kezelő hozzáadja ezt az értéket a bérlő olvasások és írások helyett. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Bérlői erőforrás típusa adatgyűjtési kérelmek fennmaradó hatókörét.<br /><br />Ez a fejléc csak bérlői szintű kérelmek ad hozzá, és csak akkor, ha egy szolgáltatás rendelkezik felül az alapértelmezett korlát. |

Az erőforrás-szolgáltató a válasz fejléceit is visszaküldheti a fennmaradó kérelmekkel kapcsolatos információkkal. A számítási erőforrás-szolgáltató által visszaadott válasz fejlécekkel kapcsolatos információkért tekintse meg a [hívási sebességre vonatkozó tájékoztatási fejléceket](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>A fejléc értékek beolvasása

Ezek a kódot vagy szkriptet fejléc az értékek beolvasása semmiben nem különbözik minden fejléc értékének beolvasása. 

Például a alkalmazásban **C#** a fejléc értékét egy **Válasz** nevű **HttpWebResponse** objektumból kell lekérnie a következő kóddal:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

A **PowerShellben**a fejléc értékét egy meghívó-webkérési műveletből kéri le.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Teljes PowerShell-példaként tekintse [meg az előfizetés Resource Manager-korlátainak ellenőrzését](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)ismertető témakört.

Ha szeretné megtekinteni a további hibakeresési kérelmeket, megadhatja a **-Debug** paramétert a **PowerShell** -parancsmagon.

```powershell
Get-AzResourceGroup -Debug
```

Több értékhez, többek között a következő választ értéket ad vissza:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Írási korlátok lekéréséhez használja az írási művelet: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Több értékhez, többek között a következő értékeket ad vissza:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

Az **Azure CLI**-ben a fejléc értékét a részletesebb lehetőség használatával kérdezheti le.

```azurecli
az group list --verbose --debug
```

Több értékhez, többek között a következő értékeket ad vissza:

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Írási korlátok lekéréséhez használja az írási művelet: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Több értékhez, többek között a következő értékeket ad vissza:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Következő lépések

* Teljes PowerShell-példaként tekintse [meg az előfizetés Resource Manager-korlátainak ellenőrzését](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)ismertető témakört.
* További információ a korlátozásokról és a kvótáról: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* További információ az aszinkron REST-kérelmek kezeléséről: az [aszinkron Azure-műveletek nyomon követése](async-operations.md).
