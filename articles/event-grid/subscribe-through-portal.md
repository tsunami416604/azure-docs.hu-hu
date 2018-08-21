---
title: Az Azure Event Grid-előfizetések a portálon keresztül
description: Ismerteti, hogyan hozhat létre Event Grid-előfizetések a portálon keresztül.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: tomfitz
ms.openlocfilehash: 72eaa17e78086a4e5338bb3198ef7471c44b785f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234948"
---
# <a name="subscribe-to-events-through-portal"></a>Fizessen elő az eseményekre a portálon keresztül

Ez a cikk ismerteti, hogyan hozhat létre Event Grid-előfizetések a portálon keresztül.

## <a name="create-event-subscriptions"></a>Esemény-előfizetések létrehozása

Bármelyik támogatott az Event Grid-előfizetés létrehozása [eseményforrások](event-sources.md), kövesse az alábbi lépéseket. Ez a cikk bemutatja, hogyan hozhat létre egy Event Grid-előfizetés Azure-előfizetésre.

1. Válassza az **Összes szolgáltatás** elemet.

   ![Válassza ki az összes szolgáltatás](./media/subscribe-through-portal/select-all-services.png)

1. Keresse meg **Event Grid-előfizetések** , és jelölje ki az elérhető lehetőségek közül.

   ![Keresés](./media/subscribe-through-portal/search.png)

1. Válassza a **+ Esemény-előfizetés** lehetőséget.

   ![Előfizetés hozzáadása](./media/subscribe-through-portal/add-subscription.png)

1. Válassza ki a létrehozni kívánt előfizetést. Feliratkozás az Azure-előfizetés eseményeire, például válassza ki a **Azure-előfizetések** és a cél előfizetésben.

   ![Válassza ki az Azure-előfizetés](./media/subscribe-through-portal/azure-subscription.png)

1. Fizessen elő az összes eseménytípusra ehhez eseményre adatforráshoz, hagyja a **fizessen elő az összes eseménytípusra** lehetőség be van jelölve. Ellenkező esetben válassza az eseménytípusok, ehhez az előfizetéshez.

   ![Válassza ki az események típusai](./media/subscribe-through-portal/select-event-types.png)

1. Az esemény-előfizetés, például az események és a egy előfizetésnevet végpontját további részleteket nyújtanak.

   ![Adja meg az előfizetés részletei](./media/subscribe-through-portal/provide-subscription-details.png)

## <a name="create-subscription-on-resource"></a>Előfizetés létrehozása az erőforráson

Néhány eseményforrások támogatja az adott erőforráshoz a portál felületén keresztül egy esemény-előfizetés létrehozása. Válassza ki az esemény forrását, és keressen **események** bal oldali panelen.

![Adja meg az előfizetés részletei](./media/subscribe-through-portal/resource-events.png)

A portál megjelenít egy esemény-előfizetést, amely kapcsolódik az adatforráshoz való létrehozásának lehetőségeit.

## <a name="next-steps"></a>További lépések

* Eseménykézbesítés és újrapróbálkozás [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).
