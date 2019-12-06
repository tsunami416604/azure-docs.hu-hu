---
title: Az Azure Cosmos Emulator letöltése és kibocsátási megjegyzései
description: Szerezze be az Azure Cosmos Emulator kiadási megjegyzéseit a különböző verziókhoz, és töltse le az információkat.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: eb73551c4226df8bc20428558720c0ebbfff96d9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873590"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos Emulator – kibocsátási megjegyzések és letöltési információk

Ez a cikk az Azure Cosmos Emulator kiadási megjegyzéseit mutatja be az egyes kiadásokban végrehajtott szolgáltatások frissítéseinek listájával. Emellett felsorolja az emulátor legújabb verzióját is a letöltéshez és a használatához.

## <a name="download"></a>Letöltés

| | |
|---------|---------|
|**MSI letöltése**|[Microsoft letöltőközpont](https://aka.ms/cosmosdb-emulator)|
|**Első lépések**|[Helyi fejlesztés az Azure Cosmos emulatorrel](local-emulator.md)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="270"></a>2.7.0

- Ez a kiadás javít egy olyan regressziót, amely megakadályozta, hogy a felhasználók a .NET Core vagy x86 .NET-alapú ügyfelek használatakor lekérdezéseket futtassanak az emulátorból az SQL API-fiókból.

### <a name="246"></a>2.4.6

- Ez a kiadás paritást biztosít az Azure Cosmos szolgáltatás szolgáltatásaival július 2019-ig, az [Azure Cosmos Emulator segítségével helyileg fejleszthető](local-emulator.md)kivételekkel. Emellett javít számos, az emulátor leállításával kapcsolatos hibát, amikor a parancssorból és a belső IP-címek felülbírálásait használja a közvetlen módú kapcsolattal rendelkező SDK-ügyfelek számára.

### <a name="243"></a>2.4.3

- Alapértelmezés szerint le van tiltva a MongoDB szolgáltatás elindítása. Csak az SQL-végpont engedélyezett alapértelmezettként. A felhasználónak manuálisan kell elindítania a végpontot az emulátor "/EnableMongoDbEndpoint" parancssori kapcsolójának használatával. Most, mint az összes többi szolgáltatási végpont, például Gremlin, Cassandra és Table.
- Kijavított egy hibát az emulátorban a "/AllowNetworkAccess" kifejezéssel kezdődően, ahol a Gremlin, Cassandra és Table végpontok nem tudták megfelelően kezelni a külső ügyfelektől érkező kéréseket.
- Közvetlen kapcsolódási portok hozzáadása a tűzfalszabályok beállításaihoz.

### <a name="240"></a>2.4.0

- Kijavítva egy probléma az emulátorban, amely nem indul el, ha a hálózati figyelési alkalmazások (például a Pulse-ügyfél) jelen vannak a gazdagépen.
