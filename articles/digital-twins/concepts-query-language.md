---
title: Lekérdezés nyelve
titleSuffix: Azure Digital Twins
description: Ismerje meg az Azure digitális Twins lekérdezési nyelvének alapjait.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0faa0af3bb793cbd75139ab42edd0aa7e20de78a
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543843"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Az Azure Digital Twins lekérdezési nyelvének ismertetése

Ne felejtse el, hogy az Azure Digital Twins középpontja a **digitális ikrekből** és **kapcsolatokból**kialakított [**Twin gráf**](concepts-twins-graph.md). Ez a gráf lekérdezhető a digitális ikrek és a benne található kapcsolatok adatainak lekérdezéséhez. Ezeket a lekérdezéseket az **Azure Digital Twins lekérdezési nyelve**nevű egyéni SQL-szerű lekérdezési nyelven írták.

Ha egy ügyfélalkalmazás számára szeretne lekérdezést küldeni a szolgáltatásnak, az Azure Digital Twins [**lekérdezési API**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)-ját fogja használni. Ez lehetővé teszi, hogy a fejlesztők írási lekérdezéseket és szűrőket alkalmazzanak a digitális ikrek készletének megkereséséhez a Twin gráfban, valamint az Azure digitális Twins forgatókönyvével kapcsolatos további információkat.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan írhat lekérdezéseket, és hogyan tekintheti meg az ügyféloldali kód példáit [*: a Twin gráf lekérdezése*](how-to-query-graph.md).
