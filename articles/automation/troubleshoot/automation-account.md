---
title: A fiókkal kapcsolatos problémák elhárítása Azure Automation
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a problémákat egy Azure-fiókkal.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: bebc89514da7f28015da5cb145ae50bf3fccdb6a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680079"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>A fiókkal kapcsolatos problémák elhárítása Azure Automation

Ez a cikk a Azure Automation-fiók használatakor felmerülő problémák megoldásait ismerteti. Az Automation-fiókokkal kapcsolatos általános információkért lásd: [Azure Automation fiók hitelesítésének áttekintése](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Forgatókönyv: nem sikerült regisztrálni az Automation erőforrás-szolgáltatót az előfizetésekhez

### <a name="issue"></a>Probléma

Ha például az Automation-fiókban Update Management, a felügyeleti funkciókkal dolgozik, a következő hibaüzenet jelenik meg:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Ok

Az Automation erőforrás-szolgáltató nincs regisztrálva az előfizetésben.

### <a name="resolution"></a>Megoldás:

Az Automation erőforrás-szolgáltató regisztrálásához kövesse az alábbi lépéseket a Azure Portalban:

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com).

2. Lépjen az **előfizetések**elemre, és válassza ki az előfizetését.   

3. A **Beállítások**területen válassza az **erőforrás-szolgáltatók**elemet.

4. Az erőforrás-szolgáltatók listájában ellenőrizze, hogy a **Microsoft. Automation** erőforrás-szolgáltató regisztrálva van-e.

5. Ha a szolgáltató nem szerepel a listáján, regisztrálja az [erőforrás-szolgáltatói regisztráció hibáinak elhárítása](/azure/azure-resource-manager/resource-manager-register-provider-errors)című témakörben leírtak szerint.

## <a name="next-steps"></a>További lépések

Ha ez a cikk nem oldja meg a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) . Ez a hivatalos Microsoft Azure fiók az Azure-Közösség megfelelő erőforrásokhoz való csatlakoztatásához: válaszok, támogatás és szakértők.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése**lehetőséget.