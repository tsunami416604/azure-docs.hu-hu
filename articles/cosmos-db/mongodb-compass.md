---
title: Csatlakozás az Azure Cosmos DB-hez az Iránytű használatával
description: Ismerje meg, hogyan használhatja a MongoDB Compass segítségével az azure Cosmos DB-ben tárolt és felügyelt adatokat.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: c683ec0c4b3a536b0627a7c1c8abf28ee4f83663
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757032"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>A MongoDB Iránytű használatával csatlakozhat az Azure Cosmos DB MongoDB-hoz való API-jához

Ez az oktatóanyag bemutatja, hogyan használhatja a MongoDB Iránytű adatok tárolása és/vagy kezelése a Cosmos DB.This tutorial demonstrates how to use [MongoDB Compass](https://www.mongodb.com/products/compass) when data in Cosmos DB. Ehhez az átjáráshoz az Azure Cosmos DB MongoDB-hoz való API-ját használjuk. Azoknak, ismeretlen, Compass egy GUI a MongoDB. Gyakran használják az adatok megjelenítésére, ad-hoc lekérdezések futtatására, valamint az adatok kezelésére.

A Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Gyorsan hozhat létre és kérdezhet le dokumentumokat, kulcs-érték és gráf-adatbázisokat, amelyek mindegyike a Cosmos DB központi magjában lévő globális terjesztési és horizontális méretezési képességek előnyeit élvezi.

## <a name="pre-requisites"></a>Előfeltételek

A Cosmos DB-fiókjához a MongoDB Compass használatával történő csatlakozáshoz a következőket kell tennie:

* Az [Iránytű](https://www.mongodb.com/download-center/compass?jmp=hero) letöltése és telepítése
* A Cosmos DB [kapcsolati karakterlánc](connect-mongodb-account.md) adatainak

> [!NOTE]
> Jelenleg az Azure Cosmos DB API-t a MongoDB Server 3.2-es verziójához támogatja a MongoDB Compass.

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Csatlakozás a Cosmos DB MongoDB-hoz elérhető API-jához

A Cosmos DB-fiók compass-hoz való csatlakoztatásához kövesse az alábbi lépéseket:

1. Az [itt](connect-mongodb-account.md)található utasítások alapján olvassa be az Azure Cosmos DB API-jával konfigurált Cosmos-fiók kapcsolatadatait.

    ![Képernyőkép a kapcsolati karakterlánc panelről](./media/mongodb-compass/mongodb-compass-connection.png)

2. Kattintson arra a gombra, amely a Cosmos DB **elsődleges/másodlagos kapcsolati karakterlánca** melletti **Vágólapra** gombbal rendelkezik. Erre a gombra kattintva a teljes kapcsolati karakterlánc ot a vágólapra másolja.

    ![Képernyőkép a vágólapra másolásgombról](./media/mongodb-compass/mongodb-connection-copy.png)

3. Nyissa meg az Iránytűt az asztalon/gépen, majd kattintson a **Csatlakozás,** majd **a Csatlakozás...**.

4. Az Iránytű automatikusan felismeri a vágólapon lévő kapcsolati karakterláncot, és megkérdezi, hogy ezt szeretné-e használni a csatlakozáshoz. Kattintson az **Igen** gombra az alábbi képernyőképen látható módon.

    ![Képernyőkép a csatlakozáshoz az Iránytű üzenetről](./media/mongodb-compass/mongodb-compass-detect.png)

5. Ha a fenti lépésben az **Igen** gombra kattint, a kapcsolati karakterláncadatai automatikusan kitöltődnek. Távolítsa el az automatikusan kitöltött értéket a **Replikakészlet neve** mezőben, hogy biztosan üresen maradjon.

    ![Képernyőkép a csatlakozáshoz az Iránytű üzenetről](./media/mongodb-compass/mongodb-compass-replica.png)

6. Kattintson a **Csatlakozás** gombra az oldal alján. A Cosmos DB-fiókjának és adatbázisainak mostantól láthatónak kell lenniük a MongoDB Compass-on belül.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használhatja a [Studio 3T-t](mongodb-mongochef.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Fedezze fel a [MongoDB-mintákat](mongodb-samples.md) az Azure Cosmos DB MongoDB-hoz kapcsolódó API-jával.
