---
title: Csatlakozás az Azure Cosmos DB használatával Compass nevű listáján
description: Ismerje meg, hogyan tárolhat és kezelhet az Azure Cosmos DB MongoDB Compass használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 06/24/2019
author: roaror
ms.author: roaror
ms.openlocfilehash: ad302df3144787fd231166c7052ecfde268f4086
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451037"
---
# <a name="use-compass-to-connect-to-your-cosmos-db-account"></a>A Cosmos DB-fiókhoz való csatlakozáshoz használja a Compass nevű listáján

Ez az oktatóanyag bemutatja, hogyan használható [MongoDB Compass](https://www.mongodb.com/products/compass) tárolására és/vagy a Cosmos DB-ben használt adatok kezelése során. Az Azure Cosmos DB API a mongodb-hez, ebben az útmutatóban a használjuk. Azok számára, ha nem ismerné iránytű grafikus felhasználói Felülettel a mongodb-hez. Az adatok megjelenítése, ad hoc lekérdezéseket futtatnia, és az adatok kezeléséhez gyakran szolgál. 

A cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Gyors létrehozása és lekérdezése, a dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike globális elosztási és horizontális skálázhatósági képességeket Cosmos DB középpontjában.


## <a name="pre-requisites"></a>Előfeltételek 
Ha csatlakozni szeretne a Cosmos DB-fiókot Robo 3T, tegye a következőket:

* Töltse le és telepítse [Compass nevű listáján](https://www.mongodb.com/download-center/compass?jmp=hero)
* A Cosmos DB rendelkezik [kapcsolati karakterlánc](connect-mongodb-account.md) információk

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Kapcsolódás a Cosmos DB API a mongodb-hez 
Csatlakozás a Cosmos DB-fiók Compass nevű listáján, kövesse az alábbi lépéseket:

1. Az Azure Cosmos DB API a MongoDB utasításokat követve konfigurált Cosmos-fiók kapcsolódási adatainak lekérése [Itt](connect-mongodb-account.md).

    ![A kapcsolati karakterlánc panelen – képernyőfelvétel](./media/mongodb-compass/mongodb-compass-connection.png)

2. Kattintson a gombra, amely szerint **példányt vágólapra** mellett a **elsődleges és másodlagos kapcsolati karakterlánc** Cosmos DB-ben. Erre a gombra kattintva másolja a teljes kapcsolati karakterláncot a vágólapra. 

    ![A példányt vágólapra gomb képernyőképe](./media/mongodb-compass/mongodb-connection-copy.png)

3. A desktop/gépen nyissa meg a Compass nevű listáján, majd kattintson a **Connect** , majd **kapcsolódás...** . 

4. Compass automatikusan észleli a kapcsolati karakterláncot a vágólapra, és kérni fogja kérni, hogy szeretné-e használni, amely csatlakozni. Kattintson a **Igen** az alábbi képernyőképen látható módon.

    ![Képernyőkép a a Compass parancssorhoz való csatlakozáshoz](./media/mongodb-compass/mongodb-compass-detect.png)

5. Gomb megnyomásakor **Igen** az előző lépésben az adatait a kapcsolati karakterláncból automatikusan tölti fel. Távolítsa el az értéket automatikusan kitölti a a **replika készlet neve** annak érdekében, hogy a mező üres. 

    ![Képernyőkép a a Compass parancssorhoz való csatlakozáshoz](./media/mongodb-compass/mongodb-compass-replica.png)

6. Kattintson a **Connect** az oldal alján. A Cosmos DB-fiókot és az adatbázisokhoz kell belül MongoDB Compass látható.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [a Studio 3T használata](mongodb-mongochef.md) az Azure Cosmos DB API a mongodb-hez.
- Ismerkedés a MongoDB [minták](mongodb-samples.md) az Azure Cosmos DB API a mongodb-hez.