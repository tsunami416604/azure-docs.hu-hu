---
title: Azure PowerShell-minták az Azure Cosmos DB-ben | Microsoft Docs
description: Azure PowerShell-minták – Szkriptek, amelyek segítenek az Azure Cosmos DB-fiókok létrehozásában és felügyeletében.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 10/16/2017
ms.author: mimig
ms.openlocfilehash: f651a88f71e62518d0531a2d5cee5c1cd2bc5ce4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure PowerShell-minták az Azure Cosmos DB-ben

A következő táblázat az Azure Cosmos DB-hez elérhető Azure PowerShell-példaszkriptekre mutató hivatkozásokat tartalmaz. Jelenleg csak az Azure Cosmos DB-fiókrétege kezelhető a PowerShell segítségével, más erőforrások, például adatbázisok és gyűjtemények nem kezelhetők a PowerShell segítségével.

| |  |
|---|---|
|**Azure Cosmos DB-fiók létrehozása**||
|[SQL API-fiók létrehozása](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egyetlen Azure Cosmos DB-fiókot hoz létre az SQL API-val történő használathoz. |
|**Az Azure Cosmos DB méretezése**||
|[Azure Cosmos DB-fiók replikálása több régióban és a feladatátvételi prioritások konfigurálása](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Globálisan és meghatározott feladatátvételi prioritás szerint replikál fiókadatokat több régióba.|
|**Az Azure Cosmos DB védelme**||
| [Fiókkulcsok lekérése](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Lekéri a fiók elsődleges és a másodlagos írási mesterkulcsát és elsődleges és másodlagos írásvédett kulcsát.|
| [MongoDB kapcsolati sztring lekérése](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Lekéri azt a kapcsolati sztringet, amely a MongoDB alkalmazás Azure Cosmos DB-fiókhoz való csatlakoztatására szolgál.|
|[Fiókkulcsok ismételt létrehozása](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Újból létrehozza a fiók mester- vagy írásvédett kulcsát.|
|[Tűzfal létrehozása](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy bemeneti IP-címekre vonatkozó hozzáférés-vezérlési szabályzatot, hogy a fiók hozzáférését a jóváhagyott gépekre és/vagy felhőszolgáltatásokra korlátozza.|
|**Magas rendelkezésre állás, vészhelyreállítás, biztonsági mentés és visszaállítás**||
|[Feladatátvételi szabályzat konfigurálása](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Beállítja azon régiók feladatátvételi prioritását, amelyekbe a fiók replikálva van.|
|||
