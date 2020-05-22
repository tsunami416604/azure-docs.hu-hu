---
title: Az Azure Cosmos Emulator letöltése és kibocsátási megjegyzései
description: Szerezze be az Azure Cosmos Emulator kiadási megjegyzéseit a különböző verziókhoz, és töltse le az információkat.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 6dad625bbbcb8279e83ac42e1492c251d5b0299c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747005"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos Emulator – kibocsátási megjegyzések és letöltési információk

Ez a cikk az Azure Cosmos Emulator kiadási megjegyzéseit mutatja be az egyes kiadásokban végrehajtott szolgáltatások frissítéseinek listájával. Emellett felsorolja az emulátor legújabb verzióját is a letöltéshez és a használatához.

## <a name="download"></a>Letöltés

| | |
|---------|---------|
|**MSI letöltése**|[Microsoft letöltőközpont](https://aka.ms/cosmosdb-emulator)|
|**Bevezetés**|[Helyi fejlesztés az Azure Cosmos emulatorrel](local-emulator.md)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="2110"></a>2.11.0

- Ez a kiadás a kiosztott átviteli sebesség támogatását mutatja be. Ezek az új funkciók lehetővé teszik az egyéni maximális kiosztott átviteli sebesség beállítását a kérelmek egységében (RU/s), az autoskálázást a meglévő adatbázisokon és tárolókban, valamint a programozási támogatást Azure Cosmos DB SDK-k segítségével.
- Probléma elhárítása a nagy mennyiségű (1 GB-nál több) dokumentum lekérdezése során az emulátor sikertelen lesz a belső hiba 500-as állapotkód esetén.

### <a name="292"></a>2.9.2

- Ez a kiadás kijavít egy hibát, miközben engedélyezi az MongoDb Endpoint 3,2-es verziójának támogatását. Emellett támogatást nyújt az ETL-Nyomkövetések létrehozásához a hibaelhárítási célból a LOGMAN helyett a WPR használatával.

### <a name="291"></a>2.9.1

- Ez a kiadás javít néhány problémát a lekérdezési API támogatásában, és visszaállítja a kompatibilitást a régebbi OSs-vel, például a Windows Server 2012-mel.

### <a name="290"></a>2.9.0

- Ezzel a kiadással beállíthatja, hogy a konzisztencia konzisztens előtagra legyen beállítva, és növelje a felhasználók és az engedélyek maximális korlátját.

### <a name="272"></a>2.7.2

- Ez a kiadás hozzáadja a MongoDB 3,6-es verziójú kiszolgálói támogatást a Cosmos Emulatorhoz. Egy olyan MongoDB-végpont indításához, amely a szolgáltatás 3,6-es verzióját célozza meg, indítsa el az emulátort egy rendszergazdai parancssorból a "/EnableMongoDBEndpoint = 3.6" kapcsolóval.

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
