---
title: Azure PowerShell-minták az Azure Cosmos DB-hoz – MongoDB API
description: Az Azure PowerShell-minták beszerezése különböző gyakori feladatok elvégzéséhez az Azure Cosmos DB MongoDB-hoz való API-jában
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 810161268df405d84f6c190d4d7f3b67f2a1def8
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366208"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Azure PowerShell-minták az Azure Cosmos DB MongoDB API-hoz

Az alábbi táblázat az Azure Cosmos DB MongoDB API-hoz való Azure PowerShell-parancsfájlok mintájára mutató hivatkozásokat tartalmaz.

> [!NOTE]
> Jelenleg csak 3.2-es verziót (azaz a végpontot `*.documents.azure.com`használó, formátumú fiókokat) hozhat létre az Azure Cosmos DB MongoDB-fiókokapi-jának PowerShell-, CLI- és Erőforrás-kezelő-sablonok használatával. A fiókok 3.6-os verziójának létrehozásához használja inkább az Azure Portalt.

> [!NOTE]
> A minták [az Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) felügyeleti parancsmagokat használnak. Kérjük, `Az.CosmosDB` vegye figyelembe, hogy a parancsmagok továbbra is előzetes verzióban vannak, és a kiadás előtt változhatnak. Kérjük, rendszeresen ellenőrizze a frissítéseket. `Az.CosmosDB`

| | |
|---|---|
|[Fiók, adatbázis és gyűjtemény létrehozása](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure Cosmos-fiókot, adatbázist és gyűjteményt. |
|[Adatbázisok vagy gyűjtemények listázása vagy beírása](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gyűjtemény listázása vagy begyűjtése. |
|[Ru/s beszerezni](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ru/s beszerezni e-műveleteket egy adatbázishoz vagy gyűjteményhez. |
|[Ru/s frissítése](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adatbázis vagy gyűjtemény RU/s frissítése. |
|[Fiók frissítése vagy régió hozzáadása](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adjon hozzá egy régiót egy Cosmos-fiókhoz. Más fióktulajdonságok módosítására is használható, de ezeknek el kell különedniük a régiók változásaitól. |
|[Feladatátvételi prioritás módosítása vagy eseményindító feladatátvétel](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Módosítsa az Azure Cosmos-fiók területi feladatátvételi prioritását, vagy indítson el egy manuális feladatátvételt. |
|[Fiókkulcsok vagy kapcsolati karakterláncok](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elsődleges és másodlagos kulcsok, kapcsolati karakterláncok vagy egy Azure Cosmos-fiók kulcsának újragenerálása. |
|[Cosmos-fiók létrehozása IP tűzfallal](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hozzon létre egy Azure Cosmos-fiókot, ha engedélyezve van az IP-tűzfal. |
|||
