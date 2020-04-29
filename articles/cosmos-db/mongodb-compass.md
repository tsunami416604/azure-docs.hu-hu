---
title: Kapcsolódás Azure Cosmos DB az iránytű használatával
description: Megtudhatja, hogyan tárolhatja és kezelheti Azure Cosmos DBban az MongoDB Compass használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: c683ec0c4b3a536b0627a7c1c8abf28ee4f83663
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80757032"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>A MongoDB Compass használata a Azure Cosmos DB API-MongoDB való kapcsolódáshoz

Ez az oktatóanyag azt mutatja be, hogyan használható a [MongoDB Compass](https://www.mongodb.com/products/compass) a Cosmos DBban tárolt adattároláshoz és/vagy azok kezeléséhez. Ehhez a MongoDB a Azure Cosmos DB API-ját használjuk. Az Ön számára ismeretlen, az iránytű a MongoDB grafikus felhasználói felülete. Általában az adatai megjelenítéséhez, alkalmi lekérdezések futtatásához, valamint az adatkezeléshez is használható.

Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Gyorsan létrehozhat és lekérdezheti a dokumentum-, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike kihasználja a globális elosztási és horizontális méretezési képességeket Cosmos DB középpontjában.

## <a name="pre-requisites"></a>Előfeltételek

Ha a MongoDB Compass használatával szeretne csatlakozni a Cosmos DB-fiókjához, a következőket kell tennie:

* Az [iránytű](https://www.mongodb.com/download-center/compass?jmp=hero) letöltése és telepítése
* A Cosmos DB a [kapcsolatok karakterláncával](connect-mongodb-account.md) kapcsolatos információk

> [!NOTE]
> A MongoDB Compass jelenleg támogatja a MongoDB Server 3,2-es verziójának API Azure Cosmos DB-ját.

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Kapcsolódás Cosmos DB MongoDB API-hoz

Ha Cosmos DB-fiókját az iránytűhöz szeretné kapcsolni, kövesse az alábbi lépéseket:

1. A Azure Cosmos DB API-MongoDB konfigurált Cosmos-fiókhoz tartozó kapcsolatok adatainak beolvasása az [itt](connect-mongodb-account.md)leírt utasítások alapján.

    ![Képernyőfelvétel a kapcsolatok sztring panelről](./media/mongodb-compass/mongodb-compass-connection.png)

2. Kattintson a gombra, amely a **vágólapra másolja a vágólapra** az **elsődleges/másodlagos kapcsolódási karakterlánc** mellett Cosmos db. A gombra kattintva a rendszer a teljes kapcsolódási karakterláncot a vágólapra másolja.

    ![Képernyőkép a másolás a vágólapra gombra](./media/mongodb-compass/mongodb-connection-copy.png)

3. Nyissa meg az iránytűt az asztalon/gépen, és kattintson a **Kapcsolódás** , majd a **Kapcsolódás..**. elemre.

4. Az iránytű automatikusan felderíti a kapcsolati karakterláncot a vágólapon, és rákérdez, hogy szeretné-e használni a kapcsolódást. Kattintson az **Igen** gombra, ahogy az alábbi képernyőképen is látható.

    ![A kapcsolódáshoz szükséges iránytű-üzenet képernyőképe](./media/mongodb-compass/mongodb-compass-detect.png)

5. Ha a fenti lépésben az **Igen** gombra kattint, a kapcsolati karakterlánc adatai automatikusan fel lesznek töltve. Távolítsa el az automatikusan kitöltött értéket a **replikakészlet neve** mezőben, így biztosítva, hogy a mező üres maradjon.

    ![A kapcsolódáshoz szükséges iránytű-üzenet képernyőképe](./media/mongodb-compass/mongodb-compass-replica.png)

6. Kattintson a lap alján található **kapcsolat** elemre. A Cosmos DB fiókjának és adatbázisainak most már láthatónak kell lenniük a MongoDB-iránytűn belül.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
