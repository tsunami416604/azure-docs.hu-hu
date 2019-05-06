---
title: Az Azure PowerShell-minták az Azure Cosmos DB használatával
description: Azure PowerShell-minták – Szkriptek, amelyek segítenek az Azure Cosmos DB-fiókok létrehozásában és felügyeletében.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069303"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure PowerShell-minták az Azure Cosmos DB-ben

Az alábbi táblázat tartalmazza az Azure PowerShell-példaszkriptekre mutató hivatkozásokat az Azure Cosmos DB a Core (SQL) API-t.

| |  |
|---|---|
|**Az Azure Cosmos-fiókok**||
|[Fiók létrehozása](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos SQL API-fiókot. |
|[Egy fiók létrehozása](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók tulajdonságainak beolvasása. |
|[A régió hozzáadása](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók létrehozása, és adjon hozzá egy régiót a helyek listáját. |
|[Feladatátvétel prioritásának módosítása](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók egy manuális feladatátvétel eseményindítóval feladatátvétel prioritásának módosításához. |
|[A címkék frissítése](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A címkék az Azure Cosmos-fiók frissítéséhez. |
|[Fiókkulcsok lekérése](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók elsődleges és másodlagos kulcsainak lekérése. |
|[Fiókkulcsok ismételt létrehozása](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók elsődleges és másodlagos kulcsok újragenerálása. |
|[Kapcsolati karakterláncok listája](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Az elsődleges és másodlagos kapcsolati karakterláncok az Azure Cosmos-fiók beolvasása. |
|[IP-tűzfal létrehozása](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy IP-tűzfalon egy Azure Cosmos-fiók. |
|[Egy Azure Cosmos-fiók törlése](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure Cosmos-fiók törlése. |
|**Az Azure Cosmos Database.**||
| [Adatbázis létrehozása](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy adatbázist egy Azure Cosmos-fiókon belül.|
| [Létrehoz egy adatbázist, a megosztott/adatbázisszintű átviteli sebesség](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos database adatbázisszintű átviteli sebességet, amelyet a tárolók van megosztva.|
| [Minden adatbázis listázása](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy Azure Cosmos-fiókban lévő összes az adatbázisok listája.|
| [Egy adatbázis beolvasása](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy Azure Cosmos database tulajdonságainak beolvasása.|
|**Azure Cosmos-tárolók**||
| [Tároló létrehozása](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos-tárolóhoz dedikált átviteli sebességet.|
| [Hozzon létre egy tárolót a megosztott sebessége](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos-tárolóhoz más tárolók megosztva az adatbázis átviteli sebességet.|
| [Hozzon létre egy tárolót az index házirend](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos-tárolóhoz egy egyéni index szabályzatot.|
| [Hozzon létre egy tárolót index szabályzattal nem rendelkező](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos-tárolóhoz index házirend ki van kapcsolva.|
| [Hozzon létre egy tárolót az egyedi kulcsok & élettartam](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos tároló és a egy egyedi kulcsmegkötés és a time-to-live konfigurálva.|
| [Hozzon létre egy tárolót az ütközések feloldása](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hozzon létre egy Azure Cosmos-tárolóhoz wins-utolsó-író ütközés lép fel a névfeloldási házirend.|
| [Az összes tárolót](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Listázza az összes tárolók egy Azure Cosmos database.|
| [Egy tároló beolvasása](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | A Tulajdonságok lekérése egy Azure Cosmos database a tárolóhoz.|
| [Tároló törlése](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | A tároló egy Azure Cosmos-adatbázis törlése.|
|||