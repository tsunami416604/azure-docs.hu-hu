---
title: Lekérdezés nyelve
titleSuffix: Azure Digital Twins
description: Ismerje meg az Azure digitális Twins lekérdezési nyelvének alapjait.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d656f19f6f4030025ff1393c3e5017466b3333fd
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044394"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Az Azure Digital Twins lekérdezési nyelvének ismertetése

Ne felejtse el, hogy az Azure Digital Twins középpontja a **digitális ikrekből** és **kapcsolatokból**kialakított [**Twin gráf**](concepts-twins-graph.md). Ez a gráf lekérdezhető a digitális ikrek és a benne található kapcsolatok adatainak lekérdezéséhez. Ezek a lekérdezések egy egyéni SQL-szerű lekérdezési nyelven íródnak, amelyet az **Azure digitális Twins lekérdezési nyelvének**nevezünk.

Ha egy ügyfélalkalmazás számára szeretne lekérdezést küldeni a szolgáltatásnak, az Azure Digital Twins [**lekérdezési API**](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)-ját fogja használni. Ez lehetővé teszi, hogy a fejlesztők írási lekérdezéseket és szűrőket alkalmazzanak a digitális ikrek készletének megkereséséhez a Twin gráfban, valamint az Azure digitális Twins forgatókönyvével kapcsolatos további információkat.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan írhat lekérdezéseket, és hogyan tekintheti meg az ügyféloldali kód példáit [*: a Twin gráf lekérdezése*](how-to-query-graph.md).