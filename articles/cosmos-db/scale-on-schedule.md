---
title: Az Azure Cosmos DB méretezése ütemezés szerint az Azure Functions időzítőjének használatával
description: Megtudhatja, hogyan skálázhat átviteli változásokat az Azure Cosmos DB-ben a PowerShell és az Azure Functions használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75935167"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Az Azure Cosmos DB átviteli léptékének méretezése az Azure Functions időzítőeseményének használatával

Az Azure Cosmos-fiók teljesítménye a másodpercenkénti (RU/s) kérelemegységekben kifejezett kiosztott átviteli teljesítmény összegén alapul. A kiépítés egy második granularitás, és a legmagasabb RU/s/s óránkénti számlázása alapján kerül számlázásra. Ez a kiosztott kapacitásmodell lehetővé teszi, hogy a szolgáltatás kiszámítható és konzisztens átviteli teljesítményt, garantált alacsony késést és magas rendelkezésre állást biztosítson. A legtöbb éles számítási feladatok ezeket a funkciókat. Azonban a fejlesztési és tesztelési környezetekben, ahol az Azure Cosmos DB csak munkaidőben használatos, felskálázhatja az átviteli szintet reggel, és a munkaidő után este leskálázhatja.

Az átviteli teljesítmény az [Azure Resource Manager-sablonok](resource-manager-samples.md), [az Azure CLI](cli-samples.md)és a [PowerShell](powershell-samples-sql.md)core (SQL) API-fiókok, vagy a nyelvspecifikus Azure Cosmos DB SDK-k használatával állítható be. A Resource Manager-sablonok, az Azure CLI vagy a PowerShell használatának előnye, hogy az összes Azure Cosmos DB modell API-t támogatják.

## <a name="throughput-scheduler-sample-project"></a>Átviteli ütemező mintaprojektje

Az Azure Cosmos DB ütemezés szerinti méretezésének egyszerűsítése érdekében létrehoztunk egy [Azure Cosmos átviteli ütemező](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)nevű mintaprojektet. Ez a projekt egy Azure Functions alkalmazás két időzítő eseményindítók- "ScaleUpTrigger" és a "ScaleDownTrigger". Az eseményindítók egy PowerShell-parancsfájlt futtatnak, amely beállítja az átviteli teljesítményt az egyes erőforrásokon az egyes eseményindítók ban definiált `resources.json` fájlban. A ScaleUpTrigger úgy van beállítva, hogy 8:00-kor(UTC) legyen futtatva, a ScaleDownTrigger `function.json` pedig úgy van beállítva, hogy 18:00-kor (UTC) fusson, és ezek az idők könnyen frissíthetők a fájlon belül az egyes eseményindítókhoz.

Klónozhatja ezt a projektet helyileg, módosíthatja azt az Azure Cosmos DB-erőforrások at fel- és leskálázáshoz, valamint a futásütemezéshez. Később üzembe helyezheti egy Azure-előfizetésben, és biztonságossá teheti a felügyelt szolgáltatásidentitás használatával [szerepköralapú hozzáférés-vezérlési](role-based-access-control.md) (RBAC) engedélyekkel az "Azure Cosmos DB operátor" szerepkörrel az Azure Cosmos-fiókok átviteli értékének beállításához.

## <a name="next-steps"></a>Következő lépések

- Tudjon meg többet, és töltse le a mintát az [Azure Cosmos DB átviteli ütemezőből.](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)
