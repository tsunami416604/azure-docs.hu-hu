---
title: Az Azure Cosmos Emulator letöltése és a kibocsátási megjegyzések
description: Olvassa el az Azure Cosmos-emulátor kibocsátási megjegyzéseket, és töltse le.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 5985d0d82341c76993ee91b8dff6927edd1ed8b4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332127"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Az Azure Cosmos Emulatort használja a helyi fejlesztési és tesztelési célra

Ez a cikk bemutatja az Azure Cosmos emulátor kibocsátási megjegyzések az egyes kiadások elvégzett funkciófrissítések listája. Felsorolja a letöltését és használatát emulator legújabb verziója.

## <a name="download"></a>Letöltés

| | |
|---------|---------|
|**MSI letöltése**|[Microsoft letöltőközpont](https://aka.ms/cosmosdb-emulator)|
|**Első lépések**|[Helyi fejlesztés az Azure Cosmos emulátorral](local-emulator.md)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="243"></a>2.4.3

- A letiltott alapértelmezés szerint a MongoDB-szolgáltatás indítása. Csak az SQL-végpont alapértelmezés szerint engedélyezve van. A felhasználónak kell elindítani a végpontot, manuálisan segítségével az emulátort "/ EnableMongoDbEndpoint" parancssori kapcsolót. Most a többi szolgáltatás végpont, például Cassandra, tábla és a Gremlin hasonlít.
- Kijavítva a hiba az emulátorban, amikor kezdve "/ AllowNetworkAccess", a Gremlin, a Cassandra és a tábla végpontok nem megfelelően kezelik a külső ügyfelek kéréseit.
- Közvetlen kapcsolat portjai adja hozzá a tűzfalszabályok beállításokat.

### <a name="240"></a>2.4.0

- Kijavítva emulátor sikertelen elindításához, amikor a hálózati figyelési alkalmazások, például Pulse ügyfél, az állomás számítógépén jelen.
