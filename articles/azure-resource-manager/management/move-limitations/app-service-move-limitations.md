---
title: Az Azure App Service-erőforrások áthelyezése
description: Az Azure Resource Manager használatával áthelyezi az App Service-erőforrásokat egy új erőforráscsoportba vagy előfizetésbe.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: d0ecd117bdcda9238e310a3020dba19a6871a3fc
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655775"
---
# <a name="move-guidance-for-app-service-resources"></a>Útmutató áthelyezése az App Service-erőforrásokhoz

Ez a cikk az App Service-erőforrások áthelyezésének lépéseit ismerteti. Az App Service-erőforrások új előfizetésbe való áthelyezéséhez speciális követelmények vonatkoznak.

## <a name="move-across-subscriptions"></a>Áthelyezés az előfizetések között

Amikor egy webalkalmazást előfizetések között helyez át, a következő útmutatás érvényes:

- A célerőforrás-csoport nem rendelkezhet meglévő App Service-erőforrásokkal. Az App Service-erőforrások a következők:
    - Web Apps
    - App Service-csomagok
    - Feltöltött vagy importált TLS/SSL tanúsítványok
    - App Service-környezetek
- Az erőforráscsoport összes App Service-erőforrását együtt kell áthelyezni. Vegye figyelembe, hogy az App Service-környezetek nem helyezhetők át új erőforráscsoportba vagy új előfizetésbe.
- A tls-kötések törlése nélkül áthelyezheti a webhez kötött tanúsítványt, feltéve, hogy a tanúsítvány az erőforráscsoport összes többi erőforrásával együtt kerül áthelyezésre.
- Az App Service-erőforrások csak abból az erőforráscsoportból helyezhetők át, amelyben eredetileg létrehozták őket. Ha egy App Service-erőforrás már nem az eredeti erőforráscsoportban van, helyezze vissza az eredeti erőforráscsoportba. Ezután helyezze át az erőforrást az előfizetések között.

Ha nem emlékszik az eredeti erőforráscsoportra, a diagnosztika segítségével megtalálhatja. A webalkalmazáshoz válassza **a Problémák diagnosztizálása és megoldása**lehetőséget. Ezután válassza a **Konfiguráció és kezelés**lehetőséget.

![Diagnosztika kiválasztása](./media/app-service-move-limitations/select-diagnostics.png)

Válassza **az Áttelepítési beállítások lehetőséget.**

![Áttelepítési beállítások megadása](./media/app-service-move-limitations/select-migration.png)

Válassza ki a webalkalmazás áthelyezéséhez ajánlott lépések beállítását.

![Az ajánlott lépések kiválasztása](./media/app-service-move-limitations/recommended-steps.png)

Az erőforrások áthelyezése előtt megjelennek az ajánlott műveletek. Az adatok tartalmazzák a webalkalmazás eredeti erőforráscsoportját.

![Javaslatok](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Támogatás áthelyezése

Annak meghatározásához, hogy mely App Service-erőforrások helyezhetők át, olvassa el a következők áthelyezési támogatási állapotát:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.Tanúsítványregisztráció](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>További lépések

Az erőforrások áthelyezésére vonatkozó parancsokról az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe (Erőforrások áthelyezése)](../move-resource-group-and-subscription.md)témakörben található.
