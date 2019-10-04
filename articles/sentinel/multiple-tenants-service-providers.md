---
title: Több bérlő használata az Azure Sentinel MSSP-szolgáltatók számára | Microsoft Docs
description: Több bérlő használata az Azure Sentinel MSSP-szolgáltatók számára.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 34997fb3cd91e4540c76ecdd781d21e2ed1bdb07
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240492"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Több bérlő használata az Azure Sentinelben 

Ha Ön felügyelt biztonsági szolgáltató (MSSP), és az [Azure Lighthouse](../lighthouse/overview.md) használatával felügyeli ügyfelei biztonsági üzemeltetési központját (SoC), akkor az ügyfelek Azure Sentinel-erőforrásait közvetlenül a következőhöz való csatlakozás nélkül is kezelheti: az ügyfél bérlője a saját Azure-bérlőtől. 

## <a name="prerequisites"></a>Előfeltételek
- [Az Azure Lighthouse előkészítése](../lighthouse/how-to/onboard-customer.md)
- Ahhoz, hogy ez megfelelően működjön, a bérlőt legalább egy előfizetésben regisztrálni kell az Azure Sentinel erőforrás-szolgáltatón. Ha a bérlőben regisztrált Azure Sentinel van, készen áll a kezdésre. Ha nem, a Azure Portal válassza az **előfizetések** lehetőséget, majd az **erőforrás** -szolgáltatók `Microsoft.Security.Insights` elemet, majd keresse meg és válassza a **regisztráció**lehetőséget.
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

