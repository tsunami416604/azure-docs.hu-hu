---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogy miként lehet elhárítani a különböző SDK-k hibáit, miközben Azure-erőforrásokat kérdez le az Azure Resource Graph használatával.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74303901"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Hibák elhárítása az Azure Resource Graph használatával

Az Azure-erőforrások Azure Resource Graph-ban való lekérdezése során hibák léphetnek fel. Ez a cikk az esetlegesen előforduló különböző hibákat és azok megoldását ismerteti.

## <a name="finding-error-details"></a>Hiba részleteinek megállapítása

A legtöbb hiba az Azure Resource Graph-lekérdezés futtatásakor felmerülő probléma eredménye. Ha egy lekérdezés meghiúsul, az SDK részletesen ismerteti a sikertelen lekérdezés részleteit. Ez az információ azt jelzi, hogy a probléma kijavítható, és egy későbbi lekérdezés sikeres lehet.

## <a name="general-errors"></a>Általános hibák

### <a name="toomanysubscription"></a>Forgatókönyv: túl sok előfizetés

#### <a name="issue"></a>Probléma

Azok az ügyfelek, akik több mint 1000 előfizetéshez férnek hozzá, beleértve az [Azure Lighthouse](../../../lighthouse/overview.md)-val való több-bérlős előfizetést, az Azure Resource Graph egyetlen hívásával nem hívhatják le az összes előfizetést.

#### <a name="cause"></a>Ok

Az Azure CLI és a PowerShell csak az első 1000-előfizetést továbbítja az Azure Resource Graph-ba. Az Azure Resource Graph REST API maximális számú előfizetést fogad el a lekérdezés végrehajtásához.

#### <a name="resolution"></a>Megoldás:

Batch-kérelmek a lekérdezéshez az előfizetések egy olyan részhalmazával, amely az 1000-os előfizetési korlát alatt marad. A megoldás az **előfizetés** paramétert használja a PowerShellben.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="rest-contenttype"></a>Forgatókönyv: nem támogatott Content-Type REST-fejléc

#### <a name="issue"></a>Probléma

Az Azure Resource Graph REST API lekérdező ügyfelek megkapják a _500_ (belső kiszolgálóhiba) kapott választ.

#### <a name="cause"></a>Ok

Az Azure Resource Graph REST API csak az **Application/json**`Content-Type` használatát támogatja. Néhány REST-eszköz vagy-ügynök alapértelmezett értéke **text/plain**, amelyet a REST API nem támogat.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy az Azure Resource Graph lekérdezéséhez használt eszköz vagy ügynök rendelkezik-e az **alkalmazáshoz/JSON-** hoz konfigurált REST API fejléctel `Content-Type`.

### <a name="rest-403"></a>Forgatókönyv: nincs olvasási engedély a listában szereplő összes előfizetéshez

#### <a name="issue"></a>Probléma

Azok az ügyfelek, akik explicit módon átadják egy Azure Resource Graph-lekérdezéssel rendelkező előfizetések listáját, _403_ (tiltott) választ kapnak.

#### <a name="cause"></a>Ok

Ha az ügyfél nem rendelkezik olvasási engedéllyel az összes megadott előfizetéshez, a rendszer a megfelelő biztonsági jogosultságok hiánya miatt megtagadja a kérelmet.

#### <a name="resolution"></a>Megoldás:

Vegyen fel legalább egy előfizetést az előfizetések listájában, amelyet a lekérdezést futtató ügyfélnek legalább olvasási hozzáféréssel kell rendelkezniük. További információ: [engedélyek az Azure Resource Graph-ban](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
- Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
- Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.