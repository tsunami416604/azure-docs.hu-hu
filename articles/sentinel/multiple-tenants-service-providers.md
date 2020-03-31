---
title: Több bérlővel dolgozhat az Azure Sentinelhez MSSP-szolgáltatók számára| Microsoft dokumentumok
description: Hogyan működik együtt több bérlővel az Azure Sentinel az MSSP-szolgáltatók számára.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476015"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Több bérlővel való munka az Azure Sentinelben 

Ha Ön felügyelt biztonsági szolgáltató (MSSP), és [az Azure Lighthouse](../lighthouse/overview.md) segítségével kezeli ügyfelei biztonsági műveleti központjait (SOC), kezelheti ügyfelei Azure Sentinel-erőforrásait anélkül, hogy közvetlenül az ügyfél bérlőjéhez csatlakozna a saját Azure-bérlőjéről. 

## <a name="prerequisites"></a>Előfeltételek
- [Alaplapi Azure világítótorony](../lighthouse/how-to/onboard-customer.md)
- Ahhoz, hogy ez megfelelően működjön, a bérlőnek legalább egy előfizetésen regisztrálnia kell az Azure Sentinel erőforrás-szolgáltatóhoz. Ha van egy regisztrált Azure Sentinel a bérlőben, készen áll a kezdéshez. Ha nem, válassza **az Előfizetések** lehetőséget az Azure Portalon, majd **az Erőforrás-szolgáltatók**lehetőséget.  Ezután az **SOC - Erőforrás-szolgáltatók** képernyőn `Microsoft.OperationalInsights` `Microsoft.SecurityInsights`keresse meg és válassza ki a és válassza a lehetőséget, majd válassza a **Regisztráció**lehetőséget.
   ![Erőforrás-szolgáltatók ellenőrzése](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Az Azure Sentinel elérése más bérlőktől
1. A **Directory + előfizetés**csoportban válassza ki a delegált könyvtárakat, és azokat az előfizetéseket, amelyeken az ügyfél Azure Sentinel-munkaterületei találhatók.

   ![Biztonsági incidensek létrehozása](media/multiple-tenants-service-providers/directory-subscription.png)

1. Nyissa meg az Azure Sentinelt. Látni fogja az összes munkaterületet a kiválasztott előfizetések, és képes lesz arra, hogy zökkenőmentesen működjön együtt velük, mint bármely munkaterület a saját bérlője.

> [!NOTE]
> Az Azure Sentinelben nem telepíthet összekötőket egy felügyelt munkaterületen belülről. Egy összekötő üzembe helyezéséhez közvetlenül be kell jelentkeznie abba a bérlőbe, amelyen telepíteni szeretne egy összekötőt, és ott hitelesíteni kell a szükséges engedélyekkel.





## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan kezelheti zökkenőmentesen több Azure Sentinel-bérlőt. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)

