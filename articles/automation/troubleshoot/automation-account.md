---
title: Automatizálási fiók hibaelhárítása
description: Ismerje meg, hogyan háríthatja el és oldhatja meg az Azure-fiókkal kapcsolatos problémákat.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c66b1728144b8517f6ac444059b3a8def956c6e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301004"
---
# <a name="automation-account-troubleshooting"></a>Automatizálási fiók hibaelhárítása

Ez a cikk az Automation-fiók használata során felmerülő problémák megoldásait ismerteti. A következő szakaszok konkrét hibaüzeneteket és lehetséges megoldásokat emelnek ki mindegyikhez. Az Automation-fiókokról az [Azure-fiók létrehozása című](../automation-quickstart-create-account.md)témakörben talál általános tudnivalókat.

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Eset: Nem lehet regisztrálni az automation erőforrás-szolgáltatót az előfizetésekhez

### <a name="issue"></a>Probléma

Amikor felügyeleti megoldásokkal dolgozik az Automation-fiókban, a következő hibába ütközik:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Ok

Az Automation erőforrás-szolgáltató nincs regisztrálva az előfizetésben.

### <a name="resolution"></a>Megoldás:

Az Automation Erőforrás-szolgáltató regisztrálásához kövesse az alábbi lépéseket az Azure Portalon:

1. A böngészőből nyissa meg az [Azure Portalt.](https://portal.azure.com)

2. Nyissa meg az **Előfizetések** lapot, és válassza ki az előfizetését az Előfizetések lapon.   

3. A **Beállítások csoportban**válassza **az Erőforrás-szolgáltatók**lehetőséget.

4. Az erőforrás-szolgáltatók listájából ellenőrizze, hogy a **Microsoft.Automation** erőforrás-szolgáltató regisztrálva van-e.

5. Ha nem szerepel a listában, regisztrálja a **Microsoft.Automation** szolgáltatót az [Erőforrás-szolgáltató regisztrációjával kapcsolatos hibák megoldása](/azure/azure-resource-manager/resource-manager-register-provider-errors)című lépés lépéseit követve.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, próbálkozzon az alábbi csatornákkal további támogatásért:

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport)kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Az Azure-támogatási incidens fájlja. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**