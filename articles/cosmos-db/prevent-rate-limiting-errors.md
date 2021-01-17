---
title: A MongoDB-műveletekhez tartozó Azure Cosmos DB API-k arányának korlátozására vonatkozó hibák megakadályozása.
description: Megtudhatja, hogyan akadályozhatja meg Azure Cosmos DB API-ját a MongoDB-műveletekhez, hogy a rendszer az SSR (kiszolgálóoldali újrapróbálkozás) szolgáltatással kapcsolatos hibákat korlátozza.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540606"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>A MongoDB-műveletekre vonatkozó Azure Cosmos DB API-k arányának korlátozására vonatkozó hibák megelőzése
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A MongoDB-műveletek Azure Cosmos DB API-ját a ráta korlátozásával (16500/429) kapcsolatos hibák léphetnek fel, ha meghaladják a gyűjtemény átviteli korlátját (RUs). 

Engedélyezheti a kiszolgálóoldali újrapróbálkozás (SSR) szolgáltatást, és lehetővé teheti a kiszolgáló számára a műveletek automatikus megismétlését. A kéréseket a rendszer a fiókban lévő összes gyűjtemény rövid késleltetése után újrapróbálkozik. Ez a funkció kényelmes alternatíva a ráta-korlátozó hibák kezelésére az ügyfélalkalmazás számára.


## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Navigáljon a MongoDB-fiókhoz tartozó Azure Cosmos DB API-hoz.

1. Nyissa meg a **szolgáltatások** ablaktáblát a **Beállítások** szakasz alatt.

1. Válassza a **kiszolgálóoldali újrapróbálkozás** lehetőséget.

1. Az **Engedélyezés** gombra kattintva engedélyezheti ezt a funkciót a fiókban lévő összes gyűjteményhez.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="A MongoDB Azure Cosmos DB API-hoz készült kiszolgálóoldali újrapróbálkozás funkciójának képernyőképe":::


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a gyakori hibák elhárításáról, tekintse meg ezt a cikket:

* [Az Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárítása](mongodb-troubleshoot.md)
