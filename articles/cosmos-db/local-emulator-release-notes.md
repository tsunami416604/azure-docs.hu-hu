---
title: Az Azure Cosmos emulátora letöltési és kiadási megjegyzések
description: Töltse le az Azure Cosmos emulátor kiadási megjegyzéseket a különböző verziókhoz, és töltse le az információkat.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 4dffe169908d0dd3effa4e46140b5f6696805a3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77168648"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos emulátor – Kibocsátási megjegyzések és letöltési információk

Ez a cikk az Azure Cosmos emulátor kiadási megjegyzések egy listát a funkció frissítéseket, amelyek az egyes kiadásokban készült. Ez is felsorol a legutolsó változat -ból emulátor -hoz letölt és használ.

## <a name="download"></a>Letöltés

| | |
|---------|---------|
|**MSI letöltés**|[Microsoft letöltőközpont](https://aka.ms/cosmosdb-emulator)|
|**Első lépések**|[Helyi fejlesztés az Azure Cosmos emulátorral](local-emulator.md)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="291"></a>2.9.1

- Ez a kiadás kijavítja a lekérdezési API-támogatás néhány problémánkat, és visszaállítja a kompatibilitást a régebbi számítógép-rendszerekkel, például a Windows Server 2012 rendszerrel.

### <a name="290"></a>2.9.0

- Ez a kiadás lehetőséget ad arra, hogy a konzisztenciát konzisztens előtagra állítsa be, és növelje a felhasználók és engedélyek maximális korlátát.

### <a name="272"></a>2.7.2

- Ez a kiadás hozzáadja a MongoDB 3.6-os verzióját a Cosmos emulátorhoz. A szolgáltatás 3.6-os verzióját célzó MongoDB-végpont elindításához indítsa el az emulátort egy rendszergazdai parancssorból a "/EnableMongoDBEndpoint=3.6" beállítással.

### <a name="270"></a>2.7.0

- Ez a kiadás olyan regressziót javít, amely megakadályozta, hogy a felhasználók lekérdezéseket hajtsanak végre az SQL API-fiókkal szemben az emulátorból a .NET core vagy x86 .NET alapú ügyfelek használatakor.

### <a name="246"></a>2.4.6

- Ez a kiadás 2019 júliusától paritást biztosít az Azure Cosmos szolgáltatás funkcióival, az [Azure Cosmos emulátorral helyi fejlesztés](local-emulator.md)című témakörben található kivételekkel. Azt is rögzíti, több hibát kapcsolatos emulátor leállítása, ha meghívást keresztül parancssori és belső IP-cím felülbírálja az SDK ügyfelek közvetlen módú kapcsolatot.

### <a name="243"></a>2.4.3

- Alapértelmezés szerint le van tiltva a MongoDB szolgáltatás indításakor. Alapértelmezés szerint csak az SQL-végpont van engedélyezve. A felhasználónak manuálisan kell elindítania a végpontot az emulátor "/EnableMongoDbEndpoint" parancssori kapcsolójával. Most, ez, mint az összes többi szolgáltatás végpontok, mint például a Gremlin, Cassandra és a Table.
- Javítottunk egy hibát az emulátorban, amikor az "/AllowNetworkAccess" kezdetű, ahol a Gremlin, a Cassandra és a Table végpontok nem voltak megfelelően kezelve a külső ügyfelektől érkező kéréseket.
- Adjon hozzá közvetlen kapcsolatportokat a tűzfalszabályok beállításaihoz.

### <a name="240"></a>2.4.0

- Kijavítottuk azt a problémát, amikor az emulátor nem indult el, amikor a hálózati figyelőalkalmazások, például a Pulse Client, jelen vannak a gazdaszámítógépen.
