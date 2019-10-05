---
title: Az Azure Cosmos Emulator letöltése és kibocsátási megjegyzései
description: Olvassa el az Azure Cosmos Emulator kiadási megjegyzéseit, és töltse le.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 03163d75681bee7434e2b7576a678b1094d47b94
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973710"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos Emulator – kibocsátási megjegyzések és letöltési információk

Ez a cikk az Azure Cosmos Emulator kiadási megjegyzéseit mutatja be az egyes kiadásokban végrehajtott szolgáltatások frissítéseinek listájával. Emellett felsorolja az emulátor legújabb verzióját is a letöltéshez és a használatához.

## <a name="download"></a>Letöltés

| | |
|---------|---------|
|**MSI letöltése**|[Microsoft letöltőközpont](https://aka.ms/cosmosdb-emulator)|
|**Első lépések**|[Helyi fejlesztés az Azure Cosmos emulatorrel](local-emulator.md)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="246"></a>2.4.6

- Ez a kiadás paritást biztosít az Azure Cosmos szolgáltatás szolgáltatásaival július 2019-ig, az [Azure Cosmos Emulator segítségével helyileg fejleszthető](local-emulator.md)kivételekkel. Emellett javít számos, az emulátor leállításával kapcsolatos hibát, amikor a parancssorból és a belső IP-címek felülbírálásait használja a közvetlen módú kapcsolattal rendelkező SDK-ügyfelek számára.

### <a name="243"></a>2.4.3

- Alapértelmezés szerint le van tiltva a MongoDB szolgáltatás elindítása. Csak az SQL-végpont engedélyezett alapértelmezettként. A felhasználónak manuálisan kell elindítania a végpontot az emulátor "/EnableMongoDbEndpoint" parancssori kapcsolójának használatával. Most, mint az összes többi szolgáltatási végpont, például Gremlin, Cassandra és Table.
- Kijavított egy hibát az emulátorban a "/AllowNetworkAccess" kifejezéssel kezdődően, ahol a Gremlin, Cassandra és Table végpontok nem tudták megfelelően kezelni a külső ügyfelektől érkező kéréseket.
- Közvetlen kapcsolódási portok hozzáadása a tűzfalszabályok beállításaihoz.

### <a name="240"></a>2.4.0

- Kijavítva egy probléma az emulátorban, amely nem indul el, ha a hálózati figyelési alkalmazások (például a Pulse-ügyfél) jelen vannak a gazdagépen.
