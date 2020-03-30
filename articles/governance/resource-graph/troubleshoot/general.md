---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogyan háríthatja el a különböző SDK-kkal kapcsolatos problémákat, miközben azure-erőforrásokat kérdez az Azure Resource Graph segítségével.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74303901"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Hibák elhárítása az Azure Resource Graph használatával

Előfordulhat, hogy hibákba ütközik, amikor az Azure Resource Graph segítségével lekérdezi az Azure-erőforrásokat. Ez a cikk ismerteti a különböző hibákat, amelyek előfordulhatnak, és hogyan lehet megoldani őket.

## <a name="finding-error-details"></a>Hibarészletek keresése

A legtöbb hiba egy probléma eredménye, miközben egy lekérdezést futtat az Azure Resource Graph használatával. Ha egy lekérdezés sikertelen, az SDK részleteket a sikertelen lekérdezés. Ez az információ jelzi a problémát, így kijavítható, és egy későbbi lekérdezés sikeres.

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Eset: Túl sok előfizetés

#### <a name="issue"></a>Probléma

A több mint 1000 előfizetéshez hozzáférő ügyfelek, beleértve az [Azure Lighthouse-előfizetéseket](../../../lighthouse/overview.md)is, nem tudnak adatokat lekérni az összes előfizetésben egyetlen hívással az Azure Resource Graph-ra.

#### <a name="cause"></a>Ok

Az Azure CLI és a PowerShell csak az első 1000 előfizetést továbbítja az Azure Resource Graph-ra. Az Azure Resource Graph REST API-ja a lekérdezés végrehajtásához szükséges előfizetések maximális számát fogadja el.

#### <a name="resolution"></a>Megoldás:

Kötegelt kérelmek a lekérdezés egy részhalmaza előfizetések alatt marad az 1000-es előfizetési korlát alatt. A megoldás az **Előfizetés** paraméter t használ a PowerShellben.

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

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Eset: Nem támogatott tartalomtípus REST fejléce

#### <a name="issue"></a>Probléma

Az Azure Resource Graph REST API-t lekérdező ügyfelek _500_ (belső kiszolgálói hiba) választ kapnak.

#### <a name="cause"></a>Ok

Az Azure Resource Graph REST `Content-Type` API csak egy **alkalmazás/json**támogatja. Egyes REST-eszközök vagy -ügynökök alapértelmezés szerint **szöveg/egyszerű,** amelyet a REST API nem támogat.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy az Azure Resource Graph lekérdezéséhez használt eszköz `Content-Type` vagy ügynök rendelkezik-e az **alkalmazás/json**REST API-fejlécével.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Eset: Nincs olvasási engedély a listában szereplő összes előfizetéshez

#### <a name="issue"></a>Probléma

Azok az ügyfelek, akik explicit módon adják át az Azure Resource Graph-lekérdezéssel rendelkező előfizetések listáját, _403-as_ (tiltott) választ kapnak.

#### <a name="cause"></a>Ok

Ha az ügyfél nem rendelkezik olvasási engedéllyel az összes megadott előfizetéshez, a rendszer a megfelelő biztonsági jogok hiánya miatt elutasítja a kérést.

#### <a name="resolution"></a>Megoldás:

Legalább egy előfizetést vegyen fel az előfizetési listára, amelyhez a lekérdezést futtató ügyfél legalább olvasási hozzáféréssel rendelkezik. További információ: [Permissions in Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

- Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
- Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba – a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
- Ha további segítségre van szüksége, benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**