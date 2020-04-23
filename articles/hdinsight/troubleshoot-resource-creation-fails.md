---
title: Erőforrás-létrehozási hibák elhárítása az Azure HDInsight
description: Ebben a cikkben az általános kapacitással kapcsolatos problémák és a kockázatcsökkentő technikák találhatók.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/22/2020
ms.openlocfilehash: 871e6b1253b0fc9a5b6ae5d3389982cfdd6af3bd
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103177"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Erőforrás-létrehozási hibák elhárítása az Azure HDInsight

Ez a cikk az Azure HDInsight való interakció során felmerülő problémákkal kapcsolatos hibaelhárítási lépéseket és lehetséges megoldásokat ismerteti.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Hiba: az üzemelő példány túllépi a (z) 800 kvótáját

Az Azure kvótakorlátja 800 üzemelő példány egy erőforráscsoportban. A rendszer a különböző kvótákat erőforráscsoport, előfizetés, fiók vagy más hatókörök alapján alkalmazza. Lehetséges például, hogy az előfizetése úgy van konfigurálva, hogy korlátozza a régiónként elérhető magok számát. Kísérlet történt egy olyan virtuális gép üzembe helyezésére, amely az engedélyezettnél több magot használ, így a kvóta meghaladta a kvótát.

A probléma megoldásához törölje a már nem szükséges központi telepítéseket a Azure Portal, a CLI vagy a PowerShell használatával.

További információ: [Erőforráskvótákkal kapcsolatos hibák elhárítása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Hiba: a maximális csomópont túllépte az elérhető magokat ebben a régióban.

Lehetséges, hogy az előfizetés úgy van konfigurálva, hogy korlátozza a régiónként elérhető magok számát. Kísérlet történt olyan erőforrás üzembe helyezésére, amely az engedélyezettnél több magot használ, így a kvóta túllépte a kvótát.

A kvóta növelésének kéréséhez kövesse az alábbi lépéseket:

1. Lépjen a [Azure Portalra](https://portal.azure.com), és válassza a **Súgó + támogatás**lehetőséget.

1. Válassza az **új támogatási kérelem**lehetőséget.

1. Az **új támogatási kérés** lap **alapok** lapján adja meg a következő információkat:

   * **Probléma típusa:** Válassza **a szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.
   * **Előfizetés:** Válassza ki a módosítani kívánt előfizetést.
   * **Kvóta típusa:** Válassza a **HDInsight**lehetőséget.

További információ: [Támogatási jegy létrehozása a magok számának növeléséhez](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
