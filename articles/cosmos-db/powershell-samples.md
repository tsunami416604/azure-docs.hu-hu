---
title: "Az Azure PowerShell-példák Azure Cosmos DB |} Microsoft Docs"
description: "Az Azure PowerShell-példák - parancsfájlok használatával segítséget Azure Cosmos DB fiókok létrehozásához és kezeléséhez."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 10/16/2017
ms.author: mimig
ms.openlocfilehash: f2687369b8e247f00e9de5f3f79d8e44be5b8300
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure Cosmos DB Azure PowerShell-példák

A következő táblázat az Azure Cosmos DB Azure PowerShell-mintaparancsfájlok hivatkozásokat tartalmaz. Jelenleg csak kezelheti az Azure Cosmos DB accountlayer PowerShell; más erőforrások, például adatbázisok és -gyűjtemények nem kezelhető a PowerShell segítségével.

| |  |
|---|---|
|**Azure Cosmos DB-fiók létrehozása**||
|[A DocumentDB API-fiók létrehozása](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy egyetlen Azure Cosmos DB fiókot használni a DocumentDB API-t. |
|**Az Azure Cosmos DB méretezése**||
|[Azure Cosmos DB fiók több régióba replikálja, és feladatátvételi prioritások beállítása](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Egy adott feladatátvevő prioritású több területekre globálisan replikálja a fiók adatait.|
|**Az Azure Cosmos DB biztonságos**||
| [Fiók kulcsok beszerzése](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Lekérdezi a fő írási elsődleges és másodlagos kulcsok és a fiók elsődleges és másodlagos csak olvasható kulcsok.|
| [MongoDB kapcsolati karakterlánc beolvasása](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | A kapcsolati karakterláncot a MongoDB alkalmazás csatlakoztatása az Azure Cosmos DB fiók lekérése.|
|[Fiók kulcsok újragenerálása](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|A fiók a fő vagy csak olvasható kulcsának újragenerálása.|
|[Hozzon létre egy tűzfal](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy bejövő IP hozzáférés-vezérlési szabályzat egy jóváhagyott gépek csoportja tartalmazza a fiókja korlátozhatják és/vagy a felhőalapú szolgáltatások.|
|**Magas rendelkezésre állású, katasztrófa utáni helyreállítás, biztonsági mentése és visszaállítása**||
|[Feladatátvételi házirend konfigurálása](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Minden egyes régió, ahol a fiók replikálódik feladatátvételi prioritását állítja.|
|||
