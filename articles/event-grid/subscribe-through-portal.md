---
title: Azure Event Grid-előfizetések portálon keresztül
description: Ez a cikk ismerteti, hogyan hozhat létre Event Grid-előfizetések a támogatott források, például az Azure Blob Storage, az Azure Portal használatával.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 3172c92ecae094ab5d978803d2ccac7e6404a5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721506"
---
# <a name="subscribe-to-events-through-portal"></a>Feliratkozás eseményekre a portálon keresztül

Ez a cikk bemutatja, hogyan hozhat létre Event Grid-előfizetéseket a portálon keresztül.

## <a name="create-event-subscriptions"></a>Esemény-előfizetések létrehozása

Ha eseményrács-előfizetést szeretne létrehozni a támogatott [eseményforrások](event-sources.md)bármelyikéhez, kövesse az alábbi lépéseket. Ez a cikk bemutatja, hogyan hozhat létre egy Event Grid-előfizetés t egy Azure-előfizetéshez.

1. Válassza az **Összes szolgáltatás** elemet.

   ![Az összes szolgáltatás kijelölése](./media/subscribe-through-portal/select-all-services.png)

1. Keresse meg az **Eseményrács-előfizetések kifejezést,** és válassza ki a rendelkezésre álló lehetőségek közül.

   ![Keresés](./media/subscribe-through-portal/search.png)

1. Válassza a **+ Esemény-előfizetés** lehetőséget.

   ![Előfizetés hozzáadása](./media/subscribe-through-portal/add-subscription.png)

1. Válassza ki a létrehozni kívánt előfizetés típusát. Például az Azure-előfizetés eseményeire való előfizetéshez válassza az **Azure-előfizetések** és a cél-előfizetés lehetőséget.

   ![Azure-előfizetés kiválasztása](./media/subscribe-through-portal/azure-subscription.png)

1. Ha elő szeretne fizetni az eseményforrás összes eseménytípusára, tartsa be jelölve a **Feliratkozás az összes eseménytípusra** beállítást. Ellenkező esetben válassza ki az előfizetés típusait.

   ![Eseménytípusok kiválasztása](./media/subscribe-through-portal/select-event-types.png)

1. Adja meg az esemény-előfizetés további részleteit, például az események kezelésére szolgáló végpontot és az előfizetés nevét.

   ![Előfizetés részleteinek megadása](./media/subscribe-through-portal/provide-subscription-details.png)

1. A kézbesítésben elhunyt betűk engedélyezéséhez és az újrapróbálkozási házirendek testreszabásához válassza a **További szolgáltatások lehetőséget.**

   ![További funkciók kiválasztása](./media/subscribe-through-portal/select-additional-features.png)

1. Válassza ki a nem kézbesített események tárolására szolgáló tárolót, és állítsa be az újrapróbálkozások elküldésének módját.

   ![Kézbesítési leíró és újrapróbálkozás engedélyezése](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

## <a name="create-subscription-on-resource"></a>Előfizetés létrehozása erőforráson

Egyes eseményforrások támogatják az esemény-előfizetés létrehozását az adott erőforrás portálfelületén keresztül. Jelölje ki az eseményforrást, és keresse meg az **Események** elemet a bal oldali ablaktáblában.

![Előfizetés részleteinek megadása](./media/subscribe-through-portal/resource-events.png)

A portál bemutatja az adott forráshoz kapcsolódó esemény-előfizetés létrehozásának lehetőségeit.

## <a name="next-steps"></a>További lépések

* Az eseménykézbesítésről és az újrapróbálkozásokról az [Event Grid üzenetkézbesítésével és újrapróbálkozásával](delivery-and-retry.md)kapcsolatos információkért kattintson.
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez olvassa el az [Egyéni események létrehozása és irányítása az Azure Event Griddel című témakört.](custom-event-quickstart.md)
