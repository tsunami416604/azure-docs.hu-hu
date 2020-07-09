---
title: Több bérlő használata az Azure Sentinel MSSP-szolgáltatók számára | Microsoft Docs
description: Több bérlő használata az Azure Sentinel MSSP-szolgáltatók számára.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79476015"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Több bérlő használata az Azure Sentinelben 

Ha Ön felügyelt biztonsági szolgáltató (MSSP), és az [Azure Lighthouse](../lighthouse/overview.md) használatával felügyeli az ügyfelek biztonsági üzemeltetési központját (SoC), az ügyfelek Azure Sentinel-erőforrásait anélkül felügyelheti, hogy közvetlenül az ügyfél bérlőjét, saját Azure-bérlőjét csatlakoztatja. 

## <a name="prerequisites"></a>Előfeltételek
- [Az Azure Lighthouse előkészítése](../lighthouse/how-to/onboard-customer.md)
- Ahhoz, hogy ez megfelelően működjön, a bérlőt legalább egy előfizetésben regisztrálni kell az Azure Sentinel erőforrás-szolgáltatón. Ha a bérlőben regisztrált Azure Sentinel van, készen áll a kezdésre. Ha nem, válassza az **előfizetések** lehetőséget a Azure Portal, majd az **erőforrás-szolgáltatók**elemet.  Ezután a **SoC-Resource Providers** képernyőn keresse meg és válassza ki a és a elemet, majd `Microsoft.OperationalInsights` válassza a `Microsoft.SecurityInsights` **regisztráció**lehetőséget.
   ![Erőforrás-szolgáltatók keresése](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Az Azure Sentinel más bérlők általi elérése
1. A **címtár + előfizetés**területen válassza ki a delegált címtárakat, valamint azokat az előfizetéseket, amelyeken az ügyfél Azure Sentinel-munkaterületei találhatók.

   ![Biztonsági incidensek előállítása](media/multiple-tenants-service-providers/directory-subscription.png)

1. Nyissa meg az Azure Sentinelt. Ekkor megjelenik a kiválasztott előfizetések összes munkaterülete, és zökkenőmentesen dolgozhat velük, például a saját bérlő bármely munkaterületével.

> [!NOTE]
> A felügyelt munkaterületen belülről nem telepíthet összekötőket az Azure Sentinelben. Összekötő üzembe helyezéséhez közvetlenül be kell jelentkeznie arra a bérlőre, amelyen az összekötőt telepíteni kívánja, és ott hitelesítenie kell a szükséges engedélyekkel.





## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan kezelheti zökkenőmentesen több Azure Sentinel-bérlőt. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

