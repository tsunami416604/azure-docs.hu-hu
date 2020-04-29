---
title: Automation-fiók hibaelhárítása
description: Útmutató az Azure-fiókkal kapcsolatos problémák elhárításához és megoldásához.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679382"
---
# <a name="troubleshoot-the-automation-account"></a>Az Automation-fiók hibáinak megoldása

Ez a cikk az Automation-fiók használatakor felmerülő problémák megoldásait ismerteti. Az alábbi részekben a konkrét hibaüzenetek és a lehetséges megoldások jelennek meg. Az Automation-fiókokkal kapcsolatos általános információkért lásd: [Azure-fiók létrehozása](../automation-quickstart-create-account.md).

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

2. Navigáljon az **előfizetések** lapra, és válassza ki az előfizetését az előfizetések lapon.   

3. A **Beállítások**területen válassza az **erőforrás-szolgáltatók**elemet.

4. Az erőforrás-szolgáltatók listájában ellenőrizze, hogy a **Microsoft. Automation** erőforrás-szolgáltató regisztrálva van-e.

5. Ha a szolgáltató nem szerepel a listáján, regisztrálja az [erőforrás-szolgáltatói regisztráció hibáinak elhárítása](/azure/azure-resource-manager/resource-manager-register-provider-errors)című témakörben leírtak szerint.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* Az Azure [@AzureSupport](https://twitter.com/azuresupport)-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, a hivatalos Microsoft Azure fiókkal való csatlakozással javíthatja az ügyfelek élményét: válaszokat, támogatást és szakértőket.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.