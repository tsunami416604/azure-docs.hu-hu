---
title: Azure App Service erőforrások áthelyezése
description: A Azure Resource Manager használatával áthelyezheti App Service erőforrásait egy új erőforráscsoporthoz vagy előfizetésbe.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 338b0559a5de9468ff60024b88d0f676a9fc3e8b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479799"
---
# <a name="move-guidance-for-app-service-resources"></a>Útmutató a App Service-erőforrások áthelyezéséhez

Ez a cikk a App Service erőforrások áthelyezésének lépéseit ismerteti. A App Service-erőforrások új előfizetésre való áthelyezésének speciális követelményei vannak.

## <a name="move-across-subscriptions"></a>Áthelyezés az előfizetések között

Ha egy webalkalmazást az előfizetések között helyez át, a következő útmutatók érvényesek:

- A cél erőforráscsoport nem rendelkezhet meglévő App Service erőforrásokkal. App Service erőforrások a következők:
    - Webalkalmazások
    - App Service-csomagok
    - Feltöltött vagy importált SSL-tanúsítványok
    - App Service Environment környezetek
- Az erőforráscsoporthoz tartozó összes App Service-erőforrást együtt kell áthelyezni. Vegye figyelembe, hogy App Service környezetek nem helyezhetők át új erőforrás-csoportba, és nem új előfizetésre.
- Az SSL-kötések törlése nélkül is áthelyezheti a webre kötött tanúsítványokat, ha a tanúsítványt az erőforráscsoport összes többi erőforrásával áthelyezi.
- App Service erőforrásokat csak abban az erőforráscsoporthoz lehet áthelyezni, amelyben eredetileg létrehozták őket. Ha egy App Service erőforrás már nem szerepel az eredeti erőforráscsoporthoz, helyezze vissza az eredeti erőforráscsoporthoz. Ezután helyezze át az erőforrást az előfizetések között.

Ha nem emlékszik az eredeti erőforráscsoporthoz, megkeresheti azt a diagnosztika használatával. A webalkalmazáshoz válassza a **diagnosztizálás és problémák megoldása**lehetőséget. Ezután válassza a **konfiguráció és kezelés**lehetőséget.

![Diagnosztika kiválasztása](./media/app-service-move-limitations/select-diagnostics.png)

Válassza az **áttelepítési beállítások**lehetőséget.

![Áttelepítési beállítások kiválasztása](./media/app-service-move-limitations/select-migration.png)

A webalkalmazás áthelyezéséhez válassza a javasolt lépések lehetőséget.

![Javasolt lépések kiválasztása](./media/app-service-move-limitations/recommended-steps.png)

Az erőforrások áthelyezése előtt el kell végeznie az ajánlott műveleteket. Az információ tartalmazza a webalkalmazás eredeti erőforrás-csoportját.

![Ajánlatok](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Támogatás áthelyezése

A következő témakörben megtudhatja, hogy mely App Service erőforrásokat lehet áthelyezni:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft. Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Következő lépések

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../move-resource-group-and-subscription.md).
