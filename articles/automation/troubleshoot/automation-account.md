---
title: Azure Automation fiók hibaelhárítása
description: Útmutató az Azure-fiókkal kapcsolatos problémák elhárításához és megoldásához.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 6a7defdaa9b4b0f0b3580a3ac6b1a0487434a97c
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864130"
---
# <a name="troubleshoot-an-azure-automation-account"></a>Azure Automation fiók hibáinak megoldása

Ez a cikk a Azure Automation-fiók használatakor felmerülő problémák megoldásait ismerteti. Az Automation-fiókokkal kapcsolatos általános információkért lásd: [Azure-fiók létrehozása](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Forgatókönyv: nem sikerült regisztrálni az Automation erőforrás-szolgáltatót az előfizetésekhez

### <a name="issue"></a>Probléma

Ha az Automation-fiókban felügyeleti megoldásokkal dolgozik, a következő hibaüzenet jelenik meg:

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
* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport). Ez a hivatalos Microsoft Azure fiók az Azure-Közösség megfelelő erőforrásokhoz való csatlakoztatásához: válaszok, támogatás és szakértők.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése**lehetőséget.