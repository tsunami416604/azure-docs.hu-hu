---
title: Erőforrás-létrehozási hibák elhárítása az Azure HDInsight
description: Ebben a cikkben az általános kapacitással kapcsolatos problémák és a kockázatcsökkentő technikák találhatók.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 2e9f6898dba559f557f49cc0ee9ff39644da243f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000691"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Erőforrás-létrehozási hibák elhárítása az Azure HDInsight

Ez a cikk az Azure HDInsight való interakció során felmerülő problémákkal kapcsolatos hibaelhárítási lépéseket és lehetséges megoldásokat ismerteti.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Hiba: az üzemelő példány túllépi a (z) 800 kvótáját

Az Azure kvótakorlátja 800 üzemelő példány egy erőforráscsoportban. A rendszer a különböző kvótákat erőforráscsoport, előfizetés, fiók vagy más hatókörök alapján alkalmazza. Lehetséges például, hogy az előfizetése úgy van konfigurálva, hogy korlátozza a régiónként elérhető magok számát. Kísérlet történt egy olyan virtuális gép üzembe helyezésére, amely az engedélyezettnél több magot használ, így a kvóta meghaladta a kvótát.

A probléma megoldásához törölje a már nem szükséges központi telepítéseket a Azure Portal, a CLI vagy a PowerShell használatával.

További információ: [Erőforráskvótákkal kapcsolatos hibák elhárítása](../azure-resource-manager/templates/error-resource-quota.md).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Hiba: a maximális csomópont túllépte az elérhető magokat ebben a régióban.

Lehetséges, hogy az előfizetés úgy van konfigurálva, hogy korlátozza a régiónként elérhető magok számát. Kísérlet történt olyan erőforrás üzembe helyezésére, amely az engedélyezettnél több magot használ, így a kvóta túllépte a kvótát.

A kvóta növelésének kéréséhez kövesse az alábbi lépéseket:

1. Lépjen a [Azure Portalra](https://portal.azure.com), és válassza a **Súgó + támogatás** lehetőséget.

1. Válassza az **Új támogatási kérelem** lehetőséget.

1. Az **új támogatási kérés** lap **alapok** lapján adja meg a következő információkat:

   * **Probléma típusa:** Válassza **a szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.
   * **Előfizetés:** Válassza ki a módosítani kívánt előfizetést.
   * **Kvóta típusa:** Válassza a **HDInsight** lehetőséget.

További információ: [Támogatási jegy létrehozása a magok számának növeléséhez](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>További lépések

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]